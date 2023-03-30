# Sixième jour

## Refactoring organisation

La semaine dernière c'était le workshop, on a fait un projet javascript avec nodejs.

C'est le second projet qui utilise ces technologies et à terme, une structure peut organisée peut etre source de confusion.

Je vais séparer les parties back-end et front-end de sorte à faciliter le developpement. Rien du coté du back-end ne doit avoir à se relancer.

Je me szuis rendu compte que la bibliothèque sipjs était destiné à un usage avec NodeJS. Je dois donc modifier mon projet pour l'utiliser coté front-end. C'est l'occasion d'utiliser d'autres outils pour faciliter le développement.

## Après-midi

Je vais continuer ce que j'ai fais ce matin.

Comment faire un front-end avec des modules NodeJS pouvant être intégré dans un back-end ?

```
- Back-End
    - NodeJS
        - HTTP Server 8080
            - Ends Points
                - /home
                - /peers

- Front-End
    - NodeJS
        - WebPack
            - index.html
            - style.css
            - script.js/ts
```

J'ai réussi à faire fonctionner sipjs en clonant le code source.