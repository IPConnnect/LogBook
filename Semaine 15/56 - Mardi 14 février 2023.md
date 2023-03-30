# Matinée

## IVR DAO & ses testes associés

Pas de difficulté particulière, les tests se voient améliorés par l'ajout de fonctions permettant de rendre les tests encore plus indépendants qu'avant empêchant ainsi certains comportements indésirables de se produire.

Voici par exemple, la suite de testes réalisée aujourd'hui :

```ts
import { Util } from "../views/utils/Util";
import { DestinationDAO } from "./DestinationDAO";
import { IVRDAO } from "./IVRDAO";

describe("IVR DAO", () => {
    let createdId: number;
    let readedDestinationId: number;
	
	// Cette nouvelle partie permet d'être sur que la destination soit bonne
	// NOTE: la création de destination peut s'envisager dans le cas ou aucune destination
	// n'existe, cela fera l'objet d'un travail futur.
    beforeAll(async () => {
        readedDestinationId = (await DestinationDAO.readAll())[0].destination_id;
    });
    
    test("Create", async () => {
        let response = await IVRDAO.create(
	        Util.generateRandomString(),
	        readedDestinationId,
			readedDestinationId
		);
        expect(response).toHaveProperty("ivr_id");
        createdId = response.ivr_id
    });
    
    test("Read", async () => {
        expect(await IVRDAO.read(createdId)).toHaveProperty("description");
    });
    
    test("Read all", async () => {
        expect(await IVRDAO.readAll()).toBeDefined();
    });
    
    test("Update", async () => {
        expect(await IVRDAO.update(createdId, {
            description: Util.generateRandomString()
        })).toHaveProperty("description");
    });
    
    test("Delete", async () => {
        IVRDAO.delete(createdId);
    });
});
```

## IVR entry DAO & ses testes associés

Rien à signaler si ce n'est le fait que la méthode READ n'existe pas, heureusement cela peut se contourner en utilisant la méthode de READ-ALL.

J'ai également commencé à compléter le nœuds des IVR.

# Après-midi

## Création d'une vue "multi vues"

L'un des enjeux des  IVR est de pouvoir permettre aux utilisateurs de modifier les entrées des IVR dont la quantité est variable, cela implique une liste de points dans le nœud. Cette fonctionnalité de l'interface fait alors appel à une liste de vues pouvant changer à volonté.

## Impossibilité de modifier des destinations

En voulant contourner le manque de l'API en matière de création de destination, j'ai créé une fausse méthode de mise à jour venant supprimer et recréer la destination. Cependant, cette méthode ne prends pas en charge le changement de la clé étrangère. Cette modification doit donc être ajoutée, non pas au DAO mais directement dans chaque vue modèle appelant cette méthode.