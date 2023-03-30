# Matinée

## Continuation de l'étude architecturale

La partie requêtage de l'application peut subir des modifications pour y intégrer un modèle, le soucis, c'est que toutes les requêtes n'ont pas besoin des champs du model, cependant ils ont besoin d'un modèle vide pour le remplir.

> Etant donné qu'il n'y a pas d'alternative, la création d'un modèle complet reste la meilleure option.

### Est-ce que cette méthode est autant un gain de temps que de maintenabilité ou, dans le cas contraire, le coup est-il pertinent ?

Les méthodes parsing des fichiers JSON vers des modèles Fichier et Business sont à la fois pratiques et lourds d'utilisation. Cependant, ces deux méthodes une fois implémentées, évite une redondance certaine.

### Problème rencontré avec la lecture de plusieurs entrées

Le système automatisé ne peut pas prévoir une réponse multiple comme un tableau. Cette méthode est inutilisable dans ce contexte et nécessite une approche totalement différente.

### Solution envisageable

En abandonnant l'idée de parser le résultat des requêtes, il est possible de prédire au cas-par-cas les différents types de réponses tout en gardant un usage du Parser. En effet à l'aide de la transpilation proposée par JavaScript, il est possible de passer d'un objet JSON de type `any` en modèle préconfiguré avec le mot clé `as`.

De cette façon, le problème est réglé sans porter atteinte à la simplicité et l'interopérabilité de l'ensemble.

## Traitements en cascade

L'API coté backend prends déjà en charge les appels en cascade, il est donc nécessaire de n'effectuer que le strict nécessaire coté frontend.

Les destinations sont spécifiques à une origine et son détruites en même temps qu'elle.
Les catégories et les modules sont lisibles uniquement.

### Description des besoins :

- **Destination** :
	- *Créer* en même temps qu'une origine (inbound route).
	- *Supprimer* et *recréer* lors de la mise à jour (entraîne des IDs longs).
- **Catégorie de destination** :
	- *Récupérer* chacune des catégories.
- **Module** :
	- *Récupérer* chacun des modules.
- **IVR** :
	- *Créer* avec sa/ses destinations.
	- *Modifier* les champs.
	- *Supprimer*.
- **Files d'attente** :
	- *Créer* avec sa destination.
	- *Modifier* avec ses agents.
	- *Supprimer*.
- **Groupe des sonnerie** :
	- *Créer* avec sa destination
	- *Modifier*
	- *Supprimer*.
- **Condition de temps** :
	- *Créer* avec ses destinations
	- *Modifier*
	- *Supprimer*
- **Annonces** :
	- *Créer* avec sa destination.
	- *Modifier*
	- *Supprimer*
	- (Faire attention au custom recording)
- **Mode nuit** :
	- *Créer* avec ses destinations
	- *Modifier*
	- *Supprimer*
- **Extension** :
	- *Créer* avec son device (voir comment faire) ou ne pouvoir que modifier
	- *Modifier*
	- *Supprimer*
- **Destination personnalisée**
	- *Créer*
	- *Modifier*
	- *Supprimer*
- **Classe de service** :
	- *Créer* avec sa destination.
	- *Modifier*
	- *Supprimer*

> Aucun des besoins listés ci-dessus n'admet avoir besoin de l'ensemble des méthodes CRUD.

## Après-midi

### Recherche sur le stockage du code source

https://www.electronjs.org/fr/docs/latest/tutorial/asar-archives

### Continuité du système d'accès aux données

Dans la continuité de ce qui a été fait ce matin, mon étude vise à améliorer la structure toujours plus grandissante du système d'accès aux données (ou "data-access-layer").

#### Observation structurelle mêlant fichiers et dépendances

Suite à mon observation relative aux besoins métier stricts, j'ai également étudié la pertinence d'une architecture différente au sein même des fichiers dans l'optique de gagner en maintenabilité tout en évitant une perte de temps.

En effet, la norme selon laquelle chaque classe, interface ou énumération doit être dans son fichier respectif n'est pas d'actualité en "Typescript". Il est donc intéressant de se demander comment organiser les fichiers dans ce projet. Ainsi, un nouveau concept viens s'ajouter lors de la conception architecturale : le contexte fichier.
Celui-ci entends regrouper plusieurs concepts interdépendants.
Dans notre projet, le contexte fichier est le "data-access-layer" lui-même qui, dans sa forme abstraite, réunis des classes et des interfaces totalement interdépendantes.

Cela donne ainsi le fichier `dataAccessLayer.ts` suivant :
```ts
// Interfaces models
export interface Model {
    id?: number
}

export interface RawModel extends Model {}
export interface BusinessModel extends Model {} 

// Interfaces CRUD
export interface IAllReadable<B extends BusinessModel> {
    readAll(): B[];
}

export interface ICreatable<B extends BusinessModel> {
    create(model: B): B;
}

export interface IDeletable {
    delete(id: number): void;
}

export interface IReadable<B extends BusinessModel> {
    read(id: number): B;
}

export interface IUpdatable<B extends BusinessModel> {
    update(id: number, model: B): void;
}

// Data-access-object abstract class
export class DAO {}
```

Vient s'ajouter ensuite chaque implémentation de ces classes et en particulier le DAO qui a une place centrale dans cette situation. Par exemple voici le contenu du fichier `module.ts` :

```ts
import { CPBX } from "../../utils/CPBX";
import { BusinessModel, RawModel, DAO } from "../dataAccessLayer";

// Models
export interface ModuleBusinessModel extends BusinessModel {
    name: string
}

export interface ModuleRawModel extends RawModel {
	name: string
}

// Data access object
export class ModuleDAO extends DAO {
    async find(name: string): Promise<ModuleBusinessModel> {
        for (const response of await CPBX.query("/modules")) {
            let model = response as ModuleBusinessModel;
            if (model.name === name) {
                return model;
            }
        }

        return null;
    }
  
    public EXTENSION_MODULE = this.find("extensions");
    public HOTDESKING_MODULE = this.find("hot_desking");
}
```

Cette méthode très simple, permet un gain de temps et d'espace qui fera la différente lorsque le projet prendra de l'ampleur. L'équilibre entre intelligibilité et complexité est bonne.

#### Mise en place de cette nouvelle structure

J'ai généralisé l'utilisation de cette architecture avec les catégories, les destinations, les modules et les catégories de destination. Le développement était facile et rapide, j'ai seulement eu un bug sur lequel j'ai buté en fin de journée, au final il s'agissait d'un bête erreur dans un des Parser de modèle. La partie des tests unitaires est également facilité car effectuée de concert avec le reste.