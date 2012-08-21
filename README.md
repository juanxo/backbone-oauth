backbone-oauth V0.1
===================

This simple OAuth 2.0 plugin for [Backbone.js](http://backbone.js) provides an OAuth class that handles client-side authentication with OAuth providers such as Facebook or Google.

The most simple use case would be something like this:

```javascript
// Configurate the Facebook OAuth settings.
_.extend(Backbone.OAuth.configs.Facebook, {
    client_id: 'YOURAPPID',
    redirect_url: window.location.href,

    // Called after successful authentication.
    onSuccess: function(params) {

        // Get the user's data from Facebook's graph api.
        $.ajax('https://graph.facebook.com/me?access_token=' + params.access_token, {
            success: function(data) {
                alert('Howdy, ' + data.name);
            }
        });
    }

// Create a new OAuth object and call the auth() method to start the process.
var FB = new Backbone.OAuth(Backbone.OAuth.configs.Facebook);
FB.auth();
);
```

On calling ```auth()``` a new window will be opened and the OAuth provider's OAuth dialog will be displayed. If the user chooses to authenticate with your website, the ```onSuccess()``` method you specified will be called with the returned data.

Dependencies
------------

The backbone-oauth plugin relies on
* Backbone (tested with Backbone.js v0.9.2, but should work with most other versions)
* Underscore.js

Using this Backbone.js plugin
-----------------------------

If you are not using a module loading library you will want to include the source file into your webpage:

```html
<script type='text/javascript' src='/js/lib/modules/backbone-oauth.js'></script>
```

If you are using a module loading library like [Require.js](http://require.js) you can simply add it to your collection's dependencies.

```javascript
define(['app', 'plugins/backbone.oauth'], function(app) {
    // Your module code.
});
```

You probably want to make sure that the shim configuration is set correctly.

```javascript
require.config({
    // Your path configuration goes here
    shim: {
        backbone: {
        deps: ['underscore', 'jquery'],
            exports: 'Backbone'
        },
        // Backbone.Pagination depends on Backbone.
        'plugins/backbone.oauth' : ['backbone']
    }
});  
```

Configuring backbone.oauth
--------------------------

The backbone.oauth plugin comes with two configuration objects:

* ```Backbone.OAuth.configs.Facebook``` and
* ```Backbone.OAuth.configs.Google```

You will need to extend these configuration objects with at least the following two properties:

* ```client_id``` and
* ```redirect_url```

Or you can create your own configuration object, that may hold the following properties:

* ```app_id``` (obligatory) Your OAuth provider's app_id.
* ```redirect_url``` (obligatory) The URL to redirect to after the user clicks a button in the dialog. The URL you specify must be a URL of with the same Base Domain as specified in your app settings. Otherwise, this URL can be anything you want it to be, as it is of no particular importance to the backbone.oauth plugin.
* ```auth_url``` (optional) The URL to display in the OAuth dialog.
* ```scope``` (optional) A comma separated list of permission names which you would like the user to grant your application.
* ```state``` (optional) A unique string used to maintain application state between the request and callback. You should use this to protect against Cross-Site Request Forgery.
* ```access_token_name``` (optional) The property name of the returned access token.

You are free to override the following methods:

* ```onRedirect(params)``` Called when the OAuth dialog redirects to your redirect_url. ```params``` holds the parsed url's hash.
* ```authSuccess(params)``` A function that returns ```true``` if the returned params indicate a successful authentication. ```params``` holds the parsed url's hash.
* ```onSuccess(params)``` Called when ```authSuccess``` returned true indicating, that the authentication was successful. ```params``` holds the parsed url's hash and the access_token.
* ```onSuccess(params)``` Called when ```authSuccess``` returned false indicating, that the authentication failed. ```params``` holds the parsed url's hash and probably an error description.

Choosing a ```redirect_url```
-----------------------------

It does not really matter what redirect URL you specify, as long as it is a valid redirect URL for your OAuth provider. I recommend setting up an empty ```authcallback.html``` file and redirecting to ```www.yourwebsite.com/authcallback.html```.