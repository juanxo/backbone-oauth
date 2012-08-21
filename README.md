backbone-oauth V0.1
===================

This simple OAuth 2.0 plugin for [Backbone.js](http://backbone.js) provides an OAuth class that handles client-side authentication with OAuth providers such as Facebook or Google.

The most simple use case would be something like this:

```javascript
// Configurate the Facebook OAuth settings.
_.extend(Backbone.OAuth.configs.Facebook, {
    app_id: 'YOURAPPID',
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