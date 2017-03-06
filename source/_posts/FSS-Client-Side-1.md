---
title: Client Side Part I - Setup - (Full Stack Strawpoll)
date: 2017-03-05 21:06:06
tags:
  - HTML
  - Javascript
  - JQuery
  - Node.js
  - Express
  - (Project) Full Stack Strawpoll
---
So, we've finished our server side of the full stack Strawpoll.  We've successfully created a REST API that will create, get, and modify polls and store them for later usage.  Now, we want to create the front end, or client side of the equation.  To do this, we first have a little bit of work to do to get setup, then we'll go into the details.

To briefly understand what we are trying to accomplish, we are going to use JQuery to do AJAX requests to our service based off of the data on our pages.  This means we are using asynchronous methodologies to make sure our operations are successful before we go through with anything.  
<!-- more -->
## A Bit More Server Side
Alright, I'm sorry I lied to you, we have a bit of work to do on our server side still, but not much, I swear!  The reasoning behind this is that you cannot do AJAX requests from a file hosted on your own computer, you need it hosted on a server of some sorts.  Just to be clear here, I'm going to host these files on the same server that we are using for our Strawpoll service, but they could be hosted anywhere, and that is an important thing to take note of, because many API's that you interact with will not be hosted on your own server.  

We're going to create a very simple setup to host our files, so open up our server.js file, and add the following lines somewhere near the other app.use sections of the code:

```javascript
app.use(express.static('./../client'));
```

This will allow us to serve STATIC files from our client folder on the server.  What does this mean?  It means that any type of file that is in the client directory can be accessed when the server is created.  As an example, and to complete our initial setup, modify the structure of our client directory to match this:

```
+ strawpoll
    + client
        + scripts
            - poll_create.js
            - util.js
        + pages
            - poll_create.html
    + server
```
Now, when your server is up, you will be able to access any of the files in the client directory.  To access poll_create.html, simply navigate to http://localhost:3000/pages/poll_create.html .  Notice that the '/client' is removed from the URL.  It is also possible to create a specifically named route that you want to use for all static assets in express, so you could have something like http://localhost:3000/static/pages/poll_create.html

Generally, these static files are reserved for things like just images/css/javascript/fonts that never changes, and we would instead use something like a templating engine to create our HTML pages.  However, our application is simplistic enough and I want to show a separation of concerns between the server/client, so we'll directly serve our html files here as well to narrow the scope of this project a bit.
