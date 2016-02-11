# ReactMeteorData

The simplest, most powerful data solution for React.js

Meteor Development Group, MDG, built the ReactMeteorData mixin to integrate React with Meteor. When repackaged for use in other stacks, it makes an excellent datastore for React.js

### Installation

```
npm install --save meteor-standalone-react-mixin
```

### Quick Example:

```
import { Mongo, ReactMeteorData } from 'meteor-standalone-react-mixin'

var Todos = new Mongo.Collection('todos');

TodoApp = React.createClass({

  mixins: [ReactMeteorData],

  getMeteorData() {
    // Do all your Data access here.
    return {
      todos: Todos.find().fetch(),
    };
    // /Reactive context
  },

  render() {
    return (<div>
              <ul>{this.data.todos.map(<TodoItem todo={todo}/>)}</ul>
            </div>);
  }
});
```

Just add the `getMeteorData` mixin. The returned object is available on the React class's `.data` property.  This property acts like React's `state` or `props`.  When `.data`, the component is notified. No further code necessary.

### How does it work?

`Todos.find()` returns a query cursor.  If you call `.fetch()`, `.findOne`, `.count()` or another reactive method on the cursor, you will get reactive data back.

The body of `getMeteorData` is a reactive context.  When you access any reactive variables inside of it, it will rerun when those variables change.

  This means that you can write your code in a simple imperative style, and the result will be automatically recalculated whenever data changes that your code depends on.

Too much jargon?  When the `Todos` collection changes, your `TodoApp` will update like you changed the `state` or `props`, only you didn't have to tell it.

[In more detail](http://docs.meteor.com/#/full/reactivity)

### Demo / Detailed example

Check out the [Todo](https://github.com/ivanthedeployer/todo) to see how it works in detail.

### Docs & API

This is a standalone version of [Meteor's Mixin](http://react-in-meteor.readthedocs.org/en/latest/meteor-data/) and the [ReactMeteorData mixin](https://react-in-meteor.readthedocs.org/en/latest/meteor-data/) meant for use OUTSIDE of Meteor projects.  It has been minimally modified for installation with NPM and framework-agnostic use.

[Official MDG mixin docs](https://react-in-meteor.readthedocs.org/en/latest/meteor-data/)

### Other docs

This package exposes: 
* [Minimongo & Mongo.Collection](http://docs.meteor.com/#/full/mongo_collection)
* [Meteor](http://react-in-meteor.readthedocs.org/en/latest/meteor-data/).
* [ReactiveDict](http://docs.meteor.com/#/full/reactivevar_pkg)
* [ReactiveVar](http://docs.meteor.com/#/full/reactivevar_pkg)
* [Tracker](https://www.meteor.com/tracker)
* [EJSON](http://docs.meteor.com/#/full/ejson)
* ... and more!
