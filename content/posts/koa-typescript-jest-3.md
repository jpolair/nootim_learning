---
title: "Koa Typescript Jest (partie 3)"
date: 2020-06-06T21:15:28+02:00
draft: false
---

La base de données est installée, je vais donc y stocker le password de manière sécuriser, je vais également implémenter la sécurisation de mes routes par un json web token.
Dans src, je crée un dossier globalHelper et un dossier middlewares.
J'installe ensuite les 2 dépendances dont j'aurais besoin pour sécuriser le password et générer un token

{{< highlight script >}}
npm install jsonwebtoken bcryptjs --save
{{< /highlight >}}

Par la suite dans mon fichier config/env je vais affecter une valeur à ma propriété env.SECRET_KEY: 'L@Clè!SecrèteDEtonCHoixx&2'  
Je vais en suite créer le fichier globalHelper/hash.ts qui contient 2 fonctions, une va hasher le password, l'autre comparera le password "hasher" et le password "clair". (sur npm,  bcryptjs est très bien documenté)

{{< highlight typescript >}}
const bcrypt = require('bcryptjs');

async function generateHash(password: string) {
return bcrypt.hash(password, 8);
}

async function compareHashed(clear: string, hashed: string) {
return bcrypt.compare(clear, hashed);
}
export {
generateHash,
compareHashed
}
{{< /highlight >}}

On va ensuite créer le fichier globalHelper/webtoken.ts, ce fichier aura deux fonctions… une qui va générer un token, l'autre qui vérifiera la validité du token en fonction de l'algorithme et de la phrase secrète (c'est pour cela que cette phrase doit être secrète donc non versionnée !!!)

{{< highlight typescript >}}
import env from '../config/env';


const SECRET_KEY = env.SECRET_KEY;


async function signToken(account: any) {
return jwt.sign({ account }, SECRET_KEY, { algorithm: 'HS256' });
}


async function verifyToken(token: string) {
return jwt.verify(token, SECRET_KEY);
}


export {
signToken,
verifyToken
}
{{< /highlight >}}

Je vais maintenant créer le fichier middlewares/authenticate, qui contiendra une fonction, si les infos (login, password) sont bonnes on peut naviguer sur la route protégée sinon, suivant le cas d'erreur code 401 ou 403.

{{< highlight typescript >}}
import { verifyToken } from '../globalHelper/webtoken';


async function authenticate (ctx, next) {
const bearerHeader = ctx.request.headers['authorization'];
if (typeof bearerHeader !== 'undefined') {
const bearer = bearerHeader.split(' ');
const bearerToken = bearer[1];
ctx.request.body.token = bearerToken;
try {
     const decoded = await verifyToken(bearerToken);
     ctx.request.body.me = decoded['account'];
     return next();
    }catch (e){
ctx.status = 401;
     }
  } else {
ctx.status = 403;
  }
}
module.exports = authenticate;
{{< /highlight >}}

il faudra ensuite modifier dans routes/users.routes.ts la route post('/') ce sera une route non protégée pour créer un user.
Maintenant cette route hashera le password avant de le stocker en base de données
 on estime que tout le monde peut créer un compte la seule contrainte c'est l'email unique, on a géré ça dans le fichier database.ts le champ email a la clé unique.En cas de success ici je retourne un objet data avec l'email et le firstname, je ne retourne pas le password même si il est n'est plus en clair…

{{< highlight typescript >}}
router.post('/', async ctx => {
const user = ctx.request.body;


try {
 const hashedPassword = await generateHash(user.password);
 user.password = await hashedPassword;
 const { rows } = await userController.createUser(user);
 ctx.body = { success: true, data: { email: user.email, firstname:     user.firstname } };


} catch (err) {
ctx.body = { success: false, err };
}
});

{{< /highlight >}}

Maintenant pour debug on va rajouter dans la méthode getAllUsers de userController le champ password dans le SELECT, puis après avoir poster un nouveau user dans Postman on va faire un get sur la même url http://localhost:3000/users et si tout s'est bien passé on verra un champ password comme le mien….mon mot de passe original était password  😮
le hash a été réalisé par la méthode generateHash
NB: penser à supprimer le champs password dans getAllUsers

{{< highlight json >}}
{
"id": 20,
"firstname": "Bob",
"email": "bobby2@entreprise.com",
"password": "$2a$08$9VN7c3zyYACjosxGrEngpugz1URK1ydWYYZ2fc.doLwooK0Of19Mu"
}
{{< /highlight >}}

Maintenant que mon password est sauvagardé de manière sécuriser, je vais pouvoir grâce à l'ensemble email/mot de passe ET ma clé secrète générer un jsonwebtoken….
Challenge : Créer la méthode getUserByEmail dans userController, cette méthode servira pour le login et la création du jsonwebtoken…. elle ressemble énormément à getUserById sauf que…..🤔


Par la suite, je crée le fichier routes/login.ts, cette route est bien évidement accessible à tous.
cette route attend un email et un password dans ctx.request.body: 
on verifie si l'email existe:

- si il n'existe pas on renvoi Utilisateur non trouvé
- si l'email existe et que le password n'est pas le bon : Email ou mot de passe non valide, code 401 (non autorisé)
- si l'ensemble email/password est correct on crée un jsonwebtoken
Tout ceci est possible car la méthode compareHashed peut comparer le password en base de données et le password fournit en clair, si cette méthode renvoi true les passwords sont identiques

{{< highlight typescript >}}
import Router = require('koa-router');
const routerOpts: Router.IRouterOptions = { prefix: '/login', };
const router: Router = new Router(routerOpts);
import { User } from '../models/user.model';
import * as userController from '../controllers/user.controller';
const jwt = require('jsonwebtoken');
const env = require('../config/env');
const SECRET_KEY = env.SECRET_KEY;
import { compareHashed } from '../globalHelper/hash';


router.post('/', async (ctx) => {
const { rows } = await userController.getUserByEmail(ctx.request.body.email);
const user: User = rows[0];
if (user) {
const isMatch = await compareHashed(ctx.request.body.password, user.password)
if (isMatch) {
delete user.password;
const token = await jwt.sign({ user }, SECRET_KEY, { algorithm: 'HS256' });
ctx.body = { user, token, success: true };
} else {
ctx.body = { success: false, message: 'email or password not valid' };
ctx.status = 401;
}
} else {
ctx.body = { success: false, message: 'Utilisateur non trouvé
'};
}
});
module.exports = router;
{{< /highlight >}}

A ce niveau là nous sommes capable de protéger nos routes, grâce au middleware authenticate comme ceci :
NB: Bien évidemment il faut importer authenticate dans le fichier avant….

{{< highlight typescript >}}
router.get('/', authenticate, async ctx => {
try {
  const  {rows}  = await userController.getAllUsers();
  ctx.body = { data: rows, success: true };
  } catch (e) {
    console.log('erreur',e)
  }
});
{{< /highlight >}}

Ce code dit pour accéder au corps de la méthode router.get, authenticate doit renvoyer next()…. si ce n'est pas très clair, les middleware sont super bien expliqués dans la doc d'Express… oui, oui je n'ai rien contre Express  😁
Voici la fin du Step 3…. 
- A ce stade nous avons une authentification très basique (mais fonctionnelle)
- On ne peut pas se dé-loguer (il faut attendre expiration du token)
- Mon Api n'est pas documenté… même si elle est minuscule
- Et je n'ai toujours pas de tests….. Tester ça veut dire douter ? 🤔

Lien vers repo step_3

