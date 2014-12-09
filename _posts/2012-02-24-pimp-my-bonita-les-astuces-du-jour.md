---
layout: post
title: Pimp My Bonita - Les astuces du jour
date: 2012-02-24 18:51:48.000000000 +01:00
categories:
- Tutoriels
tags:
- Bonita
- BPM
- groovy
- PimpMyBonita
status: publish
type: post
published: true
author:
comments: true
---

J'occupe actuellement mes heures perdues avec le challenge "[Pimp My Bonita](http://www.bonitasoft.org/blog/contests/pimp-my-bonita/)" !

C'est l'occasion d'alimenter mon blog avec quelques astuces que j'ai utilisées dans le cadre du challenge.

Menu du jour :

* Récupérer la liste des utilisateurs d'un groupe
* Exécution de tâches conditionnelles en parallèle

J'espère avoir assez de temps pour proposer les fruits de mon travail. Rendez-vous au plus tard le 15 mars pour plus de détails !

![pimp-my-bonita]({{ site.url }}/assets/2012-02-24/pimp-my-bonita.gif)

## Récupérer la liste des utilisateurs d'un groupe

L'objectif est de pouvoir alimenter une combobox avec la liste des utilisateurs d'un groupe.

Rien de plus simple, grâce à un connecteur Groovy et à l'IdentityAPI de Bonita :

{% highlight groovy %}
import org.ow2.bonita.facade.identity.User;
import org.ow2.bonita.facade.identity.Group;
import org.ow2.bonita.facade.identity.Role;
import org.ow2.bonita.facade.IdentityAPI;

// Get IdentityAPI
IdentityAPI identityAPI = apiAccessor.getIdentityAPI()

// Use IdentityAPI to get the ORDERERS group (platform/ORDERERS)
Group ordererGroup = identityAPI.getGroupUsingPath(["platform", "ORDERERS"])

// Get the list of orderers !
List<User> orderersList = identityAPI.getAllUsersInGroup(ordererGroup.getUUID())

// Fill the map with the list content
Map<String, String> orderersMap = [:]
orderersList.each {
    orderersMap.put(it.getUsername(), it.getUUID())
}

return orderersMap
{% endhighlight %}

La Map retournée par le script Groovy est ensuite stockée dans une variable de processus de type Java Object (java.util.Map), qui sera utilisée au niveau du formulaire pour alimenter les données de la combobox.

![orderers-list]({{ site.url }}/assets/2012-02-24/orderers-list.png)

Pour info, le HTML généré est le suivant :

{% highlight html %}
<select>
    <option value="502990d4-fad1-4d8f-82f6-f5df1c48b506">orderer1</option>
    <option value="4b36d8b8-c694-4e8d-988d-e13817856f3e">orderer2</option>
</select>
{% endhighlight %}

Le fait d'utiliser une Map permet d'avoir des valeurs différentes pour les labels et values des `<option>` du `<select>` HTML.

J'ai d'ailleurs contribué un exemple à ce sujet : [http://www.bonitasoft.org/exchange/extension_view.php?eid=285](http://www.bonitasoft.org/exchange/extension_view.php?eid=285)

## Exécution de taches conditionnelles en parallèle

La problématique est la suivante :

* Un utilisateur saisit une demande. Dans la saisie, il précise s'il y a un impact ou non en terme de sécurité.
* S'il y a un impact, des consignes de sécurité doivent être saisies.
* La réalisation de la demande et la saisie des consignes sont exécutées en parallèle (par des personnes de groupes différents)
* La demande ne peut être clôturée que si la saisie de l'impact a été faite (s'il y avait un impact bien entendu)

Voici une façon de faire, à l'aide de portes AND et XOR :

![]({{ site.url }}/assets/2012-02-24/executionconditionnelleparallele_1-0.png)

## Conclusion

Suite au prochain épisode ! :-)
