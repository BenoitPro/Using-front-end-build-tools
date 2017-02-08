
Author : [Gavin Davies](https://github.com/gavD)
> Ce livre a d'abord été publié en 2015 par Five Simple Steps. Five Simple Steps ont fermé leur boutique en ligne et ont gracieusement retourné les droits aux auteurs. Nous offrons donc ce livre gratuitement.

> Regardez l'article original sur [le blog de Radify.io "Using front-end build tools"](http://radify.io/blog/using-build-tools/)

> Il est publié sur Github dans le but de permettre sa traduction et son amélioration. Libre à vous de soumettre vos Pull Requests ou de discuter des erreurs présentes.

# Introduction

Ce livre s'adresse aux intégrateurs qui font également du développement front end. De nos jours, il semble que nous portons tous plusieurs casquettes dans notre travail !

Depuis les années 2000, le développement web est devenu plus sophistiqué. Cela a mené à plus de complexité, et nous sommes plusieurs à trouver qu'on doit désormais travaillé avec beaucoup de concepts. Cela peut être un peu intimidant (J'ai souvent souhaité moi même que le rythme du progrès ralentisse !).

Pour avoir l'avantage de cette sophistication tout en réduisant sa complexité, les développeurs front-end utilisent des outils. Ces derniers nous permettent de travailler à un plus haut niveau et d'automatiser les tâches répétitives, ainsi qu'économiser du temps et produire des résultats plus sophistiqué. L'automatisation rationnalise nos processus, nous permettant d'être focalisé sur l'aspect créatif de notre travail plutôt que de passer des heures empêtré dans les tâches pénible.

Dans ce petit livre, nous verrons certains outils de construction (*build tools*), ce qu'ils peuvent faire pour vous, et comment commencer à les utiliser. J'utiliserai des outils spécifique, mais ce livre expliquera les concepts généraux et non leurs particularités. Après tout, chaque outil de construction est bien documenté en ligne et au format papier. A la place, ce livre à pour but premier de définir ce que sont les outils de construction et comment assembler des flux de travail *workflows* efficace avec eux.

Le but de ce livre est qu'une fois lu, vous puissiez amériorer vos *workflows* en incorporant les outils de construction. J'ai pour but de vous donner la confiance et les connaissances nécessaire pour plonger dans le puissant et flexible monde de ces outils, de devenir plus productif et au final de prendre plaisir dans votre travail.

# Parlons des workflows

Un Workflow est une liste de tâches effectuées les unes après les autres. Chacun a un workflow différent, et vous pouvez avoir plusieurs workflows pour différents aspect de votre rôle. Nous allons étudier le workflow du développement web (front) et comment l'optimiser.

## Workflow basique en developpement web

Dans le développement de sites web (webfront) et d'applications (webapp), vous avez probablement les phases suivantes :

![alt text](img/workflow-basic.png?raw=true "Schema 1")

> Ne vous inquiétez pas si votre workflow est différent. J'ai juste sélectionné les tâches communes. Les principes de ce livre s'appliqueront peu importe votre workflow exacte.

Il y a un quelques problèmes avec ce workflow simpliste.

* La taille des fichiers - Il n'y a pas de minification des fichiers sources (*assets*), l'application s'exécutera plus lentement qu'elle devrait.
* Consistence - Pousser sur un serveur peut être sujet à erreurs, les fichiers sources peuvent avoir été mis en cache, le transfert peut planter etc.
* Rapidité du workflow - Pousser et rafraichir manuellement les fichiers prend du temps.

Rajoutons quelques tâches supplémentaires à ce workflow pour corriger ces problèmes.

# Créons un processus de transformations des fichiers sources (*asset pipeline*)

Pour résourdre les problèmes identifiés dans le workflow basique, nous pouvons incorporer la compression, la minification, et la concatenation. La minification diminue la taille du code en reduisant notamment les noms des variables et des fonctions. La compression diminue la taille des fichiers au niveau binaire. La concatisation combine de multiple fichiers en un seul, réduisant le nombre de requêtes http nécessaire ce qui améliore la vitesse de téléchargement. Rajoutons ces tâches dans le processus :

