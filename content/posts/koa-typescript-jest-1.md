---
title: "Koa Typescript et Jest (partie 1)"
date: 2020-06-06T19:11:19+02:00
draft: true
---

Koa est un framework écrit par l'équipe de développement d'Express, en voici une présentation complète avec l'ajout de Typescript.
Pourquoi Koa ? Koa a été écrit après Express il est super light (encore plus light qu'Express) et il se base sur le principe d' async / await ce qui le rend très très lisible

Pré-requis :

- Node, Nodemon, Typescript installé en global sur l'ordinateur.
- Un IDE moderne Visual studio code(gratuit) ou Webstorm(payant)

{{< highlight script >}}

mkdir starter_koa 
cd starter_koa
npm init -y
git init # si git est installé sur l'ordinateur
npm i koa koa-router koa-body koa-json koa-logger
touch .gitignore playground.ts
tsc --init // creation fichier conf Typescript

{{< /highlight >}}

Je télécharge le minimum pour créer une petite API avec Koa, et je me crée mes fichiers app.ts, index.ts et .gitignore (l'extention .ts est l'extension des fichiers Typescript, le .gitignore est un fichier qui permet au système de gestion de version d'ignorer certains dossiers/fichiers) 
Le fichier playground.ts sert juste à présenter très rapidement Typescript, le voici en détail, pour le lancer taper tsc playground.ts, vous verrez alors les "erreurs" typescript dans la console et éventuellement un fichier playground.js dont le contenu sera un peu différent du playground.ts (effacer les fichiers playground.ts et playground.js après avoir joué avec ils ne servent à rien)

{{< highlight typescript >}}

// fichier playground.js (facultatif)
/**
* Déclaration d'une interface
*/
interface User {
email: string;
firstname: string;
// la prop ci-dessous (facultativeProp) est optionnelle mais si elle existe elle sera un booléen
facultativeProp?: boolean;
lastname: string;
favoriteNumber: number;
birthDate: Date;
}
/**
*  Bob est un objet non typé,
*  je peux y entrer n'importe
*  quelle clé/valeur
*/
const Bob = {
email: 23,
firstname: 'Jpol',
lastname: 'Air',
luckyNumber: 3, // le champ lucky number n'existe pas sur User
birthDate: '25/02/1975'
}
/**
* Angela est un objet basé sur l'interface User
* Typescript ne compilera pas si par exemple
* on change 23 par '23' ou
* new Date(1975,1,25) par '25/02/1975'
*/
const angela: User = {
email: 'angela@domain.fr',
firstname: 'Angela',
lastname: 'Wind',
favoriteNumber: 23,
birthDate: new Date(1975,1,25)
}
function add(first: number, second: number): number {
return first + second;
}
/**
*
* @param first
* @param second
* la fonction ci-dessous (compare) prend 2 paramètres de type number et retourne un booléen
*/
function compare(first: number, second: number): boolean {
return first > second;
}
// const result1: number = compare(4, 7); 
// ceci ne compilera pas et l'ide enverra une alerte avant même la compilation
const result2: boolean = compare(4, 7); // ceci compilera et sera égal a false car (4 > 7 = false)

{{< /highlight >}}

Je crée deux dossiers src et dist au root de mon application
Je vais maintenant créer le fichier src/index.ts c'est le fichier d'entrée non compilé de l'application

{{< highlight typescript >}}

// fichier src/index.ts
import app from './app';

app.listen(3000, () => console.log('server is running on port 3000'));

{{< /highlight >}}

je crée maintenant un fichier app.ts
J'écoute le port 3000 de mon app dans un fichier différent d'index.js, le but de ceci est de pouvoir réaliser mes tests correctement (c'est une contrainte jest/supertest)
Le fichier suivant est le fichier tsconfig.json qui a été crée automatiquement quand on a initialisé Typescript, dans un premier temps on va copier/coller je reviendrais dessus plus tard

{{< highlight json >}}

{
  "compilerOptions": {
    "outDir": "./dist",
    // "rootDir": "./src",
    "lib": ["es5", "es6"],
    "target": "es6",
    "allowSyntheticDefaultImports": true,
    "esModuleInterop": true,
    "module": "commonjs",
    "moduleResolution": "node",
    "emitDecoratorMetadata": true,
    "experimentalDecorators": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "**/*.test.ts", "**/*.spec.ts"]
}


{{< /highlight >}}

{{< highlight typescript >}}

// fichier src/app.ts
import Koa = require('koa');

const bodyParser = require('koa-body');

const users = require('./routes/users.routes');

const app = new Koa();

app.use(bodyParser({
    multipart: true,
    urlencoded: true
}));

app.use(users.routes()).use(users.allowedMethods());

export = app;

{{< /highlight >}}

Je crée dans src mes dossiers routes, tests, models, controllers, middlewares, config (ce qui est fait n'est plus à faire 😁 )
On s'approche de la partie intéressante….

{{< highlight script >}}

npm i -D @types/node @types/koa @types/koa-router @types/koa-json @types/koa-logger @types/koa-bodyparser

{{< /highlight >}}

Au dessus on importe les types dont nous avons besoin, beaucoup de package npm ont un fichier @types qui permet de les décrire et donc de typer les objets qu'ils fournissent en Typescript….. et maintenant on va écrire un peu de code


{{< highlight typescript >}}

// fichier src/routes/user.routes.ts
import Router = require('koa-router');
const routerOpts: Router.IRouterOptions = { prefix: '/users' };
const router: Router = new Router(routerOpts);
import { User } from '../models/user.model';

const users: User[] = [];
router.post('/',async ctx => {
    const user: User = ctx.request.body;
    users.push(user);
    ctx.body = { success: true, data: users }
});

router.get('/',async ctx => {
    ctx.body = {
        message: "Ici je get tous les users",
        securityLevel: 0,
        classicWelcome: "Hello world !!",
        data: users
    }
});

module.exports = router;


// fichier src/models/user.model.ts
export interface User {
  id?: number;
  firstname: string;
  email: string;
  password: string;
}
// fichier .gitignore
node_modules/
dist/

{{< /highlight >}}

Je démarre le serveur comme ceci :

- 1 Je compile mon Typescript en Javascript en tapant tsc au root du projet (dans la console du super ide)
- 2 Je lance la commande node dist/index au root du projet (on va scripter ça plus tard…) et je verrais dans ma console server is running on port 3000

En postant via Postman un objet de type User à l'adresse http://localhost:3000/users on poussera cet objet dans le tableau users (pour l'instant…. on va travailler avec une base de données bientôt)
On peut vérifier cette «persistance « des données en faisant avec Postman un GET sur la même url ce GET nous renvoie le tableau users
Petit résumé :

- J'ai créé un fichier de routes users, ces routes sont préfixées par users grâce aux IRouterOptions
- J'appelle toutes les routes préfixées par users grâce à un middleware ( app.use ) dans le fichier d'entrée de mon application 
- Je poste des données grâce à ctx.request.body ( il n'y a pas de req, res comme dans Express, mais il y a bien un next…)
- Je renvoi une réponse à l'utilisateur grâce à ctx.body
- Je préfixe mes fonctions avec async car normalement les opérations CRUD sont asynchrones et c'est ce que l'on fait en principe ici
C'est le début le meilleur est à venir 😃
Fin du step 1

Lien vers repo branche step 1 (Ne pas oublier de faire npm install)

