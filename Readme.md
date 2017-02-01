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
| ------------- |:-------------:| 
| Source	/home/user/myproject/src | C:\Users\user\myproject\ |
| Build	/home/user/myproject/build | C:\Users\user\myproject\build |
 


