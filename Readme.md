# Introduction

This book is aimed at designers who also do front end development. These days, it seems that we all wear multiple hats in our jobs!

Since the turn of the century, web development has become far more sophisticated. This has led to more complexity, and many of us are finding we have to work with many new things. This can seem a little intimidating (I have certainly found myself wishing that the pace of progress would slow down!).

To take advantage of this sophistication whilst reducing its complexity, front-end developers use build tools. These tools allow us to work at a higher level and automate away repetitive tasks, thereby saving time and producing more sophisticated results. Automation streamlines our processes so that we can focus on creative work, rather than spending hours lost in the weeds of tedious tasks.

In this short book, we will go through some of the build tools that are available, what they can do for you, and how you can get started with them. I will refer to some specific tools, but this book will not teach you the specifics of a particular build tool; after all, each build tool is well documented online and in print already. Instead, this book functions as a primer on what build tools are and how to assemble powerful, efficient build workflows with them.

The goal of this book is that, once you have read it, you will be able to improve your workflow by incorporating build tools. I aim to give you the confidence and knowledge needed to dive into this world of powerful and flexible utilities, become more productive, and enjoy your work more as a result.

# Let’s talk about workflow

Workflow is the set of tasks that one goes through when doing a job. Everyone’s workflow is different, and you might have multiple workflows for different aspects of your role. We will look at web development workflow and how to optimise it.

## Basic web development workflow

In developing websites and applications, you probably have phases that look something like this:

![alt text](img/workflow-basic.png?raw=true "Schema 1")

> Don’t worry if your workflow is different. I’ve just selected a number of common tasks; the principles found in book will apply no matter what your workflow is.

There are a number of problems with this common, simplistic workflow:

* File size - there is no minification of the assets or compression of the images, so the application will perform slower than it ought.
* Consistency - pushing to a server can be error prone: assets may be cached, transfers can fail, etc.
* Speed of workflow - pushing and refreshing manually is time consuming and takes you out of the "zone" of your workflow.

Let’s add a few more tasks to this workflow to address these issues.

# Creating an asset pipeline

To resolve the problems we have identified in the basic workflow, we can incorporate compression, minification, and concatenation. Minification compresses code without changing its functionality. Concatenation combines multiple files together into a single file, improving download speeds. Let’s add these tasks to the process:

![alt text](img/workflow-asset-pipeline.png?raw=true "Schema 1")

This approach, commonly known as the asset pipeline, takes assets from an input area, transforms them, and delivers them to an output area. We will look at how to structure and automate an asset pipeline.

A common convention is to have two separate directories in the root (top level) of your project. The first is src, which contains source code - the files you edit. The second is build, which contains files that are generated and delivered to the end user. You never edit anything in build directly. The project’s specific location is not important, but this book assumes that the project is saved in /home/user/myproject on OSX/Linux or C:\Users\user\myproject on Windows.

> In some projects, build may be called dist, which is short for "distribution".

| Directory	OSX/Linux	| Windows 
| ------------- |:-------------| 
| Source	/home/user/myproject/src | C:\Users\user\myproject\src |
| Build	/home/user/myproject/build | C:\Users\user\myproject\build |

Once build and src are separated, move any existing project files into the src directory. These are the files that you will edit. Everything in build will be generated using your build tool - we never edit files in build directly. Hands off!

> It’s best to test something that’s as representative as possible of what the end-user will receive. Therefore, it’s a good practice to test in build, not in src, so as to catch any potential problems that the transformations might have introduced.

A full treatment of SCM (Software Configuration Management) is outside the scope of this book, but I will make one suggestion: when using a version control tool like Git, Mercurial, or Subversion, add the build directory to your SCM’s ignore file. You do not want to commit anything in build - it’s considered volatile. In SCM, only the source files are committed to your SCM, and these generate the build. This keeps commit logs clean and uncluttered by the build assets.

> To learn about Git, check out [Version Control with Git by Ryan Taylor](http://www.fivesimplesteps.com/products/version-control-with-git)

Here is how the asset pipeline might look:

![alt text](img/workflow-asset-pipeline-build-level.png?raw=true "Schema 1")

Assuming that you have two javascript files, src/js/a.js and src/js/b.js, and two CSS files, src/css/first.css and src/css/second.css, the build would output build/js/all.min.js and build/css/all.min.css:

![alt text](img/workflow-asset-pipeline-build-level-detail.png?raw=true "Schema 1")

Because we have concatenated multiple assets into single files, we will have to replace the references to the assets. In src/index.html, we have four such references:

```html
<head>
  <script src="/js/a.js"></script>
  <script src="/js/b.js"></script>
  <link rel="stylesheet" media="all" href="/css/first.css">
  <link rel="stylesheet" media="all" href="/css/second.css">
</head>
```

In build/index.html, this becomes just two references:


```html
<head>
  <script src="/js/all.min.js"></script>
  <link rel="stylesheet" media="all" href="/css/all.min.css">
</head>
```

At this point, the minified and concatenated assets, bundled up cleanly in the build directory, are ready to be pushed to the server. There are some problems, though:

* Isn’t this process unnecessarily complex?
* How do we know if there are problems with the code?
* How do we manage this process?

 Here is where we introduce the wonderful world of build tools!

# Automated workflows with build tools

A build tool is software that automates the transformation of source code into a deliverable. Any time a change is made to the source code, the build tool should be run - usually from the command line. The build tool is how we implement asset pipelines:

![alt text](img/automated-workflows-asset-pipeline-with-tool.png?raw=true "Schema 1")

## Available build tools

There are many build tools available. This book focuses on the web and therefore looks at those tailored to HTML, CSS, and JavaScript. Grunt and Gulp, the two most popular, will be our focus because of their widespread adoption. There are many others, such as Broccoli, Middleman and Brunch, but this book’s principles apply to all build tools.

> If your needs are simple, the package manager NPM can be used as a build tool - NPM along with Browserify can be quite a powerful combination. See http://blog.keithcirkel.co.uk/how-to-use-npm-as-a-build-tool/ for details.

## The build file

The build file tells the build tool what to do. It defines a set of tasks that the build tool can perform. In Grunt, this file is called Gruntfile.js. In Gulp, it is called Gulpfile.js. As a rule, the build file goes at the root of the project, above the build and src directories.

| Item	| OSX/Linux | Windows 
| ------------- |:-------------|:-------------| 
| Source dir | /home/user/myproject/src | C:\Users\user\myproject\src |
| Build dir | /home/user/myproject/build | C:\Users\user\myproject\build |
| Build file (Gulp)	| /home/user/myproject/Gulpfile.js | C:\Users\user\myproject\Gulpfile.js |
| Build file (Grunt) | /home/user/myproject/Gruntfile.js | C:\Users\user\myproject\Gruntfile.js |
		


