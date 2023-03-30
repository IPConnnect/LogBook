# Matinée

## Nœud de file d'attente

Ajout des champs.

## Récapitulatif des tâches

### DAO

- ✅ **IVR**: Particulièrement complexe en ce qu'il permet de multiplier les points d'origine.
	- ✅**IVR entry** : Association entre un IVR et une destination.
	- **IVR option** : ?
- ✅**Queue** : Modérément complexe dans la mesure où il est associé à des extensions, mais la DAO des extensions est terminée. Peut être liée à un IVR.
	- **Queue member** : Les extensions liés à un IVR
- ✅**RingGroup** : Possède une liste d'extensions, doit donc être affiché en conséquence et de la même manière que pour la liste d'attente.
- ✅**TimeCondition** : Très simple mais référence un time group.
- **InvalidTries** (facultatif) : Utilisé par ?
- **Strategy** (facultatif) : Utilisé par la file d'attente.

### Vues modèle & leur DAO associé

- **Recording** : Lié à plusieurs éléments, mais nécessite l'accès à des fichiers d'enregistrement. Sa configuration n'a peut être pas sa place dans cet éditeur.
- **Device** : Utilisé par les extensions, n'est pas particulièrement nécessaire pour le moment mais pourra l'être plus tard dans la mesure où il empêche de configurer de nouvelles extensions au démarrage.
- **ClassOfService** : Utilisé par plusieurs nœuds mais peut se remplacer par l'usage de l'indexe 1.
- ✅**TimeGroup** (facultatif) : Utilisé par les time condition, utile dans la finalité mais pas urgent.
- **Conference** (facultatif) : Nœud important dans le contexte métier mais pas obligatoire dans ce prototype pour le moment.

### Correction de bogues

Problème de suppression des liens, trop de liens sont supprimés.

## Pistes d'amélioration

Suite à une courte entrevue avec le chef de projet, voici quelques pistes d'amélioration prioritaires :

- **Améliorer le Canvas**
	- Fonction de Zoom ()
	- Fonction de défilement ()
- **Nœuds**
	- Supprimer ()
	- Créer ou sélectionner dans la liste des existants ()
- **Routes entrantes**
	- Créer ()
	- Supprimer ()