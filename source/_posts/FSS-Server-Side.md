---
title: Server Side REST API (Full Stack Strawpoll)
date: 2017-02-24 23:52:39
tags:
  - Node.js
  - Express
  - Mongoose
  - Javascript
  - Server Side
  - (Project) Full Stack Strawpoll
---
Alright, the boring stuff is out of the way, and we're finally ready to start building something.  We'll start off by creating the main starting point of our server.  Navigate to the server directory within our project directory and create a file, server.js.  Also, it is good practice to put configurations in a separate file than our logic, so make a file config.js in the same location.  Just as a reminder, our structure should resemble the following:
```
- strawpoll
    - client
    - server
        - models
        server.js
        config.js
```
<!-- more -->
To start out, let's get the configurations out of the way.  Open up the config file and add something along the lines of:
```javascript
module.exports = {
  server: {
    database_host: "mongodb://<dbuser>:<dbpassword>@HOST",
    port: 3000,
  }
};
```
Now, whenever we require the config file somewhere, we'll have access to this JSON object and can easily keep all of our variable or sensitive information in one place.  I find it is helpful to use javascript files for configurations over json files because you aren't able to add comments in json files.  

This is actually the only information I put into this configuration file, during this project, but you can certainly add more if you find that it makes sense to you.  A lot of programming is personal choice, and don't ever let a tutorial take the fun and art out of programming.

Moving on to the server.js file, begin to setup the basic structure of a Node.js server with Express.
```javascript
var express = require('express');
var app = express();
var config = require('./config');

app.get('/',function(req,res){
  res.send("hello world");
});

// get the port number from the server environment or the config file if running locally
const PORT = process.env.PORT || config.server.port;

// start the server
app.listen(PORT, function(){
  console.log("Server listening on: " + PORT);
});
```
It's always good to make sure that the most basic functionality of something is working before moving to more complex parts, so give it a shot by making sure you are in the strawpoll/server directory in a terminal and enter the following snippet.  Then, open a browser and navigate to the page http://localhost:3000, or whatever port you chose to use for your server and make sure that you get a screen that says 'hello world'.
```
node server
```
