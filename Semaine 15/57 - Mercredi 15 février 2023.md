# Matinée

## Debugge du nœud IVR

Un problème s'était glissé dans le nœud IVR au moment du changement de la destination, en effet, à il doit y a voir des clés étrangères dans la base de données qui empêche la destruction de certaines destinations. J'ai donc désactivé temporairement la suppression de ces destinations.

Un autre problème d'ordre de l'étourdissement était aussi présent, je l'ai corrigé et la modification de la destination fonctionne parfaitement.

## Debugge de la liste des éléments

La liste des points utilisés dans le nœud des IVR m'a donné du fil à retordre, la programmation asynchrone au sein du système de vue de React était complexe a structurer dans un contexte dynamique.

## Files d'attente

> ⚠️ Absence de fonction UPDATE et DELETE dans l'API.

# Après-midi

## Ring group

J'ai créé le DAO, les tests associés et complété le nœud.

