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
		
## Tasks
Build tools run tasks. A task is simply an action or a set of actions that can be performed. For example, a task could be "copy files from src to build".

In most build tools, tasks are composable, meaning that a task can depend on or delegate to other tasks. For example, task copy-assets could depend on task clean, so that whenever copy-assets is run, clean is automatically run beforehand. The advantage of composability is that the code for the clean task won’t be repeated every time you need to call it.

Tasks are stored inside the build file; here is an example of a build file that contains some tasks:

![alt text](img/automated-workflows-build-file.png?raw=true "Schema 1")

### Anatomy of a task

A task generally has these attributes:

#### Name
The name of the task. Most tasks can be run in isolation by passing the task’s name to the tool on the command line. For example, grunt clean or gulp clean would run the clean task.
#### Description
Some build tools supply an optional task description, an explanation of the task that doubles as documentation for people on your team.
#### Dependencies
A list of tasks that must be run before a certain task can run. For example, the build task may have clean as a dependency, meaning that if you call the build task, the clean task automatically runs first.
#### Functionality
This is the specific code or configuration that you write to implement your task. For example, the scripts task could minify and concatenate a set of files from src and then copy them into build/js.

# Creating your first build file

We will create a build file that automates the workflow that we have defined. I’m not going to write the actual code here, as it will differ based for each build tool, but I will represent it in an abstract manner.

These tasks will:

* Clean the build directory
* Compress and minify assets
* Copy the transformed results into the build directory

Here is the order in which the tasks will run:

![alt text](img/your-first-build-file-task-order.png?raw=true "Schema 1")

> Some build tools can run tasks in parallel; others will run them one at a time in sequence. In the diagram above, we have assumed that we can run scripts, styles, images and html in parallel.

## Explanation

### Task clean - cleans out build directory

This simple task cleans out the build directory by deleting everything in it.

### Task build - calls the subtasks

Depends on task clean via the dependency mechanism discussed earlier, so when build is called, it first triggers clean. After clean completes, the build task delegates to the scripts, styles, images and html tasks.

### Task scripts - transforms javascript

Takes src/js/a.js and src/js/b.js, minifies them, then concatenates them into build/js/all.min.js.

### Task styles - transforms CSS

Takes src/css/first.css and src/css/second.css, minifies them, then concatenates them into build/css/all.min.css. For designers writing in Sass or LESS, styles will also compile those files into CSS before minification and concatenation.

### Task images - compresses images

Takes the images in src/img, compresses them, and outputs them to build/img.

### Task html - transforms HTML

Takes src/index.html and minifies it. It then modifies the HTML, directing the asset paths to build, not src. The output is then directed to build/index.html.

Once the build task completes, you have your complete website, ready for use in the build directory.

## Plugins

For each task, there is an installable plugin for your build tool. This is great because it means you usually have to write very little code.

The Gulp team maintains a list of verified plugins and their community ensures that there is only one plugin for each task. In contrast, the Grunt community often offers a number of alternatives for each plugin. The grunt-contrib-* plugins are the best starting point because they are officially maintained.

> The specifics of how to install and use these plugins are beyond the scope of this book.


| Task	| Gulp plugin | Grunt plugin 
| ------------- |:-------------|:-------------| 
| clean| del | grunt-contrib-clean |
| scripts| gulp-uglify | grunt-contrib-uglify |
| styles| gulp-minify-css | grunt-contrib-cssmin |
| images| gulp-imagemin | grunt-contrib-imagemin |
| html| gulp-minify-html | grunt-contrib-htmlmin |

Look over the sample project for this book at https://github.com/gavD/5ss-build-tools. There, you will find example build files for Grunt and Gulp that follow this approach.

# Automating your workflow

At this point, the areas in green are automated:

![alt text](img/automating-your-workflow-1.png?raw=true "Schema 1")

Design and editing source code in src isn’t automatable - if it were, robots would be doing our jobs. Therefore, we will omit these workflow stages from the future diagrams. The rest of these tasks, however, we can use build tools for.

## Task watch - automating your automation

Right now, you are probably running the tool on the command line every time you make a change. This is pretty laborious, and also error prone - chances are you will make a change, forget to run the tool, reload the browser, and be confused as to why your changes aren’t showing up… I know I’ve certainly done that!

Thankfully, we can automate this. We will create a new task, watch, which will monitor the src directory, and whenever anything changes in there, it automatically runs the default task, which has the build task as a dependency:

![alt text](img/automating-your-workflow-2.png?raw=true "Schema 1")

Now, the build tool runs automatically when anything changes in src, so that any time you update your source code, the website is regenerated automatically. This automates the build:

![alt text](img/automating-your-workflow-3.png?raw=true "Schema 1")

## Task serve - run a local server

Instead of constantly pushing code up to a web server, build tools allow you to run a server locally, which serves the build directory, so you’re seeing the compressed, minified, concatenated versions of files, just like your users will. This means that we can remove the "Push to server" stage of the workflow entirely:

![alt text](img/automating-your-workflow-4.png?raw=true "Schema 1")

> Don’t worry if you were hoping to cover deployment in your build workflow; we will talk about deployment later.

## Task refresh-browser: refresh your browser

One task that we spend a lot of time doing is making changes, then going over to a browser and reloading the page. A LiveReload will ensure that when a build occurs in response to a change to src, not only is the build task called, but also the browser is automatically reloaded:

![alt text](img/automating-your-workflow-5.png?raw=true "Schema 1")

We’ve now automated a huge amount of the workflow:

![alt text](img/automating-your-workflow-6.png?raw=true "Schema 1")

## What do we use to write these tasks?

| Task	| Gulp plugin | Grunt plugin 
| ------------- |:-------------|:-------------| 
| watch| gulp-watch | grunt-contrib-watch |
| serve| gulp-connect | grunt-contrib-connect |
| refresh-browser| gulp-livereload | grunt-contrib-watch (built in) |


		
		
		






