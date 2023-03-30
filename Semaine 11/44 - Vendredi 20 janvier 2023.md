Continuité de la veille, quelques changements cependant :

Hier j'ai essayé d'améliorer le système des dialplan en créant des fonctions d'attente synchrones et un système de création de DialNode en fonction de la catégorie de destination.

**Canvas**: est capable d'afficher des noeuds complexes et de les traiter
    **Node**: possède des entrées utilisateurs et peut être déplacé dans le canvas
        **Points**: peuvent être lié à d'autre points
    **SVG**: espace de dessin HD des lignes
        **Links**: créé/supprimé en fonction des liens entre les points

InboundRoute: représente un appel entrant
    Destination: définit vers quoi l'appel se poursuit
