## Continuation de l'implémentation

Comme je l'ai suggéré hier, j'ai bâti une version miniature de mon architecture. Cette fois ci sans utiliser de modèles, l'impossibilité de faire de l'héritage devient dans cette situation la nouvelle problématique.

Je crois que la logique spécifique n'est pas une bonne approche avec React, les éléments s'ajoutent les uns à l'intérieur des autres avec des propriétés et ça s'arrête là. Toute la logique des traitements derrière les vues doit se faire par l'intermédiaire de cette structure là

```jsx
<Space>
	<Node initialPosition={{ x: 50, y: 0 }} key="pzjefo658g74e6dr8g">
		<Point key="ze6df54g674gzeg654" type={PointType.Origin}>
			<Link destination="45g45f6zef654zef">
		</Point>
	</Node>
	
	<Node key="sefg968z45g">
		<Point key="45g45f6zef654zef" type={PointType.Destination} />
	</Node>
</Space>
```

A contrario, il est aussi possible d'utiliser des Listeners plutôt que de référencer le parent.

Si les élément ont accès à leur parent (parce que celui-ci s'est donné à l'initialisation), les changements d'état peuvent s'opérer :
- Suppression de nœud en cascade
	- Je demande à l'espace de supprimer les nœuds ayant les points définis comme destination des liens de mes points.
- Ajout de nœuds en cascade
	- Je demande à l'espace d'ajouter les nœuds correspondant à mon modèle
- Déplacement d'un nœud
	- Je met à jour ma position dans mon état
	- Je demande aux liens liés à mes points de mettre à jour leur position
- Je clique sur un point
	- Je défini dans le registre le dernier point cliqué
- Je lâche la souris sur un point
	- Je créé un lien dans ce point vers le dernier point cliqué
	- Je demande aux deux nœuds de faire les modifications nécessaires

Cette manière de faire ne permet pas de supprimer des éléments car on compare l'instance de la classe au composant JSX.

## Listes dans React

J'ai regardé à nouveau la documentation React et je n'ai rien trouvé concernant la suppression d'éléments dans une liste. Leurs exemples font référence à une utilisation très simple de ces listes.

La dualité entre une partie "vue" et une partie "modèle" revient sans cesse, ce qui est logique pourtant je n'arrive pas à modéliser une architecture fonctionnelle.

Ma précédente observation par rapport à l'usage d'un identifiant ou d'un système de comparaison est intéressant ici, il y a des concepts comme celui-ci qui s'assemblent dans une même architecture et qui en fonction de minuscules détails, rendent ces architecture impossible. De plus, la particularité de React rend l'usage de patrons de conception inutiles.

Si un concept permet d'ajouter et supprimer des éléments au seins d'un tableau React existe, il est sans doute la clé au problème que je cherche. Cependant, la complexité repose sur l'accès à toutes les données sans doublons.

```tsx
class Child {
	public readonly identifier = Math.random()
	public template(): JSX.Element
}

class Parent extends React.Component {
	state = {
		children: new Array<Child>()
	}
	
	addChild(child: Child) {
		let children = Array.from(this.state.children)
		children.push(child)
		this.setState({
			children: children
		})
	}
	
	removeChild(child: Child) {
		this.setState({
			children: this.state.children.filter(
				_child => _child.identifier !== child.identifier
			)
		})
	}
	
	render() {
		return <div>{
			this.state.children.map(child => child.template())
		}</div>
	}
}
```

Une version plus comple :

