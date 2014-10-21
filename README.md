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

> Any visual element can be a view (example: button, list, even the entire screen). Views can be embeded in views.

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

An event takes the form of a *key/value* pair such as 'eventName selector': 'callbackFunction' and a number of DOM event types are supported.

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

**More complete example of a view** now that we know the elements that form it!

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

##### Listening for events

Because collections represent a group of items, we can listen for *add* and *remove*
events which occur when models are added to or removed from a collection.

```javascript
var tdCol = new Backbone.Collection.extend();

tdCol.on('add', function ( todo ) {
  console.log("I should " + todo.get('title') + ". Have I done it before? " + (todo.get('completed') ? 'Yeah!': 'No!'));
});

tdCol.add([
    { title: 'go to Jamaica', completed: false},
    { title: 'go to America', completed: true}
]);

// The above logs:
// I should go to Jamaica. Have I done it before? No!
// I should go to America. Have I done it before? Yes!
```
In addition we're also able to bind to a *change* event to listen for changes to any
of the models in the collection.

```javascript
var tdCol = new Backbone.Collection();

// log a message if a model in the collection changes

tdCol.on("change:title", function (model) {
    console.log('Changed my mind! I should ' + model.get('title'));
});

tdCol.add([
    { title: 'go to Jamaica.', completed: false, id: 3 }
]);

var myTodo = tdCol.get(3);

myTodo.set('title', 'go fishing');
// Logs: Changed my mind! I should go fishing
```

jQuery-style event maps of the form *obj.on({ click: action })* can also be used. These
can be clearer than using three separate calls to *.on* and should align better with
the events hash in views:

```javascript
var Todo = Backbone.Model.extend({
    defaults: {
        title:"",
        completed: false
    }
});

var myTodo = new Todo ();
myTodo.set({ title: "By cookies", completed: true });

myTodo.on({
    'change:title' : titleChanged, // calls function titleChanged on event change of the title
    'change:completed': stateChange
});

function titleChange() {
    console.log('The title was changed!');
}

function stateChanged() {
    console.log('The state changed!');
}

myTodo.set({ title: "Bazinga!" });

// Logs: The title was changed!
```
Backbone events also support an *once( )* method, which ensures that a callback fires only one time
when a notification arrives. It is similar to node's *once* and jQuery's *one*

```javascript
// Define an object with two counters
var TodoCounter = { counterA: 0, counterB: 0 };
// Mix in Backbone Events
_.extend(TodoCounter, Backbone.Events);

// Increment counterA, triggering an event
var incrA = function(){
  TodoCounter.counterA += 1;
  // This triggering will not
  // produce any effect on the counters
  TodoCounter.trigger('event');
};

// Increment counterB
var incrB = function(){
  TodoCounter.counterB += 1;
};

// Use once rather than having to explicitly unbind
// our event listener
TodoCounter.once('event', incrA);
TodoCounter.once('event', incrB);

// Trigger the event for the first time
TodoCounter.trigger('event');

// Check out output
console.log(TodoCounter.counterA === 1); // true
console.log(TodoCounter.counterB === 1); // true
```
Counters A and B should only have been incremented once.

##### Resetting/Refreshing Collections

Instead of removing models individually, you might want to update an entire collection
at once. *Collection.set( )* takes an array of models and performs the necessary add,
remove, and change operations.

If you simply need to replace the entire collection, use *Collection.reset( )*

Another useful tip with **reset** is to use it with no arguments. This will clear out a collection completely.
This is useful when you're dynamically loading a new page of results and want to blank out the current page of results.
*Collection.reset( )* doesn't fire any **add or remove** events. A **reset** event is fired instead. Also when listening to a reset event, the
list of previous models is available in *options.previousModels*, for convenience.

