# Matinée

- Création de l'objet d'accès aux données des appareils.
- Ajout de l'update dans le champs texte du nœud extension.
- Création d'un dialogue pour sélectionner l'extension à ajouter aux nœuds.
	- Modification de la méthode de création des nœuds en conséquence.

Champs obligatoires d'une extension :
- dial
- nom
- email
- ringtime
- class of service
- cid
- passwoord
- voicemail : boolean

## Problème de design

Etant donné que je n'ai réalisé aucune maquette, je me heurte à présent à une difficulté liée au design de l'interface utilisateur en lien avec le fonctionnement du CPBX.

En effet, la gestion des destinations semble plus complexe que prévu car il est possible de modifier une entrée ou de la sélectionner dans la liste.

Une destination est composée :
- D'une catégorie : Défini le type de destination (extension, IVR...).
- D'un module : Défini l'origine de la destination (route entrante).
- D'un index : Défini quelle entrée de la catégorie est utilisée (extension n° 1...).

> ⚠️ Une route entrante doit obligatoirement avoir une destination.

Donc un lien entre une route entrante et une extension par exemple, créerait une destination de catégorie extension et dont l'index serait définissable dans un menu déroulant.

Cependant, si l'on souhaite créer une extension OU MODIFIER !

### Problème avec la modification d'une destination

Il est possible d'afficher une fenêtre pour proposer à l'utilisateur si il souhaite créer une nouvelle entrée ou en modifier une existante.

De cette façon les modifications et les créations fonctionnent de la même manière, et le système de nœud est utilisé à son plein potentiel.

#### Par conséquent, une destination est-elle liée à une route entrante ou à un nœud de destination ?

Un nœud de destination possède un index associé à son type de destination. La relation entre la route entrante et cet index est gérée au moment de la modification d'un lien.

#### Teste concluant

J'ai essayé ce design avec les extensions et ça fonctionne correctement, le noeud réussi à récupérer les informations nécessaires et le DialPlan peut configurer sa création en lui envoyant un index créé ou lu en amont.

> Pour cette version "prototype", les autres destinations sélectionneront l'index 1 par défaut.

Il reste a créer les DAOs et les champs des nœuds manquants et cette version sera terminée.

# Après-midi

- Création de NightModeDAO et ses tests associés.
- Création des champs du nœud "mode nuit".
- Création de deux vues-modèles de points pour la destination et l'origine.

## Observation autour des points des nœuds

Les points des nœuds définissent un comportement leur permettant de valider à quoi ils peuvent être reliés, le soucis, c'est que ce comportement est susceptible de changer lorsque de nouveaux nœuds sont créés.

La solution est de créer des classes pour chacun de ces nœuds.

### Amélioration du système de création des nœuds

Pour l'instant le Canvas assure la création des nœuds de destination uniquement puisque c'est les seuls que l'utilisateur peut créer, mais il est possible de généraliser cette méthode à l'ensemble des nœuds métier. Cela permettrait de construire les sous-nœuds directement à la création du parent, ce qui serait plus logique d'un point de vue interopérabilité.