```tsx
class Child {
    public readonly identifier = Randomizer.generateRandomIdentifier()
    public parent: Parent | undefined

    public template(): JSX.Element {
        return <div>Je suis un enfant</div>
    }
}

class Link extends Child {
    private destination: {
        x: number,
        y: number
    }

    constructor(x: number, y: number) {
        super()

        this.destination = {
            x: x,
            y: y
        }
    }

    public template(): JSX.Element {
        return <svg
            width={this.destination.x}
            height={this.destination.y}
            style={{
                position: "absolute",
                pointerEvents: 'none'
            }}
        >
            <path stroke="var(--dark)" strokeWidth={3} d={`M 0 0 ${this.destination.x} ${this.destination.y}`} />
        </svg>
    }
}

class Point extends React.Component {
    private rootRef = React.createRef<SVGSVGElement>()

    props!: {
        onClick: (x: number, y: number) => void
    }

    public render(): JSX.Element {
        return <svg ref={this.rootRef} width={24} height={24} onMouseDown={(ev) => {
            if (this.props.onClick) this.props.onClick(
                this.rootRef.current?.getBoundingClientRect().x ? this.rootRef.current?.getBoundingClientRect().x : 0,
                this.rootRef.current?.getBoundingClientRect().y ? this.rootRef.current?.getBoundingClientRect().y : 0
            )
        }} onMouseUp={() => {

        }}>
            <circle cx="12" cy="12" r="12" fill={'var(--primary)'}></circle>
            <circle cx="12" cy="12" r="8" fill="var(--light)"></circle>
            <circle cx="12" cy="12" r="4" fill="var(--dark)"></circle>
        </svg>
    }
}

class Node extends React.Component {
    props!: {
        position: {
            x: number,
            y: number
        },
        children: JSX.Element | JSX.Element[]
    }

    state = {
        position: {
            x: 0,
            y: 0
        }
    }

    setPosition(x: number, y: number) {
        this.setState({
            position: {
                x: x,
                y: y
            }
        })
    }

    render(): React.ReactNode {
        return <div style={{
            position: 'absolute',
            left: this.state.position.x,
            top: this.state.position.y
        }}>
            <button onClick={async () => {
                this.setPosition(200, 800)
            }}>Bouger</button>

            {this.props.children}
        </div>
    }

    componentDidMount(): void {
        this.state.position = this.props.position
    }
}

class SpecializedChild extends Child {
    private position: {
        x: number,
        y: number
    }

    constructor(x: number, y: number) {
        super()

        this.position = {
            x: x,
            y: y
        }
    }

    setPosition(x: number, y: number) {
        this.position = {
            x: x,
            y: y
        }
    }

    private links = new Array<Link>()

    public template(): JSX.Element {
        return <Node position={this.position}>
            <button onClick={async () => {
                if (this.parent) {
                    for (const link of this.links) {
                        await this.parent.removeChild(link)
                    }

                    await this.parent.removeChild(this)
                }
            }}>Supprimer</button>

            <Point onClick={(x, y) => {
                let link = new Link(x, y)
                this.links.push(link)
                this.parent?.addChild(link)
            }} />
        </Node>
    }
}

class Parent extends React.Component {
    state = {
        children: new Array<Child>()
    }

    addChild(child: Child) {
        let children = Array.from(this.state.children)
        child.parent = this
        children.push(child)
        this.setState({
            children: children
        })
    }

    async removeChild(child: Child) {
        return new Promise<void>(resolve => {
            this.setState({
                children: this.state.children.filter(
                    _child => _child.identifier !== child.identifier
                )
            }, () => {
                resolve()
            })
        })
    }

    render() {
        return <div>
            {this.state.children.map(child => child.template())}
            <button onClick={() => {
                this.addChild(new SpecializedChild(500, 100))
            }}>Ajouter</button>
        </div>
    }
}
```

L'élément le plus intéressant dans cette architecture c'est la classe Child qui fait le lien entre la vue et le modèle, il permet de faire de l'héritage et de construire la vue de plusieurs manières

```tsx
class Child {
    public readonly identifier = Randomizer.generateRandomIdentifier()
    public parent: Parent | undefined

    public template(): JSX.Element {
        return <MyView/>
    }
}
```

Il fournit les éléments permettant de traiter avec la liste.

## Décision finale

Après avoir cherché à amélioré mon architecture, j'ai déniché quelques méthodes permettant d'implémenter la suppression de nœuds. Aucune méthode simple et rapide n'a été trouvé dans la mesure ou refactorer le projet prendrait un temps considérable. Aussi, il est donc temps de faire le choix de ne pas refactorer, et n'ajouter que le nécessaire à l'architecture précédente. Bien entendu, l'adaptation au nouveau projet web aura un impact sur le code source et sera l'occasion de perfectionner celui-ci, mais aucune modification architecturale majeure sera faite

> J'ai tout cassé, après avoir fait des modifications la dernière fois, j'ai cassé tout mon ancien projet...