``` javascript
var TodosCollection = new Backbone.Collection();

TodosCollection.add([
    { id: 1, title: 'go to Jamaica.', completed: false },
    { id: 2, title: 'go to China.', completed: false },
    { id: 3, title: 'go to Disneyland.', completed: true }
]);

// we can listen for add/change/remove events
TodosCollection.on("add", function(model) {
  console.log("Added " + model.get('title'));
});

TodosCollection.on("remove", function(model) {
  console.log("Removed " + model.get('title'));
});

TodosCollection.on("change:completed", function(model) {
  console.log("Completed " + model.get('title'));
});

TodosCollection.set([
    { id: 1, title: 'go to Jamaica.', completed: true },
    { id: 2, title: 'go to China.', completed: false },
    { id: 4, title: 'go to Disney World.', completed: false }
]);

// Above logs:
// Completed go to Jamaica.
// Removed go to Disneyland.
// Added go to Disney World.
```
**Simply replace entire content of a colection**

```javascript
var TodosCollection = new Backbone.Collection();

// we can listen for reset events
TodosCollection.on("reset", function() {
  console.log("Collection reset.");
});

TodosCollection.add([
  { title: 'go to Jamaica.', completed: false },
  { title: 'go to China.', completed: false },
  { title: 'go to Disneyland.', completed: true }
]);

console.log('Collection size: ' + TodosCollection.length); // Collection size: 3

TodosCollection.reset([
  { title: 'go to Cuba.', completed: false }
]);
// Above logs 'Collection reset.'

console.log('Collection size: ' + TodosCollection.length); // Collection size: 1
```
**Completely clear out a collection**

```javascript
myCollection.reset();
```
**options.previousModels**

```javascript
var todo = new Backbone.Model();
var todos = new Backbone.Collection([todo])
.on('reset', function(todos, options) {
  console.log(options.previousModels);
  console.log([todo]);
  console.log(options.previousModels[0] === todo); // true
});
todos.reset([]);
```

**the update method**

```javascript
// Define a model of type 'Beatle' with a 'job' attribute
var Beatle = Backbone.Model.extend({
  defaults: {
    job: 'musician'
  }
});

// Create models for each member of the Beatles
var john = new Beatle({ firstName: 'John', lastName: 'Lennon'});
var paul = new Beatle({ firstName: 'Paul', lastName: 'McCartney'});
var george = new Beatle({ firstName: 'George', lastName: 'Harrison'});
var ringo = new Beatle({ firstName: 'Ringo', lastName: 'Starr'});

// Create a collection using our models
var theBeatles = new Backbone.Collection([john, paul, george, ringo]);

// Create a separate model for Pete Best
var pete = new Beatle({ firstName: 'Pete', lastName: 'Best'});

// Update the collection
theBeatles.set([john, paul, george, pete]);

// Fires a `remove` event for 'Ringo', and an `add` event for 'Pete'.
// Updates any of John, Paul and Georges's attributes that may have
// changed over the years.
```
##### Underscore utility functions

