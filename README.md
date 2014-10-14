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

##### Model Validation

To validate the data in our model we can refer to the *model.validate( )* method.
This method allows checking the attributes values **prior** to setting them, and occurs
by default when the model is persisted via de *save( )* or *set( )* methods.
When setting an attribute with the *set()* method, validation is called by
passing in an object *{validate: true}* as an argument.

When the attributes provided are true, nothing should be returned from validate.
When an error occurs:
- an *invalid* event is triggered on the model, setting the
*validationError* property on the model with the value that is returned by this method.
- *save( )* will not continue and the attributes of the model will not be modified on the server.

```javascript
var Todo = Backbone.model.extend({

    defaults: {
        completed: false
    },

    validate: function (attribs) {
      if (attribs.title === undefined) {
          return "Remember to set a title for your todo.";
      }
    },

    initialize: function() {
        console.log("Model has been initialized");
        this.on("invalid", function (model, error) {
            console.log(error);
        });
    }
});

var todo1 = new Todo ();

todo1.set('completed', true, {validate: true});
// Logs: "Remember to set a title for your todo."

console.log("Completed: " + todo1.get('completed'));
// Logs: Completed: false ---> the default value because validation failed.
```

**Note**: If validation fails the current values of the attributes parameter passed into the
function (validate function) are not changed.
However, it is possible to change objects of nested functions.

---
#### Views

>Views in Backbone don't contain HTML markup for your application. Instead they contain the logic
>behind the presentation of the model's data to the user. This is achieved by using JavaScript
>templating (example: Underscore microtemplating, Handlebars, etc.).
>A view's *render( )* method can be bound to a model's *change( )* event, enabling
>the view to instantly reflect model changes without requiring a full page refresh.

**To create a view simple extend *Backbone.View*.**

```javascript
var todoView = Backbone.View.extend();
```

##### What is "el"?

The central property of a view is *el*. *el* is a reference to a DOM element.
All views must have one!

**The *el* property represents the markup portion of the view that will be rendered!**

Views can use *el* to compose their element's content and then insert it into the DOM
all at once, making for a faster rendering.

There are two ways to associate a DOM element with a view:
- by reference to an already existing element
- creating a new element

##### Creating a new element

To create a new element for your view, set any combination of the following properties
on the view: *tagName, id, class*. The framework will create a new element for you and a reference
to it will be available at the *el* property.
If nothing is specified, *tagName* will default to *div*.

```javascript
var todoView = Bakcbone.View.extend({
    tagName: 'ul', // required, but defaults to div if not set
    className: 'container', // optional
    id: 'todos' //optional
});

var todoV1 = new todoView();
console.log(todoV1.el); // Logs: <ul id="todos" class="container"></ul>
```
The preceeding code creates a new element but **DOES NOT** append it to the DOM.

##### Referencing a element

If an element already exists you can use *el* as a CSS selector that matches the element

```javascript
el: '#footer'
```

You can also set *el* to an existing element when creating the view.

```javascript
var V = new todoView({el:$('#footer')});
```

**Note:** When declaring a view you can define *options, el, tagName,id* and
*className* as functions, if you want their values to be determined at runtime.

##### $el and $()

To invoke view logic on the *el* element and elements nested within it,
Backbone does so by defining the **$el** property and **$( )** function.

*view.$el* property is equivalent to *$(view.el)* (used to set element characteristics such as html, css, etc)

**AND**

*view.$(selector)* is equivalent to *$(view.el).find(selector)* (used to find subelements in the selected element)

##### setElement

If you need to apply an existing view to a different DOM element, you can use *setElement*.

Overriding *this.el* (the el for the current view) needs to both change the DOM reference and
rebind event to the new element.

*setElement* will create a cached **$el** reference for you.

```javascript
// create two dom elements representing buttons
var button1 = $('<button></button>');
var button2 = $('<button></button>');

// Define a new view
var View = Backbone.View.extend ({
    events: {
        click: function (e) {
            console.log(view.el === e.target);
        }
    }
});

// Create a new instance of the view, applying it to button1
var view1 = new View({el: button1});

// Apply the view to button2 using setElement
view1.setElement(button2);

button1.trigger('click'); // call the trigger event
button2.trigger('click'); // returns true ---> the console log from the click function
```

##### Render ( )

** *render ( )* is an optional function that defines the logic for rendering a template.**
When using the default module **Underscore**, we can refer to the use of the
*_.template* method to compile JavaScript templates into functions that can be evaluated for rendering.

```javascript
var Bookmark = Backbone.View.extend({
    templ: _.template(...), // useful for rendering complicated bits of HTML from JSON data sources. http://underscorejs.org/#template
    render: function() {

        // sets whatever markup as the HTML content of the el DOM element using the $el property
        this.$el.html(this.templ(this.model.toJSON() )); // toJSON returns a shallow copy of  the model's attributes. "arguments" can be used instead of toJSON ( )

        return this; // A good convention is to return this at the end of render to enable chained calls.
  }
});
```
A good convention is to return *this* at the end of render. This is good for two main reasons:
- Makes views easily reusable on other parent views.
- Creates a list of elements without rendering and paints each of them individually, only to be drawn once the entire list is populated.

