****
Node
****

*Comment utiliser Node.js pour créer un micro serveur*

.. toctree::
    :maxdepth: 2

    2_2_node

1. Présentation
================

Pour délivrer les ressources, le *front* (pages html, fichiers json etc.) à l'utilisateur, on a besoin de paramétrer un serveur, le *back*.
C'est tout le travail du dévelopeur Backend.

On pourrait tout faire en telnet, mais plein de choses peuvent être automatisées ou rendues plus faciles. C'est le boulot des frameworks comme :
    * https://nodejs.org (petit serveur)
    * https://palletsprojects.com/p/flask/ (micro serveur)
    * https://www.djangoproject.com (moyen serveur)
    * http://projects.spring.io/spring-framework/ et https://spring.io/projects/spring-boot (gros serveur)

.. _nodejs: https://nodejs.org

Nous allons voir ici comment paramétrer un serveur nodejs_. Cet outils est idéal pour créer des petits serveur web ou
des sites à une seule page ressemblant à une application,.

Il est utilisé en conjonction d'autres bibliothèques pour créer des applications *MEAN*
(avec les bibliothèques Mongo DB, Express.js, Angular, Node.js). Actuellement, on trouve des alternatives sérieuses à
angular, comme react.js, mais Node reste de plus en plus utilisé.

Installation de node.js
^^^^^^^^^^^^^^^^^^^^^^^^

Si vous n'avez pas encore téléchargé Node.js, vous pouvez le faire en allant sur les liens suivant :
    * Sous Windows 10, suivez les instructions de https://nodejs.org/en/download/,
    * Sous linux, on pourra suivre https://nodejs.org/en/download/package-manager/,
    * Sous OSX, on passe directement par *brew*: :code:`brew install node`

.. Note:: La doc de Node.js est très bien faite, je vous invite à la consulter régulièrement en
    cas de doute. https://nodejs.org/api/synopsis.html

Node en console
^^^^^^^^^^^^^^^^

.. _JavaScript V8: https://fr.wikipedia.org/wiki/V8_(moteur_JavaScript)

Node peut être vu comme un interpréteur javascript. Il incorpore le moteur _`JavaScript V8` de google

Tapez :code:`Node` dans un terminal. Vous vous retrouverez dans un interpréteur javascript. Idéal pour tester des
commandes et apprendre justement le JavaScript.

2. Premiers pas avec Node
=========================

.. note:: Durant ce cours, nous allons créer un petit serveur tout simple pour envoyer à l'utilisateur des pages html.
    Vous pourrez retrouver le fichier complet sur le repo de mon cours au tag :code:`1_premier_serveur`

    lien de mon git :

    un simple ":code:`git checkout tag`" dans la console vous renverra à l'étape du tag indiqué.

    Bon apprentissage !!

Premier Serveur
^^^^^^^^^^^^^^^^

Commencez par créer un nouveau projet et un fichier **premier_serveur.js** dans votre IDE.

Voici le code d'un serveur node minimal.


.. code-block:: javascript
    :caption: premier_serveur.js

    const http = require('http');

    const hostname = '127.0.0.1';
    const port = 3000;

    const server = http.createServer((req, res) => {
        res.statusCode = 200;
        res.setHeader('Content-Type', 'text/plain');
        res.end('Hello World\n');
    });

    server.listen(port, hostname, () => {
        console.log(`Server running at http://${hostname}:${port}/`);
    });


On exécute ce fichier en console :

.. code-block:: sh

  node premier_serveur.js


Et on peut ensuite voir le résultat dans un naviguateur à l'url : http://localhost:3000.

Le code précédent crée un serveur *http* qui répond toujours la même chose dès qu'on le contacte. Il affiche en log la
requête reçue, qui elle dépend de l'url donnée.

Regardons le code de plus près :

  * :code:`const` : déclaration de constantes. Changer port par exemple produit une erreur.
  * :code:`require` : importation d'une bibliothèque (ici de nodejs mais on créera les nôtres bientôt) et affectation de celle-ci à une constante.
  * les fonctions peuvent se créer à la volée avec : :code:`nomFonction((paramètres) => {})`
  * création d'un objet serveur avec une fonction ayant 2 paramètres, la requête et la réponse (voir doc).
  * :code:`listen` : lien entre le serveur et le couple adresse + port.


On peut afficher l'url de la requête : On récupère les variables *hostname* et *port* et on les affiche dans la console.
  
