---
title: Client Side Part V - Pretty Poll - (Full Stack Strawpoll)
date: 2017-03-11 15:19:05
tags:
  - HTML
  - CSS
  - (Project) Full Stack Strawpoll
---
We have all the hard parts done, now it is just time to make it look like something that isn't straight out of some 80's movie from the first time anybody saw what a computer was.  I'm not designer, but you can add some simple styling to a page relatively quickly.  We could use a library like foundation or bootstrap and just utilize their default styling, but since this is a full stack tutorial, I'll show some basic styling rules.  
<!-- more -->
So, to start off, we'll need to add a new file to our pages.  So, in our project directory, alter it so it looks like the following
```
+ strawpoll
    + client
        + scripts
        + pages
        + styles
            - styles.css
    + server
```
Then, go into each of the 3 pages in our pages directory and add a line to include the css into our code, somewhere in the head element, like so.
```
<head>
  <meta charset="utf-8">
  <title>Create a Poll</title>
  <link rel="stylesheet" type="text/css" href="../styles/styles.css">
</head>
```
Now, everything is linked up so let's make our first rules.  Literal CSS Styling is kind of difficult to walkthrough without picture by picture references, and I don't think I'm doing anything fancy enough here to warrant something like that.  So, I'll simply show the CSS code that I come up with, and explain bits and pieces.
```css styles.css
button,
input[type="text"] {
  padding: 5px;
  margin: 2px 0px;
}

input.poll_title {
  margin: 10px 0px;
}

a,
a:visited
a:active {
  color: #258fb8;
}

a:hover {
  color: black;
}

h1 {
  text-align: center;
}

table {
  width: 100%;
  border-collapse: collapse;
}

td,
th {
  padding: 5px;
}

th {
  color: white;
  background-color: black;
  border: 1px solid #eeeeee;
}

tr:nth-child(odd){
  background-color: #dddddd;
}

tr:hover {
  background-color: #258fb8;
}

.content {
  width: 75%;
  margin: 0 auto;
  background-color: #eeeeee;
  border: 1px solid black;
  border-radius: 15px;
  padding: 10px;
}

.remove_option,
.add_option {
  width: 30px;
  font-weight: bold;
  border: 1px solid grey;
}

.remove_option {
  background-color: maroon;
  color: white;
}

.add_option {
  background-color: green;
  color: white;
}

.option_line {
  display: flex;
}

.poll_option {
  flex: 2;
}

.poll_link {
  text-align: center;
}

.buttons_container {
  margin: 10px 0px;
  text-align: center;
}

.cast_vote {
  width: 100%;
}
```
So, essentially the only real things of note here is the use of flexbox to make sure that the add option lines fill the entire line together with the remove button, and if you haven't used CSS selectors, you can see how to change the odd rows to a different color of a table here as well.  Other than that, most of the things shown here are pretty simplistic, and are just padding around elements.  The things to note here are the usage of percentages rather than hard pixel values.  That makes it so even this simple little poll creator works on both mobile and desktop views.

## Finale

So, here are a few final image results.  Link to the actual poll site will be coming when I deploy to Heroku.  I'll be creating one more final post after this is all made with a link to a poll example and the project retrospective, where I talk about what I liked and didn't like about the project, and some things that could be done in a different/better way.


{% asset_img poll_create.png "Poll Create Example" %}
{% asset_img poll_view.png "Poll View Example" %}
{% asset_img poll_results.png "Poll Results Example" %}
