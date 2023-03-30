# Matinée

## Aide d'Olivier

Olivier m'a suggéré que mon erreur provenait d'une mauvais utilisation de ma part de React. En effet, l'état en React ne doit pas être directement modifié.

Donc si je modifie l'ensemble de mes changement d'état dans React, ça devrait fonctionner.

## Projet React-TS

J'ai créé un nouveau projet React comme précisé dans l'entrée du journal précédente.
Ce projet est l'occasion de :
- Retirer Electron
	- Améliorer de fait le code React
- Simplifier l'architecture et le code source

# Après-midi

## Implémentation

Implémentation des vues du Dial Plan dans le nouveau projet

La gestion de liens de manière nodal est compliqué pour juste éviter de restreindre la fenêtre en haut et à gauche.

La gestion de la suppression des liens exige de stoquer les nœuds liés au nœud correspondant

La gestion des liens temporaires doit se faire avec une méthode d'ajout/suppression non spécifique

La gestion du zoom doit être pensée en amont

Débugger la partie asynchrone/ react chiante