---
title: "Koa Typescript Jest (partie 2)"
date: 2020-06-06T20:37:02+02:00
draft: true
---

Maintenant que koa, Typescript, nodemon sont installés, on va automatiser les tâches de build et de dev.
Pour cela j'ai choisi de me servir de concurrently

{{< highlight script >}}
npm install --save-dev concurrently
{{< /highlight >}}

Je rajoute quelques petits scripts au package.json, pour les scripts de tests on verra plus tard.
Maintenant la commande npm run dev permet de développer en voyant les changement immédiatement (les erreurs de compilation aussi)

{{< highlight json >}}
"scripts": {
  "test": "echo \"Error: no test specified\" && exit 1",
  "dev": "concurrently -k \"tsc -p ./ -w\" \"nodemon ./dist/index.js\"",
  "start": "tsc && node dist/index.js",
  "build": "tsc"
},
{{< /highlight >}}

Maintenant arrive la partie de la persistance des données, j'ai choisi de me créer une
base de données en PostgreSql hebergée sur ma machine, mais on peut héberger les données
dans n'importe quelle base de données.

NB: j'appelle la table account, car une table système user existe en postgreSql, on aurait pu appeler notre table user il aurait juste fallu la préfixée du nom de la db à chaque appel (ce n'est pas mon choix)

{{< highlight sql >}}
CREATE TABLE account (
id SERIAL PRIMARY KEY NOT NULL,
firstname VARCHAR(255),
email VARCHAR(255),
password VARCHAR(255)
);
ALTER TABLE account ADD CONSTRAINT unique_email UNIQUE (email);

{{< /highlight >}}

Dans l'application on installe le driver pour postgresql et le fichier types, pour Typescript, correspondant

{{< highlight script >}}
npm install  --save pg @types/pg
{{< /highlight >}}

Maintenant il faut associer cette instance de postgresql à notre application, on va donc créer un fichier env (car on va commencer à rentrer des infos qui peuvent changer suivant notre environnement: dev, prod, test) et on n'oublie pas de mettre ce fichier /src/config/env.ts dans le .gitignore (le fichier env-sample est juste un fichier exemple de env sans les infos sensibles)

NB: pour l'instant on ne s'occupe pas des if, on va juste remplir le premier objet env.DB_CONNECTION, la SECRET_KEY on verra plus tard également…..

{{< highlight typescript >}}

let env = {
DB_CONNECTION_STRING: 'string de connection à elephantsql',
SECRET_KEY: '' // clé secrète pour authentification JWT
};
if (process.env.NODE_ENV === 'dev') {
env = {
DB_CONNECTION_STRING: '',
SECRET_KEY: ''
}
}
if (process.env.NODE_ENV === 'test') {
env = {
DB_CONNECTION_STRING: '',
SECRET_KEY: ''
}
}
if (process.env.NODE_ENV === 'prod') {
env = {
DB_CONNECTION_STRING: '',
SECRET_KEY: ''
}
}
export = env;

{{< /highlight >}}

je crée le fichier de config de ma base de données

{{< highlight typescript >}} 

const pg = require('pg');
import env from './env';
const connectionString = env.DB_CONNECTION_STRING;
const client = new pg.Client(connectionString);
export async function connect ()  {
try {
return await client.connect();
} catch(err) {
console.log('err ', err);
}
}


module.exports = {
connect: async () => {
try {
return await client.connect();
} catch(err) {
console.log('err ', err);
}
},
query: async (text: string, params: any[]) => client.query(text, params),
};

{{< /highlight >}}

Je crée une méthode connect pour me connecter à ma DB et une méthode query pour pouvoir faire des requêtes SQL, les paramètres sont passés dans un tableau à part pour des raisons de sécurité (prévention basique des injections SQL)

Je modifie le fichier index.ts car c'est un bon endroit pour démarrer ma connection DB

{{< highlight typescript >}} 
import app from './app';
import  * as db  from './config/database';
const con = db.connect();
if (con) {
console.log('database connectée');
}
app.listen(3000, () => console.log('server is running on port 3000')); 
{{< /highlight >}}

Je crée mon fichier controllers/usersController.ts
Dans usersController, trois méthodes simple pour créer un user, récupérer un user ou récupérer tous les users.La méthode query a pour premier paramètre la string SQL et chaque paramètre y est représenté par $numéro_d'ordre, le second paramètre est un tableau dans lequel on insert les paramètres dans l'ordre dans lequel ils ont été référencés, ici dans la méthode getUserById $1 représente le userID c'est donc le premier élément du tableau.

{{< highlight typescript >}} 
import env from '../config/env';
import { User } from '../models/user.model';
const db  = require('../config/database');


function createUser(user: User) {
return db.query(
'INSERT INTO account (firstname, email, password) VALUES($1, $2, $3)',
[user.firstname, user.email, user.password]
);
}
function getAllUsers() {
return  db.query(
'SELECT id, firstname, email FROM account',
[]
);
}


function getUserById(userId) {
return db.query(
'SELECT firstname, email FROM account WHERE id = $1',
[userId]
)
}


export {
createUser,
getAllUsers,
getUserById
}
{{< /highlight >}}

Le fichier routes/user.routes.ts
Maintenant je vais pouvoir faire des vraies requêtes en me servant des méthodes de usersController, avec Postman (http://localhost:3000/users) je peux poster un user en json (sans l'id qui lui sera créer automatiquement par postgresql (id SERIAL PRIMARY KEY NOT NULL)
NB: bien évidemment mon router est toujours exporté dans app (on l'a fait au step 1)

{{< highlight typescript >}}
import Router = require('koa-router');
const routerOpts: Router.IRouterOptions = { prefix: '/users' };
const router: Router = new Router(routerOpts);
import { User } from '../models/user.model';
import * as userController from '../controllers/user.controller';
const users: User[] = [];
router.post('/',async ctx => {
const user: User = ctx.request.body;
try {
const { rows } = await userController.createUser(user);
ctx.body = { success: true, data: user };
} catch (error) {
ctx.body = { success: false, error };
}
});


router.get('/',async ctx => {
try {
const  {rows}  = await userController.getAllUsers();
ctx.body = { data: rows, success: true };
} catch (e) {
console.log('erreur',e)
}
});


router.get('/:id', async ctx => {
const { id } = ctx.params;
try {
const { rows } = await userController.getUserById(id);
if (rows.length === 1) {
ctx.body = { data: rows[0], success: true };
} else {
ctx.body = { data: { message: 'Id non reconnu' }, success: false }
}
} catch (error) {
ctx.body = { success: false, error };
}
});
module.exports = router;
{{< /highlight >}}

Petit résumé:

- J'ai créé des scripts npm pour ma faciliter les tâches répétitives
- J'ai créé une base de données postgresql en ligne sur elephantSQL
- J'ai lié ma base de données à mon application grâce à une string de connection
- J'ai créé un fichier usersController pour écrire mes requêtes dans des fonctions que j'exporte

Certains problème persistent: 

- toutes les routes de l'API sont accessibles à tous,
- le mot de passe du user est en clair dans la base de données,
- L'application n'est pas testée (risque de bug, regression, etc)
- Je travaille avec le même environnement en dev, prod, test

Lien vers repo step_2

