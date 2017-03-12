---
title: Project Retrospective (Full Stack Strawpoll)
date: 2017-03-12 09:55:19
tags:
  - (Project) Full Stack Strawpoll
---
We finally made it, a project done.  Generally during a project I come up with some alternative ways to do something, and am not always happy with the way my project came out.  That's okay, all of these are about learning anyways, but it is worthwhile to think about aspects of the project that went well, and some that didn't go well both during and after you finish, so the same mistakes don't get made later.  
<!-- more -->
## Project Retro
About the project itself, I would say this went well overall as an intermediate level guide.  It shouldn't be obscenely easy and shouldn't be obscenely difficult either as long as you have some programming knowledge.  So, let's review what got done during this whole experience.

* Node.js server that accepts creation, vote modification, and getting results of polls
* Storage backed by MongoDB using Mongoose schemas
* Client side DOM manipulations via JQuery to make pages to use the server functionality
* Basic CSS to clean up the look

Quite a bit got done here really.  With some work, a real working product could come of this.  

## Making it production ready
Let's talk about some of the changes that would definitely need to be done for this to be made more viable than a toy project.  This is by no means an exhaustive list, but are some of the ones that come to mind immediately for a working product.

First, more work needs to be done for protection against user input.  We put in some minor elements in our user views to protect users at least, but something should really be done server side to protect it from ever getting stored in the database and protect ourselves as well.  

Next, some work needs to get done on validation.  We did some minor validation of user input, but nothing major.  If we wanted right now, a user could input some string that is thousands of characters long, and also send hundreds of options to the server to be stored.  This doesn't seem wise.  We should probably limit the amount of options and length of user input as well.  This would need to get done both client and server side.

Finally, the user experience would need to change.  Some of the controls of this application feel clunky.  It feels odd to have to manually click on the add button to add options.  You can tab to it and hit enter, but most users aren't going to think of that.  A mechanic should be put in place when tab gets hit, or enter gets hit, a new input box appears, then they just have to click the submit button once, instead of the add button multiple times.

## What went well?
I think overall, the server side part of this project went very well.  It utilized the correct libraries, was organized rather well, and was abstracted to a good point.  Also, I liked the very even split between client side/server side, which sometimes gets blurred with templating.  I'm also relatively pleased with the end product.  It is simplistic, yet functional.

Overall, I'm very happy with this project.  There are things I would change (which are easier to remember than good things), but for the most part I think this project turned out fairly well.

## What would I change?
Personally, I would probably do the whole client side a little differently.  This tutorial was supposed to be an intermediate level project, so I didn't want to introduce too many different libraries.  JQuery is something that is pretty standard, however the DOM manipulations we used here are very 'expensive' technically.  I tried to limit them into a few injections, but I would prefer to use something that utilizes a virtual DOM and more of a modeling behavior to change the page views.  This would be a more 'modern' approach, and also much faster.  

If not a virtual DOM, I would have **maybe** liked to introduce templating with EJS or Jade or something, but I liked the idea of showing how any user could use any API without being on the same server, since this is how many front end developers use API's.  Either way, the JQuery DOM manipulation works well, but it felt a little 'hacky' at times, and could probably be cleaned up.

Secondly, the CSS.  The look and feel of this site is okay, but it doesn't feel quite right to me.  I would probably spend some more time prettying it up, but I haven't found a good way to explain CSS in this blog right now, so I wanted to keep it simple and also limit the amount of CSS knowledge needed for this level of project.

Finally, I would probably introduce some sort of library to make the actual results page a little more pretty, and not just display the current vote count.  There are many graphing libraries out there, and it would be an easy step to add something like this in to make a 'pretty' results page.

## Blog Retro
Secondly, I'm fairly happy with this project in terms of my blog about it.  It was my first project to blog about, and it ballooned a little bit out of proportion to what I expected.  I can write something like this code up in half a day or so, but adding the blogging dimension made me realize how much is really going on here.  However, I think I did a decent job explaining the major points of this project, and maybe went into a little too much detail on some other ones.  I find that it is difficult to know what a reader is going to already understand and what they aren't going to understand, especially at an intermediate level project.  

## Conclusion
Thanks for trying out my first project, hopefully this went well for you, and let me know in the comments below!
