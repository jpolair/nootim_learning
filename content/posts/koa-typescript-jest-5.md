---
title: "Koa Typescript Jest, la doc (partie 5)"
date: 2020-06-06T23:13:50+02:00
draft: false
---

Le temps de documenter l'api est arrivé, pour cela deux outils opensource sont souvent utilisés, Swagger et apidocjs.
J'ai choisi apidoc, pour sa simplicité d'installation, et la rapidité de sa prise en main, de plus sur npmtrends (donc un monde plutôt javascript) apidoc à 8280 stars et swagger 3677, toutefois swagger je le sais, est un très bon outil…..
On commence donc par installer apidoc en global sur son ordinateur (il est possible qu'il faille taper sudo avant la commande…)
NB : doc officielle super bien faite https://apidocjs.com/

{{< highlight script >}}
npm install apidoc -g
{{< /highlight >}}

on doit ensuite configurer apidoc, soit en créant en fichier apidoc.json, soit en ajoutant une clé dans le package.json, je vais donc ajouter une clé dans package.json

{{< highlight json >}}
"apidoc": {
"title": "Documentation starter_koa api",
"url": "http://localhost:3000/api",
"sampleUrl": "http://localhost:3000/api"
},
{{< /highlight >}}

Nb: Dans les sources du projet (dispo en bas) j'ai changé le préfixe des routes, c-à-d /users devient /api/users (comme dans une vraie api 😁)
c'est la raison pour laquelle dans mes deux clés de routes il y a le /api….
url, c'est l'url de l'api et sampleUrl est l'url qui sera utilisée par l'interface web d'apidoc (que l'on va mettre en place) pour faire les tests (ici c'est la même)
Comment ça marche ?
1- J'ajoute des commentaires avant chaque route, dans un format bien particulier…. ce format est bien expliqué dans la doc, voici tout de même un exemple c'est la route [post] de api/users

{{< highlight javascript >}}
/**
* @api {post} /users Create a new user
* @apiName CreateUser
* @apiGroup Users
* @apiVersion 0.1.0
*
* @apiSuccess {Object} user return a User
* @apiSuccess {String} user.email user email
* @apiSuccess {String} user.firstname user firstname
*
* @apiSuccessExample {json} Example data on success
* {
*      success: true,
*      data: { email: 'bob@societe.com', firstname: 'bob'}
* }
*/
{{< /highlight >}}

Ce qui donnera après build.....


![apiDoc_screen](/images/apiDoc_screen.png)

Pour générer ceci il faut deux choses :

- ajouter un script à package.json : "doc": "apidoc -i src/ -o doc/"
- ajouter ceci à app.ts: (on n'oublie pas le npm install de koa-static)

{{< highlight typescript >}}
const serve = require('koa-static');
app.use(serve('./doc'));
{{< /highlight >}}

le nouveau script dit va dans src chercher les infos de documentation et mets les dans un dossier nommé doc qui se trouve au root du projet…..donc maintenant dès qu'on tape npm run doc…. un dossier se générera avec les infos de tous les fichiers se trouvant dans src et ayant des infos aux formats apidoc (les routes)
La modif d'app.ts sert à servir les fichiers statiques, je vais donc chercher les fichiers statiques dans ./doc, et dans ./doc il y a un fichier index.html 😮
Ce fichier sera donc rendu à l'adresse http://localhost:3000 😁… oui le serveur dès qu'il voit un index.html c'est plus fort que lui, il nous le sert 😂😂
Pour finir:

- mettre doc/ dans le .gitignore (fichier généré)
- dans src/ au root créer un fichier _apidoc.ts (le nom _apidoc et juste une convention) et copier coller tous les blocs documentation, ce fichier fera le delta avec les différentes version de l'api et le rendu sur l'UI sera super…. comme ça on voit de suite si un objet à changer de format ou si on devait envoyer un objet et maintenant c'est une string …d'où l'importance d'apiVersion…
Fin de la série de 5.
Pour continuer on peut:

- mettre en place le middleware auth
- créer nouveaux middlewares (isMe par exemple…Ali peut pas update email d'Hana)
- vérifier la validité des données avant d'attaquer bdd (firstname null, ou avec espace avant, après)
- créer bdd de tests, qu'on clean beforeEach et afterEach (ou beforeAll et afterAll) 
- aujourd'hui le readme.md est en fait un changelog.md => créer un vrai readme… et un changelog

Lien vers step 5




