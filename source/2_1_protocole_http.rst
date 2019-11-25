*****************
Le protocole HTTP
*****************
*Qu'est-ce que le protocole HTTP, comment permet-il la liaison entre le serveur et l'utilisateur ?*

.. toctree::
    :maxdepth: 2

    2_1_protocole_http

1. Un serveur Kesaco ?
======================

Concrètement, un serveur c'est une machine accessible depuis internet. Pour la contacter, on a besoin :
    * D'un port de communication (80 pour parler sur internet par défault)
    * D'une adresse IP (par exemple http://172.217.22.131),


En tout, une machine possède 2^16 ports d'entrée (65 536). Par analogie avec les ports maritimes, les ports numériques
représentent les portes d'entrée vers le système. En fonction des protocoles de communication utilisé, l'information n'arrivera
pas sur le même port. Certains logiciels (interne à la machine) devront donc écouter sur plusieurs ports pour
être sur de recevoir l'information. C'est le cas par exemple d'un navigateur web, qui va envoyer et recevoir des requètes
via les ports 80 (protocole HTTP) ou 443 (protocole HTTPS) entre autre selon le site visité.

Un protocole, c'est un ensemble de règle qui définie la communication entre les machines. Si les règles ne sont pas suivies,
l'information ne pourra pas être transmises. C'est une manière s'assurer une certaine homogénéité dans les communications.

Les principaux protocoles sont :
    * SSH (port 22) : pour une transmition crypté
    * Telnet (port 23) : pour une transmition non-crypté
    * SMTP (port 25) : pour envoyer des mails
    * HTTP (port 80) : pour communiquer avec les serveurs web
    * HTTPS (port 443) : pour communiquer de manière chiffré avec les serveurs web

En ce qui concerne l'adresse IP, chaque machine connecté à réseau internet, reçoit un code d'identification. Ce code c'est
l'IP pour Internet Protocole.

Pour connaître l'IP de notre machine, on peut utiliser la commande :code:`nslookup` :

.. code-block:: sh

    > fbrucker$ nslookup www.google.fr

    Server:     147.94.19.254
    Address:    147.94.19.254#53

    Non-authoritative answer:
    Name:       www.google.fr
    Address:    216.58.205.99


On peut également retrouver une adresse *lisible* à partir de son adresse IP :

.. code-block:: sh

    > fbrucker$ nslookup 147.94.19.254

    Server:     147.94.19.254
    Address:    147.94.19.254#53

    Name :      resolv.ec-m.fr
    Adress:     147.94.19.254


Pour se faciliter la tâche, on associe un nom à chaque adresse IP : le DNS.

Ainsi les adresses suivantes sont identiques :
    * http://www.google.fr
    * http://www.google.fr:80
    * http://172.217.22.131 sont équivalents.

Attention, si on essaye d'afficher un site en forçant le navigateur à écouter sur un mauvais port, on ne peut pas recevoir l'information.
Ainsi, http://www.google.fr:1337 ne renverra rien puisque le serveur google envoie ses informations sur le port 80.
Aucune information ne sera donc récupéré par notre navigateur.

**Un nom particulier :** :code:`localhost`

:code:`localhost` correspondra toujours à l'ordinateur courant (équivalent de this pour le réseau)

**Un IP particulier :** :code:`127.0.0.1`

Lorsque l'on créera notre propre serveur, il tournera en développement toujours sur 127.0.0.1 (ou :code:`localhost`)
pour ne pas interférer avec un potentiel serveur web déjà existant, on utilisera un port différent (8080 ou 3000 par exemple).
On y accédera par l'url : :code:`http://localhost:8080` (ou :code:`127.0.0.1:8080`)


.. note:: Pour
    aller plus loin, la commande :code:`dig` renvoie encore plus d'information sur les sites visités.

    * w10 :  :code:`scoop install dig`
    * OSX :  :code:`brew install dig`
    * Linux: :code:`apt-get dig`

    la doc : https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/s2-bind-dig.html


2. Fonctionnement du HTTP
=========================

Le protocole HTTP est le principal protocole de communication avec les serveurs web. Il est décrit par une série de RFC,
(Request for comment) qui définissent les différents standards du net. (le RFC 2026 explique comment écrire une RFC).

