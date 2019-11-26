*******************
Ejs et les partials
*******************

*Comment générer des pages HTML sur mesure avec des requêtes post et le moteur de templating EJS*

.. toctree::
    :maxdepth: 2

    2_4_ejs_partials

1. Créer les templates
======================

.. _EJS: http://ejs.co

Dans ce cours nous vous allez apprendre à générer des fichiers HTML spécifiques pour chaque requête. On utilisera pour
cela le moteur de template EJS_.

.. note:: On reprendra le projet précédent. Vous pouvez le retrouver au tag :code:`2_serveur_express`
    Si vous voulez récupérer le projet fini de cette parti, rendez-vous au tag :code:`3_ejs_template`
    lien du git : [mettre lien]

Commençons par installer EJS et le mettre en dépendance : :code:`yarn add ejs`.

On le défini ensuite dans app.js :

.. code-block:: javascript
    :caption: app.js

    app.set('view engine', 'ejs')

Réorganisons un peu notre projet. Le but est de transformer nos fichiers HTML en templates :
    * Renommez nos fichiers .html en .ejs
    * Placez les dans un répertoire :code:`\views` à la racine. EJS saura ainsi où les trouver

Par défault, désormais, prenez l'habitude d'organiser vos fichiers sous une architecture MVC : Model - Views - controler

    * Dans le repertoire :code:`\model`, mettez y les fichiers qui gèrent les données du site (pour l'instant vous n'en avezpas rencontré, ais vous comprendrez l'interet de ce fichier lorsque nous aborderont les bases de données
    * Dans le repertoire :code:`\views`, mettez y tous vos templates
    * Dans le repertoire :code:`\controler`, on y met les fonctions JavaScript qui assurent le fonctionnement du site. (pour l'instant il contient notre fonctin tuuuut

A cette architecture on pourra bien évidement y ajouter un dosier :code:`\static` pour les images et les fichiers CSS.

Avec EJS, on peut alors simplifier completement l'écriture des routes :

.. code-block:: javascript

    app.get("/contact", (request, response) =>{

        response.writeHead(200,  {'Content-Type': 'text/html'});
        var readStream = fs.createReadStream(__dirname + "/assets/contact.html", "utf8");
        readStream.pipe(response)

        });

    app.use( (req, res, next) =>{
       res.status(404).sendFile((__dirname + "/assets/404.html"))
    });

Devient alors

.. code-block:: javascript

	app.get('/', (request, response) => {
	   response.render("index")
	});

    app.use(function (req, res, next) {
        res.status(404).render("404")
    })

On n'a d'ailleurs plus besoin du module :code:`fs`.
Remplacez ainsi les appels des routes :code:`/`, :code:`/contact` et :code:`404`,

2. Passage de paramètres
========================

L'un des nombreux interets d'EJS, c'est qu'on peut lui faire passer des information via des requetes POST, ou directement par l'URL



Je vous propose de créer une nouvelle route :code:`profile` qui vous dira bonjour avec votre prénom.
Commencez par créer la route (une route dans app.js, un lien dans index.ejs, et un nouveau template profile.ejs)

.. code-block:: html
    :caption: index.ejs

    <li><a href="/profile/Brucker">Page de profil</a></li>

.. code-block:: javascript
    :caption: profile.ejs

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
    <h1>Bonjour <%= personne%></h1>

    </body>
    </html>

.. code-block:: javascript
    :caption: app.js

    app.get("/profile/:name", (request, response) =>{
        response.render("profile", {personne : request.param.name});
    });

Vous pouvez multiplier les donnés envoyées :

.. code-block:: javascript
    :caption: app.js

    app.get("/profile/:name", (request, response) =>{
        var data = {age : 23, profession : 'étudiant'};
        response.render("profile", {personne : request.param.name, data:data});
    });

De cette manière, le moteur EJS, envoie des données au template :code:`profile`. Le fichier { var1 : valeur1, var2: valeur2}
est un fichier JSON, c'est une manière de stocker des informations simplement.

Regardez le résultat de votre travail, vous pouvez être fier de vous.

3. Partials
===========

EJS permet également de stocker un bout de code, qui sera inséré dans toutes les pages. Ou dans plusieurs pages qui
doivent afficher le même contenu.

Essayons de créer ensemble une barre de navigation qui devra apparaitre dans les 4 templates que vous avez normalement créés.
(404, contact, index, profile)

    * On crée une navbar toute simple, que l'on place dans un sous-répertoire de :code:`views`,  :code:`partials`,
    * On l'inclut dans nos templates en ajoutant dans notre fichier ejs la ligne :code:`<%- include ('partials/nav.ejs') %>`. Ici, cela pourra être la première ligne du body.


.. code-block:: html
    :caption: navbar.ejs

	<style>
        nav > ul {
            font-size: .5em;
            text-align: center;
        }
        nav > ul > li {
            display: inline;

        }
    </style>

    <nav>
        <ul>
            <li><a href="/">Home</a></li>
            <li><a href="/contact">Contact</a></li>
            <li><a href="/profile/Brucker">Profil</a></li>
            <li><a href="/hello_world">Hello word</a></li>
            <li><a href="/bienvenue">Bienvenue</a></li>
            <li><a href="/tuut">Tuuuut</a></li>
            <li><a href="/gutenberg">Gutenberg</a></li>
            <li><a href="/n-importe-quoi">404</a></li>
        </ul>
    </nav>


4. build
=========

Pour la dernière étape de ce cours, mettez votre premier serveur en production sur l'ovh !

.. note:: On fera attention aux fichiers statiques. Où doivent-ils être ?



.. note:: quelques ressources supplémentaires :

        * la doc d'EJS : http://expressjs.com/en/guide/using-template-engines.html