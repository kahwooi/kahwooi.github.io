---
layout: post
title: "Testing Express.js with Mocha and Chai"
description: "How to unit test express.js app using Mocha and Chai?"
date: 2016-8-8
tags: [express, mocha, chai, supertest]
comments: true
share: true
---

This post serves as an introduction to unit testing Express.js RESTful API with 
[Mocha](http://mochajs.org/) a test framework and [Chai](http://chaijs.com/) an 
assertion library for Node.js.

### Why Unit Tests?

> I find that writing unit tests actually increases my programming speed 
>
> -- <cite>Martin Fowler</cite>

Unit testing certainly appears to increase extra work for programmer, but there are 
few important reasons why you should unit test your app:

* Unit tests reduce bugs tremendously.
* If developers practice `test-driven development` (TDD), a process in which the unit tests
are written before writing the code. It can be a real help during development, because programmer
are clear on how the code should actually behave.   
* Unit tests are vital to `regression testing`, especially important in team working. 
It ensure that new changes in existing code don't break things that were working before.
* If your team is practicing `Continuous Integration (CI)` and `Continuous Delivery (CD)` using services like 
Travis CI, CircleCI, or Jenkins can automate your unit tests before deploying to production. 

### How to test express app with Mocha and Chai?

First, lets create a simple Express app.

Create a directory to hold the application, and make it as your working directory:

```bash
$ mkdir express-mocha-chai-tutorial
$ cd express-mocha-chai-tutorial
```

Use the `npm init` command to create a `package.json` file for the application: 

```bash
$ npm init
```

This command prompts for a number of questions. You can simply hit RETURN to accept
the defaults, with the following exceptions:

```bash
$ entry point: (index.js) app.js
$ test command: mocha test/*js
```

Entry point is `app.js` and test command is `mocha test/*js`.

Now install dependencies in the `express-mocha-chai-tutorial` directory,
`Express` is save in the dependencies list and `mocha`, `chai`, and 
`supertest` are save in the devDependencies list:

```bash
$ npm install express --save
$ npm install mocha chai supertest --save-dev
```

Create a `test` directory and two files, `app.js` in root directory and 
`index.js` in test directory:

```bash
$ mkdir test
$ touch app.js test/index.js
```

Your file / folder structure should now look like:

```bash
├── package.json
├── app.js
└── test
    └── index.js
```

Edit `app.js` file by creating a server listen to port `3000`, and with a `GET` 
route that does nothing:

```javascript
var express = require('express'),
app = express(),
PORT = 3000;

app.get('/', function(req, res, next){

});

app.listen(PORT, function(){
    console.log('Sever listen to port: ' + PORT);
});

module.exports = app;
```

Before writing the code for the route, we first create a test on what we are 
expecting from the response:  

```javascript
var server = require('supertest');
var should = require('chai').should();
var app = require('../app');

describe("Index", function() {
    it("should pass", function(done){
        server(app)
        .get('/')
        .end(function(err, res){
            if(err) done(err);
            res.status.should.equal(200);
            res.body.status.should.equal("success");
            res.body.data.person.should.be.an("object");
            res.body.data.person.should.contain.keys('name', 'age');
            
            done();
        });
    });
});
```

We expect the response status to be 200, the status is success, person is an object and 
should contain keys like name and age.

Instead of using TTD `assert` style, we use BBD `should` style, which are more expressive and readable.

Next, run the test:

```bash
$ npm test
```

You’ll see the failing test in red with a description of what went wrong. The request has a timeout, 
because the route return nothing:

```
Sever listen to port: 3000                                                                                                   
  Index                                                                                                                      
    1) should pass


  0 passing (2s)                                                                                                             
  1 failing 

  1) Index should pass:                                                                                                      
     Error: timeout of 2000ms exceeded. Ensure the done() callback is being called in this test.                             
      at ...
```

Since we know what to expect from the response, we add a JSON response to the route:

```javascript
var express = require('express'),
app = express(),
PORT = 3000;

app.get('/', function(req, res, next){
    res.json({
        status: "success",
        data: {
            person: {name: "kelvin", age: 12}
        }
    });
});

app.listen(PORT, function(){
    console.log('Sever listen to port: ' + PORT);
});

module.exports = app;
```

To test again, simply run `npm test`; and if all went well, you should see:

```
Sever listen to port: 3000                                                                                                   
  Index                                                                                                                      
    √ should pass (40ms) 


  1 passing (50ms) 
```

### Conclusion
We have setup a test environment for Express app, using Mocha as a test runner, 
along with the BDD-style syntax provided by Chai. This provides a great approach 
to unit testing. you can clone the code for this tutorial from the [repository](https://github.com/kahwooi/express-mocha-chai-tutorial).