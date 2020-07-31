---
title: "Rxjs Et Les Observables"
date: 2020-08-01T00:09:53+02:00
tags: ["RxJs","observable"]
draft: false
disable_share: true
---

Les bases

Les observables, comme les fonctions ne retournent des valeurs que quand ils sont invoqués. On crée une fonction puis on l'appelle pour avoir son résultat, de la même manière on crée un Observable et on y 'souscrit' pour avoir son résultat(données)

Les observables peuvent retourner des valeurs  synchrones ou asynchrones  (comme les fonctions), toutefois à la différence d'une fonction un observable peut retourner plusieurs valeurs.

En javascript et en typescript il y a Rxjs, c'est une librairie très complète qui permet de créer des observables, et qui nous mets à disposition de très nombreux opérateurs pour manipuler ces observables

Le framework Angular embarque RxJs, mes exemples seront donc dans un contexte d'application Angular, toutefois, je ferais un Post pour expliquer comment embarquer RxJs dans un simple projet javascript

Création d'un observable:

{{< highlight typescript >}}
// creation à la mano....
public createObservable(value: any) {
    const result$ = new Observable(function subscribe(subscriber) {
        try {
            subscriber.next(value);
        } catch(err) {
            subscriber.error(err);
        }
    });
    return result$;
}

// creation via les opérateurs RxJs

// création d'un observable de type Array
public createObservableArray(data: Array<string>) {
    const result$ = from(data);
    return result$;
}

// création d'un observable à partir d'un événement javascript
public createObservableEvent(target: any, eventName: string) {
    const result$ = fromEvent(target, eventName);
    return result$;
}
{{< /highlight >}}

Subscription à un Observable

 La méthode subscribe a 3 paramètres next, error, complete.... next est toujours appelé error et complete sont facultatif, si on appelle error on n'appelle pas complete et inversement, la subscription n'a rien à voir avec un listenner, c'est juste une manière de démarrer l’exécution de l'observable. On peut souscrire plusieurs fois avec des observers différents.

{{< highlight typescript >}}
this.boxService.getUserStatus().subscribe({
    next: (data: userStatus) => console.log('status', data)
})
{{< /highlight >}}

Unsubscribe à un observable

Chaque observable retourne, à l'appel de la méthode subscribe un objet de type Subscription , pour arrêter l'execution de l'observable (qui peut être infinie) on appelle la méthode  unsubscribe sur l'objet subscription.

{{< highlight typescript >}}
this.subscriptionStatus$ = this.boxService.getUserStatus().subscribe({
    next: (data: userStatus) => console.log('status', data)
})

// plus tard dans le code
this.subscriptionStatus$.unsubscribe();
{{< /highlight >}}

Voici une présentation rapide des Observables et de RxJs, à retenir:

- Je crée mon observable (RxJs est là pour ça)
- je mets mon $ à la fin du nom de variable (juste par convention)
- Je souscris à mon observable
- Je me sers de la data retournée par mon observable
- je "unsubscribe" quand j'ai fini de consommer la data pour éviter les memory leak [??](https://en.wikipedia.org/wiki/Memory_leak)

Enjoy !!

