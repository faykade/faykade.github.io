---
title: Server Side Part III - Logic - (Full Stack Strawpoll)
date: 2017-02-25 14:16:10
tags:
  - Node.js
  - Express
  - Mongoose
  - MongoDB
  - Javascript
  - (Project) Full Stack Strawpoll
---
Alright, we'll finally finish the server side of the Strawpoll service now!  I swear we'll be done after this one.  There has been a lot of setup, but most good projects take a fair amount of setup, then everything becomes simple.  To finish this service, all we have to do still are setup the logic in each of our routes, so we'll go through them one at a time
<!-- more -->
## Creating a New Poll
When building up an API, it is sometimes helpful to start with routes that will provide the data that other routes used, otherwise you will be forced to mock data to do any testing as you go.  Since we need a poll before we can get a poll or modify a poll, it makes sense to try to build this logic up before the other ones.  I'll spend a bit more time talking about this first route than the others to explain what is going on, so stay with me.  

As a referesher of our Poll model, here is the relevant snippet of code from poll.js.

```javascript
var pollSchema = new Schema({
  poll_name: String,
  poll_options: [{ option_name: String, vote_count: Number }],
  poll_votes: Number,
});
```
So, this is the information that we will need to provide to the model to successfully create a poll.  Now, you may have wondered what the 'bodyParser' that we included in the last post was about, but what it does is allow us to read data sent to the API via the request object. Let's change our create a poll logic to something like this:

```javascript
// CREATE A POLL ---------------------------------------------------------------
app.post('/api/poll', function(req, res){
  if(req.body && req.body.poll_name && req.body.poll_options){
    var newPoll = new Poll();
    var constructedOptions = [];
    var options = req.body.poll_options;
    options.forEach(function(currentOption){
      constructedOptions.push({
        option_name: currentOption,
        vote_count: 0
      });
    });
    newPoll.poll_name = req.body.poll_name;
    newPoll.poll_options = constructedOptions;
    newPoll.poll_votes = 0;
  } else {
    res.json({
      success: false,
      message: "Missing poll name or options",
    });
  }
});
```
So what did we do here?  We are checking a few things in the if statement:
* Is there any information in the body of the request?  If not, the if statement fails due to short circuit evaluation and will fall into the else block.  We don't want to look for something in the body of the request if the body doesn't exist, because that will always be bad news bears and throw errors
* Is there a poll name?  This is going to be a required attribute for making a poll
* Are there any options associate with the poll?  There should be.

If all of these things are true, we set the information in the **model** to the data that came from the **request**.  If anything is missing, we send a response in json format regarding the failure.  To properly set everything in our model though, we need to know the type of information we are getting.  I'm planning on getting a title sent to me, and an array of poll options.  This doesn't exactly match up with our model, since every poll option needs to have both a name and a vote count.  So, I create a temporary array (constructedOptions) and populate that with the objects that match the way the model is setup, using the information that is sent via the request.  The fields that don't come in from the request (total votes and vote count for each option) need to be filled in manually.

There is still more to add here though; we haven't sent a response for the success yet.  If somebody tried to post to this route now with all the appropriate information, the server would just 'hang' and never respond to the client.  To remedy this, let's modify the route a bit further, and actually save the data to the database using the Mongoose model method, save, and then respond back to the client whether it was successfully saved or not.  
```javascript
// CREATE A POLL ---------------------------------------------------------------
app.post('/api/poll', function(req, res){
  if(req.body && req.body.poll_name && req.body.poll_options){
    var newPoll = new Poll();
    var constructedOptions = [];
    var options = req.body.poll_options;
    options.forEach(function(currentOption){
      constructedOptions.push({
        option_name: currentOption,
        vote_count: 0
      });
    });
    newPoll.poll_name = req.body.poll_name;
    newPoll.poll_options = constructedOptions;
    newPoll.poll_votes = 0;
    newPoll.save(function(err,poll){
      if(err){
        res.json({
          success: false,
          message: "Error saving poll information",
        });
      } else {
        res.json({
          success: true,
          message: "Successfully created poll",
          data: poll,
        });
      }
    });
  } else {
    res.json({
      success: false,
      message: "Missing poll name or options",
    });
  }
});
```
Now we have a fully formed route to handle the poll creation.  However, I feel like this looks pretty messy and is starting to get garbled.  There is a lot of nested information here that becomes difficult to understand in a glance already, so I see an obvious step to clean this up and refactor.  Refactoring code as you go is important, and if you ever see a scenario where you can move thing to a single method, take it.  

Here, I see that the responses all have pretty similar formats, so I'm going to pull these out into separate functions, and we'll end up with the following code.  Refactors like this can be tricky, because usually they are quite helpful, but every time you introduce a new function into the mix, it is something else that you have to just keep in memory.  So, try to keep functions simple, and keep their names useful to their purpose, so you can 'assume' the function is acting correctly and read the logic.