![alt text](img/workflow-asset-pipeline.png?raw=true "Schema 1")

Cette approche, communément appelée "asset pipeline" (je prend toutes idées de traduction simple en français autre que "processus de transformations des fichiers sources" 😜), prend les fichiers sources depuis un chemin d'entré, les transforment et les délivrent en sortie dans un dossier de destination. Nous verrons comment structurer et automatiser cela.

Une convention commune consiste à séparer les dossiers racines de votre projet. Le premier est nommé *src*, qui contient le code source - les fichiers que vous éditez. Le second est nommé *build*, qui contient les fichiers généré et délivré à l'utilisateur final. Vous n'éditez jamais rien directement dans le dossier *build*. La localisation exacte n'est pas importante mais ce livre part du principe que le projet est sauvegardé dans /home/user/myproject sur OSX/Linux ou C:\Users\user\myproject sur Windows.

> Dans certains projets *build* peut-être appelé *dist*, le raccourci de "distribution".

| Dossier	OSX/Linux	| Windows
| ------------- |:-------------|
| Sources	/home/user/myproject/src | C:\Users\user\myproject\src |
| Build	/home/user/myproject/build | C:\Users\user\myproject\build |

Lorsque *build* et *src* sont séparés, déplacer tous les fichiers dans le dossier *src*. Ils contiendra les fichiers que vous allez éditer. Tout ce qui sera dans le dossier build sera généré par votre outil de construction - Nous n'éditons jamais les fichiers du dossier build directement. Pas touche !

> C'est mieux de tester ce qui se rapproche le plus possible de ce que l'utilisateur recevra. Par conséquent, c'est une bonne pratique de tester le projet depuis le dossier *build*, et non pas dans *src*, cela permet d'intercepter tout problème que la transformation peut avoir introduit.

Traiter des logiciels de gestion de version ne fait pas parti de ce livre, mais je vais faire une suggestion : Lorsqu'on utilise un outil tel que Git, Mercurial, ou Subversion, ignorez le dossier *build* des fichiers versionné. Vous ne voulez pas commiter le moindre fichier du dossier *build* - Il est a considérer comme volatile. Commitez uniquement les fichiers sources. Cela permet de garder les logs de commit propres et épurés.


