---
title: "Methodes Sur Les Tableaux en Javascript - 1"
date: 2020-08-08T23:04:33+02:00
draft: false
tags: ["javascript","tableaux","every","map","reduce","some"]
featured_image: "images/javascript/javascript_image.png"
disable_share: true
---

Les tableaux (ou listes) peuvent s'avérer très utiles pour stocker un ensemble de données, pour parcourir ces données bien sûr il y a dans tous les languages de programmation la boucle for, mais bien souvent des méthodes bien pensées peuvent nous faire gagner en temps et en performance, en voici quelques-une

#### La méthode map()

map nous permet d'appliquer des opérations à chaque élément d'un tableau, elle nous retourne un nouveau tableau modifié.

{{< highlight js >}}
const countries = ['france','espagne','niger','russie','maroc'];

/**
* Je passe une fonction anonyme à la méthode map, 
* le paramètre est l'élément courant du tableau
* map va itérer sur chaque élément 
* et y appliquer les modifications demandées
**/
let countriesUpperCased = countries.map(function(country) {
    return country.toUpperCase();
});

// j'enlève le mot clé function et je mets un flèche 
// après le paramètre c'est une arrow function
countriesUpperCased = countries.map((country) => { 
    return country.toUpperCase(); 
});

// j'enlève les accolades car je n'ai qu'une ligne
// et le mot clé return (mais je return quand même!!!)
countriesUpperCased = countries.map((country) => country.toUpperCase());

// comme je n'ai qu'un paramètre, je supprime 
// les parenthèses
countriesUpperCased = countries.map(country => country.toUpperCase());

console.log('countriesUpperCased', countriesUpperCased);
//  ["FRANCE", "ESPAGNE", "NIGER", "RUSSIE", "MAROC"]
{{< /highlight >}}

Pour itérer dans les tableaux on utilise bien souvent les fonctions fléchées, elles sont très lisibles et concises elles permettent de gagner en clarté et lisibilité.Je ne me servirais que des arrow function pour la suite des exemples

{{< highlight js >}}
const brands = ['apple','microsoft','linux','commodore','amstrad'];

// je retourne un objet pour chaque élément du tableau
// en ES6 { param: param } peut s'écrire { param }
// map nous met à disposition en paramètre:
// élément courant, index élément courant, tableau d'entrée
const formatedBrands = brands.map((brand, index, source) => {
return { id: index, brand, source }
});

console.log('formatedBrands', formatedBrands);
{{< /highlight >}}

Une dernière précision qui me semble importante, le map n'est utile que si on a besoin de retourner des données, si on doit juste faire une mise en forme de la donnée  mais qu'on n'a pas besoin de tableau en retour pour affecter cette donnée à une variable, on se sert de la boule forEach.

#### La méthode every()

Cette méthode test une condition pour tous les éléments du tableau, elle renvoie true si la condition est vérifiée, false dans le cas contraire

{{< highlight js >}}
// je déclare une fonction fléchée qui prend un paramètre

const allowed = (user) => user.age >= 18;

// pour info voici la version longue...
function allowedToo(user) {
  if (user.age >= 18) {
        return true;
  } else {
       return false
  }
}

const users = [
    { name: 'bob', age: 38 }, 
    { name:'ali', age:19 }, 
    { name:'léa', age: 12 }
];

const isAllowedUserList = users.every(allowed);

console.log('liste valide ?', isAllowedUserList);


{{< /highlight >}}

#### La méthode some()

Cette méthode renvoie true si au moins un des élément du tableau passe une condition donnée en paramètre

{{< highlight js >}}
// je déclare une fonction fléchée qui prend un paramètre

const allowed = (user) => user.age >= 18;

const users = [
    { name: 'bob', age: 38 }, 
    { name:'ali', age:19 }, 
    { name:'léa', age: 12 }
];

const isOneAllowed = users.some(allowed);

console.log('il y a une personne autorisée ?', isOneAllowed;


{{< /highlight >}}

#### La méthode reduce()

Cette méthode réduit un tableau à une valeur en appliquant une fonction de type accumulateur sur chaque élément..... j'explique:

{{< highlight js >}}
const bills = [125.75, 102.25, 99.00, 155.00];

// je déclare une fonction fléchée qui prend deux paramètres

const reducer = (computed, current) => computed + current;

// j'additione tous les éléments du tableau
// tour 1 = 125.75 + 102.25 = 228
// tour 2 = 228 + 99 = 327
// tour 3 = 327 + 155
// resultat = 482

let totalBills = bills.reduce(reducer);

console.log('total', totalBills);

const lastBill = 800.25;

totalBills = bills.reduce(reducer, lastBill);

console.log('total avec valeur initiale', totalBills);

// NB: la méthode reduce agit de la gauche vers la droite, 
// reduceRight fait la même chose de la droite vers la gauche
{{< /highlight >}}

