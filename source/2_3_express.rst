*******
Express
*******

*Comment utiliser le framework Express de Node.js pour améliorer notre routage*

.. toctree::
    :maxdepth: 2

    2_3_express


1. Gestion de packages
=======================

.. _Express.js : http://expressjs.com

Node vient souvent avec `Express.js_`, un framework permettant d'en tirer le meilleur parti. On va améliorer le site
que nous réalisé dans la dernière partie. Vous pouvez le récupérer sur le git au tag :code:`1_premier_serveur`.

Pour récupérer le projet complet de cette partie : :code:`git checkout 2_serveur_express`

.. _npm : https://www.npmjs.com

Pour installer les packages Node, on peut utiliser npm_ (node package manager). Aujourd'hui il existe des alternatives,
comme  :code:`yarn`, par exemple, que nous allons utiliser ici.

    * Sous W10 : :code:`scoop install yarn`
    * Sous linux : :code:`apt-get yarn`
    * Sous OSX : :code:`brew install node`

:code:`yarn` va installer les package pour nous : 

* on commence par initialier le projet par :code:`yarn init` 
* puis :code:`yarn add express`.

Il a rajouté un répertoire :code:`node_module` avec plein d'autres répertoires à l'intérieur. Ce sont les dépendances du
framework express. ainsi qu'un fichier de configuration :code:`config.json` Tout installer à la main aurait été infaisable.

.. note:: attention, si vous avez récupéré le repo sur le git, pensez à installer toutes les dépendances grace à ">> yarn install"

2. Route Express
=================

Express propose des méthodes pour gérer les routes de notre appli. On utilise les méthodes *get* de HTTP. Les autres
existent également bien sûr : http://expressjs.com/fr/starter/basic-routing.html.

créez un nouveau fichier **app.js** qui sera le point d'entrée de votre application. Insérez le code suivant qui reprend
les routes précédement créés.

.. code-block:: javascript 
    :caption: app.js

    var http = require('http');
    var fs = require('fs');

    var monModule = require("./assets/module");
    const port = 3000;

    var express = require('express');

    var app = express()

    app.get("/", (request, response) =>{

        response.writeHead(200,  {'Content-Type': 'text/html'});
        var readStream = fs.createReadStream(__dirname + "/assets/index.html", "utf8");
        readStream.pipe(response)
    });

    app.get("/hello_world", (request, response) => {
        response.statusCode = 200;
        response.setHeader('Content-Type', 'text/plain');
        response.end("Hello world");
    });

    app.get("/bienvenue", (request, response) => {
        response.statusCode = 200;
        response.setHeader('Content-Type', 'text/html');
        response.end("<html><head><title>home</title></head><body><h1>sweet home</h1></body></html>");
    });

    app.get("/tuut", (request, response) => {
        response.statusCode = 200;
        response.setHeader('Content-Type', 'text/plain');

        monModule.klaxon();
        console.log(monModule.reponse);

        response.end("regarde ta console");
    });

    app.get("/gutenberg", (request, response) => {
        response.writeHead(200,  {'Content-Type': 'text/html'});

        http.get("http://www.gutenberg.org/cache/epub/5781/pg5781.txt", (response_get) => {
            response_get.setEncoding('utf8');
            response_get.pipe(response)
        });
    });

    // manque le 404

    app.listen(3000, 'localhost');
    console.log(`Server running at localhost:${port}/`);

Pour rappel : 

.. code-block:: html
    :caption: index.html
    
    <!doctype html>
    <html>
    <head>
        <title>Maison page</title>
        <meta charset="utf-8" />

        <link href="https://fonts.googleapis.com/css?family=Indie+Flower" rel="stylesheet">


        <style>
            html, body {
                margin:0;
                padding:0;

                background: skyblue;
                color: #FFFFFF;
                font-size: 2em;
                text-align: center;

            }
            p {
                font-family: 'Indie Flower', cursive;
            }
            h2{
                font-size: 0.5em;
            }
        </style>
    </head>
    <body>
    <h1>Enfin du web !</h1>
    <p>Et on aime ça.</p>

    <p>
        <h2>à la fin du cous devriez avoir les url suivantes </h2>
        <ul>
            <li><a href="/hello_world">Hello word en "Text/Plain"</a></li>
            <li><a href="/bienvenue">Bienvenue en HTML </a></li>
            <li><a href="/tuut">Tuuuut sur la console</a></li>
            <li><a href="/gutenberg">votre livre du projet gutenberg</a></li>
            <li><a href="/n-importe-quoi">une erreur 404</a></li>
        </ul>
    </p>
    </body>
    </html>


La lecture des fichiers est également simplifiée en utilisant la méthode :code:`sendFile` du paramètre :code:`response` :

Modifiez le contenu de la route '/' en le remplaçant par la ligne :

.. code-block:: javascript

    response.sendFile(__dirname + "/html/index.html")


3. Middleware et 404
=====================

