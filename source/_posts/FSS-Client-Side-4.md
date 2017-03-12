---
title: Client Side Part IV - Poll Results - (Full Stack Strawpoll)
date: 2017-03-11 14:58:57
tags:
  - HTML
  - Javascript
  - JQuery
  - (Project) Full Stack Strawpoll
---
Finally, we made it!  The last of the functionality will be finished up in this installment.  This will likely be the most simple of the pages that we have created, but we will essentially be following the same process as before.  I'll show the dummy HTML page, we'll talk over the basic logic of what needs to happen on page load, and then go through each bit of it piece by piece.  
<!-- more -->
So, first off, the HTML.  Let's create a new file under pages/ called poll_results.html
```html poll_results.html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>Poll Results</title>
</head>
<body>
  <div class="content">
    <h1 class="poll_name"></h1>
    <table class="poll_results">
      <tr>
        <th>Option</th>
        <th>Count</th>
      </tr>
    </table>
  </div>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.1.1/jquery.min.js"></script>
  <script src="../scripts/util.js"></script>
  <script src="../scripts/poll_results.js"></script>
</body>
</html>

```
By now, this should look pretty familiar.  It is creating some placeholders for our results to get populated in.  Also, it has some headers immediately placed in the page, and each of our results will be shown as a table row beneath the headers.  So, with that in mind, we'll talk about the logic.  What needs to happen on page load?  We need to get the poll that we are referencing, then we just need to display them back to the user, easy peasy.  So, let's create a new file under scripts called poll_results.js and add the following:

```js poll_results.js
$(function(){
  var pollID = getUrlParameter("poll_id");

  if(pollID){
    requestPoll(pollID, displayPollResults);
  }
  else{
    alert("Sorry, no poll_id given to search for");
  }
});
```
Simple enough, we call the same requestPoll we created in the last tutorial, but instead of a displayPollOptions callback, we now reference a displayPollResults callback after we get the poll ID from the URL query string.  Looks like most of this functionality already exists, right?  The only thing that is new here is we need the displayPollResults.  So, in our poll_results.js file, add the following function

```js poll_results.js
var displayPollResults = function(pollData){
  if(pollData && pollData.data && pollData.success){
    var $container = $('.poll_results');
    var $title = $('.poll_name');
    var results = [];
    $title.html(escapeHtml(pollData.data.poll_name));
    $.each(pollData.data.poll_options, function(index, result){
      var currentResult = '<tr><td>' + escapeHtml(result.option_name) + '</td><td>' + escapeHtml(result.vote_count) + '</td></tr>';
      results.push(currentResult);
    });
    $container.append(results.join());
  } else {
    alert("Sorry, there was an error retrieving the poll data");
  }
};
```
Here, we simply check the response data sent back from our service, make sure it is successful, and then for each one, we follow a similar pattern to our last logic.  We build up each option one at a time, push it into an array, and then we join them back together as a single string to be injected into the DOM as a collection of table rows.  

Simple!  Here is an image of the view we have from the last sections poll after I vote Yes, I like the poll.

{% asset_img poll_results.png "Poll Results Example" %}

In the next section, we'll pretty this all up a bit and we'll have a completed result.  So, come back and I'll show some basic CSS rules that can be used to pretty this up relatively quickly.
