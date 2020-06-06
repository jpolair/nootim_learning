---
title: "Koa Typescript Jest (partie 4)"
date: 2020-06-06T21:35:41+02:00
draft: true
---

J'ai créé l' Api avec une authentification jsonwebtoken, avant d'aller plus loin, c'est à dire : ajouter des endpoints à mon Api, créer une doc Api, refactoriser, et plus globalement faire grossir mon application, je vais implémenter les tests, pour ça j'ai choisi Jest et Supertest…
NB: Pour simplifier l'exercice, je me suis créé une base données locale, toujours en PostgreSQL, mais on peut faire ceci avec n'importe quelle base de données (il faudra juste adapter les requêtes au type de bdd)
On peut même sauvegarder en local (dans ce cas mettre les méthodes dans un setTimeout pour simuler une requête asynchrone)

j'installe tous les packages nécessaires pour gérer les tests, l'ES6 et Typescript

{{< highlight script >}}
(sudo suivant install) npm install -g jest
npm install typescript // oui c'était pas fait en local
npm install jest supertest 
npm i @types/jest @types/superset
jest --init
npm install ts-jest @types/ts-jest
npm install babel-jest @babel/core @babel/preset-env
npm install --dev @babel/preset-typescript
{{< /highlight >}}

jest init va initialiser jest dans le projet en me créant un fichier jest.config.js… je répond yes à toutes les questions

Je crée un fichier babel.config.js au root de mon app, voici son contenu :

{{< highlight js >}}
module.exports = {
presets: [
   [
    '@babel/preset-env',
    {
    targets: {
      node: 'current',
    },
   },
  ],
  '@babel/preset-typescript',
 ],
};
{{< /highlight >}}

Je crée quelques scripts pour mes tests dans package.json

{{< highlight json >}}
"test": "jest --verbose",
"test": "NODE_ENV=test jest --verbose",  
"test-watch": "jest --verbose --watchAll",
"coverage": "jest --coverage",
{{< /highlight >}}

Aujourd'hui je joue avec la même bdd pour les tests, le dev, et la prod (bon y'a pas de prod encore j'avoue)
Lors du refacto bientôt on changera ça, et on fera quelques tests en plus qui prouvent qu'il est pertinent d'avoir une bdd dédiée aux tests.
Voici un exemple très basique de tests:

{{< highlight typescript >}}
import request from 'supertest';
import app from '../app';
const user = {
 id: 1,
 firstname: 'Bob',
 email: 'bob2@entreprise.com',
 password: 'password',
};
describe('[routes/users.routes.ts]', () => {
 test('should get all users with success', async () => {
   const res = await request(app.callback()).get('/users');
   expect(res.status).toBe(200);
});
test('should get user by his id but not return his password', async () => {
 const res = await request(app.callback()).get('/users/1');
 expect(res.status).toBe(200);
 expect(res.body.data.firstname).toBe('Bob');
 expect(res.body.data.password).toBeUndefined();
  });
});
{{< /highlight >}}

Supertest est très complet il fait bien plus que retrouver le code status HTTP…Pour les tests (il ya d'autres exemples dans les sources). Ce qu'il faut retenir :

- On peut placer nos fichiers tests dans un dossier __tests__
- les tests peuvent être n'importe où dans l'app si ils sont appelés comme le fichier d'origin avec .test, ou .spec en plus (users.routes == users.routes.test)
- on peut mocker très simplement dans le fichier de test avec jest.fn() ou jest.mock('truc_à_mocker');
- on peut créer des mocks réutilisable dans plusieurs fichiers , ces mocks doivent être dans un dossier __mocks__ on peut mocker un package ou une juste méthode d'un fichier, pour mocker bcryptjs on crée un fichier bcryptjs.ts dans __mocks__ , pour une méthode on crée un fichier avec le même nom que celui où se trouve la méthode et on y recrée la méthode concernée…. dans ce cas il faudra importer le fichier dans le fichier de test (dans le cas d'un package ce n'est pas nécessaire) 
- Pour l'asynchrone de type Promise, il faudra appeler done() dans la méthode de test
- La méthode de test peut s'appeler test(), ou it() comme dans d'autres framework….

Pour le reste je ferais une page dédiée aux tests…
Maintenant tout est en place, il reste à ajouter des routes pour adapter l'API aux besoins, documenter l'API tant quelle est petite
 
lien vers repo step_4