> Pour en apprendre plus sur Git, je vous conseille [Version Control with Git by Ryan Taylor](http://www.fivesimplesteps.com/products/version-control-with-git)

Voici a quoi va ressembler le workflow des fichiers sources :

![alt text](img/workflow-asset-pipeline-build-level.png?raw=true "Schema 1")

Partant du principe que vous avez deux fichiers javascript, src/js/a.js et src/js/b.js, et deux fichiers CSS, src/css/first.css et src/css/second.css, la transformation sortira les fichiers build/js/all.min.js et build/css/all.min.css:

![alt text](img/workflow-asset-pipeline-build-level-detail.png?raw=true "Schema 1")

Parce qu'on a concaténé de multiple fichiers sources dans un seul fichier, nous devons remplacer les références qui s'y référait dans l'html. Dans le fichier HTML src/index.html, nous avions 4 références :

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

Les logiciels de construction *build tool* automatisent la transformation du code source dans un délivrable. A chaque fois qu'un changement est réalisé dans le code source, l'outil de construction doit se lancer - habituellement depuis le terminal en ligne de commande. L'outil de construction définit comment nous implémentons le workflow :

![alt text](img/automated-workflows-asset-pipeline-with-tool.png?raw=true "Schema 1")

## Outils de construction disponibles

Il y a plusieurs outils de construction disponibles. Ce livre se focalise sur le web front et par conséquent sur ceux dédiés à l'HTML, les CSS et le Javascript. Nous nous focaliserons sur Grunt et Gulp, car ce sont les deux les plus populaire. Il y en a beaucoup d'autres, comme Brocoli, Middleman, et Brunch, mais les principes de ce livre s'appliquent à tous les outils de construction.

> Si vos besoins sont simple, le gestionnaire de packages NPM peut être utilisé comme outil de construction  - NPM avec Broserify peut être une combinaison puissante. Voir http://blog.keithcirkel.co.uk/how-to-use-npm-as-a-build-tool/ pour plus de précisions.

## Le fichier de construction

Le fichier de construction *build file* dit à l'outil de construction ce qu'il doit faire. Il définit une liste de tâches que l'outil de construction doit réaliser. Dans Grunt, ce fichier est appelé Gruntfile.js. Dans Gulp, il est appelé Gulpfile.js. Nous suivrons la règle consistant à mettre ce fichier à la racine du projet, au dessus des dossiers *src* et *build*.

| Item	| OSX/Linux | Windows
| ------------- |:-------------|:-------------|
| Source dir | /home/user/myproject/src | C:\Users\user\myproject\src |
| Build dir | /home/user/myproject/build | C:\Users\user\myproject\build |
| Build file (Gulp)	| /home/user/myproject/Gulpfile.js | C:\Users\user\myproject\Gulpfile.js |
| Build file (Grunt) | /home/user/myproject/Gruntfile.js | C:\Users\user\myproject\Gruntfile.js |

## Tâches
L'outil de construction lance les tâches. Une tâche est simplement une action ou une liste d'action qui peuvent être réalisées. Par exemple, une tâche peut être "copy les fichiers du dossier *src* dans le dossier *build*".

Dans la plupart des outils de construction, les tâches sont composables, ce qui signifie qu'elles peuvent dépendre ou être déléguer à d'autres tâches. Par exemple la tâche copy-assets qui copie les fichiers sources peut être dépendante de la tâche clean qui en amont supprimera les fichiers déjà présent. L'avantage de la composabilité est que le code dans la tâche de suppression des fichiers *clean* n'a pas à être répétée à chaque fois qu'on à besoin de l'appeler.

Les tâches sont stockées dans le fichier de construction, voici un exemple de fichier de construction contenant certaines tâches :

![alt text](img/automated-workflows-build-file.png?raw=true "Schema 1")

### Anatomie d'une tâche

Une tâche à généralement ces attributs :

#### Nom
Le nom de la tâche. La plupart des tâches peuvent être lancées séparemment depuis l'outil de ligne de commande. Par exemple, grunt ou gulp clean lance la tâche de suppression des fichiers.

#### Description
Certains outils de construction permettent l'ajout d'une description de la tâche. Permettant de documenter la tâche auprès de votre équipe.

#### Dépendances

La liste des tâches qui doivent être lancées avant la tâche en question. Par exemple la tâche de construction *build* peut avoir la tâche de suppression *clean* des fichiers du dossier build en dépendance, signifiant que l'outil de build devra d'abord supprimer les fichiers avant de les régénérer.

#### Fonctionnalité

Contient le code spécifique à la tâche. Par exemple, la minification ou la concatenation.

# Création de votre premier fichier de construction

Nous allons créer un fichier de construction qui automatise le workflow que nous avons défini. Je ne vais pas écrire le code ici, puisqu'il diffère d'un outil à l'autre, mais je vais le représenter d'une manière abstraite.

Ces tâches seront :

* Supprimer le dossier build.
* Compresser et minifier les fichiers sources.
* Copier les résultats transformés dans le dossier *build*.

Voici l'ordre d'exécution de ces tâches :

![alt text](img/your-first-build-file-task-order.png?raw=true "Schema 1")

> Certains outils de construction peuvent exécuter des tâches en parallèles; d'autres non et les executent les unes à la suite des autres. Dans le diagramme ci-dessus, nous partons du principe qu'on peut exécuter les tâches scripts, styles, images et html en parallèle.

## Explication

### Tâche clean - Supprime le contenu du dossier *build*

Cette tâche simple supprime le contenu du dossier *build*.

### Tâche build - Appele les sous tâches

Elle dépend de la tâche clean par le mécanisme décrit plus tôt, donc lorsque build est appelé, la tâche clean est exécuté d'abord. Après cela, la tâche build délègue aux tâches scripts, styles, images et html.

### Tâche scripts - Transforme les fichiers javascript

Prend src/js/a.js et src/js/b.js, les minifie, puis les concatène dans build/js/all.min.js.

### Tâche style - Transforme les fichiers CSS

Prend src/css/first.css et src/css/second.css, les minifie, puis les concatène dans build/css/all.min.css. Pour les développeurs écrivant en SASS ou LESS, les styles seront également compilés en CSS avant la minification et la concaténation.

### Tâche images  - Compresse les fichiers images

Prend les fichiers images de src/img, les compresse, puis les met dans le dossier  build/img.

### Tâche html - Transforme les fichiers HTML

Prend src/index.html et le minifie. Puis modifie l'HTML pour changer le chemin vers le dossier *build* et non pas *src*. Enfin elle met le résultat dans le dossier build.

## Plugins

Pour chaque tâche, il y a un plugin installable dans votre outil de construction. C'est génial parce que cela signifie que vous aurez peu de code à écrire vous même.

L'équipe Gulp maintient une liste de plugins vérifiés et leur communauté s'assure qu'il y a une seul plugin par tâche. Ce qui contraste avec la communauté Grunt qui permet de multiple alternatives pour chaque plugin. Les plugins nommés grunt-contrib-* sont le meilleur point de départ parce qu'ils sont maintenuent officiellement.

> L'installation et l'usage spécifique de ces plugins dépasse le cadre de ce livre.


| Tâche	| Gulp plugin | Grunt plugin
| ------------- |:-------------|:-------------|
| clean| del | grunt-contrib-clean |
| scripts| gulp-uglify | grunt-contrib-uglify |
| styles| gulp-minify-css | grunt-contrib-cssmin |
| images| gulp-imagemin | grunt-contrib-imagemin |
| html| gulp-minify-html | grunt-contrib-htmlmin |

Regardez l'exemple de projet de ce livre à l'adresse https://github.com/gavD/5ss-build-tools. Vous y trouverez des exemples de fichiers de construction pour Grunt et Gulp qui suivent cette approche.

# Automatiser votre workflow

A cette étape, la zone verte a été automatisée :

![alt text](img/automating-your-workflow-1.png?raw=true "Schema 1")

Les étapes de design et d'édition du code source ne sont pas automatisable - si c'était le cas, les robots feraient notre travail. Par conséquent nous les ommettrons dans nos futurs diagrammes. Cependant, pour le reste des tâches, nous pouvons utiliser les outils de construction.

## Tâche watch - Automatiser votre automatisation

Désormais, vous lancez probablement l'outil de construction à chaque fois que vous réalisez un changement. C'est plutôt pénible, et enclin aux erreurs - Il est probable que vous allez oublier de lancer l'outil ou de relancer le navigateur et être embrouillé à vous demander pourquoi les changements n'apparaissent pas... Je sais, ça met déjà arrivé !

Heureusement, nous pouvons automatiser cela. Nous allons créer une nouvelle tâche, watch, dans laquelle nous surveillerons le dossier *src*, et s'il y a un changement à l'intérieur, elle lancera automatiquement la tâche *default*, qui a pour dépendance la tâche *build* :

![alt text](img/automating-your-workflow-2.png?raw=true "Schema 1")

Maintenant, l'outil de construction se lance automatiquement lorsqu'un changement est effectué dans *src*, donc à chaque fois que vous mettez à jour votre code source, le site web est regénéré automatiquement. Cela automatise la construction :

![alt text](img/automating-your-workflow-3.png?raw=true "Schema 1")

## Tâche serve - Lance un serveur local

Au lieu de pousser le code vers un serveur, les outils de construction vous permettent de lancer un serveur localement, lequel servira le dossier *build*, donc vous verrez les versions compressés, minifiés et concaténés des fichiers, tout comme vos futurs utilisateurs. Cela signifie que nous pouvons supprimer l'étape de "Push to server" du workflow entièrement :

![alt text](img/automating-your-workflow-4.png?raw=true "Schema 1")

> Ne vous inquiétez pas si vous espériez qu'on convre le déploiement dans notre workflow de construction, nous en parlerons plus tard.

## Tâche refresh-browser: Rafrachit le navigateur

La tâche qui nous prend beaucoup de temps est de rafraichir le navigateur après un changement. Le LiveReload va s'assurer que lorsqu'un *build* arrive en réponse à un changement dans le dossier *src*, le navigateur est également automatiquement rafraichi :

![alt text](img/automating-your-workflow-5.png?raw=true "Schema 1")

Nous avons automatisé une énorme quantité de travail du workflow :

![alt text](img/automating-your-workflow-6.png?raw=true "Schema 1")

## Qu'est qu'on utilise pour écrire ces tâches ?

| Task	| Gulp plugin | Grunt plugin
| ------------- |:-------------|:-------------|
| watch| gulp-watch | grunt-contrib-watch |
| serve| gulp-connect | grunt-contrib-connect |
| refresh-browser| gulp-livereload | grunt-contrib-watch (built in) |

# Terminer le fichier de construction

Nous terminons avec une liste de tâches que nous pouvons lancer. Ici sont les tâches de "haut niveau", c'est à dire, celles qui sont habituellement appelées depuis la ligne de commande :

| Tâche	        |   Dépendances     |   Description |
| ------------- |:-------------     |:------------- |
| default| build | Tâche par défaut que votre outil de construction appelle lorsqu'il ne reçoit pas de paramètre. Dans notre cas elle délègue tout simplement à la tâche build. |
| build| clean, scripts, styles, images, html | Minifie, concatène, compresse les fichiers sources, puis les déplace dans le dossier *build*. |
| watch| live-reload, serve | C'est la tâche que vous lancez lorsque vous travaillez sur un projet, elle s'exécute en tâche de fond surveillant chaque changement, puis reconstruit le projet dans le dossier build, actualise le serveur et rafrachit le navigateur pour vous. |

# Qu'est-ce que les outils de construction peuvent faire d'autres pour nous ?

Nous avons automatisé le workflow que nous avions défini plus tôt, mais les outils de construction peuvent faire bien plus. Voici quelques exemples.

## Linting

Linting, aussi appelé analyse statique, est une inspection automatique de votre source code (les fichiers dans *src*). C'est comme si vous aviez un collègue plus expérimenté qui vérifiait votre travail et s'assurait que vous ne commettez pas d'erreurs communes.

Le code Javascript peut être automatiquement vérifié en utilisant un outil appelé *jshint*. Il peux vérifier les erreurs communes de performances, de sécurité et de style. Suivre les recommandations de Jshint permet de rendre votre code conforme aux standards.

Il en est de même pour le code CSS qui peut être vérifié en utilisant des outils tels que Csslint. Cela peux vous aider à automatiquement détecter si vous utilisez des règles dépréciées ou invalides. Il peut également vous aider à réaliser des CSS plus efficaces et mieux exécuter par votre navigateur.

L'HTML peut également être vérifié !

Je recommande généralement de suivre toutes les recommandations que le linters fait - Comme le légendaire programmeur John Carmack l'a dit "Si vous avez à l'expliquer à l'ordinateur, vous aurez à l'expliquer à vos collègues."

Tous les linters peuvent être modifié aux spécificités de votre code - par exemple, si vous cassez une règle pour une bonne raison, alors vous pourrez dire à votre linter de l'ignorer.

Voici quelques exemples que vous pouvez utiliser :

| Fonctionnalité	| Gulp plugin | Grunt plugin
| ------------- |:-------------|:-------------|
| Lint css| gulp-csslint | grunt-contrib-csslint |
| Lint javascript| gulp-jshint | grunt-contrib-jshint |
| Verify html| gulp-htmlhint | grunt-htmlhint |

## CSS pré-processing

CSS est génial mais il est délicat et verbeux à l'usage. Un pre-processeur permet de faire des CSS bien plus facilement. J'utilise LESS quotidiennement, mais il y en a plusieurs autres comme Sass et Stylus. Tous les pré-processeurs ont pour fonctionnalités l'ajout de variables, de mixins (sorte de fonctions) etc. ce qui diminue grandement la quantité de code que vous avez besoin d'écrire.

Lorsqu'on utilise un pre-processeur, la tâche "styles" peut s'occuper de les compiler en CSS brute. Ce qui signifie que vous n'avez jamais à éditer du CSS brute. Vous pouvez travailler complètement dans le monde bien plus plaisant de Less, Sass ou Stylus.


| Fonctionnalité	| Gulp plugin | Grunt plugin
| ------------- |:-------------|:-------------|
| LESS to CSS| gulp-less | grunt-contrib-less |

## Script pré-processing (transpiler)

Plutôt que d'écrire du Javascript, vous pouvez écrire en utilisant du CoffeeScript. Vous pouvez avoir besoin d'une étape de transpilation (terme utilisé pour compiler d'un langage vers un autre langage de même degré d'abstraction), qui convertit le CoffeScript vers du Javascript.

Vous pouvez également utiliser une version plus récente de Javascript qui est disponible dans les derniers navigateurs web. A l'heure où j'écris ces lignes ES6 n'est pas encore supporté par tous les navigateurs. Par conséquent vous pouvez utiliser des outils qui le transpile en ES5 avec un outil tel que *babel*.

Lorsque vous avez ces outils dans votre workflow, vous pouvez avoir votre code CoffeeScript ou ES6 dans le dossier *src* qui est automatiquement transpilé dans le dossier *build* en ES5. Il rend le débuggage un peu plus compliqué, mais il permet d'utiliser les dernières fonctionnalités.

| Fonctionnalité	| Gulp plugin | Grunt plugin
| ------------- |:-------------|:-------------|
| CoffeeScript transpileur| gulp-coffee | grunt-contrib-coffee |
| ES6 transpileur| gulp-babel | grunt-babel |

## Pousser vers le serveur

Les outils de construction peuvent également être utilisé pour le déploiement. Il y a plusieurs sortes d'options mais cela dépasse notre périmètre de les couvrir en détails, voici cependant quelques exemples :

| Fonctionnalité	| Gulp plugin | Grunt plugin
| ------------- |:-------------|:-------------|
| FTP | gulp-ftp | grunt-ftp-deploy |
| Github pages| gulp-git-pages | grunt-gh-pages |
| rsync | gulp-rsync | grunt-rsync |

# Meilleurs pratique

Généralement, vous pouvez utiliser un outils de construction pour n'importe quelle tâche d'automatisation. Comme tout logiciel, les outils de construction peuvent engendrer de mauvaises utilisations. Cependant voici quelques règles pour vous aider à les utiliser comme ils ont été pensé.

## Ne jamais éditer le moindre fichier du dossier *build*

Vous ne devez jamais éditer le moindre fichier dans le dossier *build*. Réciproquement, votre outil de construction ne doit rien modifier dans le dossier des fichiers sources *src*.

Si quelques chose doit être fait dans le dossier *build*, vous pouvez à coup sûr trouver une solution pour le faire faire par l'outil pour vous.

## Ne jamais laisser votre outil de construction modifier le moindre fichier du dossier src   

Inversement, si votre outil de construction modifie votre dossier *src*, vous aller passé un sale quart d'heure. Ce genre de comportement casse l'approche workflow et peut créer des actions circulaires. Par conséquent vous devez vous assurer que votre outil de construction ne touche jamais le contenu du dossier *src*.

Pensez comme ceci : *src* est à vous, *build* appartient à votre outil de construction.

## Rester concis

Même si vous pouvez créer d'énormes tâches avec beaucoup de code, il est conseillé de les séparer en de plus petite tâche et d'utiliser les dépendances de tâche.

## Votre workflow normal doit être encapsulé dans une seule tâche

Vous ne devez pas constamment lancer un paquet de tâches séparées. A la place, la tâche doit reposer sur un ensemble de sous tâches. Généralement, vous devez utiliser les tâches *watch* en arrière plan. Si vous stoppez constamment votre tâche *watch* pour executer un emsemble de tâches extérieur, vous devriez considérer sérieusement de mettre ces tâches dans votre tâche principale *default*.

## Séparer les tâches qui n'ont rien a voir avec votre workflow

Inversement, pour les tâches qui ne font pas parties de votre workflow ne les intégrez pas dans votre workflow principal.

## Ne stockez pas vos codes d'accès en clair

Lorsqu'on parle de déploiement, il peut être tentant de stocker les mots de passes et les tokens d'identifications dans votre fichier de configuration. C'est généralement une très mauvaise idée d'un point de vue sécuritaire. Si une personne accède à votre fichier de configuration il peut compromètre votre système.

Un traitement complet des questions de sécurité est en dehors du cadre de ce livre, mais il est déjà suffisant de dire qu'il ne faut pas stocker en clair vos codes d'accès dans vos fichiers de configuration.

## Débuguer avec les fichiers "source maps"

Si vous avez "compilé" (minifié et concaténé) le code Javascript dans la construction, ils seront alors différent de votre code dans *src*. Les espaces blanc et retours à la ligne sont supprimés, les noms de variable raccourcies, bref plein de changement qui vont rendre difficile le debugage. Les *sources maps* peuvent alors vous aider. La *source map* contient des informations sur votre fichier source original. Les navigateurs comme Chrome et Firefox supportent les source maps et vous permettent de debuguer comme si vous utilisez le fichier original. C'est vraiment pratique car vous tester avec le même code que l'utilisateur final va avoir, mais vous debuguer comme s'il s'agissait du fichier original.

Il en est de même pour les pre-processeurs CSS, car ces derniers aussi supporte les *sources maps*.

## Etre idiomatique

Essayer d'être idiomatique. C'est à dire que vous devez suivre les conventions suivient par les autres développeurs de projet similaire. Cela aura beaucoup d'avantages :

* Il sera plus facile d'appliquer les extraits de code trouvé en ligne
* Il sera plus facile d'expliquer aux autres votre code
* Vous ne réinventerez pas la roue !

Il y a deux manières de faire cela. La première, consiste à cloner un repository existant et de la modifier pour correspondre à vos besoins. N'hésitez pas à cloner le code source d'exemple de ce livre depuis https://github.com/gavD/5ss-build-tools.

La deuxième option est d'utiliser un outil d'échaffaudage (*scaffolding*). Ces outils créent un squelette de projet contenant les outils, dépendances et une structure prête à l'emploi.

Si vous commencer une nouvelle application, Yeoman est un super outil d'échaffaudage qui utilise des générateurs pour vous généré une structure de projet adequate et vous lancer rapidement dans le développement. Il supporte une énorme gamme de types de projet. Par exemple, construisez-vous une application AngularJS ? Alors utilisez le générateur Angular de Yeoman. De même pour un projet PhoneGap par exemple. Peu importe ce que vous construisez, il y a une grande chance qu'un générateur Yeoman existe pour cela.

## Voir également : Browserify

Aller voir Browserify. Ce n'est pas un outil de construction comme on peut le l'imaginer, mais il offre une approche minimaliste au Workflow de transformation Javascript.

# Aller de l'avant et construisez !

Dans ce livre, nous avons commencé par regarder les workflows commun en développement web. Nous avons ensuite approfondi ce qu'était un outil de construction, ce qu'était un fichier de construction, et ce qu'étaient les tâches. Puis nous avons vu de bout en bout la création de tâche pour automatiser le workflow.

Merci de m'avoir, j'espère que ce livre aura été une introduction utile au monde merveilleux des outils de construction !


# A lire également

* James Cryer’s Pro Grunt.js
* Travis Maynard’s Getting Started with Gulp

# Remerciement

Je remercie les correcteurs éprouvé suivant pour leur relecture  :
* Nate Abele
* Andrew Canham
* Kathryn Davies
* Aryella Lacerda
* John Mercer

# Télécharger au format PDF (version anglaise)

[Download as PDF](http://radify.io/downloads/Using-Build-Tools.pdf)
