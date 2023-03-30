# Matinée

## Réparation des DAO

Certaines DAO ont généré des erreurs, à l'aide des tests unitaires, j'ai pu identifié d'où venait le problème. J'en ai profité pour améliorer les tests en remplaçant les fonctions susceptibles de bloquer dans certains cas.

> Dans l'idéal, un Mock de la base de données devrait être réalisé pour que les tests soient indépendants, mais le temps nécessaire à ce chantier ne m'est pas alloué.

## Time Group & Time condition DAO, tests & nœuds

Création de tout le nécessaire…

# Après-midi

## Réalisation d'un système de placement des nœuds

J'ai ajouté un algorithme dans le constructeur de nœuds permettant de déplacer les nœuds liés à coté du point auquel ils sont lié. Cela permet de créer rapidement une hiérarchie lisible évitant aux utilisateurs de manipuler trop régulièrement et ainsi améliorer l'expérience utilisateur.

![[Pasted image 20230216163257.png]]

