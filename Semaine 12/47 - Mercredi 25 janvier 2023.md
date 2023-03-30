# Matinée

## Problème d'architecture

La gestion des évènements spécifiques à un type de point et à un type de nœud est difficile.
Je dois pouvoir :
- Supprimer un lien avec précision
- Ajouter un lien en fonction de certaines conditions très précises.
- Récupérer un lien en particulier
- Récupérer un nœud

- On vient de supprimer un lien existant
- On ajoute un lien
- On tente d'ajouter un lien

- De quoi vient un lien ?
  - Inbound route ?
  - Fin d'appel ? etc...

Le type de noeud doit figurer dans le lien -> ça c'est bon

## Problème résolu

Problème résolu en utilisant des évènements dans les points et en intégrant le "DialNode" dans le point :

```ts
<Point ref={this.originPointRef} parent={this} name="Destination" type={PointType.Origin} validateLink={(origin, destination) => {
    // Suppression des liens vers ce point
    this.props.canvas.deleteLink({
        origin: origin
    }, false);

    if (destination.props.parent instanceof TerminateCallNode) {
        // traitement spécifique
    }

    // If origin and destination are differents
    return origin !== destination;
}} onCreateLink={(origin, destination) => {
    console.log("Nouveau lien depuis la route entrante !");

    // Si la destination est liée à un noeud de fin d'appel
    if (destination.props.parent instanceof TerminateCallNode) {
        this.props.inboundRoute.destination = destination.props.parent.props.destination;

        // Mettre à jour la route entrante
        loadingImgRef.current.hidden = false;

        new InboundRouteDAO().update(this.props.inboundRoute.id, this.props.inboundRoute).then(() => {
            loadingImgRef.current.hidden = true;
            console.log("Nouveau lien mis à jour");
        })
    }
}} onDeleteLink={() => {
    this.props.inboundRoute.destination = defaultDestination;
    new InboundRouteDAO().update(this.props.inboundRoute.id, this.props.inboundRoute);
    this.props.canvas.addNode(<TerminateCallNode canvas={this.props.canvas} destination={this.props.inboundRoute.destination} />)
}} />
```

# Après-midi

Tâches restantes :
- Créer la fenètre de selection de noeud FAIT
- Créer un nœud pour les files d'attente FAIT
- Créer un nœud pour les groupes de sonnerie FAIT
- Créer un nœud pour les IVR FAIT
- Créer un nœud pour les conditions de temps FAIT
- Créer un nœud pour les annonces FAIT
- Créer un nœud pour les modes nuit FAIT
- Créer un nœud pour les extensions FAIT
- Créer un nœud pour les destinations personnalisées INTROUVE

Nouvelles tâches :
- Transformer l'update des inbound route par l'update de la destination FAIT
- Créer les modèles manquants
- Créer les DAO manquants
- Faire des tests unitaires ?
- Ajouter les champs dans les noeuds
  - Route entrante FAIT
  - Groupe de sonnerie
- Trouver un moyen de générer les categories manquantes
- Modifier le système de requetage