# Matinée

## Tâches suivantes

Je dois réaliser certains noeuds pour la maquette :
 - **Route entrante** : description, Did & CID -> envoyer sur un groupe de sonneries
 - **Ring group**: code, desc, liste des extensions, temps de sonneries, stratégie de sonnerie, ringback sound
 - **Queue**: code, desc, stratégie, music on queue, queue time out, agent time out, ring buzzy agent, agents*
pour les classes de services, les lister mais pas de noeud
 - **IVR**: desc, classe de service non metttre par default, instruction message, entrées(options).
 - **Time condition**: desc, time group(liste)
 - **Annonce**: desc, choix de l'enregistyrement
 - **night mode**: toggle code #80#80#82, desc.
 - **custom destination**: name, number to dial, class de service
 - **Extensions**: numéro, nom, classe de service, external cid number, boite vocal + mdp boite vocal, email, durée de sonnerie) device et mdp aléatoire en sip créer et associer un device

Lister les APIs manquantes.

Réaliser un menu latéral pour les routes entrantes

### Problème rencontré

Le système actuel de gestion des liens entre les noeuds est au mieux bancale au pire disfonctonnel.
Je dois donc mettre au point un nouveau système.

# Après-midi

- Créer un composant pour remplacer le "canvas" appelé NodeEditor. fait
- Créer un composant pour l'ipphone et pour la dialview. fait
- Remplacer le système des noeuds ayant un lien et un lienAvec par un lien unique.
- Créer un système de listener et le généraliser.