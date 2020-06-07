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
`git submodule add `[`https://github.com/budparr/gohugo-theme-ananke.git`](https://github.com/budparr/gohugo-theme-ananke.git "https://github.com/budparr/gohugo-theme-ananke.git")` `

`{{< /highlight >}}`