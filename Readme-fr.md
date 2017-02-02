Author : Gavin Davies
> Ce livre a d'abord été publié en 2015 par Five Simple Steps. Five Simple Steps ont fermés leur boutique en ligne and ont gracieusement retourné les droits aux auteurs. Nous offrons donc ce livre gratuitement.

> Regardez l'article original sur [le blog de Radify.io "Using front-end build tools"](http://radify.io/blog/using-build-tools/)

> Il est publié sur Github dans le but de permettre sa traduction et son amélioration. Libre à vous de soumettre vos Pull Request ou de discuter des erreurs présentes.

# Introduction

Ce livre s'adresse aux intégrateurs qui font également du développement front end. De nos jours, il semble que nous portons tous plusieurs casquettes dans notre travail !

Depuis les années 2000, le développement web est devenu plus sophistiqué. Cela a mené à plus de complexité, et nous sommes plusieurs à trouver qu'on doit désormais travaillé avec beaucoup de concepts. Cela peut être un peu intimidant (J'ai souvent souhaité moi même que le rythme du progrès ralentisse !).

Pour prendre l'avantage de cette sophistication tout en réduisant sa complexité, les développeurs front-end utilisent des outils. Ces derniers nous permettent de travailler à un plus haut niveau et d'automatiser les tâches répétitives, ainsi qu'économiser du temps et produire des résultats plus sophistiqué. L'automatisation rationnalise nos processus, nous permettant d'être focalisé sur l'aspect créatif de notre travail plutôt que de passer des heures empêtré dans les tâches pénible.

Dans ce petit livre, nous verrons certains outils de construction (*build tools*), ce qu'ils peuvent faire pour vous, et comment commencer à les utiliser. J'utiliserai des outils spécifique, mais ce livre expliquera les concepts généraux et non leur particularité. Après tout, chaque outils de construction est bien documenté en ligne et au format papier. A la place, ce livre à pour but premier de définir ce que sont les outils de construction et comment assembler des flux de travail *workflows* efficace avec eux.

Le but de ce livre est qu'une fois l'avoir lu, vous puissiez amériorer vos workflows en incorporant les outils de construction. J'ai pour but de vous donner la confiance et les connaissances nécessaire pour plonger dans le puissant et flexible monde de ces outils, de devenir plus productif et au final de prendre plaisir dans votre travail.

# Parlons des workflows

Un Workflow est une liste de tâches effectuer les unes après les autres. Chacun a un workflow différent, et vous pouvez avoir plusieurs workflows pour différents aspect de votre rôle. Nous allons étudier le workflow du développement web (front) et comment l'optimiser.

## Workflow basique en developpement web

Dans le développement de sites web (webfront) et d'applications (webapp), vous avez probablement les phases suivantes :

![alt text](img/workflow-basic.png?raw=true "Schema 1")

> Ne vous inquiétez pas si votre workflow est différent. J'ai juste sélectionné les tâches communes. Les principes de ce livre s'appliqueront peu importe votre workflow exacte.

Il y a un certains problèmes avec ce workflow simpliste.

* La taille des fichiers - Il n'y a pas de minification des fichiers sources (*assets*), l'application s'exécutera sera plus lentement qu'elle devrait.
* Consistence - Pousser sur un serveur peut être sujet à erreurs, les fichiers sources peuvent avoir été mis en cache, le transfert peut planter etc.
* Rapidité du workflow - Pousser et rafraichir manuellement les fichiers prend du temps et vous fait sortir du workflow.

Rajoutons quelques tâches supplémentaires à ce workflow pour corriger ces problèmes.

# Créons un processus de transformations des fichiers sources (*asset pipeline*)

Pour résourdre les problèmes identifié dans le workflow basique, nous pouvons incorporer la compression, la minification, et la concatenation. La minification diminu la taille du code en reduisant notamment les noms des variables et des fonctions. La compression diminue la taille des fihciers au niveau binaire. La concatisation combine de multiple fichiers en un seul, reduisant le nombre de requête http nécessaire ce qui améliore la vitesse de téléchargement. Rajoutons ces tâches dans le processus :

![alt text](img/workflow-asset-pipeline.png?raw=true "Schema 1")

Cette approche, communément appelée "asset pipeline" (je prend toutes idées de traduction simple en français autre que "processus de transformations des fichiers sources" 😜), prend les fichiers sources depuis un chemin d'entré, les transforment et les délivrent en sorti dans un dossier de detination. Nous verrons comment structurer et automatiser cela.

Une convention commune consiste à séparer les dossiers racines de votre projet. Le premier est nommé *src*, qui contient le code source - les fichiers que vous éditez. Le second est nommé *build*, qui contient les fichiers généré et délivré à l'utilisateur final. Vous n'éditez jamais rien directement dans le dossier *build*. La localisation exacte n'est pas importante mais ce livre part du principe que le projet est sauvegardé dans /home/user/myproject sur OSX/Linux ou C:\Users\user\myproject sur Windows.

