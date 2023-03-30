# Matinée

## Manquement fonctionnel

Le problème rencontré hier a débouché ce matin sur la conclusion qu'on s'agissait d'un manquement fonctionnel. En effet, le passage d'une réponse au format texte au format du modèle RAW est inexistante et empêche cette partie de fonctionner.

> Le transtypage dynamique comme par exemple : `as DestinationRawModel` peut ne fonctionner que dans des cas très particuliers.

Ainsi, des tests de transtypage s'imposent.

### Résultats des tests

Après avoir effectué des tests sur l'ensemble des transtypages des tables : `DestinationCategory`, `Module` et `Destination` ; le résultat a démontré que l'erreur ne provenait pas du transtypage mais en réalité de la réponse reçue lors d'une création qui n'est pas l'objet créé dans son entier, mais uniquement son id.

Je m'interroge donc sur la pertinence d'un RAW modèle si la structure des réponses des requêtes change. Il serait intéressant d'envisager un modèle par requête et donc de commencer par les définir en amont.

## Etude d'architecture du système de gestion des données lié au requêtage

Voici un prototype de DAO simplifié :
```ts
async myDataAccessMethod(
	category_id: string,
	index: string,
	module_id: string): Promise<{
		destination_id: number
	}> {
	return await CPBX.query("/create_destination/", {
		category_id: category_id,
		index: index,
		module_id: module_id
	});
}

async myBusinessMethod() {
	let response = await this.myDataAccessMethod("24", "1", "29");
	response.destination_id;
	
	// Nécéssite des traitements supplémentaires pour récupérer 
	// des sous-parties spécifiques
}
```

Cette méthode beaucoup plus spécifique aux requêtes permet d'avoir la main sur l'ensemble des appels qui sont fait, cependant cela peut engendrer des redondances dans le code.
L'une des options serait de créer un modèle séparé pour chaque requête et un Parser de chacun d'entre eux vers un modèle business. Cependant cela engendrerait un travail trop important.

#### Exemple de DAO avec ses tests associés

```ts
import { CPBX } from "../utils/CPBX";

export class ModuleDAO {
	/**
	 * Read specific module
	 * @param id module id
	 * @returns full module JSON response
	*/
	public static async read(id: number): Promise<{
		module_id: number,
		name: string,
		displayname: string,
		has_dialplan: boolean,
		admin: boolean,
		portal: boolean,
		version: string
	}> {
		return await CPBX.query("/module/" + id);
	}
}
```

```ts
import { ModuleDAO } from "./ModuleDAO";

describe("Module DAO", () => {
	test("Read module n° 1", async () => {
		expect(await ModuleDAO.read(1)).toBeDefined();
	});
});
```

Les avantages de cette architecture sont nombreux :
- **Gain de temps** sur l'ensemble du développement.
- **Gain d'intelligibilité** et de maintenabilité inhérent à la simplicité de l'ensemble.
- **Gain de robustesse** grâce à la testabilité immédiate et interopérable.
- **Gain de performance** grâce à l'utilisation métier en avale des plus spécifique.

##### Résultat des tests de l'objet d'accès aux données relatives à la table "Module" :

```
 PASS  src/renderer/data-access-layer/ModuleDAO.test.ts
  Module DAO
    √ Read module n° 1 (2518 ms)
    √ Find module with name="extensions" (52 ms)
    √ Find module with empty name (8 ms)
    √ find EXTENSION_MODULE (1 ms)
    √ find HOTDESKING_MODULE (1 ms)
    √ find ARS_MODULE (1 ms)
    √ find CLASS_OF_SERVICE_MODULE
    √ find CONFERENCE_MODULE
    √ find CUSTOM_DESTINATION_MODULE
    √ find DIAL_RULES_MODULE (1 ms)
    √ find EXTENSION_STATUS_MODULE (1 ms)
    √ find FEATURE_CODE_MODULE
    √ find QUEUES_MODULE
    √ find QUEUES_PRIORITY_MODULE
    √ find RING_GROUP_MODULE (1 ms)
    √ find SPEED_DIAL_MODULE
    √ find VM_GROUP_MODULE
    √ find INBOUND_ROUTE_MODULE (1 ms)
```

On peut noter le gain de temps des requêtes.

# Après-midi

## Création de DAOs

Création de DAO supplémentaires, tel que :

```ts
import { CPBX } from "../utils/CPBX";

export class DestinationDAO {
	/**
	 * Create the specified destination
	 * @param category_id destination category
	 * @param module_id destination module
	 * @param index destination index
	 * @returns JSON response
	 */
	public static async create(
		category_id: number,
		module_id: number,
		index: number
	): Promise<{
		destination_id: number,
	}> {
        if (category_id < 0 || Number.isNaN(category_id)) {
            throw new TypeError("category_id must defined properly");
        }

        if (module_id < 0 || Number.isNaN(module_id)) {
            throw new TypeError("module_id must defined properly");
        }

        if (index < 0 || Number.isNaN(index)) {
            throw new TypeError("index must defined properly");
        }

        return CPBX.query("/create_destination", {
            category_id: category_id.toString(),
            module_id: module_id.toString(),
            index: index.toString()
        });
    }

    /**
     * Read a particular destination entry
     * @param id Destination id
     * @returns JSON response
     */
    public static async read(id: number): Promise<{
        destination_id: number,
        category_id: number,
        module_id: number,
        index: number
    }> {
        return CPBX.query("/destination/" + id);
    }

    /**
     * Destroy and recreate a specified destination
     * @param id Destination id to destroy
     * @param category_id New destination category
     * @param module_id New destination module
     * @param index New destination index
     * @returns JSON response
     */
    public static async update(
        id: number,
        category_id: number,
        module_id: number,
        index: number
    ): Promise<{
        destination_id: number,
        category_id: number,
        module_id: number,
        index: number
    }> {
        await DestinationDAO.delete(id);
        let newResponse = await DestinationDAO.create(category_id, module_id, index);

        return {
            destination_id: newResponse.destination_id,
            category_id: category_id,
            module_id: module_id,
            index: index
        }
    }
  
    /**
     * Delete a particular destination entry
     * @param id Destination id
     * @returns JSON response
     */
    public static async delete(id: number): Promise<void> {
        await CPBX.query("/destroy_destination/" + id);
    }
}
```

