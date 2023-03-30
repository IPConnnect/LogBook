# Continuer le 2nd projet

- extension
- file d'attente
- route entrante

## Expérimentation autour du CRUD :

```typescript
class Queue implements CRUD {
    create() {
        IO.request(Singleton.completePBXIpAddress + "/create_queue", {
            token: await IO.persistedRequest(Singleton.completePBXIpAddress + "/authenticate", {
                user: Singleton.completePBXUser,
                password: Singleton.completePBXPassword
            }),

            description: "My queue description"
        });
    }

    read() {
        IO.request("192.168.11.210/create_queue", {
            token: await IO.persistedRequest("192.168.11.210/authenticate", {
                user: "admin",
                password: "password"
            }),

            description: "My queue description"
        });
    }

    update() {
        IO.request("192.168.11.210/create_queue", {
            token: await IO.persistedRequest("192.168.11.210/authenticate", {
                user: "admin",
                password: "password"
            }),

            description: "My queue description"
        });
    }

    delete() {
        IO.request("192.168.11.210/create_queue", {
            token: await IO.persistedRequest("192.168.11.210/authenticate", {
                user: "admin",
                password: "password"
            }),

            description: "My queue description"
        });
    }
}
```

Tâches :
 - Créer une classe avec les méthodes :
   - post()
   - authenticate()
   - read()
   - create()
   - update()
   - delete()

## Note

La méthod eproposée par Olivier est élégante mais difficilement généralisable.
J'ai tenté une approche plus spécifique à chaque modèle ce qui semble être efficace mais long.

> J'ai tenté de générer du code avec Swagger mais le code généré bug et l'authentification est très difficile a configurer.

La méthode choisie risque d'être la seconde.

## Liste des objet de l'API

- Inbound route : Appel entrant