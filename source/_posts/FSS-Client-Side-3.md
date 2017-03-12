---
title: Client Side Part III - Poll Voting - (Full Stack Strawpoll)
date: 2017-03-11 14:05:56
tags:
  - HTML
  - Javascript
  - JQuery
  - (Project) Full Stack Strawpoll
---
So, now we have the ability for people to create new polls.  Now we want people to be able to access them.  The first step is again going to be to create our basic HTML layout, and then we'll attach some JS logic to actually handle everything.  So, go to your client/pages directory and create a poll_view.html file and add the following.
<!-- more -->
```html poll_view.html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>Poll View</title>
</head>
<body>
  <div class="content">
    <h1 class="poll_name"></h1>
    <form id="vote_form">
      <div class="options_container">
      </div>
      <div class="buttons_container">
        <button type="submit" class="cast_vote">Cast Vote</button>
      </div>
    </form>
  </div>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.1.1/jquery.min.js"></script>
  <script src="../scripts/util.js"></script>
  <script src="../scripts/poll_view.js"></script>
</body>
</html>

```
Essentially, all we are doing is creating a place where the poll items will get populated, and a button to actually cast the vote.  So, go back into your scripts directory and make a poll_view.js file.  What are the first things that need to happen?  First of all, we need to know which poll we're going to get.  Then, we're going to have to actually call our service and find out what options are available for that poll.  Finally, we'll need an ability to vote on the results.  So, let's start with the initial logic for that.  Add the following to your poll_view.js file:
```js poll_view.js
$(function(){
  var pollID = getUrlParameter("poll_id");

  if(pollID){
    requestPoll(pollID, displayPollOptions);
    $(document).on('submit', '#vote_form', function(e){
      addVote(e, pollID, function(){
        window.location.href = 'poll_results.html?poll_id=' + pollID;
      });
    });
  }
  else{
    alert("Sorry, no poll_id given to search for");
  }
});
```
So, this is essentially the basics of what I just said.  We're initially going to get the poll_id from the **query string** that we passed into the url when we made our link from the create a poll page.  Then, we request the poll information, and when the user submits their vote, it adds a vote and goes to the poll results page.  Easy enough, right?  Now, lets add in the basic functionality discussed here.  Open up the **util.js** file, and add the following:

```js util.js
var getUrlParameter = function getUrlParameter(sParam) {
  var sPageURL = decodeURIComponent(window.location.search.substring(1));
  var sURLVariables = sPageURL.split('&');
  var sParameterName;
  var i;

  for (i = 0; i < sURLVariables.length; i++) {
    sParameterName = sURLVariables[i].split('=');

    if (sParameterName[0] === sParam) {
      return sParameterName[1] === undefined ? true : sParameterName[1];
    }
  }
};
```
Now, this is stolen shamelessly from some Stack Overflow question, because for something like parsing a query string, it is usually best to go look for something that has already been written, since it is a task that **many** people have needed to do before.  There are other ways of doing this, but if you read through this code, essentially the author gets the query string, splits up the parameters on it, and then loops through them based on the parameter name passed in searching for the one we need.  If it is found, the function returns the value.  

So, we've gotten our poll ID, now we just need to get the potential options related to the poll ID on this page.  So, again, in util.js, we will add our logic to get a poll.  I'm adding this to util.js instead of poll_view.js because we'll be using the same method on our poll results page, since we are doing the same service call to get the poll either way.  
```js util.js
var requestPoll = function(id, callback){
  $.ajax({
    url: "http://localhost:3000/api/poll?poll_id=" + id,
    method: "GET",
  })
    .done(callback)
    .fail(function(xhr, status, err){
      alert("Error finding poll, please try again");
    });
};
```
So, when the page loads, we get the ID from the query string, then request the poll based on that ID.  We should have our results, so we utilize the **callback** function passed to the request poll. In this case, our callback function references displayPollOptions back on line 5 of poll_view.js in the example shown above.  

