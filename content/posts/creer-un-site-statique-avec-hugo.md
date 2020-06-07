+++
date = 2020-06-07T16:36:55Z
draft = true
title = "Créer un site statique avec Hugo"

+++
Il y a quelque temps quand on parlait de site ou de blog on pensait très vite à wordpress, voire joomla (des CMS développés en PHP)  
Que les choses soient claires wordpress ou joomla sont des très bon outil pour créer un site web, toutefois quand ce site est créé (par un pote) puis laissé à l'abandon sans maintenance, les risques de dysfonctionnement, d'attaques sont bien réels.

Une alternative existe, les sites statiques: la JamStack, pour faire simple ces sites sont développés en HTML, CSS, JavaScript, ils peuvent être hébergés n'importe où (gratuitement ou pour vraiment pas cher)  
Pour simuler des "vrais" sites dynamiques comme wordpress toutes les parties qui nécessitent un backend sont externalisées: les commentaires des articles, les formulaires de contact, etc.. (pour le visiteur c'est transparent mais pour le développeur c'est énorme !!! les risques de pannes à cause de mise à jour compliquées, la maintenance, les attaques à cause de failles de sécurité sont externalisés chez un expert et je me sert de son service, si il y a un problème serveur c'est lui qui gère.

Il existe plusieurs générateur de site statique Jekyll écrit en Ruby, Gatsby basé sur le javaScript, Hugo écrit en language Go, moi j'ai choisi Hugo pour sa robustesse, sa communauté, et sa prise en main super rapide

Pré-requis:

* Git installé et un compte github, gitlab ou bitbucket
* Homebrew (pour mac) chocolatey (pour windows) installé sur la machine
* 10 min de dispo  😁

Aller dans un terminal et installer hugo sur la machine:

Pour mac voici la commande

    brew install hugo

Pour windows voici la commande

{{< highlight script >}}

choco install hugo -confirm

{{< /highlight >}}

Pour vérifier que tout s'est bien passé et que hugo est dispo sur l'ordinateur

{{< highlight script >}}

hugo version

{{< /highlight >}}

Si j'ai une réponse avec un numéro de version c'est que tout s'est bien passé... sinon on recommence calmement sans se tromper et ça va marcher c'est sur !!!

Pour générer un site, c'est très très compliqué il faut se rendre depuis le terminal à l'endroit où on veut sauvegarder notre site cd Documents/mon_chemin/ et taper 

{{< highlight script >}}

hugo new site le-super-site

{{< /highlight >}}

A ce moment notre site existe, il lui faut un thème (qui sera entièrement customizable), le mieux et de télécharger un thème en tant que submodule github, on pourra ainsi profiter des mises à jour du thème si il y en a

{{< highlight script >}}

cd le-super-site  
git init  
cd themes  
git submodule add https://github.com/budparr/gohugo-theme-ananke.git

{{< /highlight >}}

J'ai téléchargé le thème ananke qui est le thème "exemple" dans la doc hugo, il est très facile à mettre en place et je le trouve bien cool, mais plein d'autres thèmes existent, on peut les télécharger en tant que submodule comme j'ai fait ou télécharger le .zip et l'extraire dans le dossier themes du projet hugo... le résultat sera exactement le même, sauf que je ne profiterais pas des mises à jour, voici le lien avec tous les thèmes

[https://themes.gohugo.io/](https://themes.gohugo.io/ "https://themes.gohugo.io/")

Maintenant que j'ai un thème, je vais ajouter du contenu, le contenu s'ajoute en markdown, le markdown est très très compliqué à apprendre ça prend au moins....5min 😁  
PS: Pour l'instant j'ajoute du contenu en markdown...mais on peut faire encore mieux 😮😉

Au root du projet dans un terminal taper la commande qui suit, puis ouvrir l'IDE préféré , visual studio code, atom, sublime-text, webstorm, enfin bref, le préféré..

{{< highlight script >}}

hugo new posts/ma-super-story.md

{{< /highlight >}}

Dans le projet suite à la commande tapée précédemment, un fichier posts/ma-super-story.md existe

{{< highlight markdown >}}

***

title: "Ma super story"  
date: 2020-06-07T18/54/22+01:00  
draft: true

***

{{< /highlight >}}

Le contenu est vide il y a juste le front matter c'est l'entête qui donne des infos sur le posts: la date, le titre et le fait que draft soit à true fait que ce post est visible en dev mais pas en production, pour le rendre visible en production on passera draft à....... false bien joué!!!

on ajoute donc du contenu en dessous des 3 tirets(ou 3 astérisks)

un tiret - créera une puce de liste à puce, du texte sera l'équivalent html d'un paragraphe et des qu'on sautera une ligne on créera un nouveau paragraphe (sauter une ligne pas retour à la ligne) 

les "#" seront les titres de 1 à 6, "#" = "<h1>" ### = "<h3>", 

\[google\](www.google.fr) sera un lien vers..... google

!\[alt-de-mon-image\](/images/mon_image.jpeg) affichera une image placée dans le dossier static de mon application

Et pour la suite il y a beaucoup de doc sur le markdown partout sur le net !!!

{{< highlight markdown >}}

# Mon site

un petit paragraphe

un autre ici

* et une liste
* oui une liste

{{< /highlight >}}