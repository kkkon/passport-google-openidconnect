# Passport-Google-OpenID Connect

[Passport](https://github.com/jaredhanson/passport) strategy for authenticating
with [Google OpenID Connect](https://developers.google.com/accounts/docs/OpenIDConnect).

This module lets you authenticate using Google OpenID Connect in your Node.js
applications.  By plugging into Passport, Google OpenID Connect authentication can be
easily and unobtrusively integrated into any application or framework that
supports [Connect](http://www.senchalabs.org/connect/)-style middleware,
including [Express](http://expressjs.com/).

## Install

    $ npm install passport-google-openidconnect

## Usage for non Google+

#### Configure Strategy

The Google OpenIDConnect authentication strategy authenticates users using a Google account
and OpenIDConnect tokens.  The strategy requires a `verify` callback, which accepts
these credentials and calls `done` providing a user, as well as `options`
specifying a client ID, client secret, and callback URL.

    var StrategyGoogle = require('passport-google-openidconnect').Strategy;
    passport.use(new StrategyGoogle({
        clientID: GOOGLE_CLIENT_ID,
        clientSecret: GOOGLE_CLIENT_SECRET,
        callbackURL: "http://127.0.0.1:3000/auth/google/callback"
      },
      function(iss, sub, profile, accessToken, refreshToken, done) {
        User.findOrCreate({ googleId: profile.id }, function (err, user) {
          return done(err, user);
        });
      }
    ));

#### Authenticate Requests

Use `passport.authenticate()`, specifying the `'google-openidconnect'` strategy, to
authenticate requests.

For example, as route middleware in an [Express](http://expressjs.com/)
application:

    app.get('/auth/google',
      passport.authenticate('google-openidconnect'));

    app.get('/auth/google/callback', 
      passport.authenticate('google-openidconnect', { failureRedirect: '/login' }),
      function(req, res) {
        // Successful authentication, redirect home.
        res.redirect('/');
      });


## Usage for Google+

#### Configure Strategy

The Google OpenIDConnect authentication strategy authenticates users using a Google account
and OpenIDConnect tokens.  The strategy requires a `verify` callback, which accepts
these credentials and calls `done` providing a user, as well as `options`
specifying a client ID, client secret, and callback URL.

    var StrategyGoogle = require('passport-google-openidconnect').Strategy;
    passport.use(new StrategyGoogle({
        clientID: GOOGLE_CLIENT_ID,
        clientSecret: GOOGLE_CLIENT_SECRET,
        callbackURL: "http://127.0.0.1:3000/auth/google/callback",
        userInfoURL: "https://www.googleapis.com/plus/v1/people/me"
      },
      function(iss, sub, profile, accessToken, refreshToken, done) {
        User.findOrCreate({ googleId: profile.id }, function (err, user) {
          return done(err, user);
        });
      }
    ));

#### Authenticate Requests

Use `passport.authenticate()`, specifying the `'google-openidconnect'` strategy, to
authenticate requests.

For example, as route middleware in an [Express](http://expressjs.com/)
application:

    app.get('/auth/google',
      passport.authenticate('google-openidconnect'));

    app.get('/auth/google/callback', 
      passport.authenticate('google-openidconnect', { failureRedirect: '/login' }),
      function(req, res) {
        // Successful authentication, redirect home.
        res.redirect('/');
      });


## Extended Permissions(more scope)

If you need extended permissions from the user, the permissions can be requested
via the `scope` option to `passport.authenticate()`.

For example, this authorization requests permission to the user's statuses and
checkins:

    app.get('/auth/google',
      passport.authenticate('google-openidconnect', { scope: ['email', 'profile'] }));

Your array of scopes doesn't need to contain the scope of `openid`, since it added by this module automatically

## Dynamic Scope Requests

If you need to be able to modify which scopes are requested based on the user/request, then you can pass a `getScope` function when configuring the strategy. The getScope function accepts a request object as its only parameter and syncrhronously returns a space-seperated string of scopes

    var StrategyGoogle = require('passport-google-openidconnect').Strategy;
    passport.use(new StrategyGoogle({
        clientID: GOOGLE_CLIENT_ID,
        clientSecret: GOOGLE_CLIENT_SECRET,
        callbackURL: "http://127.0.0.1:3000/auth/google/callback",
        userInfoURL: "https://www.googleapis.com/plus/v1/people/me"
        getScope: function (req) {
          // pass the scope query param used from the login page 
          // along to Google
          var requestedScopes = req.query.scopes
          return requestedScopes
        }
      },
      function(iss, sub, profile, accessToken, refreshToken, done) {
        User.findOrCreate({ googleId: profile.id }, function (err, user) {
          return done(err, user);
        });
      }
    ));

## Usage for non Google+ and only openid

#### Configure Strategy

The Google OpenIDConnect authentication strategy authenticates users using a Google account
and OpenIDConnect tokens.  The strategy requires a `verify` callback, which accepts
these credentials and calls `done` providing a user, as well as `options`
specifying a client ID, client secret, and callback URL.

    var StrategyGoogle = require('passport-google-openidconnect').Strategy;
    passport.use(new StrategyGoogle({
        clientID: GOOGLE_CLIENT_ID,
        clientSecret: GOOGLE_CLIENT_SECRET,
        callbackURL: "http://127.0.0.1:3000/auth/google/callback",
        skipUserProfile: true // doesn't fetch user profile
      },
      function(iss, sub, profile, accessToken, refreshToken, done) {
        User.findOrCreate({ googleId: profile.id }, function (err, user) {
          return done(err, user);
        });
      }
    ));

#### Authenticate Requests

Use `passport.authenticate()`, specifying the `'google-openidconnect'` strategy, to
authenticate requests.

For example, as route middleware in an [Express](http://expressjs.com/)
application:

    app.get('/auth/google',
      passport.authenticate('google-openidconnect'));

    app.get('/auth/google/callback', 
      passport.authenticate('google-openidconnect', { failureRedirect: '/login' }),
      function(req, res) {
        // Successful authentication, redirect home.
        res.redirect('/');
      });

## Revoke AccessToken

For example, as route middleware in an [Express](http://expressjs.com/)
application:

    app.get('/auth/google/revoke', function(req, res, next) {
      var user_accessToken = FETCH_FROM_DB_OR_SESSION;
      var strategy = req._passport.instance._strategy('google-openidconnect');
      strategy.revoke( { accessToken: user_accessToken }, function(err, body, res) {
        next();
      });
    });

## Credits

  - [Jared Hanson](http://github.com/jaredhanson)
  - [Kiyofumi Kondoh](http://github.com/kkkon)

## License

[The MIT License](http://opensource.org/licenses/MIT)

Original work Copyright (c) 2011-2013 Jared Hanson <[http://jaredhanson.net/](http://jaredhanson.net/)>

Modified work Copyright (c) 2015 Kiyofumi Kondoh
