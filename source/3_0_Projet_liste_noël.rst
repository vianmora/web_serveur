*********************
Projet Liste de Noël
*********************

*Nous allons appliquer dans ce cours toutes les notions que nous avons vu précédement*

.. toctree::
    :maxdepth: 2

    3_0_Projet_liste_noël


1. Présentation du projet
======================
C'est bientôt Noël ! Il faut donc faire sa liste de cadeaux ! Créons donc une appli qui nous permettra de compléter cette liste !

.. note:: Retrouvez la correction pas-à-pas sur ce repository: https://github.com/pisabel-git/Liste-de-Noel

#. :code:`npm init` (on va appeler notre fichier de départ *app*),
#. :code:`npm install express --save`,
#. :code:`npm install ejs --save`,
#. création des dossiers annexes:

  * :file:`assets` pour les fichiers statiques. On y met une image pour les 404 appelée :file:`404.jpg` (prenez par exemple l'image de `<https://fr.wikipedia.org/wiki/Peugeot_404>`_)
  * :file:`views` pour les vues (avec le template *ejs*) et créez le dossier :file:`partials` dans celui-ci.


On doit obtenir l'architecture de dossiers suivante (en utilisant la commande tree qu'il faut peut-être installer d'abord), sans regarder les sous dossiers de :file:`node_modules` (d'où la commande :code:`grep`) :

.. code-block:: sh

  port-mac-brucker:app fbrucker$ tree -f | grep -v "node_modules/.*"
  .
  ├── ./assets
  │   └── ./assets/404.jpg
  ├── ./node_modules
  ├── ./package-lock.json
  ├── ./package.json
  └── ./views
      └── ./views/partials



app.js
^^^^^^^^^

C'est la base du serveur :

.. code-block:: javascript

  var express = require('express')
  var app = express()

  app.set('view engine', 'ejs')

  app.use("/static", express.static(__dirname + '/assets'))


  app.listen(8080);
  console.log("Appli lancée");


Regardons si ça marche... Lançons l'application `$ node app.js`, allons sur `<http://localhost:8080>`_ et voir si notre assets fonctionne :
`<http://localhost:8080/static/404.jpg>`_

.. note:: on a dissocié les URL (static) du dossier effectif (./assets/)


404.ejs
^^^^^^^


On commence par gérer les 404, puis on rajoutera toutes les autres routes au-dessus de celle-ci.

On place notre :file:`404.ejs` :


.. code-block:: html

  <html>
      <head>
          <meta charset="utf-8" />
          <title>404</title>

          <style>
              img {
                  display: block;
                  width: 580px;
                  height: 419px;
                  margin: auto;
              }
          </style>
      </head>
      <body>
          <h1>Oooops !</h1>
          <img src="/static/404.jpg" />
      </body>
  </html>


