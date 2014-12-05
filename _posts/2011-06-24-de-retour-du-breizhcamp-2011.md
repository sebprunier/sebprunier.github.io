---
layout: post
title: De retour du BreizhCamp 2011 ...
date: 2011-06-24 12:35:01.000000000 +02:00
categories:
- Communautés
tags:
- ".net"
- breizhcamp
- cassandra
- cdi
- cloud
- communautés
- hadoop
- java
- javaee
- nosql
status: publish
type: post
published: true
author:
comments: true
---

Vendredi dernier, le 17 juin 2011, a eu lieu le [BreizhCamp](http://www.breizhcamp.org/) à Rennes, premier - espérons-le - d'une longue série !

![breizhcamp1]({{ site.url }}/assets/2011-06-24/breizhcamp1.png)

Les conférences étaient organisées selon 4 thèmes, reflétant très bien les tendances du moment :

*   Web2.0 &amp; Mobilité
*   Architecture &amp; Cloud
*   Persistance &amp; NoSQL
*   Innovation &amp; Agilité

Je profite donc de cet article pour vous faire un petit retour sur mon parcours lors de cette journée, certes pluvieuse ("Bienvenue en Bretagne" comme dirait l'autre), mais avant tout heureuse ! :-)

## 9h00 - 10h00 : KeyNote, par Sébastien Douche

Les **Communautés** ... le thème de prédilection pour l'ouverture des journées de ce type. La keynote de [Sébastien Douche](http://twitter.com/#!/sdouche) n'est pas sans rappeler celle d'[Emmanuel Bernard](http://twitter.com/#!/emmanuelbernard) à [l'édition 2010 du JugSummerCamp](http://sites.google.com/site/jugsummercamp/edition2010/).

Retenons que :

*   Une communauté est une "réunion ou association de personnes morales ou physiques, ayant des buts ou des intérêts communs" (source [wiktionnaire](http://fr.wiktionary.org/wiki/communaut%C3%A9)).
*   Participer à une communauté, s'y investir, vaut toutes les expériences professionnelles du monde.
*   Une communauté est synonyme de partage, de plaisir, de travail collaboratif, de relation gagnant-gagnant.
*   Sébastien Douche est un bon vivant :-) Cf. la moitié des photos projetées dans la présentation, alternant bière, choucroute, bière, barbecue, bière, méchoui, bière ...

Une keynote agréable, avec un support léger et efficace - comme je les aime - qui a sûrement mis en appétit une grande partie de l'amphi !

## 10h10 - 11h10 : Enfin de la biodiversité dans l'écosystème des bases de données, par Olivier Mallassi

Une présentation généraliste, idéale pour introduire les conférences à suivre dans le thème "Architecture et Cloud", notamment les sessions "Hadoop", "Cassandra" et "Hibernate/Cloud".

Olivier Mallassi nous explique les limites des SGBDR, notamment en terme de scalabilité (problématique des requêtes en écriture dans les architectures distribuées) et nous présente les concepts fondamentaux des bases NoSQL.

Quelques moments techniques intéressants lors de cette session, notamment :

*   MapReduce
*   Théorème de CAP

### Algorithme Map/Reduce

L'algorithme Map/Reduce est emprunté à la programmation fonctionnelle. Son principe est assez simple, en voici le fonctionnement dans les grandes lignes :

*   Un noeud reçoit un problème à traiter. L'étape _Map_ consiste à découper le problème en sous-problèmes et à déléguer chacun des sous-problèmes à un autre noeud (qui peut lui aussi découper le sous-problème à son tour). Chaque noeud résout ainsi une partie du problème.
*   Dans l'étape _Reduce_, chaque noeud remonte à son noeud parent la solution au sous-problème. Le parent calcul ainsi la solution partielle, ou totale s'il s'agit du noeud d'origine.

Le framework [Hadoop](http://hadoop.apache.org/) met en oeuvre cet algorithme. Petit exemple très simple : [WordCount](http://hadoop.apache.org/common/docs/current/mapred_tutorial.html#Example%3A+WordCount+v1.0).

### Théorème de CAP

Le théorème qui a du être évoqué lors de toutes les sessions NoSQL :-) Prenons les contraintes suivantes :

*   Cohérence (_**C**onsistency_) : tous les noeuds du système voient les mêmes données au même moment.
*   Disponibilité (_**A**vailability_) : le système peut toujours traiter des requêtes, même en cas de perte de noeuds.
*   Tolérance au partitionnement (_**P**artition tolerance_) : Le système continue de fonctionner en cas de partitionnement.

Le théorème nous dit qu'à un instant T, au maximum deux des trois contraintes peuvent être respectées. Les bases NoSQL sont donc "AP", là où les SGBDR sont "CA".

## 11h20 - 12h20 : Déploiement Continu en production, par Claude Falguière

Un peu de présence féminine pour présenter un sujet fort intéressant concernant LE but ultime recherché dans les processus de fabrication logicielle : le déploiement continu en production.

La co-fondatrice de [Duchess France](http://jduchess.org/duchess-france/) commence par un petit rappel sur les différents stades d'industrialisation :

*   **Continuous Integration** : intégrer de manière continue les modifications réalisées sur le code source. A ce stade, des outils comme [maven](http://maven.apache.org/) et [jenkins](http://jenkins-ci.org/) peuvent être utilisés afin de lancer régulièrement les tâches de compilation, tests unitaires, etc ...
*   **Continuous Delivery** : mettre à disposition les composants packagés et testés, de manière automatisée. Ici interviennent par exemple les référentiels d'artéfacts tels que [nexus](http://nexus.sonatype.org/) ou [artifactory](http://www.jfrog.com/products.php), alimentés par les résultats produits lors de l'intégration continue.
*   **Continuous Deployment** : déployer automatiquement tout ou partie d'une application, en utilisant les composants mis à disposition lors de l'étape précédente.

![breizhcamp-2]({{ site.url }}/assets/2011-06-24/breizhcamp-2.png)

[Claude Falguière](http://twitter.com/#!/cfalguiere) nous fait part ensuite de deux de ses retours d'expérience, en particulier un projet à l'infrastructure complexe et aux technologies hétérogènes, avec ses réussites et ses difficultés, notamment dans la conduite du changement. En effet, l'appropriation du nouveau processus de fabrication logicielle par les équipes de développement n'a semble-t-il pas été de tout repos ! (alors que les équipes de production/exploitation étaient plutôt motrices).

## 12h30 - 13h30 : JavaEE compareTo .Net, par Nicolas De Loof et Jean-Philippe Gouigoux

Quoi de mieux qu'un petit match JavaEE / .Net avant de déjeuner, histoire d'ouvrir l'appétit !

![breizhcamp-1]({{ site.url }}/assets/2011-06-24/breizhcamp-1.png)

L'objectif annoncé dès le départ par [Nicolas De Loof](http://twitter.com/#!/ndeloof) et [Jean-Philippe Gouigoux](http://blogs.dotnet-france.com/jeanphilippeg/), qui consistait à éviter les clichés du genre "_Microsoft, c'est le mal_", n'a pas été respecté très longtemps :-) La preuve en image :

![breizhcamp-5]({{ site.url }}/assets/2011-06-24/breizhcamp-5.png)

Plus sérieusement, le "match" s'est déroulé en plusieurs rounds (10 au total) présentant les forces et faiblesses de chacun. Ainsi JEE et .Net se sont affrontés dans les domaines de la persistance, l'approche composant, l'interopérabilité, etc ... A quelques exceptions près (notamment [WCF](http://msdn.microsoft.com/en-us/library/aa702682.aspx) et [Linq](http://msdn.microsoft.com/fr-fr/library/bb397926.aspx) pour .Net), le match était à mon sens assez équilibré. D'ailleurs [le premier slide de la présentation](https://github.com/BreizhJUG/breizhcamp/blob/master/slides/Java-vs-Net.pdf) résume très bien les choses : les stacks JEE et .Net sont finalement très similaires !

## 13h30 : Galette Saucisse !

Un peu d'attente avant de pouvoir déguster la fameuse spécialité bretonne :-) Mais ça valait le coup !

![breizhcamp-4]({{ site.url }}/assets/2011-06-24/breizhcamp-4.png)

## 14h30 - 15h30 : La vie dans du code legacy, par Emmanuel Hugonnet

"**J2EE m'a tuer**" ... tel est le titre introductif à cette présentation ! Session à mon avis nécessaire afin de ne pas oublier la dure réalité à laquelle nous sommes parfois confrontée au cours de notre carrière professionnelle.

Emmanuel Hugonnet ([AlpesJUG](http://www.alpesjug.fr/)) nous fait part de son expérience dans le code legacy et nous donne quelques pistes pour s'en sortir :

#### Standardisation de l'usine de développement

*   Fini notepad ! On utilise un vrai IDE :-)
*   Migration CVS vers SVN et rapidement vers GIT
*   Utilisation de Maven pour le build des projets, en remplacement de ANT
*   Mise en place du moteur d'intégration continue Jenkins

#### Modernisation de l'architecture

*   On oublie les EJB 1.X :-)
*   On s'appuie sur des standards JavaEE récents : JPA 2, REST
*   On migre petit à petit

#### Application de méthodes agiles pour la gestion de projet (Post-It power !)

#### Mise en oeuvre d'un processus d'amélioration continue de la qualité

*   Ecriture de tests unitaire
*   Outillage avec Sonar
*   Une consigne : quand on modifie du code existant, la qualité doit être au moins équivalente, idéalement meilleure mais surtout pas moins bonne !

## 15h40 - 16h40 : Elasticité et haute-disponibilité avec Cassandra, par Michaël Figuière

Une présentation de [Michaël Figuière](http://twitter.com/#!/mfiguiere) qui reprend l'explication des limites des SGBDR (scalabilité / accès concurrents en écriture) et les réponses apportées par les bases de données NoSQL.

Une approche pragmatique avec un cas d'exemple autour des commandes (panier virtuel) sur les sites de vente en ligne :

*   Beaucoup d'accès en écriture (ajout, suppression d'articles) en ce qui concerne le panier virtuel des internautes
*   Beaucoup d'accès, mais cette fois en lecture, sur le catalogue produit. Les accès en écriture sont moins fréquents et se font du côté Back-Office (administrateurs et/ou batchs)

Autant le catalogue produit peut être mis en oeuvre avec un SGBDR classique (bonne scalabilité pour des accès essentiellement en lecture), autant il est préférable de mettre en oeuvre le panier virtuel à l'aide d'une base NoSQL, par exemple en enregistrant des couples "clé/valeur" où la clé est l'id de l'internaute et la valeur l'id du produit choisi (en référence à l'id dans le catalogue produit). Le lien NoSQL/SGBDR pourra se faire au moment de la validation de la commande.

Les approches sont donc différentes en terme de conception, et c'est là qu'est toute la difficulté lors de la mise en oeuvre d'une base NoSQL :

*   SGBDR : on modélise en fonction du modèle métier de notre entreprise.
*   NoSQL : on modélise en fonction de comment la donnée va être accédée.

Il faut donc retenir que SGBDR et NoSQL peuvent être complémentaires. A chacun son rôle !

## 17h00 - 18h00 : Injecter ou ne pas injecter, CDI est la question, par Antonio Goncalves

[Antonio Goncalves](http://twitter.com/#!/agoncal) nous présente une des nouveautés de la spécification JEE 6 : CDI (Contexts and Dependency Injection - JSR 299).

CDI apporte enfin une standardisation de l'injection de dépendances dans la stack JEE, cela manquait vraiment. Les prémices se faisaient sentir dans JEE5 (@Resource, @PersistenceContext, @EJB ...), CDI va maintenant beaucoup plus loin en apportant un maximum de flexibilité grâce notamment à l'annotation @Inject.

Ce n'est pas la peine que je m'étende plus dans cet article, toutes les infos nécessaires sont déjà en ligne !

*   La présentation d'Antonio : [http://agoncal.wordpress.com/2011/05/23/to-inject-or-not-to-inject-cdi-is-the-question/](http://agoncal.wordpress.com/2011/05/23/to-inject-or-not-to-inject-cdi-is-the-question/)
*   Les tutos sur le blog d'Antonio : [http://agoncal.wordpress.com/2011/04/07/injection-with-cdi-part-i/](http://agoncal.wordpress.com/2011/04/07/injection-with-cdi-part-i/)

![breizhcamp-3]({{ site.url }}/assets/2011-06-24/breizhcamp-3.png)

## Conclusion

Malheureusement je n'ai pas eu le temps d'assister à la présentation de [Julien Dubois](http://twitter.com/#!/juliendubois) (Hibernate vs le cloud computing) ...dommage car l'approche pragmatique de sa présentation a apparemment séduit !  (cf. le [retour](http://blog.courtine.org/2011/06/23/retour-sur-le-breizhcamp/) de [Benoît Courtine](http://twitter.com/#!/bcourtine))

En résumé, une excellente journée, qui j'espère sera renouvelée l'année prochaine !

Prochain grand rendez-vous : le [Jug Summer Camp](http://www.jugsummercamp.org/) à La Rochelle à la rentrée.