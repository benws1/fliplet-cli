# Fliplet Core JS APIs

"Fliplet Core" is our most important package and it's included on all screens of your apps as long as all widgets that have declared it in their dependencies.

This package enables you to read and write data in the device storage, navigate to different screens as long as dozens of other different interactions and features.

The following namespaces are publicly available under this package. Please click on any of them to see their documentation and public methods:

- [Analytics](#analytics)
- [API](#api)
- [App](#app)
- [Apps](#apps)
- [Encode](#encode)
- [Environment](#environment)
- [Hooks](#hooks)
- [Modal](#modal)
- [Navigate](#navigate)
- [Navigator](#navigator)
- [Organizations](#organizations)
- [Pages](#pages)
- [Profile](#profile)
- [Storage](#storage)
- [Studio](#studio)
- [User](#user)
- [Widget](#widget)

---

The `fliplet-core` package also contains the following methods:

- [compile](#compile)
- [guid](#guid)

If you're looking for other namespaces, make sure to check popular ones the [Media](Fliplet-Media-JS-APIs) or [Data Sources](Fliplet-DataSources-JS-APIs) dependencies.

One more thing: `Fliplet` is also a function returning a promise which can be used to ensure your code runs when all plugins and features have loaded on the screen:

```js
Fliplet().then(function () {
  // code running here will have all dependencies and plugins available
});
```

---

## Environment

### Get an environment variable

```js
var appId = Fliplet.Env.get('appId');
```

These variables are usually available on app screens as long as components and providers:

- `organizationId` - the user's organization id
- `appId` - the current app id
- `user` - the current user
- `development` - `true / false` if using CLI or not
- `interact` - `true / false` if you are in edit mode in Studio
- `preview` - `true / false` if you are in preview mode in Studio
- `platform` - either `'web'` or `'native'`
- `mode` - `'preview' / 'view' / 'interact'`
- `provider` - `true / false` whether the context is running in provider mode

### Set an environment variable

```js
Fliplet.Env.set('appId', 2);
```

---

## Hooks

### Register a callback for a hook

```js
Fliplet.Hooks.on('beforeFormSubmit', function (data) {
  console.log('just got', data);

  data.foo = 2;

  return Promise.resolve('bar'); // you can return a promise if your hook is async
});
```

```js
Fliplet.Hooks.on('afterFormSubmit', function (data) {
  // data.formData is form data
  // data.result is null for insert and the entry when update
  console.log('just got', data);

  data.foo = 2;

  return Promise.resolve('bar'); // you can return a promise if your hook is async
});
```

```js
Fliplet.Hooks.on('onFormSubmitError', function (data) {
  // data.formData is form data
  // data.error is the error
  console.log('something went wrong', data);

  data.foo = 2;

  return Promise.resolve('bar'); // you can return a promise if your hook is async
});
```

### Run a hook

```js
var data = { foo: 1 };

Fliplet.Hooks.run('beforeFormSubmit', data).then(function (results) {
  // results[0] is "bar"
  // data.foo is 2
}, function onError (err) {
  // woop woop, an hook fired a rejection!
});
```

---

## Modal

We use [Bootbox](http://bootboxjs.com/) under the hood for Modals, hence check their documentation for the full set of options you can pass.

### Display a confirmation message

```js
Fliplet.Modal.confirm({ message: 'Are you sure?'}).then(function (result) {
  if (result) {
    // user pressed yes
  }
});
```

### Display an alert message

```js
Fliplet.Modal.alert({ message: 'Thanks for confirming'}).then(function () {
  // fired when the user pressed "OK"
});
```

---

## User

### Get the user auth token

```js
var userAuthToken = Fliplet.User.getAuthToken();
```

### Set the user auth token

```js
Fliplet.User.setAuthToken('eu--abcdef');
```

### Set the user details

```js
Fliplet.User.setUserDetails({
  auth_token: 'eu--abc',
  id: 2,
  email: 'foo@example.org'
});
```

### Set (extend) the user preferences

```js
// promise will resolve with all user preferences
Fliplet.User.Preferences.set({
  foo: 'bar',
  baz: { barbaz: true }
});
```

### Get the user preferences

```js
// promise will resolve with all user preferences
Fliplet.User.Preferences.get().then(function (preferences) {
  // preferences.foo
});
```

---

## Studio

### Forward an event to Fliplet Studio

```js
Fliplet.Studio.emit('foo', { bar: 1 });
```

### Navigate to a page in Fliplet Studio

```js
Fliplet.Studio.emit('navigate', {
  name: 'appSettingsGeneral', // route name
  params: { appId: 11 } // parameters to pass to the route
});
```

---

## API

### Make an API request to the Fliplet APIs

```js
// Returns a promise
Fliplet.API.request({
  url: 'v1/user',
  method: 'PUT',
  data: { email: 'new@email.org' }
}).then(function onSuccess(response) {
  console.log(response);
});
```

---

## Widget

### Emit an event to the parent widget or Fliplet Studio

```js
Fliplet.Widget.emit('foo', { bar: 1 });
```

### Get the widget instance id

This method is usually meant to be called from a widget interface, to get the widget instance id if necessary.

```js
var id = Fliplet.Widget.getDefaultId();
```

### Get a widget instance data

This method is usually meant to be called from a widget interface, to get the saved data.

```js
var data = Fliplet.Widget.getData();
```

You can also get the data of a specific widget instance in the current page by passing its ID:

```js
var data = Fliplet.Widget.getData(1);
```

### Get the URL to an asset from the relative path of a widget

```js
// the first parameter is the widget id as usual
var url = Fliplet.Widget.getAsset(123, 'img/icon.png');

// on the interface, you can skip the id (same as getData and getUUID)
var url = Fliplet.Widget.getAsset('img/icon.png');
```

### Get a widget instance unique identifier

The widget instance ID might change overtime when an app is published. If you need to use an ID which won't change, you can use the `getUUID(<widgetInstanceId>)`.

```js
var uuid = Fliplet.Widget.getUUID(1);
```

### Display an error message in Fliplet Studio

```js
Fliplet.Widget.displayMessage({ text: 'The email is not valid' });
```

### Sets the widget interface info message in Fliplet Studio

```js
Fliplet.Widget.info('2 files selected');
```

### Toggle the wide mode on the interface

```js
// Enable the wide mode
Fliplet.Studio.emit('widget-mode-wide');

// Disable the wide mode
Fliplet.Studio.emit('widget-mode-default');
```

### Toggle the save button

```js
// Enable the button
Fliplet.Widget.toggleSaveButton(true);

// Disable the button
Fliplet.Widget.toggleSaveButton(false);
```

### Set & reset the save button label

```js
// Set the button label
Fliplet.Widget.setSaveButtonLabel('Pick');

// Reset the button label (to 'Save & Close')
Fliplet.Widget.resetSaveButtonLabel();
```

### Toggle the cancel button

```js
// Enable the button
Fliplet.Widget.toggleCancelButton(true);

// Disable the button
Fliplet.Widget.toggleCancelButton(false);
```

### Set & reset the cancel button label

```js
// Set the button label
Fliplet.Widget.setCancelButtonLabel('No thanks');

// Reset the button label (to 'Save & Close')
Fliplet.Widget.resetCancelButtonLabel();
```

### Autosize

Tells the parent widget or studio the new height of this widget.

```js
Fliplet.Widget.autosize();
```

As a rule of thumb, you are responsible of calling the above function every time the height of your widget (or provider) is changing.

### Open a provider

```js
var myProvider = Fliplet.Widget.open('com.fliplet.link', {

  // If provided, the iframe will be appended here,
  // otherwise will be displayed as a full-size iframe overlay
  selector: '#somewhere',

  // You can send data to the provider, to be used similar to a widget instance data
  data: { foo: 'bar' },

  // You can also listen for events fired from the provider
  onEvent: function (event, data) {
    if (event === 'interface-validate') {
      Fliplet.Widget.toggleSaveButton(data.isValid === true);
    }

    // return true to stop propagation up to studio or parent components
  }
});

myProvider.then(function (data) {
  // data will contain the result
});

// You can also resolve an array of providers
Fliplet.Widget.all([myProvider]).then(function (results) {
  // results is an array with data from all providers you resolved
});
```

You can also stop the provider from being closed once resolved, by passing the `closeOnSave: false` option. You can then close it manually by calling `myProvider.close()` at any time.

### Attach an event on save request

Optionally attach an event handler to be called when the "save" button will be called in studio. Here's the typical usage of the function:

```js
Fliplet.Widget.onSaveRequest(function () {
  // Save data when the save button in studio is clicked
  return Fliplet.Widget.save({ foo: 1 });
}).then(function onSave() {
  // Closes this widget interface
  Fliplet.Widget.complete();
});
```

### Save data

Used to save JSON-structured data to the current widget instance. The `save` function is usually meant to be triggered from `onSaveRequest` described above.

```js
Fliplet.Widget.save({ foo: 1 }).then(function () {
  // Closes this widget interface
  Fliplet.Widget.complete();
});
```

### Close the interface when done

```js
Fliplet.Widget.complete();
```

---

## Organizations

### Get the user organizations

```js
Fliplet.Organizations.get().then(function (organizations) {

});
```

### Get the current organization settings

```js
Fliplet.Organization.Settings.getAll()
  .then(function (settings) {
    // Your code
  });
```

### Set settings. Current settings are extended with this ones.

```js
Fliplet.Organization.Settings.set({
  user: 'foo',
  _password: 'bar' // Settings start with _ are encrypted
})
  .then(function(settings) {
    // Your code
  });
```

### Get a single setting

```js
Fliplet.Organization.Settings.get('foo')
  .then(function (value) {
    // Your code
  })
```

### Check if a setting is set

```js
Fliplet.Organization.Settings.isSet('_password')
  .then(function(isSet) {
    if (isSet) {
      // Your code
    }
  });
```

### Unset a setting

```js
Fliplet.Organization.Settings.unset(['user','_password'])
  .then(function (currentSettings) {
    // Your code
  })
```

---

## App

### Get the current app settings

```js
var settings = Fliplet.App.Settings.getAll();
```

### Get a single settings from the current app

```js
var datum = Fliplet.App.Settings.get('foo');
```

### Get the logs for an app

```js
Fliplet.App.Logs.get({
  where: { type: 'jobs' }
}).then(function (logs) {
  // logs<Array>
});
```

### Create a log for an app

```js
Fliplet.App.Logs.create({
  foo: "bar"
}).then(function (log) {
  // log<Object>
});
```

### Save or update some settings of the current app

```js
Fliplet.App.Settings.set({ foo: 'bar', hello: 'world').then(function () {
  // optional promise callback to be called when the APIs have saved the data
});
```

### Deletes a list of settings from the app

```js
Fliplet.App.Settings.unset(['foo', 'hello']).then(function () {
  // optional promise callback to be called when the APIs have deleted the data
});
```

### Check if your app is running in preview mode

Use the following snippet to check if your app is running inside Fliplet Viewer (or Fliplet Studio) or it's the production version from the App Store / Play Store / Web apps.

```js
var isPreview = Fliplet.App.isPreview(true);
```

### Lock the device orientation

```js
// If called with no parameters, the app orientation from the settings will be used
Fliplet.App.Orientation.lock(newOrientation)
```

### Unlock the device orientation

```js
Fliplet.App.Orientation.unlock()
```

### App Storage

Use this to store arbitrary keys in the app storage for later use.
Note: this is a [namespaced storage](#namespaced) wrapper using `Fliplet.Storage` for the current app.

```js
Fliplet.App.Storage.set(key, val)
```

### Profile

A [namespaced storage](#namespaced) for the user profile.

```js
Fliplet.Profile.set('firstName', 'John')

Fliplet.Profile.get('firstName').then(function (value) {
  // value is 'John'
})
```

The following variables are reserved for common use and publicly accessible.

* `email`
* `firstName`
* `lastName`
* `name`
* `department`
* `company`
* `phone`

---

## Apps

### Get the list of apps the user has got access to

```js
Fliplet.Apps.get().then(function (apps) {

});
```

**Note**: when returning apps, the API will return both **V1** and **V2** apps created with Fliplet. Most likely, you want to filter and use V2 apps only. This can be done by filtering out apps where the boolean `app.legacy` is `true`.

---

## Pages

### Get the list of pages of the current app

```js
Fliplet.Pages.get().then(function (appPages) {

});
```

---

## Navigator

### Check whether the device is online

```js
var isOnline = Fliplet.Navigator.isOnline();
```

### Wait for the device to be ready before running some code

```js
Fliplet.Navigator.onReady().then(function () {
  // your code
});

// you can also use this shorthand
Fliplet().then(function () {
  // your code
});
```

### Wait for Cordova plugins to be ready before running some code

```js
Fliplet.Navigator.onPluginsReady().then(function () {
  // your code
});
```

### Get the device/user location

```js
Fliplet.Navigator.location().then(function (position) {
  // position.coords.latitude
  // position.coords.longitude
}).catch(function (error) {
  // User didn't allow to share the location
});
```

---

## Storage

### Store data

```js
Fliplet.Storage.set('key', value).then(function () {});
```

### Read data

```js
Fliplet.Storage.get('key').then(function (value) {});
Fliplet.Storage.get('key', { defaults: { defaultProperty: 'defaultValue' } }).then(function (value) {});
Fliplet.Storage.get('key', { defaults: 'defaultValue' }).then(function (value) {});
```

You can optionally provide a default value in case the key has not been assigned a value yet.

### Remove data

```js
Fliplet.Storage.remove('key', value).then(function () {});
```

### Namespaced

Gives you the ability to namespace your data.

```js
var myNamespaceStorage = Fliplet.Storage.Namespace('foo');
```

#### Set data
```js
myNamespaceStorage.set('bar', 'my data')
```

#### Get data

```js
myNamespaceStorage.get('bar').then(function(value) {})
```

#### Get all data

```js
myNamespaceStorage.getAll().then(function(data) {})
```

#### Remove

Remove a key or list of keys from the namespaced storage
```js
myNamespaceStorage.remove('bar')
myNamespaceStorage.remove(['foo', 'bar'])
```

#### Clear

Clear all namespaced storage
```js
myNamespaceStorage.clear()
```

---

## Navigate

### Query parameters

```js
Fliplet.Navigate.query;
```

### Navigate the app to the previous page

```js
Fliplet.Navigate.back();
```

### Navigate the app to a URL

```js
Fliplet.Navigate.url('http://fliplet.com');
```

### Navigate the app to a specific screen by its ID

To find out the ID of a page, you can use Fliplet Studio (it's displayed on the browser bar) or simply run `Fliplet.Env.get('pageId')` from any page.

```js
// Navigate to the screen with ID 1
Fliplet.Navigate.screen(1);

// Navigate to the screen with ID 2 optionally passing some more details
Fliplet.Navigate.screen(2, { query: '?foo=bar' });

// Navigate to the screen with ID 3 using a slide transition
Fliplet.Navigate.screen(3, { transition: 'slide.right' });

// Navigate to the screen with ID 3 specifying the transition duration (defaults to 500ms)
Fliplet.Navigate.screen(3, { duration: 250 });

// Navigate to the screen with ID 4 using a fade transition
Fliplet.Navigate.screen(4, { transition: 'fade' });
```

Here's a list of transitions you can use:

- `none` - No transition
- `fade` - Fade in
- `slide.left` - Slide Left
- `slide.right` - Slide Right
- `slide.up` - Slide Up
- `slide.down` - Slide Down
- `flip.left` - Flip from Left / Slide Right (Windows)
- `flip.right` - Flip from Right / Slide Left (Windows)
- `flip.up` - Flip from Top / Slide Up (Windows)
- `flip.down` - Flip from Bottom / Slide Down (Windows)
- `curl.up` - Curl Up (iOS) / Slide Up (Android/Windows)
- `curl.down` - Curl Down (iOS) / Slide Down (Android/Windows)

### Navigate using the options given by the link provider

When using the `com.fliplet.link` provider to get the navigation details, the function below can be used to parse such details.

```js
// Data coming from the link provider
var data = {
  action: 'screen',
  page: 1,
  query: '?param1=value1'
};

Fliplet.Navigate.to(data);
```

### Open a popup

```js
var options = {
  title: 'Foo',
  message: 'Bar',
};
Fliplet.Navigate.popup(options);
```

### Open a confirm dialog

```js
var options = {
  title: 'Foo',
  message: 'Bar',
  labels: ['Agree','No'] // Native only (defaults to [OK,Cancel])
};
Fliplet.Navigate.confirm(options)
  .then(function(result) {
    if (!result) {
      return console.log('Not confirmed!');
    }
    console.log('Confirmed!');
  });
```

### Open a prompt dialog

```js
var options = {
  title: 'Foo',
  message: 'Bar',
  labels: ['Send','Cancel'], // Native only (defaults to [OK,Cancel])
  default: 'Foo Bar'
};
Fliplet.Navigate.prompt(options)
  .then(function(input) {
    if (!input) {
      return console.log('Canceled or left empty!');
    }
    console.log(input);
  });
```

### Open a gallery
We are using [PhotoSwipe](http://photoswipe.com/).
Note: You need to add `photoswipe` on your dependencies list to use this.
```js
var data = {
  images: [
    {
      title: 'Foo',
      path: '/foo.jpg', // On native platform if present, path is used instead of web url
      url: 'http://lorempixel.com/1280/720/'
    },
    {
      url: 'http://lorempixel.com/400/200/'
    }
  ],
  options: { // You can pass Photo Swipe options
    index: 1
  }
};
Fliplet.Navigate.previewImages(data);
```

---

## Analytics

### Enable/disable tracking
The app might have an analytics widget active, although you can also prompt the user if he accpets tracking.

```js
Fliplet.Analytics.enableTracking()
Fliplet.Analytics.enableTracking()
```

### Check tracking status
```js
Fliplet.Analytics.isTrackingEnabled()
````

---

## Encode

### Encode data to base64

```js
var encoded = Fliplet.Encode.base64('mystring');
```

---

## Compile

Compiles a string with a handlebars-like template replacement.

```js
var compiledString = Fliplet.compile('Hello {{name}}', { name: 'Nick' });
```

## Guid

Generates a global unique identifier with a format like: `"2682df5f-2679-7de5-c04c-d212f4314897"`

```js
var guid = Fliplet.guid()
```

---
