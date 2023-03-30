# Matinée

Fini ce matin :
- Classe Lien
- Mise à jour de la position du lien avec le canvas

# Après-midi

Nouvelles tâches :
- Finaliser le point
- Finaliser le lien
- Ajouter le SVG dans le canvas

Finalisé !

Nouvelles tâches :
- configurer le conditionnement des liens
- ajouter des conditions de base des liens
- ajouter des callbacks pour généraliser
- réparer le problème des liens cachés par les noeuds
- créer un noeud d'exemple
- basculer la partie métier au plus haut niveau (root.tsx)
- ajouter la gestion de la modification des noeuds
- Ajout de fleches courbées

Terminé !

Nouvelles tâches :
- Modifier les Tables -> FAIT
- Gêrer la selection d'une route entrante -> FAIT
- Créer un noeud par concept VPBX
  - InboundRoute -> FAIT
- Ajouter les "key"

> Problème rencontré avec la callback de création et de destruction de lien.

Comment déterminer à quoi corresponds chaque lien ?
- En utilisant une map et l'identifier des points
- En insérant l'objet dans le point

Comment déterminer le type d'objet ?
- En vérifiant si les champs existent
  - Les champs de deux objets peuvent être équivalents
- En utilisant des classes
  - Possible mais un peu lourd à développer
- En précisant le type
  - Le type ne peut être qu'un string ce qui peut rendre difficile la maintenabilité
    - Utiliser un champ type de type string
      - deux types peuvent être les mêmes
    - Utiliser une enum
      - Comment l'appeler et où la mettre ?
        - Dans root.tsx et DialPlanType
      - peut interopérable ?
        - Négligeable ?