# Week 10

## Mongoose

### Question #1

Describe the differences between a SQL and NoSQL DB, and when you might use each.

```text
Your answer...

```

### Question #2

What's wrong with this mongoose code and how might we fix it?
(Hint: Assuming there is a document with a name of "Bob", why is results not an author model on the second line?)

```js
var results = AuthorModel.find({name: "Bob"});
console.log(results);
```

```js
// You have to call the function
var Schema = require("./db/schema.js");
var AuthorModel = Schema.AuthorModel

var authorsController ={
  index: function(){
    AuthorModel.find({}, function(err,doc){
      console.log(doc);
    });
  },
  show: function(req){
    AuthorModel.findOne({"name": req.name}, function(err, docs){
      console.log(docs);
    });
  }
};
authorsController.index();
authorsController.show({name: "Bob"});

```

### Question #3

Convert the following ActiveRecord sequence to Mongoose:

```rb
@andy = Instructor.find_by(name: "Andy")
@andy.wishlist_items.create(description: "Resin Laying Deer Figurine, Gold")
```

```js
// find
db.Instructor.find({name: "Andy"});
// insert
db.wishlist_items.insert({description: "Resin Laying Deer Figurine, Gold"
})
```

### Question #4

Convert the following create method in Mongoose to ActiveRecord.

```js
  var authors = {
  create: function(req, res){
  var author = new AuthorModel({name: req.body.name})
  author.save(function(err){
    if (!err){
      res.redirect("authors")
    }
  })
  }  
}
```

```rb
Bob = Author.create!(name: "Bob")
```
## Express

### Question #5

How does module.exports help us with separation of concerns?

```text
module.exports allows us to separate our js files by exposing their contents as one global variable. The global variable isn't assigned until we require the file.

Well, we can actually separate our concerns using module.exports If we change our get request in index.js

to this instead. We could create a routes module that defines our index route. Let's create a controllers/bottles.js file with the following contents:


You can see that almost nothing has changed, really we just moved the functionality into a different file. What advantages does that bring to us with regard to separation of concerns in MVC? (st-wg)
You can start to see the necessity of module.export when we start to add models to our application. If we had the 7 RESTful routes that rails have for each model, you can start to see how keeping everything in the index.js can begin to become unwieldy.
```

### Question #6

Write one Express route for each of the four HTTP methods.

Then, make each route respond with a one-word string containing the RESTful action that would most likely be associated with this route.

```js

var express = require("express");
var parser  = require("body-parser");
var hbs = require("express-handlebars");
var mongoose = require("./db/connection");

var app = express();

var Author = mongoose.model("Author");

app.set("port", process.env.PORT || 3000);
app.set("view engine", "hbs");
app.engine(".hbs", hbs({
  extname:        ".hbs",
  partialsDir:    "views/",
  layoutsDir:     "views/",
  defaultLayout:  "layout-main"
}));
app.use("/assets", express.static("public"));
app.use(parser.urlencoded({extended: true}));

app.get("/", function(req,res){
  res.render("app-welcome");
});

app.get("/authors/:name", function(req, res){
  Author.findOne({name: req.params.name}).then(function(author){
    res.render("author-index", {
      authors: authors
    });
  });
});

app.post("/authors", function(req, res){
  Author.create(req.body.author).then(function(author){
    res.redirect("/authors/" + author.name);
  });
});

app.put("/authors/:name", function(req, res){
  Author.findOneAndUpdate({name: req.params.name}, req.body.author, {new: true}).then(function(author){
    res.redirect("/authors/" + author.name);
  });
});

app.delete("/authors/:name/delete", function(req, res){
  Author.findOneAndRemove({name: req.params.name}).then(function(){
    res.redirect({success: true});
  });
});

app.listen(3000, function(){
  console.log("Workie Work");
});
// Your code starts here...

```


```js
// Found this example that is better and cleaner
var express = require('express');
var app = express();
var router = express.Router();

router.param('student_id', function(req, res, next, id) {
  req.student = {
    id: id,
    name: 'Frish'
  };
  next();
});

router.route('/students/:student_id')
.all(function(req, res, next) {

  next();
})
.get(function(req, res, next) {
  res.json(req.student);
})
.put(function(req, res, next) {

  req.student.name = req.params.name;

  res.json(req.student);
})
.post(function(req, res, next) {
  next(new Error('not implemented'));
})
.delete(function(req, res, next) {
  next(new Error('not implemented'));
});
```
### Question #7

Describe the differences between Express and Rails as backend frameworks.

```text

```

### Question #8

What is the importance of using body-parser in our express application for post requests?

```js

Parse request bodies. Extracts the entire body portion of an request stream and exposes it on req.body.

Parse urlencoded request bodies,
providing the parsed object as req.body.

It is important bc body-parser populates req.body with the value of post params. This will allow us to grab information from the POST. You are able to  grab POST parameters using the parser.

Body parser urlencoded Parses the text as URL encoded data (which is how browsers tend to send form data from regular forms set to POST) and exposes the resulting object (containing the keys and values) on req.body.



```