.. _telnet: https://fr.wikipedia.org/wiki/Telnet

Pour mieux comprendre le fonctionnement, nous allons émuler un navigateur avec le terminal qui utilisera la méthode telnet.
Cette methode permet d'envoyer et de recevoir des données texte de manière non-sécurisée. Pour utiliser cette méthode,
vous aures besoin d'utiliser la commande du même nom. Si vous ne l'avez pas, il vous faudra donc l'installer :

* w10 :  :code:`scoop install telnet`
* OSX :  :code:`brew install telnet`
* Linux: :code:`apt-get telnet`

Commençons pour nous connecter au serveur, la commande nécessite 2 paramètres :
    * Son adresse de serveur : fbrucker.perso.centrale-marseille.fr
    * le port sur lequel on va écouter : 80 puisque c'est du protocole http

Lancez donc la commande suivante : :code:`telnet fbrucker.perso.centrale-marseille.fr 80`

Le serveur nous indique normalement qu'il est connecté et attend des requètes de votre part.

.. code-block:: sh

    > fbrucker$ telnet fbrucker.perso.centrale-marseille.fr 80

    Trying 78.109.84.114...
    Connected to www.wikipedia.fr.
    Escape character is '^]'.

Les requètes HTTP
^^^^^^^^^^^^^^^^^

Nous pouvons alors lui poser des questions. Lui demander de nous envoyer des donnés. On fait ce que l'on appelle des
reètes HTTP. Voyons ce que peut nous renvoyer la page wikipedia. Rentrez les 2 lignes suivantes dans la console.

.. code-block:: sh

    GET /index.html HTTP/1.1
    Host: www.wikipedia.fr

La première ligne (ici :code:`GET` :code:`/index.php` :code:`HTTP/1.1`) correspond à la requète. On y trouve :
    * le type de requète (:code:`GET`, :code:`PUT`, ...),
    * A quoi on fait la requête (adresse suivant le serveur),
    * Le protocole : :code:`HTTP/1.1`.

Chaque type de requète permets de faire une demande spécifique au serveur.
    * GET   Le client demande une ressource
    * POST 	Le client demande la création d'une ressource
    * PUT 	Le client envoie une ressource vers une url
    * DELETE Le client demande la suppression d'une ressource

Seuls les requètes :code:`Get` et :code:`POST` sont disponibles via un navigateur.

La ligne suivantes correspond au header. Voici les principaux :
    * Host 	Le domaine vers lequel est fait la requête (:code:`Host: www.wikipedia.fr`)
    * Referer 	Adresse de la page web précédent cette requête 	(:code:`Referer: http://www.imagotv.fr`)
    * Accept 	Types de contenu acceptés par le navigateur 	(:code:`Accept: text/plain`)
    * Accept-Language 	Language acceptés par le navigateur (:code:`Accept-Language: fr-fr`)

La liste précédente est non-exaustive. Notez cependant que seul la méthode :code:`Host` est obligatoire.

Les réponses HTTP
^^^^^^^^^^^^^^^^^

Pour notre requête précédente, vous devriez avoir à peu près la réponse suivante :

.. code-block:: sh

    HTTP/1.1 200 OK

    Date: Sun, 24 Nov 2019 18:05:08 GMT
    Server: Apache
    Last-Modified: Thu, 29 Aug 2019 12:50:15 GMT
    ETag: "167-59140f2714fc0"
    Accept-Ranges: bytes
    Content-Length: 359
    Vary: Accept-Encoding
    Connection: close
    Content-Type: text/html

    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
    <html xmlns="http://www.w3.org/1999/xhtml">
    <head>
    <meta http-equiv="refresh" content="0; url=index.php" />
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <title>Wikipedia.fr</title>
    </head>
    <body>
    </body>
    </html>

Cette réponse HTTP contient :
    * La ligne de statut : la version du protocole, le code de retour et le libellé associé
    * Le header qui donne quelques informations supplémentaires
    * Le body qui renvoie la ressource demandée

