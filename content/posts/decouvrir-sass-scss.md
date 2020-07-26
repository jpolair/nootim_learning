---
title: "Découvrir Sass"
date: 2020-06-30T20:36:05+02:00
tags: ["css", "scss", "sass"]
draft: true
disable_share: true
---

On ne verra pas comment installer sass, on va juste voir comment s'en servir correctement.Pour faire simple sass permet d'écrire du css avec pas mal de bénéfices, c'est pour cette raison qu'il est possible,  assez facilement, d'embarquer sass dans de nombreux framework front (Angular, Vue, React)

#### L'imbrication

{{< highlight  css >}}
/* css classique */
div .btn { /* classe btn dans élément div */
    border-radius: 5px;
}
div .btn.red { /* classes btn et red sur même élément dans div */
    background: red;
}
div .btn:hover { /* comportement au survol de classe btn dans div */
    background: pink;
}
{{< /highlight >}}

{{< highlight  scss >}}
/* scss */
div {
    .btn { /* classe btn dans div */
        border-radius: 5px;
        &.red { /* classe btn et red dans div */
            background: red;
        }
        &:hover { /* comportement au survol de classe btn dans div */
            background: pink;
        }
    }
}
{{< /highlight >}}

Comme on peut le voir au dessus: 

- pour imbriquer on écrit le code entre accolades
- pour concatener on écrit & dans le code imbriqué
- & permet de ne pas repeter les sélecteurs avant les accolades

#### Les variables

Aujourd'hui les variables existent en css, toutefois, l'usage des variables sass reste pertinent.

{{< highlight  scss >}}
/* scss */
// def variable et affecte valeur
$primary: #3366AA;
$large-screen: "media only screen and (min-width: 991px)"

// def variable et affecte valeur si pas encore définit
$secondary: #77EE88!default; 

div {
    .main-div {
        background: $primary; // affecter variable;
    }
}

// #{nom-variable} pour séléctionner var dans sélécteur
@media #{$large-screen} { 
    width: 90vw;
}
{{< /highlight >}}

On peut importer des fichiers (donc des variables) en sass, bien souvent on se sert de la fonction d'import pour découper proprement l'applications.Pour importer : @import "path-du-fichier".

la création de variable pour les couleurs, les break-points, les icônes permet de gagner beaucoup de temps et d'énergie (en développement et en maintenance)