Backbone takes full advantage of its hard dependency on [Underscore](http://underscorejs.org/#),
by making many of its utilities directly available on collections.

**forEach: iterate over collections**

```javascript
var todos = new Backbone.Collection();

todos.add([
  { title: 'go to Belgium.', completed: false },
  { title: 'go to China.', completed: false },
  { title: 'go to Austria.', completed: true }
]);

// iterate over models in the collection
todos.forEach(function(model){
  console.log(model.get('title'));
});
// Above logs:
// go to Belgium.
// go to China.
// go to Austria.
```
**sortBy(): sort a collection on a specific attribute**

```javascript
// sort collection
var sortedByAlphabet = todos.sortBy(function (todo) {
    return todo.get("title").toLowerCase();
});

console.log("- Now sorted: ");

sortedByAlphabet.forEach(function(model){
  console.log(model.get('title'));
});
// Above logs:
// - Now sorted:
// go to Austria.
// go to Belgium.
// go to China.
```

**map(): iterate through a collection, mapping each value through a transformation function**

```javascript
var count = 1;
console.log(todos.map(function(model){
  return count++ + ". " + model.get('title');
}));
// Above logs:
//1. go to Belgium.
//2. go to China.
//3. go to Austria.
min()/max(): retrieve item with the min or max value of an attribute

todos.max(function(model){
  return model.id;
}).id;

todos.min(function(model){
  return model.id;
}).id;
pluck(): extract a specific attribute

var captions = todos.pluck('caption');
// returns list of captions
filter(): filter a collection
```

**Filter by an array of model IDs**

```javascript
var Todos = Backbone.Collection.extend({
  model: Todo,
  filterById: function(ids){
    return this.models.filter(
      function(c) {
        return _.contains(ids, c.id);
      })
  }
});
```
**indexOf(): return the index of a particular item within a collection**

```javascript
var people = new Backbone.Collection;

people.comparator = function(a, b) {
  return a.get('name') < b.get('name') ? -1 : 1;
};

var tom = new Backbone.Model({name: 'Tom'});
var rob = new Backbone.Model({name: 'Rob'});
var tim = new Backbone.Model({name: 'Tim'});

people.add(tom);
people.add(rob);
people.add(tim);

console.log(people.indexOf(rob) === 0); // true
console.log(people.indexOf(tim) === 1); // true
console.log(people.indexOf(tom) === 2); // true
```

**any(): confirm if any of the values in a collection pass an iterator truth test**

```javascript
todos.any(function(model){
  return model.id === 100;
});

// or
todos.some(function(model){
  return model.id === 100;
});
```

**size(): return the size of a collection**

```javascript
todos.size();

// equivalent to
todos.length;
```

**isEmpty(): determine whether a collection is empty**

```javascript
var isEmpty = todos.isEmpty();
```

**groupBy(): group a collection into groups of like items**

```javascritp
var todos = new Backbone.Collection();

todos.add([
  { title: 'go to Belgium.', completed: false },
  { title: 'go to China.', completed: false },
  { title: 'go to Austria.', completed: true }
]);

// create groups of completed and incomplete models
var byCompleted = todos.groupBy('completed');
var completed = new Backbone.Collection(byCompleted[true]);
console.log(completed.pluck('title'));
// logs: ["go to Austria."]
```

#### RESTful Persistence

For most single-page applications, the models are derived from a data set residing on the server.
This is an area in which Bakcbone dramatically simplifies the code you need to write to perfom RESTful
synchronization with a server through a simple API on its models and collections.

##### Backbone.sync

Backbone.sync is the function that Backbone calls every time it attempts to read or save a model to the server.
By default, it uses jQuery.ajax to make a RESTful JSON request and returns a jqXHR. You can override it in order
to use a different persistence strategy, such as WebSockets, XML transport, or Local Storage.

The method signature of Backbone.sync is sync(method, model, [options])

- method – the CRUD method ("create", "read", "update", or "delete")
- model – the model to be saved (or collection to be read)
- options – success and error callbacks, and all other jQuery request options

With the default implementation, when Backbone.sync sends up a request to save a model, its attributes will be passed,
serialized as JSON, and sent in the HTTP body with content-type application/json. When returning a JSON response,
send down the attributes of the model that have been changed by the server, and need to be updated on the client.
When responding to a "read" request from a collection (Collection#fetch), send down an array of model attribute objects.

The sync function may be overriden globally as Backbone.sync, or at a finer-grained level, by adding a sync function to
a Backbone collection or to an individual model.

The default sync handler maps CRUD to REST like so:

- create → POST   `/collection`
- read → GET   `/collection[/id]`
- update → PUT   `/collection/id`
- patch → PATCH  `/collection/id`
- delete → DELETE   `/collection/id`

##### Options

Each RESTful API method accepts a variety of options. Most importantly, all methods accept success and error callbacks that can be used
to customize the handling of server responses.

Specifying the *{patch: true}*  option to *Model.save( )* will cause it to use HTTP PATCH to send only the changed attributes to the server,
 instead of the entire model -  that is *model.save(attrs, {patch: true})*

```javascript
// Save partial using PATCH
model.clear().set({id: 1, a: 1, b: 2, c: 3, d: 4});
model.save();
model.save({b: 2, d: 4}, {patch: true});
console.log(this.syncArgs.method);
// 'patch'
```


##### Fetching models from the server

*Collections.fetch( )* retrieves a set of models from the server in the form of a JSON
array by sending an HTTP GET request to the URL specified by the collection's url property
(which may be a function).
When this data is received, a *set( )* will be executed to update the collection.

```javascript
var Todo = Backbone.Model.extend({
  defaults: {
    title: '',
    completed: false
  }
});

var TodosCollection = Backbone.Collection.extend({
  model: Todo,
  url: '/todos'
});

var todos = new TodosCollection();
todos.fetch(); // sends HTTP GET to /todos
```

##### Saving Models to the Server

Although Backbone can retrieve an entire Collection of models from the server at once,
updates to models are done individually through the *save( )* method of the model.
When *save* is called on a retrieved model, it constructs a URL and sends it an HTTP PUT to the server,
appending the model's *id* to the *collection's* url.
**Returns a jqXHR if validation is successful and false otherwise.**
If it's a new instance of the model (no *id*) then an HTTP POST request is sent instead.
*Collections.create( )* can be used to create a new model, add it to the collection, and send it to the server
in a single method call.

```javascript
var Todo = Backbone.Model.extend({
  defaults: {
    title: '',
    completed: false
  }
});

var TodosCollection = Backbone.Collection.extend({
  model: Todo,
  url: '/todos'
});

var todos = new TodosCollection();
todos.fetch();

var todo2 = todos.get(2);
todo2.set('title', 'go fishing');
todo2.save(); // sends HTTP PUT to /todos/2

todos.create({title: 'Try out code samples'}); // sends HTTP POST to /todos and adds to collection
```

##### Deleting Models form the Server

To remove a model from the containing collection, call the *destroy( )* method. *Model.destroy( )* will send an
HTTP DELETE request to the collection's URL (Backbone.sync).
Returns a jqXHR object, or false if the model isNew. Accepts success and error callbacks in the options hash,
which will be passed (model, response, options). Triggers a "destroy" event on the model, which will bubble up
through any collections that contain it, a "request" event as it begins the Ajax request to the server, and a "sync"
event, after the server has successfully acknowledged the model's deletion. Pass {wait: true} if you'd like to wait
for the server to respond before removing the model from the collection.

```javascript
var Todo = Backbone.Model.extend({
  defaults: {
    title: '',
    completed: false
  }
});

var TodosCollection = Backbone.Collection.extend({
  model: Todo,
  url: '/todos'
});

var todos = new TodosCollection();
todos.fetch();

var todo2 = todos.get(2);
todo2.destroy(); // sends HTTP DELETE to /todos/2 and removes from collection
```

#### Backbone.Events

Backbone.events is a module that can be mixed in to any object, giving the object the ability
to bind and trigger custom named events!
Events to have to be declared before they are bound, and may take passed arguments.

```javascript
var object = {};

_.extend(object, Backbone.Events);

object.on("alert", function(msg) {
  alert("Triggered " + msg);
});

object.trigger("alert", "an event");
```

Mastering events is one of the quickest ways to become more productive with Backbone.

Since Backbone is globally visible, it can be used as a simple event bus.

```javascript
Backbone.on('event', function() {console.log('Handled Backbone event');});
Backbone.trigger('event'); // logs: Handled Backbone event
```

##### trigger

*object.trigger(event, [*args])*

Trigger callbacks for the given event, or space-delimited list of events.
Subsequent arguments to trigger will be passed along to the event callbacks.

##### on

*object.on(event, callback, [context])Alias: bind*

Bind a callback function to an object. The callback will be invoked whenever the event is fired.
If you have a large number of different events on a page, the convention is to use colons to namespace
them: `poll:start`, or `change:selection`. The event string may also be a space-delimited list of several events...

`book.on("change:title change:author", ...);`

To supply a context value for this when the callback is invoked,
pass the optional third argument: `model.on('change', this.render, this)`

Callbacks bound to the special `all` event will be triggered when any event occurs, and are passed the name of the
event as the first argument. For example, to proxy all events from one object to another:

```javascript
proxy.on("all", function(eventName) {
  object.trigger(eventName);
});
```

All Backbone event methods also support an event map syntax, as an alternative to positional arguments:

```javascript
book.on({
  "change:title": titleView.update,
  "change:author": authorPane.update,
  "destroy": bookView.remove
});
```

##### off

`object.off([event], [callback], [context]) Alias: unbind`

Remove a previously-bound **callback** function from an object. If no **context** is specified, all of the versions of
the callback with different contexts will be removed. If no callback is specified, all callbacks for the **event** will be removed.
If no event is specified, callbacks for all events will be removed.

**Note that calling *model.off()*, for example, will indeed remove all events on the model —
including events that Backbone uses for internal bookkeeping.**

```javascript
// Removes just the `onChange` callback.
object.off("change", onChange);

// Removes all "change" callbacks.
object.off("change");

// Removes the `onChange` callback for all events.
object.off(null, onChange);

// Removes all callbacks for `context` for all events.
object.off(null, null, context);

// Removes all callbacks on `object`.
object.off();
```

##### listenTo()/stopListening()

**listenTo( )**

*object.listenTo(other, event, callback)*

Tell an **object** to listen to a particular event on an **other** object. The advantage of using this form,
instead of `other.on(event, callback, object)`, is that **listenTo** allows the **object** to keep track of the events,
and they can be removed all at once later on. The **callback** will always be called with **object** as context.

`view.listenTo(model, 'change', view.render);`

**stopListening( )**

*object.stopListening([other], [event], [callback])*

Tell an **object** to stop listening to events. Either call **stopListening** with no arguments to have the **object** remove
all of its registered callbacks ... or be more precise by telling it to remove just the events it's listening to
on a specific object, or a specific event, or just a specific callback.

`view.stopListening();`

`view.stopListening(model);`

##### Ghost Views
> When you remove a view that had registered to be notified about events on a model, but you don't remove the model or call
> **off** to remove the view's event handler, a problem arises. Since the model has a reference to the view's callback function, the
> JavaScript garbage collector cannot remove the view from memory. This is called a *ghost view* and is a commom for of memeory leak since
>the models generally tend to outlive the corresponding views during an application's lifecycle.
** For further details on the topic visit** http://bit.ly/ZN0Sci

#### Events and Views

Inside a view, there are two types of events you can listen for:

- DOM events

- Events triggered using the Event API.

The differences in how views bind to these two events and the context in which their callbacks are invoked is important to understand:

- Bind DOM events using the view's *event* property or using *jQuery.on( )*
- Within callbacks bound using the events property, *this* refers to the view object.
- Any callback bound directly using jQuery, will have *this* set to the handling DOM element
- All DOM event callbacks are passed an *event* object by jQuery


- In event API's, if you bind the event using *on( )* on the observed object, you can pass a context as the third argument.
- If you bind the event using *listenTo( )*, then in the callback, *this* refers to the listener.
- The arguments passed to Event API callbacks depend on the type of event.

```javascript
<div id="todo">
    <input type='checkbox' />
</div>
```

```javascript
var View = Backbone.View.extend({

    el: '#todo',

    // bind to DOM event using events property
    events: {
        'click [type="checkbox"]': 'clicked',
    },

    initialize: function () {
        // bind to DOM event using jQuery
        this.$el.click(this.jqueryClicked);

        // bind to API event
        this.on('apiEvent', this.callback);
    },

    // 'this' is view
    clicked: function(event) {
        console.log("events handler for " + this.el.outerHTML);
        this.trigger('apiEvent', event.type);
    },

    // 'this' is handling DOM element
    jqueryClicked: function(event) {
        console.log("jQuery handler for " + this.outerHTML);
    },

    callback: function(eventType) {
        console.log("event type was " + eventType);
    }

});

var view = new View();
```



---

### Resources

- Youtube Backbone tutorial fo beginners - https://www.youtube.com/watch?v=jM8KE_Fa6JI
- Addy Osmani's book on backbone: http://addyosmani.github.io/backbone-fundamentals/
