# Model (working title)

This is a library for creating models.  It is the M in MVC.


## Definition

```js
var model = require('model');

Foo = model.define('Foo', {
  idField: 'name',
  fields: {
    name: { type: model.STRING },
    bar: { type: model.NUMBER, default: 42 },
    baz: { type: model.BOOLEAN, default: true }
  }
});

var foo = new Foo({ name: 'asdf', bar: 99 });
```

## Getting Field Values

```js
foo.get(); // { name: 'asdf', bar: 99, baz: true }
foo.get('bar'); // 99
foo.id(); // 'asdf'
```

## Setting Field Values

```js
foo.id('new id');
foo.id(); // 'new id'

foo.set('bar', 123);
foo.get('bar'); // 123
```

================================================

### Coming Soon

* computed fields

* validation (field/model)

* dirty state (field/model)

* static methods

* save routines

* shorthand definition
