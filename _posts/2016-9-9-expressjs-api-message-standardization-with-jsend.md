---
layout: post
title: "Express.js API Message Standardization with JSend"
description: "How to standardize your team API message using JSend specification?"
date: 2016-9-9
tags: [json, api, express, jsend, jsendie]
comments: true
share: true
---

By following shared and consistent format on how JSON should response, it promotes unity
with less argument between backend developers and frontend developers / designers, as everyone 
following the shared conventions. 

### What is JSend?

[JSend](https://labs.omniti.com/labs/jsend) is a simple yet well structured specification 
created by Omniti that lays down rules for how JSON message response from web API should be 
formatted. It focuses on application level messaging which make it ideal for REST APIs.

### How to response a JSend-compliant message using Express?

A simple Express app response with a succesful JSend-compliant message:

```javascript
var express = require('express')
  , app = express()
  , PORT = 3000;

app.get('/success', function (req, res){
  res.json({
      "status":"success",
      "data": { 
          "post" : { "id" : 1, "title" : "A blog post", "body" : "Some useful content" }
        } 
      });
});

app.listen(PORT, function(){
    console.log('Server listening on port ' + PORT);
})
```

Run the following command on your terminal:

```
curl http://localhost:3000/success
```

It should return with a successful JSend-compliant response:

```json
{"status":"success","data":{"post":{"id":1,"title":"A blog post","body":"Some useful content"}}}
```

### Introducing JSendie Middleware

To prevent from writing the JSend-compliant response repeatedly for each route, you can use 
[JSendie](https://github.com/kahwooi/jsendie) middleware.

Install JSendie middleware:

```
npm install jsendie --save
```

Setup the middleware in your Express app before any routes:

```javascript
var express = require('express')
  , jsendie = require('jsendie')
  , app = express()
  , PORT = 3000;

// remember to use it before any routes
app.use(jsendie());

app.get('/success', function (req, res){
  res.success({
    "post" : { "id" : 1, "title" : "A blog post", "body" : "Some useful content" }
  });
});

app.listen(PORT, function(){
    console.log('Server listening on port ' + PORT);
})
```

Run the following command on your terminal:

```
curl http://localhost:3000/success
```

Should return the same successful JSend-compliant response:

```json
{"status":"success","data":{"post":{"id":1,"title":"A blog post","body":"Some useful content"}}}
```

[Read more](https://github.com/kahwooi/jsendie/blob/master/README.md) on how to use 
methods provides by JSendie to response `success`, `fail`, and `error` JSend-compliant 
message.
 