##### The events hash

Backbone's event hash allows you to attach event listeners to either el-relative custom
selectors, or directly to *el* if no selector is provided.

An event takes the for of a *key/value* pair such as 'eventName selector': 'callbackFunction' and a number of DOM event types are supported.

```javascript
// A sample view
var TodoView = Backbone.View.extend({
    tagName: 'li',

    // with an events hash containing DOM events specific to an item
    events: {
        'click .toggle': 'toggleCompleted', // click event on element with class name 'toggle' and callback function toggleCompleted
        'dbclick label': 'edit', // double click event on a label with callback edit
        'click .destroy': 'clear',
        'blur .edit': 'close'
    }
});
```

Keep in mind that when defining the callback functions these must remain in scope of the your view.

Within each callback function, *this* can be used to refer to the **TodoView** object, in the previous example.

**More complete example of a view** no that we know the elements that form it!

```javascript
var TodoView = Backbone.View.extend({

    tagName: 'li',

    // Cache the template function for a single item
    todoTpl: _.template( "An example template"),

    events: {
        'dbclick label': 'edit',
        'keypress .edit': 'updateOnEnter'
    }

    // Render the titles of the todo item
    render: function () {
        this.$el.html( this.todoTpl( this.model.toJSON() ));
        this.input = this.$('.edit');
        return this;
    }

    // Callbacks
    edit: function () {
        // executed when label is dbclicked
    },

    updateOnEnter: function () {
        // executed on each keypress when in todo mode, but we'll wait for enter to get in action
    }
});

var todo1 = new TodoView();

console.log(todo1.el); // logs <li></li>
```
---

#### Collections

Collections are sets of models, and you create them by extending **Backbone.Collection**, just
as we've done for views and models.

You can bind "change" events to be notified when any model in the collection has been modified, listen for "add" and "remove" events, fetch the collection from the server, and use a full suite of Underscore.js methods.

Any event that is triggered on a model in a collection will also be triggered on the collection directly, for convenience. This allows you to listen for changes to specific attributes in any model in a collection, for example: documents.on("change:selected", ...)

```javascript
var Collection = Backbone.Collection.extend({});
```

When creating a collections you will also want to define a property specifying the type
of model that your collection will contain, along with any instance properties required.

```javascript
var Todo = Backbone.Model.extend({
    defaults: {
        title: '',
        completed: false
    }
});

var todoCollection = Backbone.Collection.extend({
    model: Todo
});

var myTodo = new Todo({title:'Bake cookies', id: 2});

// pass array of models on collection instantiation
var todos = new todoCollection([myTodo]);

console.log("Collection size: " + todos.lenght); // Logs: Collection size: 1
```
##### Adding and removing models

After a collection has been created you can add and remove models using the *add( )* and
*remove( )* methods.

**Note** that *add( )* and *remove( )* accept both individual models and lists of models.

Also when using *add( )* on a collection, passing in *{merge: true}* causes the duplicate
models to have their attributes merged into the existing models instead of being ignored!

##### collection.add(models, [options])

Add a model (or an array of models) to the collection, firing
an "add" event. If a model property is defined, you may also pass
raw attributes objects, and have them be vivified as instances of the model.
Returns the added (or preexisting, if duplicate) models. Pass {at: index} to
splice the model into the collection at the specified index. If you're adding models to the collection that are already in the collection,
they'll be ignored, unless you pass {merge: true},
in which case their attributes will be merged into the corresponding models, firing any appropriate "change" events.

```javascript
var ships = new Backbone.Collection;

ships.on("add", function(ship) {
  alert("Ahoy " + ship.get("name") + "!");
});

ships.add([
  {name: "Flying Dutchman"},
  {name: "Black Pearl"}
]);
```
Note that adding the same model (a model with the same id) to a collection more than once
is a no-op.

##### collection.remove(models, [options])
Remove a model (or an array of models) from the collection, and returns them. Fires a "remove" event, which you can use silent to suppress. The model's index before removal is available to listeners as options.index.

##### Retrieving models

The most straightforward method for retrieving a model from a collection
is to use *Collection.get( )*, which accepts a single **id**.

```javascript
var myTodo = new Todo ({ title: "Get cookies", id: 2});

// pass aray of models on collection instantiation
var todos = new TodosCollection([myTodo]);

var todo2 = todos.get(2);

// Models, as objects, are passed by reference
console.log(todo2 === myTodo); // true
```

However in client-server applications, collections contain models obtained from the server.
You need a way to uniquely identify models.
In Backbone you can do so using the **id, cid(client id)** and the **idAttribute** properties.

**cid** is automatically generated by Backbone when a model is created, which is helpful when you don't have a true **id**.

**idAttribute** is the identifying attribute of the model returned form the server (i.e., the ide
  in your database).
This tells Backbone which data field from the server should be used to populate the **id** property.

For instance, if your server sets a unique attribute on your model named userId, then you would set **idAttribute** to userId in
your model definition.

The value of a model's **idAttribute** should be set by the server when the model is saved.
