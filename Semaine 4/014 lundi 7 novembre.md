# Salut

Je dois continuer à implémenter mon archi..

## Les joies de Jest

En implémentant la lecture de fichiers, j'ai décidé de mettre en place les tests unitaires, mais étant donnée que les tests avec Jest fonctionnent sous node et ne connaissent pas les objects "windows" et "XMLHttpRequest"... pourant la fonctionne marche très bien.

J'ai réussi grace à une information contradictoire entre la doc et la dernière version qui n'intégrait pas l'environnement de test "jsdom" de manière native à JEST...

Mon test fonctionne quand j'y met simplement ma fonction, mais en regardant de plus pret la couverture de code, je dois rajouter toutes les possibilités logiques. C'est au final une très bonne chose, car c'est suggéré par Jest et l'outil a donc son utilité.
Après quelques configurations et réécriture du code, je trouve que les tests de ce type n'ont aucune utilité puisqu'un code qui va fonctionner parfaitement dans son environnement final ne fonctionnera peut etre pas en test et en plus les tests ne testent pas l'entièreté du code, donc du code-faux, c'est stupide... Et ça me prend trop de temps...

J'ai essayé d'installer eletron-builder pour générer des executables du projet mais avec webpack ça ne fonctionne pas comme ça, de plus mes fichiers js prennent de plus en plus de place parmi les nombreuses classes que je dois créer. Je pense qu'il est nécéssaire que l'architecture du projet évolue, c'est une des particularité de javascript, il est très modulable et y passer du temps aujourd'hui me fera gagner du temps au long terme.

J'ai trouvé un template utilisant electron-forge, webpack, typescript, react et lint. Je risque le modifier pour obtenir ce que je cherche. Le projet est interessant à analyser mais la méconnaissance des configurations peut être gênant au long terme si de nouvelles fonctionnalités sont envisagées. Il serait mieux de n'utiliser ce projet comme d'un exemple et d'en construire-un pas à pas de sorte à maitriser l'ensemble des parties et avoir quelque chose de très spécifique à la demande.

En suivant les indications de electron-forge, étant donné que ça n'a pas fonctionné, j'ai utilisé diverses template de projets proprosés dans le guide et heureusement je suis tombé sur un qui utilise exactement ce que je cherchais à faire dans le guide. J'ai donc réuni la compréhension de la configuration grace au guide et j'ai aussi une architecture fonctionnelle grace au template. Je vais fournir une documentation pour les futurs développeurs de ce guide et de la configuration du template.