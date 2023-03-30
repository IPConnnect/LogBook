# Matinée

## Suppression des points

Continuité de la veille

### Correction du système

La correction que j'avais proposé hier fonctionne parfaitement, il y a uniquement certains nœuds d'une hiérarchie qui ne sont pas supprimés, mais je peux les supprimer manuellement par la suite.

Pour résoudre les problèmes restants, je vais tenter de supprimer ls liens après avoir supprimé les nœuds.

> Non ca ne règle rien, c'est même pire

J'ai désactivé la suppression des liens, et le résultat est le même, cela signifie bien que les liens existent toujours mais ne peuvent s'afficher par non cohérence des données.

Une solution envisageable serait de récupérer la liste des nœuds à supprimer avant des les supprimer de sorte qu'aucun nœuds ne risque d'être supprimé avant d'être récupéré.
Non, cela ne changera rien.
Le problème vient du fait que le nœud précèdent est immédiatement supprimé une fois arrivé au nœud suivant, donc la solution serait de faire passer le nœud précédent pour pouvoir l'éliminer de l'algorithme.
Une autre solution serait de changer l'ordre de suppression, effet, si je récupère les nœuds alentours, que je supprime le nœud en cours puis que je passe la main aux nœuds récupérés précédemment, ces nœuds ne risques pas de repasser sur le même noeud... à tester

Cela est pire ! La suppression du premier nœud ne permet pas de supprimer les suivants…

Avec la solution précédente, le résultat est revenu comme avant, il semble que le problème ne vienne pas de l'endroit ou je pense.

> Problème identifié : La suppression de nœuds dont l'index est inférieur au nœud lié engendre des erreurs. Il faut toujours supprimer le dernier nœud d'une file.

# Après-midi

## Tentative de compréhension de l'erreur dénichée

Je pense qu'il s'agit d'un problème d'architecture profonde, j'ai construit l'interface dans la logique où tout est constant, les points, les nœuds etc... Cela signifie que chaque variables de ce genre étant copié en javascript peut d'un coté être supprimé et resté intact de l'autre coté.

Je me heurte à un problème bien connu dans le patron de conception composite, les nœuds dans une hiérarchie sont toujours en mouvement et nécessitent donc un système puissant de sélection ou d'évènement avancé.

Pour rester simple, le mieux serait d'utiliser des identifiant pour les liens plutôt que des points. De cette façon les liens se feront par références et non par duplication. Les références pointeront vers la liste du Canvas. 

Un lien possède actuellement :
```ts
origin: { x: number, y: number }
destination: { x: number, y: number }
```
L'idée serait de le remplacer par :
```ts
origin: Identifier
destination: Identifier
```

La nouvelle classe identifier définirait une méthode de randomisation et de concaténation

```ts
export class Identifier {
	private value: string;
	private parent: Identifier;
	
	constructor(parent?: Identifier) {
		this.value = Randomise(16);
		this.parent = parent;
	}
	
	public get value() {
		return this.value;
	}
	
	public get parent() {
		return this.value;
	}
	
	compareTo(other: Identifier): boolean {
		if (this.value === other.value) {
			if (this.parent && other.parent) {
				return this.parent.compareTo(other.parent);
			}
			
			return !this.parent && !other.parent;
		}
	}
}
```

### La lourdeur de JSX

Le système de référence des nœuds JSX m'empêche quelques fois d'effectuer des traitements pourtant simples à réaliser simplement parce que je dois attendre que les références soient créées.

L'impossibilité de faire de l'héritage m'irrite également profondément…

J'ai recréé un petit système de création/suppression d'élément JSX, et le constat reste le même :
- L'utilisation d'un identifier et/ou d'une méthode `compareTo` sont essentiel pour éviter les erreurs de référencement.

> ⚠️ Rectification : Le problème vient d'une mauvaise utilisation de React et de l'état des composants.

Avec Angular je n'ai jamais eu ce genre de problèmes…

En intégrant tout dans une seule classe ça fonctionne :
```ts
class Test extends React.Component {
    state: {
        elements: {x: number, y: number}[]
    } = {
        elements: []
    }
	
    render() {
        return <Overflow>{
            this.state.elements.map(value => {
                return <div style={{
                    position: 'absolute',
                    left: value.x,
                    top: value.y
                }} onClick={(ev) => {
		            // Supression par comparaison
                    for (let index = 0; index < this.state.elements.length; index++) {
                        if (this.state.elements[index] == value) {
                            this.state.elements.splice(index, 1);
                            break;
                        }
                    }
					
                    this.setState(this.state);
                }}>Test</div>
            })
        }</Overflow>
    }
	
    componentDidMount() {
        this.state.elements.push({
            x: 50, y: 200
        });
        this.state.elements.push({
            x: 50, y: 100
        });
        this.state.elements.push({
            x: 50, y: 300
        });
		
        this.setState(this.state);
    }
}
```

Tout réside dans le fait que les éléments supprimables sont créés à partir des informations du tableau, ainsi, les méthodes liées à la suppression font immédiatement référence à leur parent sans problème de référence. Cependant en situation réelle ce serait impossible…

Voici une petite modification pour pallier à ce problème :
```ts
class Node extends React.Component {
    props: {
        left: number,
        top: number,
        onClick: () => void
    }
	
    render() {
        return <div style={{
            position: 'absolute',
            left: this.props.left,
            top: this.props.top
        }} onClick={(ev) => {
            this.props.onClick();
        }}>Test</div>
    }
}

class Test extends React.Component {
    state: {
        elements: { x: number, y: number }[]
    } = {
            elements: []
        }
		
    render() {
        return <Overflow>{
            this.state.elements.map(value => {
                return <Node left={value.x} top={value.y} onClick={() => {
                    for (let index = 0; index < this.state.elements.length; index++) {
                        if (this.state.elements[index] == value) {
                            this.state.elements.splice(index, 1);
                            break;
                        }
                    }
			        
                    this.setState(this.state);
                }}/>
            })
        }</Overflow>
    }
	
    componentDidMount() {
        this.state.elements.push({
            x: 50, y: 200
        });
        this.state.elements.push({
            x: 50, y: 100
        });
        this.state.elements.push({
            x: 50, y: 300
        });
		
        this.setState(this.state);
    }
}
```

Dans cette situation le système fonctionne parfaitement, il ne me reste plus qu'à l'implémenté dans mon logiciel.