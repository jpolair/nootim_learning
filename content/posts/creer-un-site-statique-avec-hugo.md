+++
date = 2020-06-07T16:36:55Z
draft = true
title = "Créer un site statique avec Hugo"

+++
Il y a quelque temps quand on parlait de site ou de blog on pensait très vite à wordpress, voire joomla (des CMS développés en PHP)  
Que les choses soient claires wordpress ou joomla sont des très bon outil pour créer un site web, toutefois quand ce site est créé (par un pote) puis laissé à l'abandon sans maintenance, les risques de dysfonctionnement, d'attaques sont bien réels.

Une alternative existe, les sites statiques: la JamStack, pour faire simple ces sites sont développés en HTML, CSS, JavaScript, ils peuvent être hébergés n'importe où (gratuitement ou pour vraiment pas cher)   
Pour simuler des "vrais" sites dynamiques comme wordpress toutes les parties qui nécessitent un backend sont externalisées: les commentaires des articles, les formulaires de contact, etc.. (pour le visiteur c'est transparent mais pour le développeur c'est énorme !!! les risques de pannes à cause de mise à jour compliquées, la maintenance, les attaques à cause de failles de sécurité sont externalisés chez un expert et je me sert de son service, si il y a un problème serveur c'est lui qui gère.