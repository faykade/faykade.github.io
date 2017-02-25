---
title: Project Setup and Structure (Full Stack Strawpoll)
date: 2017-02-24 22:32:29
tags:
  - Project Structures
  - (Project) Full Stack Strawpoll
---
Before starting any project, it is important to spend some time thinking about the directory structure.  It may seem like a waste of time, because we all just want to start writing code once we get it into our heads to start something like this, but it is always worthwhile to really think out the way your project will be laid out, and will pay dividends in the long run.  

Determining project structure becomes especially important when using different build systems like Gulp.  This post also covers the different installations needed for this project.  
<!-- more -->
## Structure
This project won't be too complex, and won't involve any build systems, so we will have a pretty simple project setup.  Create the following directories.  While these directories are both housed in the same project, it is important to realize that they are decoupled as much as possible, and wouldn't need to be in the same project folder.
```
+ strawpoll
    + client
    + server
        + models
```

## Setup

* Recommended
  * [Git](https://git-scm.com/downloads)
  * [Postman](https://www.getpostman.com/)
* Required
  * [Node.js && NPM](https://node.js)
  * A MongoDB instance, I personally recommend using [MLab](https://mlab.com), but you can also use a local setup if you want to install MongoDB

Now that we have the basic installations, lets get the packages we will need for the project installed.  Open a terminal, navigate to the project directory (I named it strawpoll), and execute the following commands one at a time:

```
npm init     // follow the on screen prompts to create your node package
npm install --save express
npm install --save body-parser
npm install --save mongoose
```
Express will be our router, and body-parser is needed to effectively read information posted to our different routes.  Mongoose is useful because it simplifies all of the transactions we will need to utilize with our database and allows us to create schemas, which is something that is missing if you are using something like the mongodb package.  

That's it! We're ready to actually start the project.