Alright, so since we are only displaying poll options to be voted on for this one page, let's navigate back to our poll_view.js file, and we can add the logic to actually add in the poll options onto the page.  This is where our containers in the HTML that we created before are invaluable.
```js poll_view.js
var displayPollOptions = function(pollData){
  if(pollData && pollData.data && pollData.success){
    var $container = $('.options_container');
    var $titleContainer = $('.poll_name');
    $titleContainer.html(escapeHtml(pollData.data.poll_name));
    var options = [];
    $.each(pollData.data.poll_options, function(index, option){
      var checkedStatus = index === 0 ? ' checked' : '';
      var optionHTML = '<label><input type="radio" name="option" value="' + option._id + '"' + checkedStatus + '>' + escapeHtml(option.option_name) + '</label>';
      options.push(optionHTML);
    });
    var allOptions = options.join('</br>');
    $container.html(allOptions);
  } else {
    alert("Sorry, there was an error retrieving the poll data");
  }
};
```
So, what's happening?  I'm taking the response data that was passed to this callback, checking that it was successful, and filling the containers with the relevant data.  One of the things in this that may not be immediately understandable is the **escapeHtml** function usage.  Escaping the HTML is to prevent some cross site scripting.  There is a lot of literature on that online, so I suggest taking a more detailed look, but essentially it prevents people from inserting their own HTML or scripts onto the page for other users to view.  

This is important because I did not escape any of the input from users on our create a poll page.  So, if somebody wanted, they could make their "poll option" something like <script>window.location.href = www.myhorriblevirus.com</script> and instead of showing that to somebody who viewed the page, the script would execute and just take you directly to the bad page, or worse.  In real practice, the input should be cleansed before ever being saved in the database, but this is more of a simple project, and doing this at least ensures that if somebody DID set this as one of their options, it will actually just show the plain text version of the script tag and the information they tried to show.  

I placed this function in the util.js file as well, since it is useful whenever displaying user input back on a page.  This is another one that is taken from a stack overflow question, because cross site scripting is a **very** real problem, and many people have tried to solve it.  Essentially, this solution plays on the way native javascript grabs data from elements and escapes it already.  Just remember to always escape user input before displaying it back to a user as a general rule of thumb.
```js util.js
var escapeHtml = function(str) {
    var div = document.createElement('div');
    div.appendChild(document.createTextNode(str));
    return div.innerHTML;
};
```
That was a bit of a tangent, but an important one.  The other thing that may not be glaringly obvious when I'm creating the different radio buttons for the options is how I'm creating the HTML.  What is going on, is that I'm creating the option HTML one at a time, and pushing them into an array.  Each option has its unique ID set as its value, so I can access it later.  The checkedStatus is just making sure that the first option is selected by default.  Then, when all the options are done, I'm joining them into a single string, and putting a new line between each one to make sure they all appear on different lines.  Finally, the entire chunk is placed on the page.

Finally, we can see our options, now we just need to handle the voting logic.  So, in our poll_view.js file, add the last piece of the puzzle:
```js poll_view.js
var addVote = function(e, poll, callback){
  e.preventDefault();
  var selectedOption = $('#vote_form input[type="radio"]:checked').val();
  $.ajax({
    url: "http://localhost:3000/api/poll",
    method: "PUT",
    data: {
      poll_id: poll,
      option_id: selectedOption
    },
    dataType: "json"
  })
    .done(callback)
    .fail(function(xhr, status, err){
      alert("Error adding vote to poll, please try again");
    });
};
```
So, here, I'm essentially just getting the option ID from the value of the checked radio button using JQuery selectors, and then I call the service we created.  When it is complete, the callback that was passed in is called.  In this scenario, the callback was shown way up in our first code example of poll_view.js, and it just redirects the user to the poll results page for that specific poll ID.

Here is an example of a poll view from a poll that I just created

{% asset_img poll_view.png "Poll View Example" %}

Not super pretty, but again, we're just worrying about functionality for now.  Then we'll worry about making it look better.  So, when I cast my vote, I will get redirected to the final page we need to make.  So, check out the next part of the series to finish everything up functionality wise.
