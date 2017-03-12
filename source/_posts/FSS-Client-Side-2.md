---
title: Client Side Part II - Creating a Poll - (Full Stack Strawpoll)
date: 2017-03-05 21:28:00
tags:
  - HTML
  - Javascript
  - JQuery
  - (Project) Full Stack Strawpoll
---
Making polls in Postman is all good fun, but we probably want a Graphical User Interface (GUI) for people to interact with our service, so we're going to start with poll creation.  In many ways, this has the most going on with it as well, so it will most likely be the most difficult part of the client side of this project, but we'll get through it.  Remember, our only goal right now is functionality, we'll come back later and pretty everything up.
<!-- more -->
## HTML
To start, let's make the skeleton of our HTML page.  Open up the create_poll.html file and add something like the following:
```html poll_create.html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>Create a Poll</title>
</head>
<body>
  <div class="content">
    <h1>Create a Poll</h1>
    <form id="create_poll" name="create_poll" action="#" method="POST">
      <input type="text" placeholder="Poll Title" name="poll_title" class="poll_title"></br>
      <div class="options">
        <div class="option_line">
          <input type="text" placeholder="Option..." class="poll_option">
          <button type="button" class="remove_option" tabindex="-1">-</button>
        </div>
      </div>
      <div class="buttons_container">
        <button type="button" class="add_option">+</button>
        <button type="reset">Clear</button>
        <button type="submit">Submit</button>
      </div>
    </form>
    <div class="poll_link">
    </div>
  </div>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.1.1/jquery.min.js"></script>
  <script src="../scripts/util.js"></script>
  <script src="../scripts/poll_create.js"></script>
</body>
</html>
```
The output of this html is shown here:

{% asset_img create_poll_boilerplate.png "Create Poll Boilerplate" %}

Our goal is that a user can enter a poll title, then enter a poll option.  Then, they have the ability to add more poll options via the + button, or remove a specific line via the - button.  They can also clear the form or submit it at any time.  Makes sense, right?  We want users to have a poll title and poll options, so make sure to give them good functionality for that.  

The important things to notice here is that each option/remove button has its own container with the class "option_line", and each of those lines is housed in a container with the class "options", and that we have an empty div with the class "poll_link".

## Javascript

Now, onto the javascript portion of the client side, where the vast majority of the work is going to take place.  What are the main things we want to accomplish from this page?
* Add an option (+ button)
* Remove an option (- button)
* Submit the request (submit button)
* Somehow see the poll when completed

So, let's go through these one at a time.  

## Add an option (+ button)
To add an option, we need a new input field, so open the poll_create.js file and add the following lines.
```javascript poll_create.js
var addOption = function(e){
  e.preventDefault();
  e.stopPropagation();
  var div = '<div class="option_line">';
  var input = '<input type="text" placeholder="Option..." class="poll_option">';
  var button = '<button type="button" class="remove_option">-</button>';
  $('.options').append(div + input + button + '</div>');
  $('.options input[type=text]').last().focus();
};

// SCRIPT EXECUTION ------------------------------------------------------------
$(function(){
  $('.add_option').on('click', addOption);
});
```
So what is going on here?  First off, we stop the default behavior of the button, which is to submit the form.  Then, we are essentially recreating all of the pieces of the 'option_line' container that is in our boilerplate example above as strings.  Then, we are using a JQuery selector to find the container with the class 'options' and appending all of those strings at the **end** of the HTML for that container/part of the document object model(DOM).  So, this preserves the original HTML in our 'options' container, and adds new HTML to the end of it.  So, basically, the relevant part of our HTML would result in something like this after completion, if we were going from the example above:

```html
<div class="options">
  <div class="option_line">
    <input type="text" placeholder="Option..." class="poll_option">
    <button type="button" class="remove_option">-</button>
  </div>
  <div class="option_line">
    <input type="text" placeholder="Option..." class="poll_option">
    <button type="button" class="remove_option">-</button>
  </div>
</div>
```
and the relevant image:
{% asset_img poll_create_added.png "Create Poll After Added Option" %}

Since this is happening dynamically and not on page load, if you use inspector tools in your browser, you will be able to see the new DOM element there, but not when you do a view page source.  As you can see, there is a problem with the way these remove buttons are lining up already.  This is because in our HTML we put the input box and the remove box on different lines, but in our Javascript, we are just creating one big string and throwing it into the DOM at once.  But, we're not worried about appearances for now, just functionality.

The final part of the code we have, is that when you add an input box, it only makes sense that you are ready to add another option, so we use a JQuery selector once again to find the input box we just added, and focus it so you can start typing immediately once it has been added to the page.  This is more just a quality of life aspect than functional, but these are what differentiate a good experience from a bad experience usually.

Sweet, now we can add new input boxes for a user to enter options into.  

## Removing an option (- button)
So, we have the Yin of adding options, let's do the Yang and add the ability to remove options.  Edit your code to have the following in it in the poll_create.js file.  

```javascript poll_create.js
// ...
var removeOption = function(e){
  e.preventDefault();
  e.stopPropagation();
  var $parent = $(this).parent();
  $parent.remove();
};
// ...
// SCRIPT EXECUTION ------------------------------------------------------------
$(function(){
  $('.add_option').on('click', addOption);
  $(document).on('click', '.remove_option', removeOption);
});
```
Even easier, right?  It makes less sense though.  What we are doing is immediately stopping the form from submitting again.  Then, remember the way our DOM is setup, as shown below.
```js
options
    option_line
        input
        remove button
    option_line
        input
        remove button
//... etc  
```
When a user clicks the remove button, an event (e) is triggered, and our code knows exactly where that event came from, and that is always 'this' in javascript, the context of where a function was called from.  Using 'this' can get tricky at times, but it is necessary in situations like this.  What I want to know is **which** remove button was clicked, and then remove the whole line from the DOM.  So, I find out which element was clicked, find its **parent**, and then remove that, which in turn removes everything inside of it.  So, as a result that piece of the DOM is removed from the web page.

