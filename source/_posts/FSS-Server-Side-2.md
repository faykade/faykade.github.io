---
title: Server Side Part II - Mongoose - (Full Stack Strawpoll)
date: 2017-02-25 12:22:32
tags:
  - Node.js
  - Express
  - Mongoose
  - Javascript
  - (Project) Full Stack Strawpoll
---
We're rocking now.  We have almost everything we need to get this bad boy going.  We just finished all of the routes that we will need to make the strawpoll service work, now we just need to add some logic in there.  The last big hurdle we need to overcome is that we need to store these polls somewhere.  We want users to be able to create a poll, then come back a few days later and see how it is coming along.  To do this, we need persistent storage, and that is where MongoDB comes in, along with Mongoose.  
<!-- more -->
## Mongoose Setup
Since every poll is going to have the same type of structure, we want to utilize the ideas of a Schema, which was popularized in more traditional relational database models.  What we want to do is set up a Mongoose __model__ to handle our polls.  So, open up our models directory within server, and create a file called 'poll.js'.  As a reminder, our structure now looks like this:
```
+ strawpoll
    + client
    + server
        + models
            - poll.js
        - server.js
        - config.js
```
Determined to use a schema, we need to settle on a structure that we want all of our polls to be stored as.  There are many ways to do this, but MongoDB is reknowned for handling json structures, so one simple one that I'm going to use here is the following:
```javascript
{
  _id: "UNIQUE POLL ID"
  poll_name: "Do you like this poll?",
  poll_votes: 25,
  poll_options: [
    {
      option_name: "Yes",
      vote_count: 20,
      _id: "UNIQUE OPTION ID",
    },
    {
      option_name: "No",
      vote_count: 5,
      _id: "UNIQUE OPTION ID",
    },
  ],
}
```
So, each poll will be a json object, and each one will have a few attributes and an array of possible options.  Now, we just need to replicate this type of structure using a schema in Mongoose.  Open our poll.js file, and add the following lines
```javascript
var mongoose = require('mongoose');
var config = require('../config');
var Schema = mongoose.Schema;

// plugin promise library
mongoose.Promise = global.Promise;

// define the behavior we want each poll to have
var pollSchema = new Schema({
  poll_name: String,
  poll_votes: Number,
  poll_options: [{ option_name: String, vote_count: Number }],
});

// attach schema to the model
var Poll = mongoose.model('Poll', pollSchema);

// export the model to be used elsewhere
module.exports = Poll;
```
Here, we are essentially just defining what type of data is in each Poll model when we instantiate the Schema with an object of the data types.  Each json object will automatically get an ID attached to it that will be a unique identifier in MongoDB (why we don't need to explicitly declare an id field).  By exporting the model, we can use it in other files and also utilize the functionality that mongoose models offer, such as simplified queries and an easier experience creating new documents.  The promise is just to stop a deprecation warning, and explicitly defines which type of promises we want to use.  There are better performing libraries such as bluebird, but the global Promise, which is the native one included in Node.js will work fine for our purposes.

## Final Setup Steps
With this information, we now have the model that is going to be used, but we still need to do a bit of work in our server file before we can finally finish the service.  We need to link our server up with the model, then link with the database, and finally, add the final piece of the puzzle which lets us read data sent to the API.

So, first thing is first, open up the server.js file and add a few lines to the top so we will end this section of the tutorial with something along the lines of the following.  I removed the initial app.get('/'), because we won't need it anymore, it was just for testing purposes.
```javascript
var express = require('express');
var app = express();
var config = require('./config');
var bodyParser = require('body-parser');
var mongoose = require('mongoose');
var Poll = require('./models/poll');

// connect to the database in our configuration file for our mongoose models
mongoose.connect(config.server.database_host);

// setup our body parser to read data sent from various sources to the API
app.use(bodyParser.urlencoded({
    extended: true
}));
app.use(bodyParser.json());

// CREATE A POLL ---------------------------------------------------------------
app.post('/api/poll', function(req, res){
  res.send('creating a new poll');
});

// GET A POLL ------------------------------------------------------------------
app.get('/api/poll/:poll_id', function(req, res){
  res.send('getting a poll');
});

// ADD VOTE  -------------------------------------------------------------------
app.put('/api/poll/:poll_id/:option_id', function(req, res){
  res.send('adding a vote');
});

// get the port number from the server environment or the config file if running locally
const PORT = process.env.PORT || config.server.port;

// start the server
app.listen(PORT, function(){
  console.log("Server listening on: " + PORT);
});
```
The final section of the
