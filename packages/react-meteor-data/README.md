## ReactMeteorData

This is a standalone version meant for use OUTSIDE of Meteor projects.  It has been minimally modified for installation with NPM and framework-agnostic use.

Check out the [Todo](https://github.com/ivanthedeployer/reactivetodo) to see how it works

This mixin is a convenient way to use data from a Meteor reactive data source in a React component, with automatic updates when the data changes.  

`getMeteorData` is a reactive context.  Whatever is returned is available on the React object's `.data` property.

More complete docs available at the [example Todo repo](https://github.com/ivanthedeployer/reactivetodo)

### Quick Example:

```
TodoApp = React.createClass({
  mixins: [ReactMeteorData],
  getMeteorData() {
    // Reactive context
    // do all your reactive data access in this method.
    return {
      todos: app.Todos.find().fetch(),
    };
    // /Reactive context
  },
  render() {
    return <div>
      <span>{this.data.todos.map(<TodoItem todo={todo}/>)}</span>
    </div>;
  }
});
```

For more information, see [the guide](http://react-in-meteor.readthedocs.org/en/latest/meteor-data/).


### API
This package exposes: 
* LocalCollection
* Minimongo
* ReactiveDict
* ReactiveVar
* Tracker
* EJSON
* ... and more!