One thing that I should also explain is why the syntax is different between our add_option event handler and our remove_option event handler.  Add option is a single button that is always present on the page, so when I use the $('.add_option') selector, JQuery knows which element I'm talking about.  However, since only the **first** option input and remove button are present on the page at page load, JQuery only knows about that one.  If I use the same type of syntax, we will only ever be able to remove the very first input box, since JQuery doesn't know about the dynamically generated DOM elements.  

To get around this, we instead attach our listener to the document object, and wait for anything with the remove_option class to get clicked instead, which JQuery will recognize, and properly call our event handler.  This is a fine detail, but is helpful to understand.

That's it! Done with removing options.

## Submit the request (submit button)
Okay, the biggest part of the whole deal, submitting the request.  Let's break this one down a bit more, since there is a bit more going on.  Start by simply adding these lines to your poll_create.js file

```javascript poll_create.js
// ...
var isValidTitle = function($title){
  return $title && $title.val() && $title.val().trim();
};

var areValidOptions = function($options){
  return $options && $options.length > 0;
};

var createPoll = function(e){
  e.preventDefault();
  e.stopPropagation();
  var $title = $('.poll_title');
  var $options = $('.poll_option');

  if(isValidTitle($title) && areValidOptions($options)){
    var request = buildRequest($title, $options);
    makeRequest(request, displayPollLink);
  }
  else {
    alert("Invalid data to make a poll");
  }
};
// ...
// SCRIPT EXECUTION ------------------------------------------------------------
$(function(){
  $('.add_option').on('click', addOption);
  $(document).on('click', '.remove_option', removeOption);
  $('#create_poll').on('submit', createPoll);
});
```
What got added here?  We created an event listener for when the form is submitted (enter key or submit button clicked), and we call createPoll with the event.  We stop the form from immediately submitting, and instead extract the title and options the page using JQuery selectors.  Then, we make sure that the title exists, and that there is 1 or or more option associated with the form.  I abstracted those out into separate functions in case I wanted to change the title and option validation later, such as add a minimum/maximum amount of options, etc.

The rest is a little abstract right now because I haven't shown all of the code, but just theoretically try to understand what is going on.  If the title and options are valid, I am going to call some function buildRequest to physically create the request, and then make the request to the server using the makeRequest function (using the request that was built in buildRequest).  Here, displayPollLink is going to be used as a **callback** function.  So, when the request is completed and I know everything was successful, I want to display the poll link.  Now that the core idea is understood here, add the functions that I was just describing to the code.  


```javascript poll_create.js
// ...
var buildRequest = function($title, $options){
  var ajaxData = {
    poll_name: $title.val().trim(),
    poll_options: []
  };

  $.each($options, function(index, option){
    var $option = $(option);
    if($option.val() && $option.val().trim()){
      ajaxData.poll_options.push($option.val().trim());
    }
  });

  return ajaxData;
};

var makeRequest = function(req, callback){
  $.ajax({
    url: "http://localhost:3000/api/poll",
    method: "POST",
    dataType: 'json',
    data: req
  })
    .done(callback)
    .fail(function(xhr, status, err){
      alert("Error creating poll, please try again");
    });
};

var displayPollLink =function(res){
  if(res && res.data && res.success){
    $('div.poll_link').html('<a href="poll_view.html?poll_id=' + res.data._id + '">Link to Poll</a>');
  } else {
    alert("Sorry, there was an error creating the poll");
  }
}
// ...
```
So, buildRequest is essentially going through all of the different values that are in the input entries as options, and if they exist, adding them to the object that will get sent to the server.  It will eventually return an object that looks something like this:

```javascript
{
  poll_name: "Do You Like This Poll?",
  poll_options: ["Yes","No","Maybe"]
}
```
Then, makeRequest takes that data, and sends a POST request using that data as an AJAX call, since that is what we defined in our server endpoint for creating polls.  Now, AJAX has these helpful functions .done and .fail.  .done will fire whenever the server responds, and .fail will call with some sort of malformed request.  So, when .done is called, it means my service has responded with the data that we set up server side in the create poll endpoint, and the callback function is called.  This function could have just as easily been written like this:
```javascript
var makeRequest = function(req, callback){
  $.ajax({
    url: "http://localhost:3000/api/poll",
    method: "POST",
    dataType: 'json',
    data: req
  })
    .done(function(res){
      if(res && res.data && res.success){
        $('div.poll_link').html('<a href="poll_view.html?poll_id=' + res.data._id + '">Link to Poll</a>');
      } else {
        alert("Sorry, there was an error creating the poll");
      }
    })
    .fail(function(xhr, status, err){
      alert("Error creating poll, please try again");
    });
};
```
However, I like to try and keep the logic a little more separate within AJAX requests, because the different callback functions can get tricky to comprehend when they get nested deeply within an AJAX request.  Either way, once the response is returned, we do a check to see if everything was successful (remember successResponse and errorResponse from our server side?) and either alert the user of an error, or insert a link to the poll if everything went as planned.  

Remember how our request/responses were structured?  This is where we can find out how to access the data received.  

{% asset_img Postman-REQ.png "Postman Request/Response" %}

## Conclusion
Whew!  Try it out, run your server with a 'node server' in a terminal, then try navigating to http://localhost:3000/pages/poll_create.html and see if you are able to get any links to show up when you add information to the form and submit it.