Ainsi que ses tests associés :

```ts
import { DestinationDAO } from "./DestinationDAO";

describe("Destination DAO", () => {
    var createdDestinationId: number;

    test("CREATE destination", async () => {
        let response = await DestinationDAO.create(24, 29, 1);
        expect(response).toHaveProperty("destination_id");
        createdDestinationId = response.destination_id;
    });

    test("READ destination", async () => {
        expect(await DestinationDAO.read(createdDestinationId)).toBeDefined();
    });

    test("UPDATE destination", async () => {
        let updatedDestination = await DestinationDAO.update(
	        createdDestinationId, 24, 29, 1);
        expect(updatedDestination).toHaveProperty(
	        "destination_id", createdDestinationId + 1);
        expect(updatedDestination).toHaveProperty(
	        "category_id", 24);
        expect(updatedDestination).toHaveProperty(
	        "module_id", 29);
        expect(updatedDestination).toHaveProperty(
	        "index", 1);
        createdDestinationId = updatedDestination.destination_id;
    });

	test("Delete destination", async () => {
		await DestinationDAO.delete(createdDestinationId);
		// Test non concluant
	});
});
```

On peut remarquer la facilité d'implémentation des besoins métier ainsi que les tests, seules les fonctionnalités de tests inhérentes à la bibliothèque `jest` est source de perte de temps dans des cas particuliers tel que les fonctions asynchrones doublées de la gestion des exceptions.

## Liaison des vues avec les DAOs

Il convient que toute partie doit être interopérable, ainsi les informations propres aux réponses des requêtes sont inconnues du coté des vues. Heureusement, l'absence de modèle métier empêche de franchir cette limite. Cependant, les vues ont besoin de certaines données propres aux DAOs.

Dans ce contexte, différentes architectures peuvent s'envisager, tel que :
- MVC pour "Modèle-Vue-Contrôleur" composé de vues, de modèle et de contrôleur faisant le parallèle entre les deux.
- MVVM pour "Modèle-Vue-Vue-Modèle" composé de vues, de modèles et de vues-modèles faisant le lien entre les deux.
- MVP pour "Modèle-Vue-Présentation" composé de vues, de modèles et de présentations faisant le lien entre les deux.

Tous ces modèles fonctionnent selon le même principe : ils séparent la partie interface-humaine-machine de la partie d'accès aux données à l'aide d'une partie intermédiaire. La différence réside dans cet intermédiaire qui n'a pas tout à fait le même rôle selon l'architecture utilisée.

- Le contrôleur, il écoute les changements des modèles pour informer les vues et met à jour les modèles lors des actions des utilisateurs.
- Le modèle-vue, quant à lui, défini des vues génériques affichants les valeurs d'un modèle.
- La présentation agit comme un parser qui traite les données des modèles pour les fournir à la vue. Elle agit ensuite comme un contrôleur.

### Example de présentation

```ts
class Presenter<V> {
	public Presenter(view: V) {
		this.view = view;
		
		DAO.onUpdate(() => {
			this.view.redraw();
		});
	}
	
	public modifyData(value: any) {
		DAO.update(value);
	}
}

class View {
	private presenter: Presenter<View>;
	
	public View() {
		this.presenter = new Presenter(this);
	}
	
	public redraw() {
		// redraw logic with `modifyData()` event(s)
	}
}
```

De cette façon, la vue n'utilise que des données primaires et aucun modèle "business" n'est nécessaire. Les données transitent entre la présentation et les DAO pour être affichées par la vue.

Si l'on considère l'éventualité que les fonctionnalités de la présentation puisse faire partie intégrante de la vue, cela devient alors une vue-modèle en ce qu'elle permet le lien direct entre la vue et le traitement des données. C'est l'architecture que j'ai plus ou moins utilisé jusqu'à maintenant, les nœuds spécifiques à une fonctionnalité du CPBX sont définis en utilisant des sous-vues génériques intégrant les évènements logiques nécessaires aux actions de l'utilisateur dans n'importe quel contexte.

### Observation sur l'usage des requêtes préparées

Bien que leur usage puisse être tout à fait justifié, l'utilisation de requêtes préparées peut empêcher des traitements plus spécifiques tel que ceux nécessitant l'accès à la donnée fondamentale des dites requêtes préparées. Un usage plus approprié voudrait qu'on ne stock que ces informations permettant de préparer d'éventuelles requêtes ultérieurement. De cette façon, l'information atomique est connue et disponible dans l'ensemble du code et le besoin des requêtes préparées est également respecté.

# Fin de journée