> Dans certains projets *build* peut-être appelé *dist*, le raccourci de "distribution".

| Dossier	OSX/Linux	| Windows
| ------------- |:-------------|
| Sources	/home/user/myproject/src | C:\Users\user\myproject\src |
| Build	/home/user/myproject/build | C:\Users\user\myproject\build |

Lorsque *build* et *src* sont séparés, déplacer tous les fichiers dans le dossier *src*. Ils contiendra les fichiers que vous allez éditer. Tout ce qui sera dans le dossier build seront généré par votre outils de construction - Nous n'éditons jamais les fichiers du dossier build directement. Pas touche !

> C'est mieux de tester ce qui se rapproche le plus possible de ce que l'utilisateur recevra. Par conséquent, c'est une bonne pratique de tester le projet depuis le dossier *build*, et non pas dans *src*, cela permet d'intercepter tout problèmes que la transformation peut avoir introduit.

Traiter des logiciels de gestion de version ne fait pas parti de ce livre, mais je vais faire une suggestion : Lorsqu'on utilise un outil tel que Git, Mercurial, ou Subversion, ignorez le dossier *build* des fichiers versionné. Vous ne voulez pas commiter le moindre fichier du dossier *build* - Il est a considérer comme volatile. Commitez uniquement les fichiers sources. Cela permet de garder les logs de commit  propres et épurées.


