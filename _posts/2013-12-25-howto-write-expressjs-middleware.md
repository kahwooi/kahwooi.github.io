---
layout: post
title: "How to Write Express.js Middleware"
description: "Middleware pattern is awesome. How can you write a middleware for express app?"
date: 2013-12-25
tags: [node, express, middleware]
comments: true
share: true
---

### What is Express Middleware?

Before we can write a middleware, we need to know what it is.

If you have written Express app, you've probably seen this 
before routes in `app.js`:

```javascript
app.use(logger('dev'));
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false }));
app.use(cookieParser());
```

This is how middleware functions are pluggled into your Express app. 
To setup middleware, we use `app.use()`, a method provided by the 
Express API.

When working with Express.js, you should be familiar with the `req`, `res` 
and `next` objects. Put simply, middleware is a function that have access 
to the request(`req`) objects, response(`res`) objects and the next(`next`) 
middleware function in the stack.

### What can you do with Middleware?

* Modify the request or response objects before passing them to the next middleware.
* Execute any extra code before request, response objects.
* When some conditions are not met, deliberate end the request or response cycle.
* Call the next middleware in the stack using `next` object.

### Writing Middleware

A simple midddleware function that logs the remote IP for each request
looks like this:

Create a file name `app.js` 

```javascript
var express = require('express');
var app = express();
var PORT = 3000;

function logger(req, res, next){
    console.log(req.connection.remoteAddress);
    next();
}

app.use(logger);

app.get('/', function (req, res){
  res.json({
    message: {humans: [{"name": "John"}, {"name": "Jane"}]}
  });
});

app.listen(PORT, function(){
    console.log('Server listening on port ' + PORT);
})
```

In this particular example, a `logger` prints remote IP address of these request
to the server console, and then continues the following function in the stack by 
calling `next()`;

Since middleware is a amazing pattern that allows developers to reuse code (DRY) 
within applications and even distribute it through NPM modules, I am going to show
you how to create a middleware module that can be easily use and publish it to the 
NPM.

Create a file name `logger.js`:

```javascript
modules.export = function () {
    return function(req, res, next) {
        console.log(req.connection.remoteAddress);
        next();
    }
};
```

Change the `app.js` code : 

```javascript
var express = require('express');
var app = express();
var PORT = 3000;
var logger = require('./logger');

app.use(logger);

app.get('/', function (req, res){
  res.json({
    message: {humans: [{"name": "John"}, {"name": "Jane"}]}
  });
});

app.listen(PORT, function(){
    console.log('Server listening on port ' + PORT);
})
```

Now instead of having the function written in `app.js`, you can import `logger` module 
and use it with `app.use(logger)`.

### Pitfalls

Some of the pitfalls to take note when using Express middleware:

###### Not ending response 

If you want to continue with the next middleware function use `next` object.
If the function is the end of the middleware function chain, you must end the response 
by using `res.end()`,  if not Express app will just hang.

###### Ordering of the middleware is important

Express.js run through middleware as a chain, and it keeps going to the next function 
until a respose is ended (`app.end()`). For this reason, the order in which you 
register(`app.user()`) the middleware is important. 