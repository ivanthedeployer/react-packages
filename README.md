# React TodoMVC with Minimongo
How do you manage data with React.js?

Build the frontend like the backend - with a database.  React is a JavaScript library for creating user interfaces.  Minimongo is MongoDB, in your browser. MiniMongo is reactive, so changes in your collection will automagically propagate to any React view that depends on them.

## Installation

```
npm install --save meteor-standalone-react-mixin 
```


## Use

```
// Import the mixin and MiniMongo collection constructor
import { Mongo, MeteorDataMixin } from 'meteor-standalone-react-mixin'

// Make a MiniMongo collection
var Todos = new Mongo.Collection('todos');

var MyReactClass = React.createClass({
    // Mixin your mixin
    mixins: [MeteorDataMixin],

    // Define 'getMeteorData'.  Any data accessed in this function will 
    // trigger an update in the UI when it changes
    getMeteorData: function(){
        // Return an object.  Anything returned will be available 
        // on the ReactClass on the .data property
        return {todos: Todos.find().fetch()}
    },
    ...
    render () {
        var todos = this.data.todos.map((todo) => <TodoItem todo={todo}/>);
        return (
            <ul>
                {todos}
            </ul>
        )
    }
});
```

For a full example and walkthrough, check out the (Todo example app)[https://github.com/ivanthedeployer/reactivetodo]

## Benefits

* Use a database! Everybody loves a database!
* Less code!
* Automatic UI updates!
* Nearly the entire MongoDB query language is available, including `$regex` and spatial queries!  Make your filtering code easy to read!
* Jargon free! (except maybe [reactive programming](https://en.wikipedia.org/wiki/Reactive_programming)) 
* Heavily tested!
* Includes other reactive data structures for whatever you can imagine!

## Further info

Minimongo is built by the [Meteor Development Group](http://docs.meteor.com/). It is built on [Tracker](https://www.meteor.com/tracker), a tiny reactivity library released by MDG. They are heavily tested and [well documented](http://docs.meteor.com/#/full/mongo_collection). This package is an NPM compatible standalone fork of MDG's [React mixin](https://github.com/meteor/react-packages/blob/devel/packages/react-meteor-data/meteor-data-mixin.jsx)


## FAQ

> "I don't like Mongo.  I hear bad things about it"

Don't worry, this is just a javascript implementation of its API.  You can take advantage of its powerful query syntax and thorough documentation and use whatever you like on your backed.



## Example App

(Todo)[https://github.com/ivanthedeployer/reactivetodo]


### Selectively watching data

What if you only want to listen to part of a collection?

```
var CompletedTodosExample = React.createClass({
    ...
    getMeteorData: function () {
        return {
            completedTodos: Todos.find({completed: true}).fetch()
        };
    },
```

Just narrow your query.  `CompletedTodosExample` only changes when a `Todo` matching that query changes.  Y

You can pay attention to a single `Todo` if you want

```
var SingleTodoExample = React.createClass({
    getMeteorData: function () {
        return {
            todo: app.Todos.find({_id: '1'}).fetch()
        };
    },
```

When the `Todo` with an ID of '1' is changed or deleted, `SingleTodoExample` is updated.  

More complicated - the `Todos` you didn't get to in the last week

```
var UnfinishedLastWeekExample = React.createClass({
    getMeteorData: function () {
        return {
            thisWeeksTodos: Todos.find({
                completed: false,
                createdAt: {
                    $gte: thisDayLastWeek
                }
            }).fetch()
        };
    },

    clearCompleted: function () {
        Todos.remove({completed: true});
    },
```

If I trigger `clearCompleted`, `UnfinishedLastWeekExample` won't change, but anything relying on `{completed: true}` will.

Because `Todos` are accessible from anything that imports them, `clearCompleted` go wherever its needed most, which saves a lot of passing functions as props.

```  
app.TodoItem = React.createClass({
    setTitle: function (val) {     
        Todos.update(this.props.todo._id, {
            $set: {
                title: val
            }
        });
    },

    markCompleted: function () {
        Todos.update(this.props.todo._id, {
            $set: {
                completed: true
            }
        });
    }
});
```

In the above example, `TodoApp` will update and pass down any changed props to its children, `TodoItem`s

With a single, universally accessible Source Of Truth, we can keep the whole app in sync without much code

### Quick API

The [Meteor Docs](http://docs.meteor.com/#/full/mongo_collection) are the best place to look for this, but your most common methods on `Collection` will be `find`, `findOne` , `insert`, `remove`, and `update`;

```
// Create a Todo
Todos.insert({title: 'Laundry', createdAt: new Date(), completed: false});

// Querying against dates just works by the way
Todos.find({title: 'Laundry', {createdAt: {$lte: {new Date()}}}}).count() // 1

// Updating
Todos.update({title: 'Laundry', completed: false}, {$set: {completed: true}}); // 1 updated

// Removing a specific item
Todos.remove({title: 'Laundry'});
```

Updating all incomplete laundry related todos created before now

```
// Update the owner of all completed todos - {query}, {statement}, {extras}
Todos.update({
    createdAt: {$gte: new Date(),
    completed: false,
    name: {
        $regex: /laundry/
    }
  }, {
    $set: {
        completed: true
    }
});

// Or a specific todo
Todos.update(todo._id, {$set: {completed: false}})
```

Deleting

```
Todos.remove(todo._id) // Remove a specific todo

Todos.remove({completed: true}) // All completed todos
```


The [Meteor Docs](http://docs.meteor.com/#/full/mongo_collection) have more details.  The query syntax is also documented at MongoDB.

This package depends on, exposes, and uses the following from MDG:
* `MeteorDataMixin`: Mixin for react classes that provides reactive context
* `Mongo.Collection`: A reactive Collection for Minimongo
* `ReactiveDict`: A reactive dictionary for when you need more than a variable and less than a collection
* `ReactiveVar`: The simplest unit of reactivity.  A single reactive variable.
* `Minimongo`: Frontend database created by MDG.  Access its functionality through `Mongo.Collection`
* `Tracker`: The engine behind all of this reactive programming.  A tiny, mindbending library worth exploring. [Docs here](https://www.meteor.com/tracker)
* `EJSON`: Extended JSON format

### Bonus

Other reactive data sources: 

* `ReactiveDict` allows you to create a reactive javascript dictionary.  When it changes it notifies anything listening
* `ReactiveVar` is even simpler.  A single reactive variable

use `.get(...)` and `.set(...)` to take advantage of these

Observe collections - You can pay attention to a `Mongo.Collection` query without the mixin.  
```
app.Todos.find({createdAt: {$lte: aMonthAgo}, completed: false}).observeChanges({
    removed: function(_id, changes) {
        if(changes.completed === true) {
            alert('Wow! you took a month to do that.  Good job');
        }
    }
});
```

In this case, when a `Todo` created over a month ago gets marked as complete, you get alerted

### Documentation

* [MiniMongo and Collection](http://docs.meteor.com/#/full/mongo_collection)
* [Tracker](https://www.meteor.com/tracker)
* [Mongo Query Syntax](https://docs.mongodb.org/manual/tutorial/query-documents/)
* [React Documentation](http://facebook.github.io/react/docs/getting-started.html)
* [React API Reference](http://facebook.github.io/react/docs/reference.html)
* [Meteor docs](http://docs.meteor.com/#/full)
