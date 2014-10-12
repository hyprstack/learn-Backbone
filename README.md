# BackboneJs 1-on-1

### What is Backbone?

>Backbone, contrary to what many might think at first, is a lightweight JavaScript library, and not a framework,
>that adds structure to your client-side code.

### What is it used for?

>Develpers usually use libraries like Backbone to create *single-page applications* (SPA's).

### What is a MVC?

>MVC stands for "Model View Controller".
>A number of modern JavaScript frameworks provide developers with an easy path to organising their code using
>variations of a pattern known as MVC.

**MVC seperates the concerns in an application into thee parts:**
- Models represent the domain-specific knowledge and data in an application. Think of this a being a type
of data you can model.
- Views typically constitute the user interface in an application (such as markup and templates). **They observe models.**
- Controller handles the input and update models.

### Why consider using BackboneJs?

>Backbone provides a minimal set of data-structuring (models, collections) and user
> interface (views, URLs) primitives that are helpful when your'e building dynamic applications using
>Javascript.

>It's not opinionated, meaning you have the freedom to and flexibility
>to build the best experience for your web application however you see fit.

>It doesn't prescribe any specific template engine; while you are free to use
>the microtemplating offered by Underscore.js (one of its dependencies), views
>can bind to HTML constructed via your templating solution of choice.

### Getting started.


#### Models

**Backbone models contain data for the application along with the
logic around this data.**

##### Creating a model

If we wanted to create the concept of a todo list, with its attributes *title* and
*completed*, we could do the following:

```javascript
var Todo = Backbone.Model.extend({});
// We can then create our own new instance of a todo model with no values at all

var todo1 = new Todo();

// Or with some arbitrary data
var todo2 = new Todo({
  title: "Check this todo already as a title attribute",
  completed: true
});
```
##### Initialization of the model

The *initialize()* method is called when a new instance of a model is created.
Its use is optional, but it's best practice to use it.
Here is why:

```javascript
var Todo = Backbone.Model.extend({
    initialize: function(){
        console.log("This model has been initialized!");
    }
});

var todo1 = new Todo();
// Logs: This model has been initialized
```
