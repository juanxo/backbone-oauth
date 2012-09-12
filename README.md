backbone-oauth V0.1
===================

This simple OAuth 2.0 plugin for [Backbone.js](http://backbone.js) provides an OAuth class that handles client-side authentication with OAuth providers such as Facebook or Google.

The most simple use case would be something like this:

```javascript
// Configurate the Facebook OAuth settings.
_.extend(Backbone.OAuth.configs.Facebook, {
    client_id: 'YOURAPPID',
    redirect_url: window.location.protocoll + '//' + window.location.host + '/auth_redirect.html',

    // Called after successful authentication.
    onSuccess: function(params) {

        // Get the user's data from Facebook's graph api.
        $.ajax('https://graph.facebook.com/me?access_token=' + params.access_token, {
            success: function(data) {
                alert('Howdy, ' + data.name);
            }
        });
    }
});

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

If you are using a module loading library like [Require.js](http://require.js) you can simply add it to your module's dependencies.

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
        // backbone.oauth depends on Backbone.
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

* ```client_id``` (obligatory) Your OAuth provider's app_id.
* ```redirect_url``` (obligatory) The URL to redirect to after the user clicks a button in the dialog. The page you redirect to, should serve a single script (see below).
* ```auth_url``` (obligatory) The URL to display in the OAuth dialog.
* ```scope``` (optional) A comma separated list of permission names which you would like the user to grant your application.
* ```state``` (optional) A unique string used to maintain application state between the request and callback. You should use this to protect against Cross-Site Request Forgery.
* ```access_token_name``` (optional) The property name of the returned access token. Defaults to 'access_token'.

You are free to override the following methods:

* ```onSuccess(params)``` Called when ```authSuccess``` returned true, indicating that the authentication was successful. ```params``` holds the parsed url's hash and the access_token.
* ```onError(params)``` Called when ```authSuccess``` returned false, indicating that the authentication failed. ```params``` holds the parsed url's hash and probably an error description.

If you really have to, you can also override these methods.

* ```onRedirect(params)``` Called when the OAuth dialog redirects to your redirect_url. ```params``` holds the parsed url's hash.
* ```authSuccess(params)``` A function that returns ```true``` if the returned params indicate a successful authentication. ```params``` holds the parsed url's hash.

The file you are redirecting to...
----------------------------------

... should contain a single script:

```javascript
<script>var hash=window.location.hash;window.close();opener.OAuthRedirect(hash);</script>
```