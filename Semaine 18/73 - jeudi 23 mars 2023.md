# Matinée

Dans le but de régler le problème de sécurité des appels vers l'API, je dois créer une API pour éviter au client de communiquer directement à l'API :

![[Pasted image 20230323094248.png]]

# Après-midi

Continuation...

### Problème rencontré :

La solution proposée par olivier était de faire passer le chemin dans un paramètre URL mais comme les appels sont en GET, les informations en dans l'URL ne sont pas maintenues, seul l'authentification fonctionne.

```
<IfModule mod_rewrite.c>
	RewriteEngine on
	RewriteCond %{REQUEST_FILENAME} !-d
	RewriteCond %{REQUEST_FILENAME} !-f
	RewriteRule ^(.*)$ index.php?api=$1 [L,QSA] ## Cette oartie en particulier
</IfModule>
```

