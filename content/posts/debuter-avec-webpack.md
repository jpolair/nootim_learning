---
title: "Débuter Avec Webpack"
date: 2020-08-15T15:36:09+02:00
draft: false
featured_image: "images/webpack/webpack_image.svg"
description: "webpack starter"
disable_share: true
---

En Javascript, hors contexte node.js, on ne pouvait pas (pendant longtemps) découper notre code en plusieurs fichiers et importer tel fichier dans tel fichier comme on le fait avec node ou en PHP.Si on veut découper le code en plusieurs fichiers pour le rendre plus lisible, il faut importer dans le fichier html  chaque fichier dans le bon ordre, comme on le ferait avec jQuery et bootstrap.js (car bootstrap.js se base jQuery).On peut vite faire face à des problèmes de dépendences ou de conflits à cause du nom des variables, ou des fonctions....pour ceci et pour bien d'autres choses il y a webpack:

On peut très facilement se créer un environnement de développement javascript (ou typescript) fort sympathique grâce à webpack, surtout que depuis la version 4 webpack n'a plus besoin de son fichier de configuration (il a une configuration par défaut très très basique)

Webpack est capable de "découper" notre code en fichier, et de nous générer un ou plusieurs bundles, j'explique les différents concepts clé en créant un fichier de configuration car bien que ce fichier n'est plus obligatoire (car il a maintenant des valeurs par défaut) il nous permet de faire vraiment plein de choses.

NB : le découpage en fichiers javascript (module) n'est pas le seul intérêt de webpack, on peut faire ceci grâce à un import de script type "module"

## Entry

C'est comme son nom l'indique le point d'entrée, il indique à webpack le module à partir duquel webpack va construire son "depedency graph", par défaut le point d'entrée est './src/index.js' mais on peut le changer

{{< highlight js >}}
// fichier webpack.config.js

module.exports = {
    entry: './chemin/fichier_entree_application.js'
}

{{< /highlight >}}

## Output

La propriete output dit a webpack où mettre les "bundles" qu'il a créé et comment les nommer, par défaut il y aura un "bundle" et il se nommera main.js et sera dans le dossier dist.

{{< highlight js >}}
// fichier webpack.config.js

const path = require('path'); // module node
module.exports = {
    entry: './chemin/fichier_entree_application.js',
    output: {
        // webpack attend un chamin absolu donc je me sers de path
        path: path.resolve(__dirname, 'dist'),
        filname: 'mon_bundle.js'
    }
}
{{< /highlight >}}

## loaders

Webpack en principe ne manipule que les fichiers javascript et json mais grâce aux loaders webpack peut prendre en charge d'autres type de fichiers et les convertir en modules compatibles avec notre application.
Les loaders ont 2 propriétés test: une regex pour identifier les fichiers concernés, et use: qui nous dit quel loader utilisé

{{< highlight js >}}
// fichier webpack.config.js
const path = require('path'); // module node
module.exports = {
    entry: './chemin/fichier_entree_application.js',
    output: {
        // webpack attend un chamin absolu donc je me sers de path
        path: path.resolve(__dirname, 'dist'),
        filname: 'mon_bundle.js'
    },
    module: {
        rules: [
            {
                test: /\.txt$/,
                use: 'raw-loader'
            }
        ]
    }
}
{{< /highlight >}}

## Plugins

Les plugin servent à accomplir pleins de tâches, comme l'optimisation des "bundle", la gestion des assets, l'injection de variable d'environnement, on installe les plugin via npm (ou yarn)
 
{{< highlight js >}}
// fichier webpack.config.js

const HtmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
    module: {
        rules: [
            {
                test: /\.txt$/,
                use: 'raw-loader'
            }
        ],
        plugins: [
            new HtmlWebpackPlugin({template: './src/index.html'})
        ]
    }
}
{{< /highlight >}}

## Mode 

La propriété mode nous permet de basculer sur 3 valeurs: production, development ou none, suivant la valeur de cette propriété la génération des bundles ne sera pas la même (minification, etc...), par défaut cette valeur est sur production

{{< highlight js >}}
// fichier webpack.config.js
const HtmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
    module: {
        rules: [
            {
                test: /\.txt$/,
                use: 'raw-loader'
            }
        ],
        plugins: [
            new HtmlWebpackPlugin({template: './src/index.html'})
        ],
        mode: 'development'
    }
}
{{< /highlight >}}

Voici une présentation des différentes propriétés du fichier webpack.config.js, on peut faire bien plus de choses avec webpack, on peut par exemple prévoir plusieurs entry points (pour séparer les vendors et notre code) , pour cela on fournirai un array de string à la propriété entry, on peut également avoir pleins de loaders: pour le sass, le typescript, les images, etc...

Webpack est un outil super, c'est la raison pour laquelle il est embarqué dans les frameworks javascript moderne, il ne faut pas avoir peur de son apparente complexité, la [documentation officielle](https://webpack.js.org/) est très bien faite et beaucoup de ressources existent sur le web pour bien l'appréhender

Si vous voulez démarrer un projet avec webpack il vous faudra installer au minimum (via npm ou yarn) webpack webpack-cli et webpack-dev-server, voici un lien vers un projet "starter" un peu plus complet que cette petite présentation : [projet starter webpack](https://github.com/nootim/webpack-starter) la commande npm run dev lancera un serveur ici: http://localhost:8080

