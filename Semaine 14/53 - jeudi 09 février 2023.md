# Matinée

- Remplacement des requêtes préparées par des énumération des champs recherchables.
	- Et création des tests associés
- Création d'une méthode de mise à jour des routes entrantes.
	- Amélioration de l'évènement de validation des champs textuels.
	- Ajout d'un bouton de sauvegarde des données modifiées.
		- Modification du style des boutons iconographiques pour pouvoir être désactivés.

# Après-midi

- Correction du bug des requêtes FIND ne testant pas correctement le fait que la réponse retourne un tableau d'objet et pas un objet unique.
- Ajout de la logique de création d'un nouveau lien entre deux nœuds.
- Modification des points et des liens pour pouvoir définir si un lien est supprimable s'il est lié à un point modifiable ou non.
- Extraction et isolation de l'algorithme de création d'un nœud dans la vue modèle du Dial Plan.
- Ajout des catégories de destination manquantes.
- Ajout de la callback `onModifyData` aux nœuds du Dial Plan.
- Création de l'algorithme de création des nœuds liés à la route entrante chargée lors de sa sélection dans la liste.
- Ajout de l'objet d'accès aux données de la table "Annonces".
- Ajout du `CTRL+S`.
- Ajout des champs du nœuds des extensions.

> :luc_info: Il manque la fonction DELETE (et UPDATE) des annonces dans l'API.

# Fin de journée

## Notes sur l'avancement

Il manque de nombreuses DAO, celle des extensions lui manque beaucoup de méthodes et de nombreuses sous-tables utilisées pour le peuplement de champs "select" sont également à envisager prochainement.

### Pour les extensions par exemple :

- **ExtensionDAO** : CREATE, UPDATE, DELETE
	- **DeviceDAO** : ⚠️ Inexistante
		- *DeviceTechnologyDAO* : ⚠️ Inexistante :luc_arrow_right: Facultatif
		- *DeviceProfileDAO* : ⚠️ Inexistante :luc_arrow_right: Facultatif
	- **RingTimeDAO** : ⚠️ Inexistante
	- **ClassOfServiceDAO** : ⚠️ Inexistante