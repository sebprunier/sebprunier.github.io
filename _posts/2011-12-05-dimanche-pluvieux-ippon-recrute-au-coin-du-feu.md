---
layout: post
title: Dimanche pluvieux, "Ippon Recrute" au coin du feu !
date: 2011-12-05 15:48:19.000000000 +01:00
categories:
- Fun
- News
tags:
- ippon
- java
- recrutement
status: publish
type: post
published: true
author:
comments: true
---

La société "Ippon Technologies" a lancé vendredi soir dernier la deuxième version de son jeu de recrutement "Ippon Recrute" !

Pour ceux qui sont passés à côté, c'est ici : [http://blog.ippon.fr/2011/11/30/jeu-ippon-recrute-deuxieme-edition/](http://blog.ippon.fr/2011/11/30/jeu-ippon-recrute-deuxieme-edition/)

Franchement, félicitations, le moment ne pouvait pas être mieux choisi ! Lancer le jeu juste avant un week-end pluvieux, rien de mieux :-) Donc - passez-moi l'expression - "dimanche pluvieux, Ippon Recrute au coin du feu" !

Comme pour le jeu proposé par [Pod Programming](http://www.pod-programming.com/ "Pog Programming") il y a quelques mois, je me suis amusé un peu avec le jeu Ippon. Petit article donc.

**Attention, la suite de l'article dévoile les solutions ! N'allez pas plus loin si vous voulez vous amusez un peu vous aussi !** :-)

![Nuvola_filesystems_file_important]({{ site.url }}/assets/2011-12-05/Nuvola_filesystems_file_important.png)

## Forkons, Forkons (etc ...)

Pour commencer, un petit fork :

*   Mon fork : [https://github.com/sebprunier/IpponRecrute](https://github.com/sebprunier/IpponRecrute)
*   L'original : [https://github.com/ippontech/IpponRecrute](https://github.com/ippontech/IpponRecrute)

Première étape du jeu, lancer l'appli ! (Je vous fait grâce de l'import du projet Maven dans l'IDE - Eclipse en ce qui me concerne, et de l'ajout du projet dans Tomcat 7).

Allez c'est parti ! Amusons-nous un peu : [http://localhost:8080/ippon-recrute/](http://localhost:8080/ippon-recrute/)

Et là, BAM, ca commence, nous voilà dans le vif du sujet :

    org.jasypt.exceptions.EncryptionOperationNotPossibleException
    org.jasypt.encryption.pbe.StandardPBEByteEncryptor.decrypt(StandardPBEByteEncryptor.java:981)
    org.jasypt.encryption.pbe.StandardPBEStringEncryptor.decrypt(StandardPBEStringEncryptor.java:717)
    org.jasypt.util.text.BasicTextEncryptor.decrypt(BasicTextEncryptor.java:112)
    fr.ippon.rh.service.Etape2Service.decriptText(Etape2Service.java:86)
    fr.ippon.rh.web.IpponController.etape3(IpponController.java:33)


## Départ pour Devoxx France, ou l'algorithme du sac à dos

Après une courte analyse, on arrive rapidement à la problématique posée, permettant de résoudre l'étape 2. Il faut trouver un mot de passe en apportant une solution au problème suivant :


>Vous partez à Devoxx France, et votre sac peut contenir pour 30 Kg d'objets.
>
>Malheureusement, vous voulez emporter de nombreux objets: votre iPhone, votre iPad, votre MacBook Pro, votre exemplaire de "Spring par la pratique" à faire dédicacer...
>
>Tous ces objets n'ont pas le même poids et n'ont pas la même valeur: votre "Spring par la pratique" est plus lourd que votre iPhone, et a moins de valeur.
>
>Vous cherchez donc à optimiser ce que vous allez emporter: quels objets devez-vous prendre pour avoir le maximum de valeur dans votre sac ?
>
>Voici la liste des objets, avec leur poids et leur valeur. Le mot de passe recherché est la concaténation des ids (triés par ordre croissant) de tous les objets à emporter :
>
>ID  valeur  poids
>
>1   737     7
>
>2   842     4
>
>3   158     1
>
>4   36      28
>
>5   578     26
>
>...


