# Matinée

Absent.

# Après-midi

Mise à jour des APIs manquantes :
- Announcements : CREATE, READ & READALL présent mais non mentionnés dans le google-doc.
- RingGroups Members : Remove, READ, CREATE, READ ALL présents mais non mentionnés.
- Customs destinations : Tout est présent rien n'est mentionné.

Reste à faire :
- Retirer Electron
- Faire les DAOs manquantes :
  - RingGroup
  - Annonces
  - TimeConditions
- Faire les tests unitaire des DAOs créés précédemment :
  - RingGroup
  - Annonces
  - TimeConditions
- Ajouter les champs dans les noeuds

## Etude architecturale

De sorte à améliorer l'architecture de la partie "Input/Output" de l'application, j'ai réalisé une études sur le découpage structurel des différents modèles et de la redondance éventuelle de leur traitement.

Il s'avère que la partie visant à passer d'un réponse au format JSON à un format Typescript ne peut être généralisé à cause de la structure du système de requêtage. Les deux automatisations ne sont pas interopérable et il vaut mieux intégrer l'ensemble pour les mêler dans un seul traitement manuel.

Il est cependant peut etre possible de créer un système basé sur les requetes indépendamment des appels en cascade.

> La partie de lancement des requetes est redondante, elle est toujours composée d'un token et d'un modèle JSON, la possibilité d'automatiser cette partie en utilisant un champ token et un champ FileModel peut être envisagé.

`cd /usr/share/ombutel/www/api`
`cpbxapi -q class_of_services`
`ls *ars*`