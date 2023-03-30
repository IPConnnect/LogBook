# Neuvième jour

## Accès SSL

Après avoir installé un certificat sur mon serveur apache en local et obtenu un nom de domaine à partir d'un DNS pour l'autocom et l'application WEB; j'ai réalisé des tests dans l'espoir que les problèmes proviennent de la sécurité de la connexion.

Il s'avère que le problème ne vient pas de là.

Par ailleurs, j'ai cloné à partir de la branche principale, je vais utiliser la dernière release à la place.

J'ai réussi à faire fonctionner la librairie avec NPM et Webpack, en fournissant un fichier .mjs à webpack, il est ensuite capable de générer un fichier javascript contenant l'ensemble des dépendances NPM pouvant tourner sur le web (à condition qu'il soit hébergé sur un serveur).

Cependant, il semble que le problème ne venait pas de la différence entre la dernière release et la branche principale.