Première  idée (attention, #troll :-)) : il suffit de ne pas prendre "Spring par la pratique" - objet lourd et de faible valeur (le 4 à priori). Je tente donc le mot de passe suivant : 1235678910111213141516171819202122232425262728293031323334353637383940. Echec ! :-)

Plus sérieusement, il s'agit ici d'un problème connu : Le Problème du Sac à Dos

*   Plus de détails ici : [http://fr.wikipedia.org/wiki/Problème_du_sac_à_dos](http://fr.wikipedia.org/wiki/Probl%C3%A8me_du_sac_%C3%A0_dos)
*   Et une solution possible expliquée ici (celle que j'ai utilisée) : [http://www.siteduzero.com/tutoriel-3-95368-introduction-a-la-programmation-dynamique.html](http://www.siteduzero.com/tutoriel-3-95368-introduction-a-la-programmation-dynamique.html#ss_part_3)

![Knapsack]({{ site.url }}/assets/2011-12-05/Knapsack.svg)

Il y a deux types de solutions pour ce problème : des solutions approchées et des solutions exactes.

### Algorithme Glouton

J'ai d'abord essayé une solution approchée, l'algorithme glouton. L'idée est simple : on ajoute d'abord les objets ayant le plus de valeur, jusqu'à saturation du sac. La qualité de cet algorithme est souvent médiocre, dans notre cas il ne permet pas de trouver la bonne solution. A n'utiliser que si vous partez à DevoxxFrance précipitemment ;-)

Voici un extrait de l'algorithme, porté en Java (le code complet est sous GitHub, dans la classe [Etape2Glouton.java](https://github.com/sebprunier/IpponRecrute/blob/master/src/main/java/fr/ippon/rh/support/Etape2Glouton.java)) :

{% highlight java %}
Arrays.sort(DEVOXX_OBJECTS);
Integer total_weight = 0;
String result = "";
for (int i = 0; i < DEVOXX_OBJECTS.length; i++) {
    DevoxxObject devoxxObject = DEVOXX_OBJECTS[i];
    if (devoxxObject.weight + total_weight <= MAX_WEIGHT) {
        total_weight += devoxxObject.weight;
        result += devoxxObject.id;
    }
}
{% endhighlight %}

Cet solution permet de remplir le sac à dos (30 kg) avec les objets 25 et 29, pour une valeur totale de 1857 euros. Mais "2529" n'est pas le bon mot de passe, on peut donc faire mieux !

### Algorithme Dynamique

D'autres solutions existent pour résoudre le problème du sac à dos. Notamment la programmation dynamique, qui apporte une solution exacte au problème, et qui s'adapte bien dans notre cas pour les raisons suivantes :

*   le problème est de petite taille (40 objets pour une capacité de 30 kg),
*   les données du problème, en particulier le poids des objets, sont des entiers.

L'idée générale est de décomposer le problème en sous-problèmes, sachant que la solution optimale du problème d'origine peut être construite à partir des solutions optimales des sous-problèmes.

Dans notre cas, on fera varier le nombre total d'objets (i de 1 à 40) et la capacité du sac à dos (j de 0 à 30) afin d'établir les sous-problèmes. On détermine ainsi une matrice M[i][j] contenant les solutions optimales de l'ensemble des sous-problèmes. Un parcours de la matrice (en partant de la dernière case) permet enfin de trouver les objets donnant la solution optimale.

Voici des extraits de l'algorithme (le code complet est sous GitHub, dans la classe [Etape2DynamicProg.java](https://github.com/sebprunier/IpponRecrute/blob/master/src/main/java/fr/ippon/rh/support/Etape2DynamicProg.java)).

Alimentation de la matrice :

{% highlight java %}
for (int i = 1; i < DEVOXX_OBJECTS.length; i++) {
    for (int j = 0; j <= MAX_WEIGHT; j++) {
        if (DEVOXX_OBJECTS[i].weight > j) {
            M[i][j] = M[i - 1][j];
        } else {
            M[i][j] = Math.max(
                M[i - 1][j],
                M[i - 1][j - DEVOXX_OBJECTS[i].weight] + DEVOXX_OBJECTS[i].value
            );
        }
    }
}
{% endhighlight %}

Recherche du résultat :

{% highlight java %}
while (j > 0) {
    while (i > 0 &amp;&amp; M[i][j] == M[i - 1][j]) {
        i--;
    }
    j = j - DEVOXX_OBJECTS[i].weight;
    if (j >= 0) {
        result += DEVOXX_OBJECTS[i].id + "|";
    }
    i--;
}
{% endhighlight %}

Cet solution permet de remplir le sac à dos (30 kg) avec les objets 1, 2, 16, 26, 27, 28, 29 et 39, pour une valeur totale de 5463 euros. C'est à priori la bonne solution !

## Quoi de mieux qu'un Sudoku au coin du feu ?

Nous voici donc arrivé à l'étape 3, avec un nouveau mot de passe à trouver ! Cette fois-ci, c'est une grille de Sudoku qu'il faut résoudre. La solution correspond aux lettres qui seront positionnées sur la ligne #FF0000, donc la ligne rouge :-)

![ippon-etape3]({{ site.url }}/assets/2011-12-05/ippon-etape3.png)

Trois solutions s'offrent à moi :

*   Résoudre la grille à la main,
*   Ecrire un programme qui va résoudre ma grille automatiquement (Programmation Linéaire, Algorithme Simplexe),
*   Utiliser un programme déjà existant qui va me permettre de résoudre la grille en un clic.

Comme dans notre métier on essaie de ne pas continuellement réinventer la roue (solution 2), et comme je ne suis pas un adepte du jeu Sudoku (solution 1), j'ai donc choisi la solution 3 !

Google est notre ami et nous amène directement sur le site : [www.top-sudoku.com](http://www.top-sudoku.com/sudoku/fr/rentrer-un-enonce-sudoku.php) ! La seule difficulté consiste à faire le mapping entre les lettres (A, B, C, D, E, G, H, I, J ... tient, où est passé le F ?) et les chiffres 1 à 9. Et voila là solution :

![sudoku-ippon]({{ site.url }}/assets/2011-12-05/sudoku-ippon.png)

## Que la Force (Brute) soit avec toi !

Donc ca y est, nous voici à l'étape finale (étape 4) ! Que nous dit cette étape ? :

> En fait vous pouviez arriver directement à cette étape en utilisant une méthode "brute force", par exemple en prenant comme principe que le mot-clef "ippon" devait bien se trouver quelque part dans le texte à décoder, et en testant tous les mots de passe possibles, sachant que notre méthode de cryptage utilise uniquement des caractères ASCII.

Personnellement, je n'avais pas pensé immédiatement à tenter le méthode "force brute" puisque "top-sudoku.com" m'a permis de résoudre l'étape 3 en quelques secondes ;-)

Réfléchissons tout de même un peu ... Quelques remarques importantes, déduites de la règle du jeu du Sudoku :

*   Le mot de passe fait 9 caractères
*   Le mot de passe contient les caractères A, B, C, D, E, G, H, I, J
*   Il n'y a pas de répétition de lettre dans le mot de passe.

Le nombre de mots de passe possible est 362880. En effet, il s'agit d'un [arrangement sans répétition](http://fr.wikipedia.org/wiki/Combinatoire#Arrangements_sans_r.C3.A9p.C3.A9tition "Arrangement sans répétition") A(k,n), où k=n=9, donc le nombre de combinaisons est 9! = 362880.

Voici un petit programme récursif permettant de déterminer l'ensemble des mots de passe :

{% highlight java %}
public static void permute(String sStart, String sEnd, Set<String> passwords) {
    if (sEnd.length() <= 1) {
        passwords.add(sStart + sEnd);
    } else {
        for (int i = 0; i < sEnd.length(); i++) {
            String newString = sEnd.substring(0, i) + sEnd.substring(i + 1);
            permute(sStart + sEnd.charAt(i), newString, passwords);
        }
    }
}
{% endhighlight %}

Pour notre cas concret, il suffit de faire l'appel suivant :

{% highlight java %}
Set<String> passwords = new HashSet<String>();
permute("", "ABCDEGHIJ", passwords);
{% endhighlight %}

Ensuite, on peut rechercher le bon mot de passe en les testant un-à-un, en vérifiant la présence de la chaîne "ippon" dans le texte décrypté :

{% highlight java %}
for (String password : passwords) {
    try {
        BasicTextEncryptor textEncryptor = new BasicTextEncryptor();
        textEncryptor.setPassword(password);
        String decryptedText = textEncryptor.decrypt(etape4Texte);
        if (decryptedText.contains("ippon")) {
            return password;
        }
    } catch (Exception e) {
        // Bad password !
    }
}
{% endhighlight %}

Le code complet est sur GitHub, dans la classe [ForceBrute.java](https://github.com/sebprunier/IpponRecrute/blob/master/src/main/java/fr/ippon/rh/support/ForceBrute.java).

## Conclusion

L'exemple par "force brute" fonctionne et permet de trouver le bon mot de passe en moins de 2 minutes. On pourrait cependant faire beaucoup mieux (et plus joli, pour répondre à une des exigence du jeu :-)) ! Quelques idées :

*   Utiliser le framework fork/join de Java 7 pour la recherche du bon mot de passe (cela serait utile, voire impératif, sur de plus gros volumes)
*   Regarder du côté de la programmation fonctionnelle, qui semble plus adaptée pour ce genre d'exercice.

Quand j'aurais un peu de temps libre, j'essaierais :-)

En tout cas merci à Ippon Technologies qui nous permet de nous amuser un peu et qui recrute de façon originale !