```javascript
// HELPERS ---------------------------------------------------------------------
var errorResponse = function(msg){
  return {
    success: false,
    message: msg,
  };
};

var successResponse = function(msg, data){
  return {
    success: true,
    message: msg,
    data: data,
  };
};

// CREATE A POLL ---------------------------------------------------------------
app.post('/api/poll', function(req, res){
  if(req.body && req.body.poll_name && req.body.poll_options){
    var newPoll = new Poll();
    var constructedOptions = [];
    var options = req.body.poll_options;
    options.forEach(function(currentOption){
      constructedOptions.push({
        option_name: currentOption,
        vote_count: 0
      });
    });
    newPoll.poll_name = req.body.poll_name;
    newPoll.poll_options = constructedOptions;
    newPoll.poll_votes = 0;
    newPoll.save(function(err,poll){
      if(err){
        res.json(errorResponse("Error saving poll information"));
      } else {
        res.json(successResponse("Successfully created poll", poll));
      }
    });
  } else {
    res.json(errorResponse("Missing poll name or options"));
  }
});
```

COOL! We've got something that **should** work, so let's try it out, right?  Fire up your server and Postman, and try making a request to this route endpoint by posting data as shown below.  The top half is the request being sent, and the bottom is the response.

{% asset_img Postman-REQ.png "Postman Request/Response" %}

Awesome!  You should be getting information into your database now, and polls are being stored.  The big chunks of the problem are done.  Something to notice about the response here are the id fields.  These are the ID's that we are going to use to navigate the database when we are trying to get a poll's current status, or when we are trying to modify a route.  Whew, that was a lot, the rest of the routes should be easier to understand though now that the first one is done.  I'm going to throw the whole code in, then explain what each route is doing for the next two.

## Getting an Active Poll

```javascript
// GET A POLL ------------------------------------------------------------------
app.get('/api/poll', function(req, res){
  if(req.query && req.query.poll_id){
    Poll.findOne({'_id': req.query.poll_id}, function(err, poll){
      if(err){
        res.json(errorResponse("Poll ID not found in database"));
      } else {
        res.json(successResponse("Found poll", poll));
      }
    });
  }
});
```

Here, all we are trying to do is do a MongoDB query.  All the MongoDB queries I've needed have been available via the Mongoose model, so here we simply try to find the 'document' that has the id of the paramater passed in.  We know that the overall poll has an object ID, so that is the ID we need to search for.  

In the Postman example above, we would want to use the 58bcf13c70aad429bd9db245 as our poll_id parameter, so if we did another Postman request (or a browser request, since those default to GET requests), we would set our url to something like http://localhost:3000/api/poll&poll_id=58bcf13c70aad429bd9db245 and retrieve the appropriate data.


## Modifying an Active Poll Vote

```javascript
// ADD VOTE  -------------------------------------------------------------------
app.put('/api/poll', function(req, res){
  if(req.body && req.body.poll_id && req.body.option_id){
    Poll.findOneAndUpdate(
      {'_id': req.body.poll_id, 'poll_options._id' : req.body.option_id},
      {'$inc': {'poll_votes' : 1, 'poll_options.$.vote_count': 1}},
      {new: true},
      function(err, poll){
        if(err){
          res.json(errorResponse("Error updating vote"));
        } else {
          res.json(successResponse("Successfully updated vote", poll));
        }
      }
    );
  }
});
```
This route is a bit trickier to understand, and it took me a little while to figure out the way to query for it.  What we are doing is finding the documents that match the actual poll id and the specific option id.  This way, we know which poll to increment the total votes on, and also know the option to increment specifically.  

We use the $inc operator to do this, and the 1 is just a parameter about how much we want to increment the value that is currently in there.  Adding {new, true} to the query also tells MongoDB to return the updated document, otherwise the default is to return the original document before the increment.  We don't want to know what the vote counts were BEFORE we incremented them, we want to know what they are after the increment.

To call this, we simply need the poll id and the vote id.  So, in the Postman example above, an example request to modify the 'Yes' vote would be to do a PUT request to http://localhost:3000/api/poll and send the data as shown below.  both the total votes and the specific option vote count will get incremented.

```javascript
{
  "poll_id": "58bcf13c70aad429bd9db245",
  "option_id": "58bcf13c70aad429bd9db247"
}
```

## Conclusion of Server Side
That's it!  We have the basic service set up for our Strawpoll.  Next up, we'll start on the client side to actually interact with this service, since this is a full stack project after all and we want to learn how to effectively interact with a REST API.  We'll set up the basic functionality, and then try and pretty it up a bit to see the full project spectrum.  