Vous avez remarqué, pour l'instant, le 404 n'est pas défini. Pour paramétrer une erreur 404, nous allons faire appel à
un middleware. Le middleware se trouve entre la réception de la requête par node et le rendu donné par :code:`app.METHOD`.

Plus d'informations ici : http://expressjs.com/fr/guide/using-middleware.html

Les appels aux middlewares se font dans l'ordre. Le paramètre next permettant d'aller à l'élément suivant de la route.

testez rapidement le code suivant :

.. code-block:: javascript 

    var express = require('express')
    var app = express()

    app.use(function (req, res, next) {
        console.log('Time:', Date.now());
        next(); // sans cette ligne on ne pourra pas poursuivre.
    })

    app.use(function (req, res, next) {
        console.log("ensuite");
        next(); // sans cette ligne on ne pourra pas poursuivre.
    })


    app.get('/', (request, response) => {
            response.sendFile(__dirname + "/html/index.html")
    })

    app.use(function (req, res, next) {
        console.log('comme un 404');
    })

    app.listen(3000);
    console.log("c'est parti")


Pour toute requête, on affiche la date. Ensuite, si la requête est un get que l'on réceptionne, on effectue la
méthode puis on s'arrête puisqu'il n'y a pas de :code:`next()`. On écrit donc "comme un 404" que si aucune requête
get n'est interceptée : c'est notre 404 !

supprimez les lignes que vous venez d'écrire et ajoutez donc un app.use entre les route, et la ligne :code:`app.listen`

.. code-block:: javascript
    :caption: app.js

    app.use(function (req, res, next) {
          res.status(404).sendFile(__dirname + "/html/404.html")
    })


Je vous laisse créer la page :code:`404.html` dans le dossier assets


.. code-block:: html
    :caption: 404.html
    
    <html>
        <head>
            <meta charset="utf-8" />
            <title>404</title>

            <style>
                html, body {
                    margin:0;
                    padding:0;

                    background: skyblue;
                    color: #FFFFFF;
                    font-size: 2em;
                    text-align: center;
                }

            </style>
        </head>
        <body>
            <h1>Oooops !</h1>
            <pre>
                             ,-._
                           _.-'  '--.
                         .'      _  -`\_
                        / .----.`_.'----'
                        ;/     `
                 404   /_;

                    ._      ._      ._      ._
                _.-._)`\_.-._)`\_.-._)`\_.-._)`\_.-._
            </pre>
        </body>
    </html>


4. Fichiers statiques
======================

Pour pouvoir utiliser des fichiers statics (images, feuilles css ...), Express a besoin de savoir où est-ce que vous les rangez.
Créons donc une route :code:`\contact`. Vous devrez donc créer :

    * La route avec la méthode get dans le fichier app.js
    * Le fichier contact.html dans le fichier assets
    * Un petit lien en plus dans le fichier index.html

.. code-block:: html
    :caption: contact.html

    <html>
        <head>
            <meta charset="utf-8" />
            <title>Contact</title>

            <style>
                html, body {
                    margin:0;
                    padding:0;

                    background: skyblue;
                    color: #FFFFFF;
                    font-size: 2em;
                    text-align: center;
                }

                img {
                    display: block;
                    width: 452px;
                    height: 600px;
                    margin: auto;
                }
            </style>
        </head>
        <body>
            <h1>Contact</h1>

            <img src="https://www.mauvais-genres.com/6047/full-contact-affiche-40x60-fr-90-jean-claude-van-damne-movie-poster-.jpg" />
        </body>
    </html>


Dans un répertoire :code:`static`, ajoutez une image. Remplaçons le lien vers l'image du fichier contact.html par le chemin de
l'image que vous venez d'importer.

.. code-block:: html

    <img src="/static/contact-us.png" />

Et ça ne marche plus... On a un 404. C'est parce que notre serveur ne répond qu'à nos requêtes, pas aux fichiers réels.
Il faut trouver un moyen que notre serveur puisse à la fois servir nos requêtes et les fichiers css, images, javascript
front et autres inclus dans les fichiers html.

En développement, on pourra utiliser un middleware qui servira en tant que fichier toutes les demandes commençant par
:code:`/static/`, mais c'est une mauvaise idée en production où l'on perd inutilement de la performance.

On utilisera ainsi un autre serveur, :code:`nginx`, dont la spécialité est de servir les fichiers statiques,
les autres routes étant dirigées vers express et node. Vous verrez ça plus tard lorsque l'on mettra le site en
production. Une configuration production possible est décrite ici : http://blog.danyll.com/setting-up-express-with-nginx-and-pm2/.

Pour l'instant, utilisons un petit middleware. Ajoutez la ligne suivante avant la définition des routes :

.. code-block:: javascript

    app.use("/static", express.static(__dirname + '/static'))


.. note:: on supprimera ce middleware en prod puisque la configuration de nginx a une route pour les fichiers statiques.

Maintenant, notre application sait où aller chercher les fichiers statics.

Passons maintenant à la partie templating. Nous y apprendrons à insérer des formulaire et créer des pages html sur mesure.