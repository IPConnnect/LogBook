# Matinée

## Intégration du système vu hier

Le problème entre les deux systèmes, c'est que coté Canvas, les nœuds ne sont pas métier, ce qui signifie sur l'information stockée dans le tableau et la méthode de génération sont inconnus.

Il est impossible de comparer deux JSX.Element, un canvas générique est donc complexe à créer.

Pour contourner ce problème j'ai utilisé trois informations pour stocker les nœuds :

```ts
state: {
	nodes: { 
		identifier: string,
		type: string,
		position: {
			x: number,
			y: number
		}
	}[]
}
```

J'ai donc également du remonter la création des éléments :

```ts
props: {
    onCreateNode: (type: string) => JSX.Element
}
```

Cette méthode peut donc être définie dans le modèle vue :

```ts
onCreateNode={(type) => {
	if (type === DestinationCategoryType.INBOUND_ROUTE) {
        return this.builder.createInboundRouteNode();
	} else {
        return this.builder.createDestinationNode(type);
    }
}}
```

Cela change radicalement la fabrique, elle devient plus interopérable.

> ⚠️ Le problème majeur c'est que cela empêche la construction en cascade pour les éléments ayant une hiérarchie d'origine.

Le modèle peut engendrer une vue et d'autres modèles etc...
Il s'agit d'une récursive
Le patron de conception approprié serait "composite"

# Après-midi

Après quelques expérimentations, j'ai trouvé un moyen de créer des nœuds hiérarchisés, pour cela j'ai repris mon projet de test en y ajoutant quelques éléments :

```ts
class NodeView extends React.Component {
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

class NodeData {
    public readonly position: {
        x: number;
        y: number;
    };
	
    public readonly hasChild: boolean;
	
    constructor(x: number, y: number, hasChild?: boolean) {
        this.position = {
            x: x,
            y: y
        }
		
        this.hasChild = hasChild;
    }
	
    compareTo(data: NodeData) {
        if (this.position.x !== data.position.x) return false;
        if (this.position.y !== data.position.y) return false;
        return true;
    }
}

class NodeBuilder {
    build(data: NodeData, onClick: () => void): JSX.Element {
        return <NodeView left={data.position.x} top={data.position.y} onClick={onClick} />
    }
}

class Test extends React.Component {
    state: {
        elements: NodeData[]
    } = {
            elements: []
        }
		
    addElement(element: NodeData) {
        this.state.elements.push(element);
		
        if (element.hasChild) this.addElement(
	        new NodeData(element.position.x + 50, element.position.y)
	    );
    }
	
    update() {
        this.setState(this.state);
    }
	
	render() {
        return <Overflow>{
            this.state.elements.map(element => {
                return new NodeBuilder().build(element, () => {
                    for (let index = 0; index < this.state.elements.length; index++) {
                        if (this.state.elements[index].compareTo(element)) {
                            this.state.elements.splice(index, 1);
                            break;
                        }
                    }
					
                    this.update();
                })
            })
        }</Overflow>
    }
	
    componentDidMount() {
        this.addElement(new NodeData(50, 100, true));
        this.addElement(new NodeData(50, 200));
        this.addElement(new NodeData(50, 300));
        this.addElement(new NodeData(50, 400));
        this.addElement(new NodeData(50, 500));
		
        this.update();
    }
}
```

![[Pasted image 20230303150307.png]]

Le principe est simple, tout est géré entre le Canvas et le NodeData, c'est au moment d'ajouter un nœud que la hiérarchie se construit en ajoutant d'autre NodeData si nécessaire :

```ts
addNode(node: NodeData) {
    this.state.elements.push(element);
		
    if (element.hasChild) this.addElement(
	    new NodeData(element.position.x + 50, element.position.y)
	);
}
```

Ainsi, la partie lié à la vue est indépendante du modèle mais pas complètement car la fabrique a besoin de ses informations. Dans le cas ci-dessus, c'est normal, étant donné que la seule information du modèle c'est la position.

> La position est-elle sensée se trouver là ?
> 	Oui car il s'agit d'une information métier

### Dans un cas réel

Le Canvas n'est qu'une toile de fond dans laquelle viennent s'ajouter des éléments placés n'importe où dans un espace bidimensionnel. Ces éléments sont générés grâce à une fabrique spécialisée à partir du type d'élément à créer et de d'autres informations métiers le cas échéant.

![[Pasted image 20230303154541.png]]

Voici à quoi pourrait ressembler une version généralisée de cette architecture, au final, la problématique initiale de ne pas pouvoir utiliser de l'héritage avec JSX est contournée.

L'héritage permet ainsi de connaitre le type d'objet à générer sans utiliser de champs spécifique grâce à la fonction `instanceof`.

Pour implémenter cette architecture dans le système actuel, il faut modifier le Canvas, cela va entrainer de nombreuses modifications sur les sous-parties tel que les points et les liens.

La question reste de savoir comment généraliser la vue avec les nœuds et les liens, il est vrais qu'un nœud en soit n'est qu'un élément avec une position absolue et qu'un lien n'est qu'un SVG avec un PATH. La notion de point devient donc un élément métier. 

La fabrique spécialisée ressemblerait donc à ça :
```ts
class DialPlanBuilder implements ICanvasChildBuilder {
	build(canvas: Canvas, canvasChild: CanvasChild) {
		if (canvasChild instanceof InboundRouteCanvasChild) {
			return <InboundRouteNode inboundRouteId={canvasChild.inboundRouteId} />
		}
	}
}
```

Et l'initialiseur associé permettant de créer les destinations :
```ts
class DialPlanInitializer {
	initialize(canvas: Canvas, canvasChild: CanvasChild) {
		if (canvasChild instanceof InboundRouteCanvasChild) {
			let inboundRoute = InboundRouteDAO.read(canvasChild.inboundRouteId);
			canvas.addChild(new DestinationCanvasChild(inboundRoute.destination_id));
		}
	}
}
```

Etant donné le nombre de modification que ce changement engendrerait, peut-être serait-il intéressant de générer un nouveau projet. Cela permettrait aussi de retirer Electron.

> - Un autre ajout profitable serait la fonctionnalité de retour en arrière.
> - Ainsi que l'amélioration des échanges avec l'API qui alourdissent le code avec des fonctions asynchrones trop présentes.

Les étapes consisteront donc à :
- Générer un projet `Typescript/React`
- Implémenter la partie Canvas généralisée.
- Ajouter la fabrique et l'initialiseur spécialisé.
- Ajouter les nœuds, points et liens.
- Ajouter les DAOs et appels d'API en apportant des modifications
- Ajouter l'ensemble des éléments manquants requis.