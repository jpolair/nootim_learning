---
title: "Découvrir Flexbox"
date: 2020-06-08T17:03:04+02:00
featured_image: "/images/flexbox/flexbox2.png"
tags: ["css", "flexbox"]
draft: false
disable_share: true
---

Le développeur Fullstack doit avoir plusieurs compétences pour développer en autonomie une application, forcément il doit savoir développer en back, en front, savoir requêter une base de données
, il doit avoir des compétences minimes en réseaux, en sécu (la première fois sur AWS ça fait bizarre) et j'en oublie, c'est la raison pour laquelle, perso, que j'évite le 
terme de développeur Fullstack, car je pense qu'il n'est vraiment réservé qu'aux Papas...
Mais.... j'ai pu remarquer, même si je ne sais pas l'expliquer qu'une compétence fait souvent défaut... c'est le CSS.
Je vais donc faire une présentation rapide du positionnement en CSS via flexbox, car même si on se sert d'un framework CSS pour aller plus vite, on a toujours besoin de connaitre les bases du positionnement,
de plus, la plupart des frameworks CSS sont basés sur Flexbox (Bootstrap, Vuetify, Angular Material, FlexBoxLayout, etc..)

Je commence super easy je crée une div parente et trois div enfant avec des noms de classes en français et assez clairs je pense

{{< highlight html >}}
<!doctype html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport"
        content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <link rel="stylesheet" href="style.css">
  <title>flexBox tuto</title>
</head>
<body>
<div class="papa">
  <div class="enfant-un">

  </div>
  <div class="enfant-deux">

  </div>
  <div class="enfant-trois">

  </div>
</div>

</body>
</html>
{{< /highlight >}}

et voici le CSS associé

{{< highlight css >}}
.papa {
    display: flex;
    width: 90%;
    background: #ccc;
    height: 300px;
  }
  
  .enfant-un,
  .enfant-deux,
  .enfant-trois {
    width: 25%;
    height: 240px;
  }
  
  .enfant-un {
    background: purple;
  }
  
  .enfant-deux {
    background: cyan;
  }
  
  .enfant-trois {
    background: teal;
  }
{{< /highlight >}}

ce qui nous donne trois carré aligné de la gauche vers la droite.
Explications : 

- display flex sur .papa var ajouter le comportement "flex" sur toutes les balises enfants de type block
- je ne précise pas flex-direction : row car c'est le comportement par défaut (en ligne)
- si j'avais mis flex-direction : column les éléments seraient les uns sous les autres

![flexbox1](/images/flexbox/flexbox1.png)

Maintenant je vais faire quelques modifications sur la div.papa : 

- J'ajoute justify-content : space-between pour espacer les éléments
- align-items : center => va centrer les éléments sur l'axe secondaire ici c'est (Y)

Je modifie le comportement d'un élément (enfant-deux) : 

- align-self: flex-end pour mettre l'élément en "end" de l'axe secondaire

{{< highlight css >}}
.papa {
    display: flex;
    width: 90%;
    background: #ccc;
    height: 300px;
    justify-content: space-between;
    align-items: center;
  }
  
 .enfant-deux {
    background: cyan;
    align-self: flex-end;
  }  
{{< /highlight >}}

![flexbox1](/images/flexbox/flexbox2.png)

Maintenant, et c'est là que ça devient un tout petit peu plus compliqué, je vais affecter le comportement flex à une div enfant,
la div.enfant-trois, et je vais lui mettre trois div.petit-enfant-trois, et cette fois elles seront en colonne, les unes sous les autres,
je vais aussi mettre un petit padding de 10px a div.papa (histoire d'y voir plus clair sur les rebords)
c'est parti...

Le HTML :

{{< highlight html >}}
<div class="enfant-trois">
    <div class="petit-enfant-trois"></div>
    <div class="petit-enfant-trois"></div>
    <div class="petit-enfant-trois"></div>
 </div>   
{{< /highlight >}}
   
Le CSS : 

{{< highlight css >}}
.enfant-trois {
    background: teal;
    display: flex;
    flex-direction: column;
    justify-content: space-evenly;
  }

.petit-enfant-trois {
      width: 100%;
      height: 60px;
      background: pink;
  }
{{< /highlight >}}

![flexbox1](/images/flexbox/flexbox3.png)

Maintenant j'ai trois div rose positionnées en colonnes avec un espace qui est le même partout dans la div (space-evenly)
Il faut bien noter que l'axe principal maintenant c'est (y) à cause du flex-direction : column , c'est donc sur lui que je fais mon justify-content
le align-items, maintenant agira sur (x), la preuve en modifiant le 2ème petit-enfant class="petit-enfant-trois rebel"

{{< highlight css >}}
.enfant-trois {
    background: teal;
    display: flex;
    flex-direction: column;
    justify-content: space-evenly;
    align-items: center;
  }
 .rebel {
     align-self: flex-end;
  }
{{< /highlight >}}

![flexbox4](/images/flexbox/flexbox4.png)

Pour finir je vais ajouter du contenu dans une div... comme en vrai, pour ça je crée deux paragraphes dans le premier petit-enfant.
Pour que les choses soient simples je demande au contenu du premier petit enfant d'adopter un comportement flex, puis avec ce comportement flex je positionne mon contenu

le HTML : 

{{< highlight html >}}
<div class="enfant-trois">
    <div class="petit-enfant-trois avec-texte">
      <p>Un peu de texte</p>
      <p>Un peu de texte</p>
    </div>
    <div class="petit-enfant-trois rebel"></div>
    <div class="petit-enfant-trois"></div>
  </div>
{{< /highlight >}} 

Le CSS :

{{< highlight css >}}
.avec-texte {
      display: flex;
      justify-content: space-between;
      align-items: center;
  }
  .avec-texte > p {
      padding: 5px;
  }
{{< /highlight >}}

![flexbox5](/images/flexbox/flexbox5.png)

Voici une présentation rapide du fonctionnement de flexbox, on peut en faire beaucoup plus encore, étirer, rétrécir, se servir des propriétés raccourcies....etc
la porte est ouverte.....
Enjoy !!





