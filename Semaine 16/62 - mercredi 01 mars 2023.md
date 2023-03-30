# Matinée

## Suppression de nœuds

> On ne peut supprimer que les nœuds non liés

1. Créer les boutons de suppression
	1. Annonces
	2. Extension
	3. Inbound route
	4. IVR
2. Créer les évènements de désactivation du bouton
	3. Annonce
	4. Extension
	5. Inbound route (impossible)
	6. IVR
3. Ajouter l'identifier
	1. Annonce
	2. Extension
	3. Inbound route (non nécessaire)
	4. IVR

### Problème avec les intermédiaires à liaisons bidirectionnelles

Les IVRs par exemple ne peuvent pas être supprimés au même titre que les routes entrantes, cependant, il serait intéressant pour l'utilisateur de les supprimer quand ils ne sont pas lié à une origine. Une nouvelle s'applique alors :

> La suppression d'un nœud entraine la suppression des nœuds liés

Cela implique qu'un nœud est toujours supprimable, il faut donc retirer les évènements créés précédemment.

> Note personnelle : passer plus de temps sur la pertinence d'un choix de design pour éviter des pertes de temps, à fortiori lorsque je ne suis pas en pleine possession de mes facultés cognitives.

### Système de suppression en cascade

Pour supprimer une hiérarchie entière, j'ai utilisé une fonction récursive :

```ts
export interface IDeletableNode {
    delete(deleteOrigins: boolean, deleteDestinations: boolean): Promise<void>
}
```

L'interface ci-dessus permet d'appeler la méthode de suppression partout dans le code, les arguments `deleteOrigins` et `deleteDestinations` permettent d'éviter les dépendances cycliques en propageant le sens de suppression à chaque itération.

```ts
async delete(deleteOrigins: boolean, deleteDestinations: boolean): Promise<void> {
        await this.props.canvas.deleteLink({
            destination: this.getDestinationPoint()
        });
        
        if (deleteOrigins) {
            ((await this.props?.canvas?.getLink(
            this.getDestinationPoint()))
            .origin?.props?.parent as IDeletableNode).delete(true, false);
        }
        
        if (deleteDestinations) {
            ((await this.props?.canvas?.getLink(
            this.getOriginPoint()))
            .destination?.props?.parent as IDeletableNode).delete(false, true);
        }
        
        await this.props.canvas.deleteNode(this.identifier);
    }
```

# Après-midi

## Continuation du travail de la matinée

### Problème avec le système anti dépendances cyclique

Dans le cas où un nœud dispose de plusieurs points du même type, certains points ne sont pas supprimés dans le processus.

Je dois donc changer de méthode, un tests de suppression peut être envisagé pour éviter les boucles infinies.

> Problème également avec les identifier qui se réinitialisent au changement d'état. 
> Solution : stocker dans les props.

Le problème de l'identifier est réglé