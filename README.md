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

The *initialize( )* method is called when a new instance of a model is created.
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

##### Setting default values in the model

There are times when you want your model to have a set of default values.
You can do this by using a property called *defaults* in your model.

```javascript
var Todo = Backbone.Model.extend({
    defaults: {
        title: "",
        completed: false
    }
});

// Now when you create a new instance of the model it will be set with those default values

var todo1 = new Todo();

console.log(JSON.stringify(todo1)); // Logs: {"title": "", "completed": "false"}


// You can override some or all of the default values by setting the values in the new instance of the model
var todo2 = new Todo({
    title: "Say Hello",
    completed: true
});
console.log(JSON.stringify(todo2)); // Logs: {"title": "Say Hello", "completed": 'true'}
```

##### Getters and Setters

*Model.get( )*

Model.get( ) provides easy access to a models attributes:

```javascript
var Todo = Backbone.Model.extend({
    defaults: {
        title: "",
        completed: false
    }
});

var todo1 = new Todo();
console.log(todo1.get('title')); // Logs: ""
console.log(todo1.get('completed')); // Logs: "false"
```
To use Model.get( ) simply replace "Model" with the name
of your new model instance (in our example - todo1) and pass
the name/key of the attribute as string to the *get* method (in our example -
  title and completed).

If you need to read or clone all of a model's **data attributes**, use
its *toJSON( )* method. This method returns a copy of the attributes as an
object (not a JSON string, despite its name).

```javascript
var Todo = Backbone.Model.extend({
    defaults: {
        title: "",
        completed: false
    }
});

var todo1 = new Todo();
var todo1Attributes = todo1.toJSON();
console.log(todo1Attributes); // Logs: {"title": "", "completed": "false"}
```
---
*Model.set( )*

Model.set( ) sets a hash containing one or more attributes on the model.
When any of these attributes alter the sate of the model, a change event is triggered
on it. Change events for each attribute are also triggered and
can be bound to the model (such as *change:name*, *change:age*).

```javascript
var Todo = Backbone.Model.extend({
    defaults: {
        title: "",
        completed: false
    }
});

var todo1 = new Todo();
todo1.set("title", "Title set through model.set()");
console.log(set1); // Logs: {"title": "Title set through model.set()", "completed": "false"}
```

Setting map of attributes through Model.set( )

```javascript
var Todo = Backbone.Model.extend({
    defaults: {
        title: "",
        completed: false
    }
});

var todo1 = new Todo();

todo1.set({
  title: "Set through ...",
  completed: true
});

console.log(todo1); // Logs: {"title": "Title set through...", "completed": "true"}
```

**Note**: Notice that to set a single attribute through *Model.set()*, the target
attribute is passed in as a string, as is its value. To set a map of attributes, the object
is passed in as one would when setting the defaults values or when creating a model.

##### Listening for changes to the Model

If you want to receive a notification when  a Backbone model changes,
you can bind a listener to the model for its *change* event. A convenient place
to add listeners is in the *initialize( )* function.
You can also listen for changes on individual attributes in a model.

**General changes**
```javascript
var Todo = Backbone.Model.extend({
    initialize: function(){
        console.log("This model has been initialized!");
        // add the listener for any changes
        this.on('change', function(){
            console.log('Values for this model have been changed!');
        });
    }
});

var todo1 = new Todo();
todo1.set('title', "Bazinga!");
console.log(todo1);

todo1.set('completed', true);
console.log(todo1.get('completed'));

// The above will log:
// This model has been initialized!
// Values for this model have been changed!
// {"title": "Bazinga"}
// Values for this model have been changed!
// true
```

**Individual changes**
```javascript
var Todo = Backbone.Model.extend({
    initialize: function(){
        console.log("This model has been initialized!");
        // add the listener for any changes
        this.on('change:title', function(){ // notice the difference here by specifying the attribute. Any change to other attributes will not trigger the listener!!!
            console.log('Values for the title attribute have been changed!');
        });
    },

    // We can also declare a specific function to set attributes to trigger the listener above
    setTitle: function (newTitle) {
        this.set({title: newTitle});
    }
});

var todo1 = new Todo();

// both of the following changes trigger the listener
todo1.set('title', 'Normal setter');
todo1.set('setTitle function');

// The above will log:
// This model has been initialized!
// Values for the title attribute have been changed!
// Values for the title attribute have been changed!
```