> Pour en apprendre plus sur Git, je vous conseille [Version Control with Git by Ryan Taylor](http://www.fivesimplesteps.com/products/version-control-with-git)

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

# Finished build file

We end up with a set of tasks that we can run. Here are the "top level" tasks, i.e., the ones that we will commonly call from the command line:

| Task	| Dependencies | Description
| ------------- |:-------------|:-------------|
| default| build | Default task so that when your build tool is called with no parameters, it simply delegates to the build task. |
| build| clean, scripts, styles, images, html | Minifies, concatenates, and compresses assets, then moves the compressed versions into the build directory. |
| watch| live-reload, serve | Launches the local server, which serves the build directory. Watches the src directory. Any change to any file inside src will trigger the default task. This is the task you will run when you are working on your project, and it will whirr away in the background keeping everything up to date for you. |

 # What else can build tools do for us?

We’ve automated the workflow that we defined earlier, but build tools can do lots more. Here are a few examples.

## Linting

Linting, also known as static analysis, is the automated inspection of your source code (files in src). Think of it as having a more experienced colleague checking your work and making sure that you haven’t made any common mistakes.

JavaScript can be automatically checked using a tool called jshint. This can check for common performance, security, and stylistic problems. Following the recommendations of jshint makes your code standards-compliant, and protects you from common clangers.

Similarly, CSS can be linted using tools like csslint. This can help you to automatically detect whether you’ve used invalid or deprecated rules. It can also help you to make more efficient CSS that the browser can use without friction.

HTML can also be linted!

I generally recommend obeying every recommendation that linters make - as legendary programmer John Carmack once put it, "If you have to explain it to the computer, you’ll probably have to explain it to your colleagues."

All linters can be "tuned" to your specific needs - for example, if you are breaking one rule for a good reason, then you can tell your linter to ignore it.

Here are some example tools you could use:

| Functionality	| Gulp plugin | Grunt plugin
| ------------- |:-------------|:-------------|
| Lint css| gulp-csslint | grunt-contrib-csslint |
| Lint javascript| gulp-jshint | grunt-contrib-jshint |
| Verify html| gulp-htmlhint | grunt-htmlhint |

## CSS preprocessing

CSS is great but it’s fiddly. A preprocessor makes CSS far easier to work with. I use LESS day-to-day, but there are many others, including Sass and Stylus. All preprocessors add features like variables, mixins, and so forth, which greatly reduce the amount of code you need to write and make it far easier to maintain.

Using a preprocessor, the styles task can transform styles into raw CSS. This means that you would never need to edit raw CSS; you could work entirely in the far more pleasant world of LESS (or whichever you prefer).

| Functionality	| Gulp plugin | Grunt plugin
| ------------- |:-------------|:-------------|
| LESS to CSS| gulp-less | grunt-contrib-less |

## Script preprocessing (transpiling)

Instead of plain JavaScript, you might be using something like CoffeeScript. You would need a transpiling stage, which converts from CoffeeScript into JavaScript.

You might also be using a more cutting-edge version of JavaScript than is available in current web browsers. At the time of writing, ES6 is a forthcoming version of JavaScript that is not available for general usage in many web browsers. Therefore, we can use tools to transpile it into ES5.

Once you have these tools in your workflow, you would have your CoffeeScript or ES6 code in src, and standard ES5 code in build. It makes debugging a little more difficult, but it can be worth it if you want to use cutting edge features.

| Functionality	| Gulp plugin | Grunt plugin
| ------------- |:-------------|:-------------|
| CoffeeScript transpiler| gulp-coffee | grunt-contrib-coffee |
| ES6 transpiler| gulp-babel | grunt-babel |

## Push to server

Build tools can even be used for deployment. There are all sorts of options here and it is out of our scope to cover them in detail, so here are a few examples:

| Functionality	| Gulp plugin | Grunt plugin
| ------------- |:-------------|:-------------|
| FTP | gulp-ftp | grunt-ftp-deploy |
| Github pages| gulp-git-pages | grunt-gh-pages |
| rsync | gulp-rsync | grunt-rsync |

# Best practice

Generally, you can use a build tool for any automation task. As with any software, build tools are open to misuse, however, so here are some guidelines to help you to make sure you are using the build tool as it was intended.

## Never edit anything in the build directory

You should never manually edit anything in the build directory. Similarly, your build tool should never modify anything in the src directory.

If there is anything that you have to do in the build directory, you can almost certainly find a way to get the tool to do it for you.

## Never let your build tool modify anything in the src directory

Conversely, if your build tool modifies your src directory, you are going to have a bad time. Such behaviour breaks the "pipeline" approach and can create circular actions that never fully resolve. Therefore, you need to be confident that your build tool will stay out of the src directory entirely.

Think of it like this: src is yours, build belongs to your build tool.

## Short, sharp, composable tasks

Whilst you CAN create tasks that are huge and have a large amount of code, it is usually better to separate these into smaller tasks and compose them using task dependencies.

## Your normal workflow should be encapsulated by a single task

You shouldn’t need to be constantly running a bunch of separate tasks. Instead, one task should rely upon or kick off the subtasks that it needs. Generally, you should use some kind of watch and run it in the background. As you do your normal work, it will detect changes, and then run everything it needs to.

If you’re finding yourself constantly having to stop your watch task and run things, then consider putting them into your main (default) task.

## Have tasks that aren’t part of your main workflow as separate tasks

Conversely, for tasks that aren’t part of your regular workflow, don’t have the main workflow call them.

For example, you don’t want to deploy every tiny little tinker you make in your local workspace. Therefore, you might have a task like deploy that you only call once you’ve done all of your testing and you’re confident that it’s time to go live.

## Don’t store credentials in plain text

Speaking of deployments, it might be tempting to store things like passwords or authentication tokens in your build file. This is generally a bad move from a security perspective - if someone accesses your build file, they could compromise your live systems.

A full treatment of security issues is outside of the scope of this book, but suffice to say, don’t store credentials in plain text and certainly don’t keep them directly in your build file.

## Debugging with source maps

If you have "compiled" (minified, concatenated) JavaScript code in build, it will look different to your code in src. It will have had whitespace removed, variable names shortened - all sorts of changes that make it hard to debug. You can use a source map to help you here. A source map holds information about your original source files. Browsers like Chrome and Firefox support source maps, and enable you to debug as if you were using your original code. This is really handy as it means you are testing the same code the user would receive, but debugging it in its original form.

Similarly, with a CSS preprocessor like LESS or SaSS, the CSS in build is different to the CSS in src. LESS and SaSS support source maps as well, so you can use the same approach.

## Being idiomatic

Try to ensure that your project is idiomatic. By that, I mean that you should follow the conventions that other people use for similar projects. This has the following benefits:

* Easier to apply code snippets you find online
* Easier to explain to people than some bespoke system
* The wheel remains un-reinvented!

There are a couple of ways you could go about this. Firstly, you could clone an existing repository and modify it to suit your needs. Feel free to clone the sample source code for this book from https://github.com/gavD/5ss-build-tools.

The second option is to use a scaffolding tool. These are tools that create a "skeleton" project for your app that has the tools you will need installed and ready to use. So, you don’t have to create the build and src directories, and you don’t have to create your build file - your scaffolding tool does it all for you. Handy!

If you’re starting with a brand-new app, Yeoman is a great scaffolding tool that uses generators to get you up and running with a project structure. It supports a huge range of project types. For example, are you building an AngularJS app? Then use the yeoman angular generator! Working on a PhoneGap project? Then use the yeoman phonegap generator! No matter what you are building, there is likely to be a Yeoman generator that can help you to get started.

## See also: Browserify

Check out Browserify. It’s not a build tool as such, but it offers a nice minimalist approach to the JavaScript asset pipeline.

# Go forth and build!

In this book, we started out by looking at common web development workflows. We then went on to look at how we can improve upon that workflow. We then took a detailed look at what a build tool is, what a build file is, and what tasks are. We then went through creating some tasks to automate the workflow.

Thanks for reading, I hope that this book has been a useful introduction to the wonderful world of build tools!

# Further reading

* James Cryer’s Pro Grunt.js
* Travis Maynard’s Getting Started with Gulp

# Thanks

Many thanks to the following proof readers for valuable insights and corrections:
* Nate Abele
* Andrew Canham
* Kathryn Davies
* Aryella Lacerda
* John Mercer

# Download as PDF

[Download as PDF](http://radify.io/downloads/Using-Build-Tools.pdf)
