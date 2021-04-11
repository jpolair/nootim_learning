---
title: "Rxjs Combiner Plusieurs Observables"
date: 2021-04-11T18:10:16+02:00
draft: false
featured_image: "images/rxjs/rxjs_image.png"
tags: ["rxjs", "opérateur"]
disable_share: true
---

Il existe plusieurs opérateurs pour combiner les observables, pour choisir l'opérateur qui réponde parfaitement à notre besoin il faut se poser quelques questions essentielles:

* Quels sont mes observables en entrée
* l'ordre d'arrivée de mes observables en entrée est-il important
* Ce que je veux en sortie

Tout d'abord un petit rappel:

* les opérateurs d'instance (instance method) s'enchainent dans la méthode pipe(), et chaque opérateur renvoi un nouvel observable, ce qui signifie que le paramètre en entrée de l'opérateur2 sera le resultat de l'opération sur l'opéarteur1

* les opérateurs static (static method) prenent directement en paramètre les observables qu'ils doivent combinner 

{{< highlight typescript >}}

// http.get renvoi un observable (c'est le cas du HttpClient d'Angular)
const users$ = this.http.get('users_url');
const todos$ = this.http.get('todos_url');

// opérateur de transformation
const usersTransformed$ = user$.pipe(
    operateur1((res1) => nouvel_observable1),
    operateur2((res2) => nouvel_observable2)
);

// opérateur de combinaison
const combineUsersAndTodos$ = operateurCombinaison(users$, todos$);

{{< /highlight >}}

## Forkjoin

On utilise forkjoin quand:

* on veut combinner des observables
* l'ordre d'arrivée des stream n'est pas important
* On veut agir si et seulement si tous les observables sont complete

NB: Attention si un seul observable n'est pas complete forkjoin ne produira rien

{{< highlight typescript >}}

// http.get renvoi un observable (c'est le cas du HttpClient d'Angular)
const user$ = this.http.get('users/1');
const todo$ = this.http.get('users/1/todos');

// version avec un objet en paramètre
const userTodo1$ = forkJoin({
    user: user$,
    todo: todo$
});

// version avec un tableau en paramètre
const userTodo2$ = forkJoin([user$,todo$]);

// exploitation de la donnée
const userTodoSubscription = 
userTodo1$.subscribe(res => console.log('res', res));
{{< /highlight >}}


## CombineLatest

On utilise CombineLatest quand:

* On veut combinner des observables
* On sait que rien ne sera émis tant que chaque observable aura émis au moins une fois
* On veut toujours la dernière valeur de chaque observable


{{< highlight typescript >}}
enum StatusUser {
    connected,
    disconnected,
    unknow
}

const userStatus: BehaviorSubject<StatusUser> = 
new BehaviorSubject(StatusUser.unknow);
const userScore: BehaviorSubject<number> = 
new BehaviorSubject(0);


const userInfos$ = combineLatest(userStatus, userScore)

// le resultat sera un tableau [valeur userStatus, valeur userScore]
userInfos$.subscribe(res => console.log('res', res));

{{< /highlight >}}

## merge

On utilise merge quand:

* On veut combinner des observables,
* Quand l'ordre d'arrivée du stream n'est pas important (execution en parallèle)
* Pour convertir plusieurs observables en un seul
* on veut recevoir l'erreur (même si un seul observable est en erreur)

NB: merge s'utilise en static method ou en instance method (dans pipe() ou pas)

{{< highlight typescript >}}

const val1$ = of('Hello');
const val2$ = of('world');

// pas de soucis je peux souscrire à val2$ même si val1$ n'est pas complete
merge(val1$, val2$).subscribe(res => console.log('res', res))

{{< /highlight >}}



## concat

On utilise concat quand:

* On veut combinner des observables,
* Quand l'ordre d'arrivée du stream doit être respecté (execution séquentielle)
* Pour convertir plusieurs observables en un seul
* on veut recevoir l'erreur (même si un seul observable est en erreur)

{{< highlight typescript >}}

const val1$ = interval(2000);
const val2$ = interval(1000);

// On va attendre que val1$ soit complete pour souscrire à val2$
merge(val1$, val2$).subscribe(res => console.log('res', res))

{{< /highlight >}}

## swichMap

On utilise swichMap quand:

* On a besoin d'un observable1 pour en créé un observable2
* quand observable2 est créé observable1 n'a plus d'utilité
* Pour unsubscribe automatiquement de l'observable1


{{< highlight typescript >}}

const itemID$ = this.http.get('api_today_itemID');

// dès que je réccupère l'id je unsubscribe de itemID
// j'évite ainsi le memory leak
const userItem = itemID$.pipe(
    switchMap(id => this.http.get('url_user/item' + id))
);

{{< /highlight >}}