Le code de retour résume le résultat de l'éxécution de la requête. Voici les principaux :
    * 200 : OK (Le corps contient le document demandé)
    * 301 : Move permanently (Document déplacé de façon permanente)
    * 302 : Found Moved temporarly 	Document déplacé de façon temporaire
    * 304 : not modified (document déjà chargé précédement)
    * 400 :	Bad Request (La syntaxe de la requête est erronée)
    * 401 :	Unauthorized (Une authentification est nécessaire)
    * 404 :	Not found (Document non trouvé)
    * 500 :	Internal server error (Erreur interne du serveur)

.. _ici: https://fr.wikipedia.org/wiki/Liste_des_codes_HTTP

Vous retrouverez tous les *status codes* ici_.

.. _ici aussi: https://http.cat/

Et détaillés avec des chats `ici`_.

3. Quelques outils
==================

On peut vérifier nos informations grace à d'autres outils du même style.

Le navigateur
^^^^^^^^^^^^^^

Sur votre navigateur, vous avez également acces à des outils de développement pour réaliser des requètes HTTP :

#. Ouvrir les outils de développement (clique droit -> examiner l'élément)
#. Aller sur l'onglet **réseau**,
#. Recharger la page. On voit tout ce qui est téléchargé. Pour :code:`index.php` on voit :
    * son status (200),
    * son type (:code:`document`),
    * le temps que le téléchargement à mis.

#. En cliquant sur le nom du fichier, on peut accéder à son header complet.

.. note:: On voit que tout un tas d'autres fichiers ont été téléchargés.

On pourra voir que le navigateur envoit également tout un tas d'autres informations au serveur. C'est la partie *Request Headers*.
Regardez par exemple la méthode :code:`User-Agent` de l'entête.


Quelques variantes de réponses :

**Redirect**

.. code-block:: sh

    fbrucker$ telnet www.google.com 80
    Trying 216.58.210.196...
    Connected to www.google.com.
    Escape character is '^]'.
    GET / HTTP/1.1
    Host: www.google.com



Le statut est 302 (redirect). Regardez sur *chrome* pour voir ce qu'il s'est passé.

**Timer et text/plain**

.. code-block:: sh

    fbrucker$ telnet www.gutenberg.org 80
    Trying 152.19.134.47...
    Connected to gutenberg.org.
    Escape character is '^]'.
    GET /files/20262/20262-0.txt HTTP/1.1
    host: www.gutenberg.org

Attention au timer qui ne vous laissera sans doute pas le temps de recopier la commande...


**Type binaire application/pdf**

.. code-block:: sh

    fbrucker$ telnet www.jeuxavolonte.asso.fr 80
    Trying 213.186.33.19...
    Connected to jeuxavolonte.asso.fr.
    Escape character is '^]'.
    HEAD /regles/formula_d.pdf HTTP/1.1
    host: www.jeuxavolonte.asso.fr

On peut télécharger ce que l'on veut. Ici on ne demande que les headers et pas le contenu, car il est binaire. Le type de résultat est alors *text/html*.


Curl
^^^^^


Pour télécharger directement une ressource (html, pdf ou autre) de l'internet, on peut utiliser
l'utilitaire https://en.wikipedia.org/wiki/CURL On pourra par exemple l'utiliser pour transférer votre site de votre
visible au site distant.

Très simple d'utilisation, il permet cependant de faire des choses complexes. Téléchargeons la documentation :

.. code-block:: sh

    curl -#O https://www.gitbook.com/download/pdf/book/bagder/everything-curl


.. note:: L'option :code:`-O` permet à ce que l'output soit écrit dans un fichier de même nom qui est sauvegardé dans le
    dossier courant. L'option :code:`-#` ne fait que changer la manière d'afficher la *progress bar*.
    https://fr.wikipedia.org/wiki/Hypertext_Transfer_Protocol#HTTP_1.1


POSTMAN
^^^^^^^

.. _Postman: https://www.getpostman.com/products

Enfin, pour faire des requètes de manière plus visuelles, et pour pouvoir paramétrer des requètes à l'avance, le
logiciel Postman_ est tout à fait adapté. Installez le, nous allons nous en servir pendant les cours suivants.

