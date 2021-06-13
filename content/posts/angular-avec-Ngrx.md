---
title: "Angular Avec Ngrx"
date: 2021-06-13T17:40:22+02:00
featured_image: "images/ngrx/ngrx.svg"
draft: false
---

Toutes les applications Angular ne nécessitent pas un gestion d'état très précis, toutefois quand on veut gérer précisément l'état d'une application Angular, le projet Ngrx nous mets tout les outils nécessaires à disposition.

 Ngrx est basé sur le Redux pattern, je ne vais pas expliquer toute la théorie du pattern, je vais juste expliquer comment mettre en place Ngrx dans un nouveau projet Angular. La mise en place est assez longue et la prise en main quand on ne connait pas le pattern Redux peut paraitre assez lourde, mais à l'usage ce pattern à beaucoup d'avantages: le degug, le testing, la séparation des responsabilités, etc...



#### Le package @ngrx/store

Le package @ngrx/store est le seul package nécessaire pour créer une application ngrx (application avec un store)

{{< highlight js >}}
npm install @ngrx/store --save
import { StoreModule } from '@ngrx/store';
@NgModule({
    imports: [
      BrowserModule,
      // import des reducers
      StoreModule.forRoot({ books: booksReducer, collection: collectionReducer }),
      HttpClientModule,
    ],
    declarations: [AppComponent],
    bootstrap: [AppComponent],
  })
  export class AppModule {}
{{< /highlight >}}

- Le store est une structure de données immutable dans laquelle on stocke l'état de la data à un instant T, on peut également y stocker l'état du loader de l'app, l'état d'ouverture des boites de dialogues, le statut de connection du user, etc.
- On modifie le store grace à des fonctions pures (les reducer)
- les requêtes HTTP se feront dans les services mais ces services seronts appelés par des effects (on téléchargera un package spécifique pour cela)

Voici un apperçu du pattern:

- Dans un composant je "dispatch" une "action" pour charger la data (des books par exemple) LOAD_BOOKS
- L'effect connait cette action, il réagit à l'arrivée de cette action en appelant la méthode getBooks du "booksservice"
-  En cas de success de la méthode getBooks l'effect enverra une nouvelle action LOAD_BOOKS_SUCCESS, ainsi que la data (les books)
- Le booksReducer connait cette action, il recréra un nouveau state avec la nouvelle data

On peut constater que:

- le composant n'a pas appelé directement le service,
- que la data (le state) arrive du store (il est la seule source de vérité de l'application)
- Le state a été recréé par le reducer






