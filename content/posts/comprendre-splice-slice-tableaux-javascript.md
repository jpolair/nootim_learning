---
title: "Comprendre Splice Slice Tableaux Javascript"
date: 2020-09-16T19:14:07+02:00
draft: false
featured_image: "images/javascript/javascript_image.png"
images: 
- "images/javascript/javascript_image.png"
tags: ["javascript","tableaux","splice","slice"]
description: "splice et slice JavaScript"
disable_share: true
---

Au début j'ai eu beaucoup de mal avec splice et slice, en général en tapant le nom d'une méthode dans google trad on comprend un peu plus à quoi elle sert, dans le cas de splice et slice ce n'est pas aussi évident.

Je vais tenter d'expliquer au mieux ces deux méthodes qui finalement n'ont rien de plus complexe qu'un pop() ou un push() 

## splice()
La méthode splice() est appliquée sur un tableau, cette méthodde modifie directement le tableau initial, en y ajoutant ou y retirant des éléments, c'est donc à splice() qu'on fera appelle pour modifier un tableau existant.

#### Remplacer un élément du tableau avec splice()

- J'indique l'index à partir duquel je vais faire mes modifs
- J'indique le nombre d'élément à retirer (ici c'est 1)
- Je donne la valeur du nouvel élément "bananas"

{{< highlight javascript >}}

const fruits = ["orange", "strawberry", "apple"];

fruits.splice(1, 1, "bananas");
// fruits = ["orange", "bananas", "apple"]

{{< /highlight >}}

#### Ajouter un élément dans le tableau avec splice()

- J'indique l'index à partir duquel je vais faire mes modifs
- J'indique le nombre d'élément à retirer (ici c'est 0)
- Je donne la valeur du nouvel élément "red"

{{< highlight javascript >}}

const colors = ["orange", "blue", "purple"];

colors.splice(1, 0, "red");
// colors = ["orange", "red", "blue", "purple"]

{{< /highlight >}}

#### Retirer un élément dans le tableau avec splice()

- J'indique l'index à partir duquel je vais faire mes modifs
- J'indique le nombre d'élément à retirer (ici c'est 2)
- Je ne donne pas de valeur pour le nouvel élément (il n'y en a pas)

{{< highlight javascript >}}

const friends = ["Lulu", "Polo", "Dédé", "Pat", "Lolo"];

friends.splice(2, 2);
// friends = ["Lulu", "Polo", "Lolo"]

{{< /highlight >}}

#### Attention à splice()

Certaines fois on ne veut pas modifier directement le tableau fournit en paramètre à un fonction, je pense à des applications basées sur un store/state genre ngRx, Redux ou Vuex ou plus globalement dans un contexte de programmation fonctionnelle, dans ce cas là il peut être préférable de faire une copie du tableau et de travailler sur cette copie ceci est réalisable très facilement avec le spread operator (ou slice on le voit après...)

{{< highlight javascript >}}

const countries = ["France", "Italy", "Spain"];
const copyCountries = [...countries];

// countries ne sera pas modifié
copyCountries.splice(1, 1);

{{< /highlight >}}

[documentation officielle splice](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Objets_globaux/Array/splice)

## slice()

La méthode slice() quand elle appliquée sur un tableau nous renvoie une copie d'une portion du tableau, cette méthode ne modifie pas le tableau d'origine.

#### Garder une portion d'un tableau avec slice()

- je crée une nouvelle constante
- j'applique slice() sur le tableau d'origine
- j'indique à slice l'index de départ de la copie
- j'indique à slice l'index de fin de la copie (index exclus dans la copie)

{{< highlight javascript >}}

const brands = ["google", "amazon", "facebook", "apple", "microsoft"];

const gafa = brands.slice(0,4);
// gafa = ["google", "amazon", "facebook", "apple"];

{{< /highlight >}}

Attention dans l'exemple précédent on pourrait croire qu'un pop() aurait eu exactement le même résultat, la réalité est bien différente, le pop() sur brands, aurait retourné ["microsoft"], gafa serait alors égal à ["microsoft"], donc bien différent du résultat attendu, de plus brands aurait été modifié il serait égal à ["google", "amazon", "facebook", "apple"], donc le slice() même sur le dernier élément d'un tableau a bien son intérêt.

#### Faire une copie d'un tableau avec slice()

- je crée une nouvelle constante
- j'applique slice() sur le tableau d'origine sans paramètres

{{< highlight javascript >}}

const numbers = [1, 22, 53, 14];

const favoriteNumbers = numbers.slice()
// favoriteNumbers = [1, 22, 53, 14]

{{< /highlight >}}

#### Copier un tableau jusqu'à la fin

- je crée une nouvelle constante
- j'applique slice() sur le tableau d'origine
- j'indique seulement l'indice du début de la copie

{{< highlight javascript >}}

const maybe = [false, false, true, true, true];

const truth = maybe.slice(2);
// truth = [true, true, true]

{{< /highlight >}}

[documentation officielle slice](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Objets_globaux/Array/slice) 