Et on ajoute la route dans le :file:`app.js` (à la toute fin, juste avant le lancement de l'appli. Si on a rien trouvé avant, c'est que c'est un 404) :

.. code-block:: javascript

  // 404 aucune interception
  app.use(function (req, res, next) {
        res.status(404).render("404")
  })


On peut le vérifier avec Chrome et les outils de développement (on doit voir le status 404 dans l'onglet *network*.
N'oubliez pas d'actualiser la page pour que l'onglet *network* fonctionne).


main.css
^^^^^^^^

L'architecture générale fonctionne, on va commencer notre premier nettoyage : séparer HTML et style pour que l'on puisse
facilement s'y retrouver plus tard (aucun css dans les html). Ici :

* On chargera un fichier :file:`main.css` contenant les caractéristiques générales d'une image,
* On spécifiera la taille de l'image voulue dans la balise img.


Ce qui donne l'ajout de la ligne suivante dans le header de :file:`404.ejs` :

.. code-block:: html

  <html>
      <head>
          <meta charset="utf-8" />
          <title>404</title>

          <link rel="stylesheet" type="text/css" href="/static/main.css">
      </head>
      <body>
          <h1>Oooops !</h1>
          <img src="/static/404.jpg" width="580px" height="419px" />
      </body>
  </html>


Et dans le :file:`main.css` on a ajouté, outre le comportement général d'une image (block et centré), les marges des balises body et html.


.. code-block:: css

  html, body {
    margin:0;
    padding:0;

    background: skyblue;
    color: #FFFFFF;
    font-size: 2em;
  }

  img {
    display: block;
    margin: auto;
  }


index.ejs
^^^^^^^^^^

On ajoute maintenant notre première page, le home (ou la fame).

On va mettre les éléments dans le répertoire :file:`views`. On commence par ajouter la route à :file:`server.js`
(avant la route par défaut qui est le 404) :

.. code-block:: javascript

  app.get('/', (request, response) => {
          response.render("index")
  })




Puis on crée notre vue :file:`index.ejs` dans le répertoire :file:`views`.
der
.. code-block:: html

  <html>
      <head>
          <meta charset="utf-8" />
          <title>Maison page</title>

          <link rel="stylesheet" type="text/css" href="/static/main.css">
      </head>
      <body>
          <h1>Le site</h1>
          <p>Il va y avoir des données (plein).</p>
      </body>
  </html>

.. todo :: L'appli est initialisée ! Nous allons pouvoir commencer à créer notre Liste au Père Noël. Si vous avez des soucis avec l'installation, jetez un coup d'oeil au :code:`tag: v.0` du repository

2. Listes dynamiques
===================

Remplaçons le code de `main.css`

.. code-block:: css

    .app
    {
        margin-right: 10%;
        margin-left: 10%;
    }

    h1
    {
        font-size: 48px;
    }

Ainsi que le code de `index.ejs` (nous utiliserons la bibliothèque bootstrap pour simplifier la partie rendu visuel)

.. code-block:: html

    <html>
    <head>
        <meta charset="utf-8" />
        <title>Liste de Noël</title>
        <link rel="stylesheet" type="text/css" href="/static/main.css">
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
    </head>


    <div class="app">
        <h1 style="margin-top: 5%">Liste au Père Noël</h1>

        <br/>

        <form style="display: flex;justify-content: space-around" method="post">
            <input type="text" name="item" class="form-control" placeholder="Nouvel Element" required/>
            <input type="text" name="price" class="form-control" placeholder="Prix" required/>
            <button type="submit" class="btn btn-success" style="margin-left: 2%">Ajouter</button>

        </form>

        <br/>

        <ul class="list-group">
            <li class="list-group-item">Item 1</li>
            <li class="list-group-item">Item 2</li>
            <li class="list-group-item">Item 3</li>
            <li class="list-group-item">Item 4</li>
            <li class="list-group-item">Item 5</li>
        </ul>

        <br/>

        <div>
            <button  class="btn btn-danger" type="button">Tout supprimer</button>
        </div>
    </div>

    </body>
    </html>

Nous avons désormais la structure visuelle de notre appli !
Nous pouvons voir une liste statique des "items". Rendons maintenant cette liste dynamique avec EJS, Express et une "Base de données" qu'on va initialiser :

`app.js` (en début de code)

.. code-block:: javascript

    // Notre BDD de base pour notre liste de Noël
    var DataBase = [{item:'Portable', price:'250'},{item:'Ferrari', price:'25000'},{item:'Chocolat', price:'4.20'}];

.. note:: Notre "Base de données" est une ArrayList en JavaScript. Chaque element de cette liste a deux arguments : "item" et "price"

Il faut ensuite inclure la base de données dans la fonction GET('/') pour l'inclure en paramètre de `index.ejs`

.. code-block:: javascript
    app.get('/', (request, response) => {
        res.render('index',{liste: DataBase})
    });

.. todo:: Réaliser une liste dynamique avec le langage EJS prenant en compte la base de données écrite précédemment. On utilisera la méthode des ArrayList `.forEach(function(element)) { ... }`.
.. note:: Vous trouverez la solution dans le `tag: v.1.1`


3. Les partials
===============

Avec cette manière de faire nous avons simplifié le code de l'index. Seulement, si nous souhaitons créer des composants plus complexes, le code va rapidement devenir lourd.
Pour éviter cela, on va utiliser un partials, qui sera notre composant "type", élément de la liste: :file:`item.ejs`
Il sera importés depuis le dossier :file:`views/partials`

.. note:: Cet élément type pourrait être utilisé dans différents templates si on en avait envie

.. todo:: Créer le partials affichant le nom de l'item et son prix, et ajoutez-y un bouton qui permettra plus tard de supprimer l'élément

Dans `index.ejs`, on utilisera la nomenclature suivante :

.. code-block:: html

    <%- include('partials/item.ejs', {value: data.item, price:data.price}); %>

.. note:: Vous trouverez la solution dans le `tag: v.1.2`


4. Refactor
============

Refactor en séparant les routes du serveur
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

En informatique, on aime bien bien séparer le code en unité fonctionnelles, de préférence dans de petits fichiers.

Ici, on va séparer ce qui est de l'ordre de la création du serveur et les routes qu'il peut prendre (pour l'instant on a peu de routes, mais cela augmente généralement très vite).

.. note :: cela va aussi nous permettre de voir comment javascript gère les modules.  `<https://www.sitepoint.com/understanding-module-exports-exports-node-js/>`_


On crée un fichier :file:`router.js` contenant toutes les réponses à nos requêtes (On y mettra aussi notre BDD pour simplifier la partie javascript):

.. code-block:: js

    // Notre BDD de base pour notre liste de Noël
    var DataBase = [{item:'Portable', price:'250'},{item:'Ferrari', price:'25000'},{item:'Chocolat', price:'4.20'}];

    var express = require('express');
    var app = express();

    app.set('view engine', 'ejs');

    app.use("/static", express.static(__dirname + '/assets'));

    // Requete GET
    app.get('/', (req, res) => {
        res.render("index",{liste: DataBase})
    });


    // 404 aucune interception
    app.use(function (req, res, next) {
        res.status(404).render("404")
        // logger.info("404 for: " + req.originalUrl); Le logger n'a pas ete defini, on en parle juste apres
    });

    module.exports = app;



Et le :file:`app.js`

.. code-block:: js

    app = require('./router.js')

    port = 8080
    app.listen(port);
    console.log("Appli lancée: http://localhost:" + port.toString())



5. Formulaire
==============

Requête POST
^^^^^^^^^^^^

Le formulaire html renvoie par défaut une requête POST si on a les arguments suivants.

.. code-block:: html

    <form ... method="post">
        <input ... />
        <input ... />
        <button type="submit"> Submit </button>
    </form>

.. note:: Comme elle renvoit déjà cette requête, nous n'aurons pas à gérer son code JS. Ce qu'il reste à faire cependant, c'est traiter cette requête.


Package BodyParser
^^^^^^^^^^^^^^^^^^

Le package BodyParser permet de récupérer les données entrées dans les input du formulaire et de les retranscrire en JavaScript dans une ArrayList, ayant pour argument les noms des input du formulaire.

Installons BodyParser :

:code:`npm install body-parser --save`

Puis dans :file:`router.js` ajoutons :

.. code-block:: js

    var bodyParser = require('body-parser');
    var urlencodedParser = bodyParser.urlencoded({extended:false});

Ainsi que notre fonction qui va récupérer la requête POST.

.. code-block:: js

    //fonction POST
    app.post('/',urlencodedParser, function(req,res)
    {
        DataBase.push(req.body);
        res.render('index',{liste: DataBase});
    });

Enfin, définissons le code html correctement pour que le body-parser renvoit la bonne ArrayList.

.. code-block:: html

    <form style="display: flex;justify-content: space-around" method="post">
        <input type="text" name="item" class="form-control" placeholder="Nouvel Element" required/>
        <input type="text" name="price" class="form-control" placeholder="Prix" required/>
        <button type="submit" class="btn btn-success" style="margin-left: 2%">Ajouter</button>
    </form>

.. todo:: Essayer de comprendre cette dernière partie et faire fonctionner le formulaire pour ajouter des éléments à notre liste de Noël.

.. note:: Vous trouverez la solution dans le `tag: v.1.3` si vous êtes bloqués.


5. Suppression d'élément
=========================

Admettons que le budget du Père Noël ait été réduit cette année et que malheureusement il ne pourra pas nous offrir la Ferrari cette année.
Nous devons donc supprimer cet élément de notre liste, ou recommencer tout depuis le début.
Nous n'irons pas trop dans le détail sur ces parties-là parce qu'il s'agit principalement de JavaScript, ce qui n'est pas l'objet de cet exercice.

Partie Requête
^^^^^^^^^^^^^^

Nous avons vu que la requête POST du formulaire n'avait pas besoin d'être créée, ce n'est pas le cas des requêtes DELETE.
Créeons un fichier :file:`actions.js` dans nos assets dans lequel nous mettrons le code suivant :

.. code-block:: js

    // Suppression d'un élément
    function deleteOne(item) {
        const xhttp = new XMLHttpRequest();
        xhttp.open("DELETE", "/" + item, true);
        xhttp.send();
        location.replace('/');
    }

    // Suppression de tous les éléments
    function deleteAll() {
        const xhttp = new XMLHttpRequest();
        xhttp.open("DELETE", "/", true);
        xhttp.send();
        location.replace('/');
    }

.. note:: Ce code est code AJAX, vous pourrez trouver plus d'infos à ce sujet ici : https://www.w3schools.com/xml/ajax_intro.asp


Appel depuis l'interface
^^^^^^^^^^^^^^^^^^^^^^^^

Maintenant que nos fonctions de requêtes sont en place, il faut pouvoir les appeler depuis notre interface, ce que l'on va pouvoir faire avec l'argument `onclick` de nos boutons.

.. todo:: Modifier les boutons de notre application pour qu'ils puissent appeler ces commandes.

Par la suite, pour que l'interface puisse atteindre les fonctions `deleteOne()` et `deleteAll()`, il faudra implémenter le script de :file:`actions.js` en ajoutant cette ligne dans le `<head>`

.. code-block:: html

    <script type="text/javascript" src="../static/actions.js"></script>


Partie Réponse
^^^^^^^^^^^^^^

Comme pour la requête POST, l'application doit récupérer ces requêtes et les traiter.
Nous allons les ajouter comme tel dans :file:`router.js` :

.. code-block:: js

    //fonctions DELETE
    app.delete('/',function (req,res) {
        DataBase.splice(0,DataBase.length);
        res.render('index',{liste: DataBase});
    });

    app.delete('/:item',function (req,res) {
        for (i=0;i<DataBase.length;i++){
            if(DataBase[i].item === req.params.item){
                DataBase.splice(i,1);
            }
        }
        res.render('index',{liste: DataBase});
    });

.. note:: La fonction `splice(x,y,...)` est une fonction d'ArrayList permettant de retirer y éléments à partir de l'index x et de les remplacer par '...', en prenant en compte les changements potentiels d'index. C'est magique !

.. todo:: Si tout s'est bien passé jusqu'ici, nous avons enfin une liste de cadeaux de Noël fonctionnelle ! Et n'hésitez pas à essayer de comprendre les fonctions utilisées.

.. note:: Vous trouverez la correction dans le :code:`tag: v.1.4`


Pour aller plus loin
====================

* On pourra imaginer que le Père Noël en lisant cette liste, ait besoin du détail de chaque cadeau souhaité. Dans notre appli on pourrait ajouter la possibilité d'afficher le détail d'un cadeau. (Réponse: :code:`tag: v.1.5`)
* On pourra également à terme remplacer notre `ArrayList DataBase` par une vraie Base de données. Ainsi notre appli déjà programmée est en soi une bonne base pour de la gestion d'inventaire par exemple...

.. note:: Je conseille le tutoriel de NetNinja, qui propose de réaliser une application similaire (une to-do-list) en
    incluant une Base de Données MongoDB : https://www.youtube.com/playlist?list=PL4cUxeGkcC9gcy9lrvMJ75z9maRw4byYp
