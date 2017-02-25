---
title: Server Side Part I - Routes - (Full Stack Strawpoll)
date: 2017-02-24 23:52:39
tags:
  - Node.js
  - Express
  - Javascript
  - (Project) Full Stack Strawpoll
---
Alright, the boring stuff is out of the way, and we're finally ready to start building something.  We'll start off by creating the main starting point of our server.  Navigate to the server directory within our project directory and create a file, server.js.  Also, it is good practice to put configurations in a separate file than our logic, so make a file config.js in the same location.  Just as a reminder, our structure should resemble the following:
```
+ strawpoll
    + client
    + server
        + models
        - server.js
        - config.js
```
<!-- more -->
## Setup
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

This is actually the only information I put into this configuration file during this project, but you can certainly add more if you find that it makes sense to you.  A lot of programming is personal choice, and don't ever let a tutorial take the fun and art out of programming.

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
So, what is going on here?  We've set up the server using our configuration file on the port 3000, and it is listening for requests.  We specifically defined a 'route' of '/' that sends a 'res'ponse of "hello world" to any GET 'req'uests that go to that route.  

## Time for Choices

Alright, we've finished most of the boilerplate for this project.  It is time to make some choices, so let's create some routes that can handle our requests.  However, let's think about the routes now that we are making real ones.  Someday we might want to use other parts of this server to actually serve content instead of just using it as an API.  

When deciding on routes, I find it helpful to categorize things by specificity, starting with the least specific, and moving towards the most specific.  For example, if I was writing a route for a date, I would write it like '2017/2/24'.  This works nicely for a few reasons, everything falls into different categories that fit, and it lends itself well to routing and organization.

So, lets prefix our API routes with 'api', and our next level of specificity for this project would be the 'poll', so we're going to prefix all of our routes with '/api/poll/'.  

With that decided, what do we want this strawpoll to do?  What are the primary actions that need to be taken?  What extra information do we need to complete these major operations?  So, add the following near your app.get('/'...) line in server.js

### Creating a new poll
So, thinking to our HTTP request types, creating anything new should be a POST request.  We won't need any other information here, we just know that we want to make a new poll.  This will be the basis of our route for this.

```javascript
// CREATE A POLL ---------------------------------------------------------------
app.post('/api/poll', function(req, res){
  res.send('creating a new poll');
});
```

### Getting an active poll
Alright, back to the HTTP requests, if we are just going to be retrieving data, we want to send a get request.  This request might look a little different because we use ':poll_id' as part of the route.  This is Express' way of showing a variable route.  This will match anything from /api/poll/dog to /api/poll/123456.  We just know we need a unique ID for every poll.

```javascript
// GET A POLL ------------------------------------------------------------------
app.get('/api/poll/:poll_id', function(req, res){
  res.send('getting a poll');
});
```

### Increasing the votes of a poll option
The final route we will need is to add a vote.  We know that we need the specific poll information, and we need to know the option the user is voting for.  Again, we will utilize Express' variable routes to handle this type of a request.  Also, we will use the HTTP request type of PUT, which is usually used for modifying existing data.

```javascript
// ADD VOTE  -------------------------------------------------------------------
app.put('/api/poll/:poll_id/:option_id', function(req, res){
  res.send('adding a vote');
});
```

## Moving Forward
Now that these are all made, restart your server (ctrl+c then restart as mentioned above), and try these routes via Postman or cURL or whatever you feel comfortable with, the routes would be accessed something like localhost:3000/api/poll.  You should receive the responses listed in the route.  After we know the server routes are working, we can begin handling the poll logic, which will be handled in the next couple of posts.  
