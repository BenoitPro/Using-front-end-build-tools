[comment]: <> (TODO : Schema title a definir ds les images)

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

Voici comment va ressembler le workflow des fichiers sources :

![alt text](img/workflow-asset-pipeline-build-level.png?raw=true "Schema 1")

Partant du principe que vous avez deux fichiers javascript, src/js/a.js et src/js/b.js, et deux fichiers CSS, src/css/first.css et src/css/second.css, la transformation sortira les fichiers build/js/all.min.js et build/css/all.min.css:

![alt text](img/workflow-asset-pipeline-build-level-detail.png?raw=true "Schema 1")

Parce qu'on a concaténé de multiple fihciers sources dans un seul fichier, nous devons remplacer les références qui s'y référait dans l'html. Dans le fichier HTML src/index.html, nous avions 4 références :

```html
<head>
  <script src="/js/a.js"></script>
  <script src="/js/b.js"></script>
  <link rel="stylesheet" media="all" href="/css/first.css">
  <link rel="stylesheet" media="all" href="/css/second.css">
</head>
```

Toujours dans build/index.html, nous devons avoir désormais deux références:


```html
<head>
  <script src="/js/all.min.js"></script>
  <link rel="stylesheet" media="all" href="/css/all.min.css">
</head>
```

A cette étape, la version minifié et concaténé des fichiers sont packagé proprement dans le dossier *build*, qui est prêt à être publié sur le serveur. Mais il reste quelques problèmes :

* Est-ce que ce processus n'est pas inutilement complexe ?
* Comment pouvons-nous savoir s'il y a eu des problèmes dans le code ?
* Comment gérer ce processus ?

Introduisons maintenant le monde merveilleux des outils de construction !

# Workflows automatisé avec les outils de construction

Les logiciels de construction *build tool* automatisent la transformation du code source dans un délivrable. A chaque fois qu'un changement est réalisé dans le code source, l'outil de construction doit se lancer - habituellement depuis le terminal en ligne de commande. L'outil de construction définit comment nous implémentons le workflow suivant :

![alt text](img/automated-workflows-asset-pipeline-with-tool.png?raw=true "Schema 1")

## Outils de construction disponibles

Il y a plusieurs outils de construction disponibles. Ce livre ce focalise sur le web front et par conséquent sur ceux dédiés à l'HTML, les CSS et le Javascript. Nous nous focaliserons sur Grunt et Gulp, car ce sont les deux les plus populaire. Il y en a beaucoup d'autres, comme Brocoli, Middleman, et Brunch, mais les principes de ce livre s'appliquent à tous les outils de construction.

> Si vos besoins sont simple, le gestionnaire de packages NPM peut être utilisé comme outil de construction  - NPM avec Broserify peut être une combinaison puissante . Voir http://blog.keithcirkel.co.uk/how-to-use-npm-as-a-build-tool/ pour plus de précisions.

## Le fichier de construction

Le fichier de construction *build file* dit à l'outil de construction ce qu'il doit faire. Il définit une liste de tâches que l'outil de construction doit réaliser. Dans Grunt, ce fihcier est appelé Gruntfile.js. Dans Gulp, il est appelé Gulpfile.js. Nous suivrons la règle consistant à mettre ce fichier à la racine du projet, au dessus des dossiers *src* et *build*.

| Item	| OSX/Linux | Windows
| ------------- |:-------------|:-------------|
| Source dir | /home/user/myproject/src | C:\Users\user\myproject\src |
| Build dir | /home/user/myproject/build | C:\Users\user\myproject\build |
| Build file (Gulp)	| /home/user/myproject/Gulpfile.js | C:\Users\user\myproject\Gulpfile.js |
| Build file (Grunt) | /home/user/myproject/Gruntfile.js | C:\Users\user\myproject\Gruntfile.js |

## Tâches
L'outil de construction lance les tâches. Une tâche est simplement une action ou une liste d'action qui peuvent être réalisées. Par exemple, une tâche peut être "copy les fichiers du dossier *src* dans le dossier *build*".

Dans la plupart des outils de construction, les tâches sont composables, ce qui signifie qu'elles peuvent dépendre ou être déléguer à d'autres tâches. Par exemple la tâche copy-assets qui copie les fichiers sources peut être dépendante de la tâches clean qui en amont supprimera les fichiers déjà présent. L'avantage de la composabilité est que le code dans la tâche de suppression des fichiers *clean* n'a pas à être répétée à chaque fois qu'on à besoin de l'appeler.

Les tâches sont stockés dans le fichier de construction, voici un exemple de fichier de construction contenant certaines tâches :

![alt text](img/automated-workflows-build-file.png?raw=true "Schema 1")

### Anatomie d'une tâche

Une tâche à généralement ces attributs :

#### Nom
Le nom de la tâche. La plupart des tâches peuvent être lancée séparemment depuis l'outil de ligne de commande. Par exemple, grunt ou gulp clean lance la tâche de suppression des fichiers.

#### Description
Certains outils de construction permettent l'ajout d'une description de la tâche. Permettant de documenter la tâche auprès de votre équipe.

#### Dépendances

La liste des tâches qui doivent être lancées avant la tâche en question. Par exemple la tâche de construction *build* peut avoir la tâche de suppression *clean* des fichiers du dossier build en dépendance, signifiant que l'outil de build devra d'abord supprimer les fichiers avant de les régénérer.

#### Fonctionnalité

Contient le code spécifique à la tâche. Par exemple, la minification ou la concatenation.

# Création de votre premier fichier de construction

Nous allons créer un fichier de construction qui automatise le workflow que nous avons défini. Je ne vais pas écrire le code ici, puisqu'il diffère d'un outil à l'autre, mais je vais le représenter d'une manière abstraite.

Ces tâches seront :

* Supprimer le dossier build
* Compresser et minifier les fichiers sources
* Copier les résultats transformés dans le dossier *build*

Voici l'ordre d'exécution de ces tâches :

![alt text](img/your-first-build-file-task-order.png?raw=true "Schema 1")

> Cerains outils de construction peuvent executer des tâches en parallèles; d'autres non et les executent les unes à la suite des autres. Dans le diagramme ci-dessus, nous partons du principe qu'on peut executer les tâches scripts, styles, images et html en parallèle.

## Explication

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
