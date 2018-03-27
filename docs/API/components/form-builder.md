# Fliplet Form Builder JS APIs

These public JS APIs will be automatically available in your screens once a **Form Builder** component is dropped into such screens.

## Retrieve an instance of a form

A Fliplet form instance contains all the form fields (eg, 'first-name'), the fields values (eg, 'Nick') and the field type (eg, in this case Nick was a text field so has type [ XXX  ]. Other field types include [number], [date] and [multiple choice]) 

Since you may have many forms into a single screen, we provide a handy function to grab a specific form instance by its form name (the form name can be found in Fliplet Studio by clicking on the form and reviewing the settings). If no form name is specified or if there are multiple forms with the same name then the first one available in the page will be retrieved.

### `Fliplet.FormBuilder.get()`

Retrieves the first or a specific form instance.

```js
// Gets the first form instance
Fliplet.FormBuilder.get()
  .then(function (form) {
    // Use form to perform various actions
  });

// Gets the form instance named 'Contact names'. Note this is case sensitive
Fliplet.FormBuilder.get('Contact names')
  .then(function (form) {
    // Use form to perform various actions
  });
```

The `form` instance variable above makes available the following instance methods.

## Instance methods

### Entering data into a form programmatically 
### `form.load(Function)`

Allows an app to overwrite form field values with new data. Useful if you manually want to populate the form based on your data. Note: the form field names are case sensitive. If no field name is found that matches the one supplied then the form instance will create a new field with the field name and value supplied.

```js
Fliplet.FormBuilder.get().then(function (form) {
  form.load(function () {
    return {
      Name: 'Nick',
      Email: 'nick@example.org'
    };
  });
});
```

You can also return a `Promise` if you're loading the data asynchronously. In the following example we are populating a form from an entry in a Fliplet data source:

```js
Fliplet.FormBuilder.get().then(function (form) {
  form.load(function () {
    return Fliplet.DataSources.connect(133).then(function (connection) {
      return connection.findById(456);
    });
  });
});
```

---

### Retrieving a field or multiple fields
A field is an object that has a `name`, `type` and `value`. You can either retrieve a single field by specifying its name or loop through all of the fields if you don't have a specific name to use. 

### `form.field(String)`

Retrieves a form field by its [@@@@@ Can we make this consistent is it a name or ID? or is the name the label? @@@@@@] `name` (the field name can be set in Fliplet Studio in the form component settings).

```js
Fliplet.FormBuilder.get().then(function (form) {
  // Get the field with ID 'foo'
  var field = form.field('foo');
});
```


### `form.instance`
This is a property that references a `Vue` object with more advanced properties of the form. For example, you can loop through all form fields using the `instance.fields` array:

```js
Fliplet.FormBuilder.get().then(function (form) {
  form.instance.fields.forEach(function (field) {
    // field is an object with "type", "name" and "value"
  });
});
```

For more information on `Vue` objects and how you can use them see [@@@@@@]

--- 



## Field methods

### Get of set the value of a field
### `form.val()` [@@@@@@ should this be field.val()? @@@@@@]

`form.val()` returns the value of a field whereas `form.val("Nick")` would set the value of the field to 'Nick'.

```js
Fliplet.FormBuilder.get()
  .then(function (form) {
    // set the input value
    form.field('foo').val('bar');

    // gets the input value
    return form.field('foo').val();
  });
```

--- 

### Add an event listener that is triggered when a field is changed
### `form.change(Function)`

Fields as 'changed' as a user starts to type (they don't necessarily need to click a submit button). The following is an example of an event listener that is triggered when a field is changed:

```js
Fliplet.FormBuilder.get()
  .then(function (form) {
    // registers a callback to be fired whenever the field value changes
    form.field('foo').change(function (val) {
      // do stuff with "val"
    });
  });
```

--- 
### Show or hide fields programmatically
### `form.toggle(Boolean)`

You may wish to hide certain fields depending on a users previous actions. The `.toggle()` method allows you to do this easily. If toggle is given the value true the field will be displayed; a value of false will hide the field; and if no value is provided toggle will invert whatever the current visibility state is (ie, visible will become hidden and hidden will become visible)

```js
Fliplet.FormBuilder.get()
  .then(function (form) {
    form.field('foo').toggle(); // Toggles field visibility
    form.field('bar').toggle(true); // Shows field
    form.field('baz').toggle(false); // Hides field
  });
```

Toggle can even accept boolean logic to specify when a toggle event should be triggered. 

```js
Fliplet.FormBuilder.get()
  .then(function (form) {
    // registers a callback to be fired whenever the field value changes
    form.field('foo').change(function (val) {
      // shows the field "bar" when the value of "foo" is greater than 10
      form.field('bar').toggle(val > 10);

      // shows the field "baz" when the value of "foo" is 50
      form.field('baz').toggle(val === 50);
    });
  });
```

## Form hooks

### beforeFormSubmit

Capture data when the form is submitted. You can modify the data and also avoid data from being saved to a data source or continuing its flow.

```js
Fliplet.Hooks.on('beforeFormSubmit', function(data) {
  // add your code here

  // e.g. mutate the data before it's sent
  data.foo = 'bar'

  // return a promise if this callback should be async.
  // reject the promise to stop the form from submitting the data or continuing
});
```

--- 

### afterFormSubmit

Runs when a form has been submitted and has finished its processing.

```js
Fliplet.Hooks.on('afterFormSubmit', function() {
  // form data has been saved and submitted
});
```

--- 

### onFormSubmitError

Runs when a form has could not be submitted.

```js
Fliplet.Hooks.on('onFormSubmitError', function(error) {
  // form encountered an error when saving or submitting
});
```
