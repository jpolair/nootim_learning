---
title: "Rxjs Dans Un Projet Javascript 1"
date: 2020-08-03T17:50:21+02:00
tags: ["RxJs","observable","map","forkjoin","tap","take","interval"]
draft: false
disable_share: true
---

On peut se servir de la librairie RxJs dans un simple projet javascript, dans un contexte Node.js ou tout simplement dans un navigateur.
On va, ici, s'en servir dans un navigateur pour une rapide présentation de quelques opérateurs.

Tout d'abord on crée un dossier avec trois fichiers dedans: index.html, style.css et app.js

index.html va juste importer RxJs via un CDN ert fera appel à notre feuille de style et notre script

{{< highlight html >}}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="style.css">
    <title>RxJs</title>
</head>
<body>
    <h1>Bienvenue dans le projet RxJS</h1>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/rxjs/6.6.0/rxjs.umd.js"></script>
    <script src="app.js"></script>
</body>
</html>
{{< /highlight >}}

Pour accèder à l'application (la page html) il suffit de double-cliquer sur le fichier index.html
A partir de maintenant j'ai accès à un objet rxjs (pour le vérifier il suffit d'ouvrir la console du navigateur et taper dedans rxjs....)

1 - RxJS interval

interval crée un observable qui emet un chiffre à un intervalle donné
pipe(), me permet d'enchainner des "opérateurs" sur mon observable initial
ici l'opérateur "take" dit après 10 répétitions (de 0 à 9) tu t'arretes automatiquement je n'aurais pas besoin de unsubscribe

NB: le $ à la fin du nom de variable est une convention de nommage pour les observables

{{< highlight javascript >}}
const { take } = rxjs.operators; // import opérateur
/**
 * Crée un observable qui emet un chiffre
 * toute les 2 secondes
 */
const interval$ = rxjs.interval(2000).pipe(take(10));

{{< /highlight >}}

Dans les lignes précédentes j'ai créé mon observable, mais attention comme pour une fonction, si je n'appelle pas l'observable il ne se passera rien
Ci-dessous, j'appelle mon observable grâce à la méthode subscribe et je log le résultat (dans la console du navigateur)

{{< highlight javascript >}}
/**
 * soubscription à l'observable interval$
 * et log du resultat
 */
interval$.subscribe(val => console.log('simple', val));

{{< /highlight >}}

2 - RxJs l'opérateur map

Je peux réaliser des actions autres que take() dans le pipe(), bien souvent après avoir réccupérer les données de l'observables (qui peuvent être le résultat d'une requetes http) je veux les mettre en forme, l'opérateur map est là pour ça...

{{< highlight javascript >}}
const { map, take, tap } = rxjs.operators; // import des opérateurs

/**
 * Création de l'observable double$ à partir de l'observable
 * interval$
 */
const double$ = interval$.pipe(
    map(val => val = val * 2),
    take(5),
    tap(val => console.log('double', val),
    )
)

{{< /highlight >}}

Ci-dessus je prends le résultat de l'observable interval$ et je retourne à l'opérateur suivant: take() la valeur initiale multipliée par 2.
NB: take(5) arretera l'observable après 5 occurences, pas après que le chiffre 5 soit atteint....

{{< highlight javascript >}}
/**
 * Subscription a double$, sans cette subscription
 * l'observable ne "démarre pas"
 * Je met le resultat de la subscription dans une variable
 * mais ce n'est pas obligatoire, c'est toutefois utile
 * pour "unsubscribe"....
 * 
 */
const result$ = double$.subscribe();
{{< /highlight >}}

3 - RxJs forkjoin

forkjoin sert à regrouper le résultats de deux observables, 2 requetes http différentes par exemple (si le client http renvoi des observables, comme le client http d'Angular)

{{< highlight javascript >}}
/**
 * Je crée 2 observable à partir de string grace à of,
 * mais, les "sources" auraient pu être autre chose
 * puis forjoin met le résultat de mes 2 observables
 * en forme dans mon objet custom
 * tap ne fait rien d'autre que loguer
 */
const joined$ = rxjs.forkJoin({
    nom: rxjs.of('nootim'),
    prenom: rxjs.of('dev')
}).subscribe(val => console.log('mon nouvel objet: ',val));
{{< /highlight >}}

NB: on peut donner en paramètre à la méthode forkjoin, des observables au lieu d'objet, elle nous renverra un tableau d'observable, essayer enlever les accolades et les noms de propriétés...