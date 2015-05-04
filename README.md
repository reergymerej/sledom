# Sledom v0.1.2 [![Build Status](https://travis-ci.org/reergymerej/sledom.svg?branch=v0.1.2)](https://travis-ci.org/reergymerej/sledom)

Why should you have to commit to one [MVC](https://developer.chrome.com/apps/app_frameworks#mvc) framework for your app?  Haven't we all learned that being modular is better?

## Features
* field normalization
* field types, automatic conversion
* enum fields
* default field values
* computed fields
* validation
* dirty state
* event binding

## Quick Start

### Define a Model
```js
var sledom = require('sledom');
var Person = sledom.define('Person', {
  fields: {
    name: { type: sledom.STRING },
    eyeColor: { type: sledom.STRING },
    age: { type: sledom.NUMBER }
  }
});
```

### Create an Instance
```js
var dude = new Person({
  name: 'Lebowski',
  eyeColor: 'blue',
  age: 42
});
```

### Use the Instance
```js
// Listen for changes.
dude.on(sledom.CHANGE, function (changes) {
  console.log(changes);
});

// Set values.
dude.set({ age: 43 });  // logs { age: 43 }

// Get values.
dude.get();  // { name: 'Lebowski', eyeColor: 'blue', age: 43 }
```

Now that's just the beginning.  Keep scrolling for more.

Sledom is in active development, so your feedback and questions are appreciated.
<https://github.com/reergymerej/sledom/issues>

===============================================================================

## API

### Model Definition

```js
var sledom = require('sledom');

var Foo = sledom.define('Foo', {
  idField: 'name',
  fields: {
    name: { type: sledom.STRING },
    bar: { type: sledom.NUMBER, default: 42 },
    baz: { type: sledom.BOOLEAN, default: true }
  }
});

var foo = new Foo({ name: 'asdf', bar: 99 });
```

### Getting Field Values

```js
foo.get(); // { name: 'asdf', bar: 99, baz: true }
foo.get('bar'); // 99
foo.id(); // 'asdf'
```

### Setting Field Values

```js
foo.id('new id');
foo.id(); // 'new id'

foo.set('bar', 123);
foo.get('bar'); // 123

foo.set({
    name: 'dude',
    bar: 3.14,
    baz: false
})
foo.get(); // { name: 'dude', bar: 3.14, baz: false }
```

### Computed Fields

Computed field values are created dynamically based on the model's other fields.

```js
var Foo = sledom.define('Foo', {
  fields: {
    firstName: {
      type: sledom.STRING
    },
    lastName: {
      type: sledom.STRING
    },
    fullName: {
      type: sledom.STRING,
      value: function (fieldValues) {
        return fieldValues.firstName + ' ' + fieldValues.lastName;
      }
    }
  }
});

var foo = new Foo({ firstName: 'Jeremy', lastName: 'Greer' });

foo.get('fullName'); // 'Jeremy Greer'
```

### ENUM Fields

```js
var Foo = sledom.define('Foo', {
  fields: {
    color: {
      type: sledom.ENUM,
      default: 'red',
      values: ['red', 'white', 'blue']
    }
  }
});

var foo = new Foo();

foo.get('color');  // 'red'
foo.set('color', 'purple');  // throws error
```

### Validation

```js
var Foo = sledom.define('Foo', {
  fields: {
    num: {
      type: sledom.NUMBER,
      valid: function (fieldValue) {
        return fieldValue > 3;
      }
    }
  }
});

var foo = new Foo();
foo.valid(); // false

foo = new Foo({ num: 4 });
foo.valid(); // true
```

### Observing Changes

You can register event listeners using `on` or `bind`.  Pass the event name (only sledom.CHANGE supported for now) and the function you want to be executed when the event is triggered.

```js
var onChange = function (values) {
  console.log(values);  // { name: 'new name', number: 8675309 }
};

foo.on(sledom.CHANGE, onChange);

foo.set({ name: 'new name', number: 8675309 });
foo.set({ name: 'new name' });  // won't fire handler because nothing changed
```

To unregister event listeners, use `off` or `unbind`.  If you want to remove all listeners, no second parameter is required.  If you want to remove a specific event handler, pass it as the 2nd parameter.

```js
foo.unbind(sledom.CHANGE, onChange);

// remove all sledom.CHANGE listeners
foo.unbind(sledom.CHANGE);
```

### Check Dirty State

```js
foo = new Foo({ name: 'a foo', bar: 66 });
foo.dirty(); // undefined

foo.set({ name: 'new name' });
foo.dirty(); // { name: 'new name' }

foo.set({ bar: 77 });
foo.dirty(); // { name: 'new name', bar: 77 }
```

### Instance Methods

```js
var Foo = app.define('Model', {
  fields: {
    number: { type: app.NUMBER }
  },

  // instance method
  getNumber: function () {
    return 'The number is ' + this.get('number');
  }
});

var foo = new Foo({ number: 999 });
foo.getNumber(); // 'The number is 999'
```

### Custom Field Types

```js
function Foo() {}
Foo.prototype.proveFooness = function () {
  return 'I am a Foo.';
};

var Model = sledom.define('Model', {
  fields: {
    foo: {
      // custom field type
      type: Foo
    }
  }
});

var model = new Model();

model.get('foo').proveFooness(); // 'I am a Foo.'
```

### Nested Models

```js
var Person = sledom.define('Person', {
  fields: {
    name: { type: sledom.STRING },
    gender: { type: sledom.ENUM, values: ['male', 'female' ] }
  }
});

var Child = sledom.define('Child', {
  fields: {
    name: { type: sledom.STRING },
    parent: { type: Person }
  }
});

var dad = new Person({ name: 'Dad', gender: 'male' });
var daughter = new Child({ name: 'Daughter', parent: dad });

daughter.get('parent') instanceof Person; // true
```

================================================

Please [create an issue](https://github.com/reergymerej/sledom/issues) for feature requests or to report bugs.

### Coming Soon

* preventable set events
* more events
* custom model validation (not just fields)
* save routines
* shorthand definitions
* model inheritence
* get nested values without fear of undefined (foo.bar.baz.quux)