Détails, en utilisant la `documentation <https://nodejs.org/en/docs/>`__ de nodejs :

    * :code:`http` : C'est le module s'occupant des échanges et du codage des données.
    * :code:`http.createServer` : demande un `requestListener <https://www.w3schools.com/nodejs/func_http_requestlistener.asp>`__ qui est ajouté à l'événement request.
    * event request : deux paramètres :code:`http.IncomingMessage`  et :code:`http.ServerResponse`. Il est émit à chaque fois qu'un *request* est demandé
    * :code:`http.IncomingMessage` a un attribut url : il sert à générer le *Readable Stream interface* et beaucoup d'autres choses (événements, méthodes, ...).

.. note:: tag : premier_serveur

    La `documentation <https://nodejs.org/api/>`__ de nodejs est très bien faite. Trouvez tous les éléments mentionnés ci-dessus.


Les Routes
^^^^^^^^^^

Le principe d'un serveur web est de servir des pages différentes selon les requêtes.

Avant de passer à un framework permettant de le faire, voyons comment faire en node pure. Ajoutez des routes dans votre serveur
**premier_serveur.js** grace à des tests conditionnels comme présenté dans le code suivant.


.. code-block:: javascript
    :caption: premiere_serveur.js

	var http = require('http')

	var server = http.createServer((request, response) =>{

	    if (request.url === "/" || request.url === "/home") {
            response.statusCode = 200;
	        response.setHeader('Content-Type', 'text/html');
            response.end("<html><head><title>home</title></head><body><h1>sweet home</h1></body></html>");
	    }

	    else if (request.url === "/hello_world") {
            response.statusCode = 200;
            response.setHeader('Content-Type', 'text/plain');
            response.end("Hello world");
        }

        else {
          response.statusCode = 404;
          response.setHeader('Content-Type', 'text/plain');
          response.end();
        })

	server.listen(3000, 'localhost')
	console.log("c'est parti")


.. code-block:: sh

    node premiere_serveur.js

Le serveur précédent est instalé sur le port 3000 de localhost répond à trois routes :
    * http://localhost:3000/
    * http://localhost:3000/home
    * http://localhost:3000/hello_world

vous voyez qu'on peut renvoyer du texte comme du code html. Il suffit de changer le :code:`Content-Type`

Dans la partie suivante, on utilisera le framework *express* pour gérer tout cela de façon un peu plus élégante.

3. Mettre en production sur le serveur distant
===============================================

Maintenant qu'on arrive arrive à faire tourner un petit serveur en local, voyons comment le mettre en production. C'est à dire
le lancer sur un serveur distant.

Enregistrez votre travail dans un github. Nous récupererons les fichiers avec l'adresse ssh de votre repository.

.. note:: Lorsqu'on fait du linux, on peut :

        * créer des fichiers :code:`cat >> mon_fichier`
        * lire des fichier avec la commande :code:`cat mon_fichier`
        * écrire dans un fichiers en faisant :code:`vim mon_fichier` ou :code:`nano mon_fichier`

    nano est plus simple d'utilisation pour écrire dans un fichier, mais vous pouvez toujours apprendre à vous
    servir de Vim qui est plus ancien avec les tutos suivants :

        * tuto : https://www.youtube.com/watch?v=ggSyF1SVFr4 (je ne sais pas ce que ça vaut)
        * https://danielmiessler.com/study/vim/
        * https://www.cs.cmu.edu/~15131/f17/topics/vim/vim-cheatsheet.pdf


configuration du serveur
^^^^^^^^^^^^^^^^^^^^^^^^^^

.. _Serveur Ngnix :https://www.nginx.com/

On a un `Serveur Ngnix`_ qui tourne.

son boulot :
    * lire les requête et les envoyer au bon endroit (port)
    * servir des fichiers statiques(html css, images, ...)

Allez voir sur le serveur : 
    * :code:`/etc/nginx/nginx.conf` : configuration générale
    * :code:`/etc/nginx/conf.d/` puis :code:`nodejs.conf` par exemple
        * routes normales : commençant par :code:`/` : le reste est passé au port
        * routes statiques : commençant par :code:`/static/` : le reste est un fichier à partir du répertoire :code:`www` de l'utilisateur. Voir fichier :code:`static.conf`

dans le dossier :code:`/node/` importez votre repo git avec votre serveur :code:`premieres_routes.js`

exécuter node
^^^^^^^^^^^^^^

Si vous lancez :code:`node premieres_routes.js` dans ce repertoire, vous retrouverez votre serveur à l'adresse
http://node.[herbe_de_provence].ovh1.ec-m.fr/

Le problème c'est que si vous quittez le terminal, node s'arrète également et votre serveur n'est plus accesible. Voyons comment faire
pour qu'il soit toujours actif.

* Trouver le port
* Exécuter en screen executer la commande : `/usr/bin/screen -d -m -S node node app.js`
    * en utilisant man et `/`
    * -S : When creating a new session, this option can be used to specify a meaningful name for the session. This name  identifies
            the session for "screen -list" and "screen -r" actions. It substitutes the default [tty.host] suffix.
    * -d -m :  Start  screen  in  "detached"  mode.  This  creates a new session but doesn't attach to it. This is useful for system startup scripts.
* http://node.[herbe_de_provence].ovh1.ec-m.fr/

screen manual : https://linuxize.com/post/how-to-use-linux-screen/

* :code:`screen -ls` : trouve les différents screen
* :code:`screen -X -S [session # you want to kill] quit` : tue le screen


.. todo:: faire un speech sur les ps et kill

:code:`ps aux| grep raifort` puis kill (sans -9) le numéro du process (le 1er numéro)


4. Quelques notions de JavaScript
=================================

    **RTFM -- Read the Fucking Manual** : https://nodejs.org/api/

Node est un interpréteur javascript, mais sa grande force est dans ses modules et son fonctionnement purement asynchrone :

    *Lorsque cet évènement se produit ou lorsque j'ai fini de faire quelque chose, j'exécute une fonction.*


Création de fonctions à la volée
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Ceci est possible car en javascript, comme en python, un fonction peut être une assignée à une variable que l'on peut
ensuite exécuter. Tester le code suivant dans l'interpréteur :code:`node`.

Ouvrez un terminal et tapez :code:`node` puis la touche entrée. Il vous suffit ensuite de copier/coller
le code dans le terminal.

.. code-block:: javascript

    function affiche_bloup() { // definition classique d'une fonction
        console.log("bloup")
    }

    affiche_bloup()

    var x = affiche_bloup //affectation de la fonction à une variable

    x() //exécution de la variable, donc de la fonction.



    //fonction sans nom assignée à une variable
    var affiche_2 = function() { // On utilisera surtout celle là.
        console.log("bloup 2")
    }

    affiche_2()

Événements
^^^^^^^^^^

On utilise la méthode :code:`setInterval` utilisable par défaut en node.

Ce qui est utilisable par défaut est définit dans https://nodejs.org/api/globals.html

.. code-block:: javascript

    function affiche_bloup() {
        console.log("bloup")
    }

    // tout est asyncrone.
    // Lorsque la condition est vérifiée on exécute une fonction.
    var timer1 = setInterval( affiche_bloup, 1000) // un intervalle

    var timer2 = setInterval(function() { // un deuxième avec une function anonyme
        console.log("bim")
    }, 2000)


Modules Node.js
^^^^^^^^^^^^^^^^

La force de Node est ses modules. On en créera lorsque l'on voudra séparer les fonctions de notre code source.

.. _NetNinja : https://www.youtube.com/watch?v=xHLd36QoS4k&index=6&list=PL4cUxeGkcC9gcy9lrvMJ75z9maRw4byYp

.. note:: Le mécanisme est expliqué dans les tutoriaux 6 et 7
    du *NetNinja_* que je vous recommence fortement.

créons ensemble un module :code:`module.js` dans le dossier :code:`assets`


.. code-block:: javascript

    //module.exports est un objet rendu par require.
    // on lui donne donc comme attribut, les méthodes et constante que l'on veut voir transmise.

    module.exports.klaxon = function() {
        console.log("tuuuut !");
    }


    module.exports.reponse = 42

Ce module demande à la console d'afficher "tuuut !"

On peut l'utiliser dans une nouvelle route de notre serveur après avoir importé votre module :

**Import du module :**

.. code-block:: javascript
    :caption: premier_serveur.js

    var monModule = require("./un_module");

**Ajout d'une nouvelle route :**

.. code-block:: javascript
    :caption: premier_serveur.js

    else if (request.url === "/tuut") {
        response.statusCode = 200;
        response.setHeader('Content-Type', 'text/plain');

        monModule.klaxon();
        console.log(monModule.reponse);

        response.end("regarde ta console");
    }


La gestion des exports est le plus souvent utilisé comme on l'a vu en front en créant un objet ayant déjà tous ces attributs.
On peut alors réécrire notre fonction de la manière suivante :

.. code-block:: javascript
    :caption: premier_serveur.js

    module.exports = {
        klaxon : () => {
            console.log("tuuuut !");
        },

        reponse: 42,
    }


5. Servir des fichiers
======================

Tout à l'heure on a chargé un code html dans nos routes. pour plus de clareté, node peut aller chercher directement
ce code dans un autre fichier. On utilisera quasi jamais cela en production, mais c'est une étape nécessaire pour comprendre
ensuite le fonctionnement des frameworks qu'on va utiliser pour simplifier tout ça. Node va "piper" des fichiers jusqu'à nous en streaming.
C'est à dire qu'il va découper notre fichier en petit morceau et nous les envoyer les uns à la suite des autres. voir en même temps pour aller plus vite.

fichier local
^^^^^^^^^^^^^^^^

Pour servir un fichier local, on va utiliser le module de gestion de fichier :code:`fs`.

Dans un répertoire "assets" créer le fichier index.html :

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
            </style>
        </head>
        <body>
            <h1>Enfin du web !</h1>
            <p>Et on aime ça.</p>
        </body>
    </html>

Dans le fichier premieres_routes.js :
    * ajoutez le module :code:`fs`
    * créez une nouvelle route avec comme contenu :

.. code-block:: javascript
    :caption: premieres_routes.js

    var readStream = fs.createReadStream(__dirname + "/assets/index.html", "utf8")
    readStream.pipe(response)

vous devriez avoir un fichier similaire au tag : :code:`servir_fichier_local` :

.. code-block:: javascript
    :caption: premieres_routes.js

    var http = require('http')
    var fs = require('fs')

    const port = 3000;

    var server = http.createServer((request, response) =>{


        if (request.url === "/" || request.url === "/home") {
            response.writeHead(200,  {'Content-Type': 'text/html'})

            //file stream
            var readStream = fs.createReadStream(__dirname + "/assets/index.html", "utf8")
            readStream.pipe(response)
        }
        else if (request.url === "/hello_world") {
            response.statusCode = 200;
            response.setHeader('Content-Type', 'text/plain');
            response.end("Hello world");

        }
        else if (request.url === "/bienvenue") {
            response.statusCode = 200;
            response.setHeader('Content-Type', 'text/html');
            response.end("<html><head><title>home</title></head><body><h1>sweet home</h1></body></html>");
        }
        else {
            response.statusCode = 404;
            response.setHeader('Content-Type', 'text/plain');
            response.end();
        }
    })

    server.listen(3000, 'localhost')
    console.log(`Server running at localhost:${port}/`)

.. note:: Le nom `__dirname` est un globals de node (https://nodejs.org/docs/latest/api/globals.html). Il permet de connaitre le
    répertoire du module courant (ici, notre application)

lancez votre application, vous devriez arriver à lire votre fichier html dans la route que vous venez de créer.

Avec cette méthode, Node ne fait pas que mettre à jour le header de la requète http, il envoie également tout un fichier en réponse.

fichier distant
^^^^^^^^^^^^^^^^

Les fichiers précédents ne sont pas volumineux, ils sont donc quasi-immédiatement chargés, mais pour de gros fichiers,
le chargement peut être long, node organise ainsi tout chargement en stream pour permettre de servir du contenu le plus tôt possible.

On va créer une nouvelle route :code:`/gutemberg` pour essayer de charger un livre.

Rendez-vous sur la page du projet gutenberg et choisissez un livre au format "Plain Text - UTF8". Notez son url.
http://www.gutenberg.org/browse/languages/fr

L'exemple suivant récupère un gros fichier de l'internet.

.. code-block:: javascript
    :caption: premier_serveur.js

    // le streaming permet de commencer à envoyer des données alors que le fichier n'est pas fini.
    //exemple avec un gros fichier.

    else if (request.url === "/gutenberg"){
        response.writeHead(200,  {'Content-Type': 'text/html'})

        http.get("url_de_mon_livre", (response_get) => {
            response_get.setEncoding('utf8');
            response_get.pipe(response)
        });
    }


Voilà, vous connaissez à peu près le fonctionnement de Node, vous savez créer des route, piper des fichiers. Nous allons maintenant
voir comment automatiser tout ça avec *Express.js* et son moteur de template *View.js*