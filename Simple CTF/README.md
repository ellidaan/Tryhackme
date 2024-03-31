# Simple CTF

liens du CTF : https://tryhackme.com/r/room/easyctf

Commençons d’abord par faire un scan avec nmap, nous pouvons voir le port 80 ouvert donc un site web.

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/Simple%20CTF/assets/Untitled.png)

Suite à la découverte d’un site sur le port 80, nous faisons une enum 
pour voir s'il n'y a pas d’autres pages cachées, page trouvée:  `/simple`

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/Simple%20CTF/assets/Untitled2.png)

La page `/simple` que nous voyons ci-dessous est faite par un `CMS Made Simple` version `2.2.8`.

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/Simple%20CTF/assets/Untitled3.png)

Nous pouvons voir qu'un exploit a été trouvé. Cet exploit est un peu cassé, il manque des parenthèses au niveau des `print`. Il faut donc les mettre afin que l'exploit puisse fonctionner.

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/Simple%20CTF/assets/Untitled4.png)

[hint] You can use /usr/share/seclists/Passwords/Common-Credentials/best110.txt to crack the pass

ous récupérons l’exploit grâce à `searchsploit -m <num exploit>`. Pour lancer le script :

`python3 [46635.py](http://46635.py/) -u http://10.10.94.176/simple --crack -w /usr/share/seclists/Passwords/Common-Credentials/best110.txt`

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/Simple%20CTF/assets/Untitled5.png)

Maintenant que j’ai un nom d'utilisateur, je cherche une autre page afin de me connecter ou de craquer un mot de passe. Je trouve la page `/admin`.

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/Simple%20CTF/assets/Untitled6.png)

Voici la page admin. Avec le nom d’utilisateur `mitch` trouvé tout à l’heure, je peux donc essayer de craquer le mot de passe.

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/Simple%20CTF/assets/Untitled7.png)

Pour ce faire, j’utilise Burp Suite afin de craquer le mot de passe. Mot de passe trouvé : `secret`

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/Simple%20CTF/assets/Untitled8.png)

Je rentre donc le nom d'utilisateur `mitch` et le mot de passe `secret` et 

j’accède donc à une page :

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/Simple%20CTF/assets/Untitled9.png)

Comme nous l’avons vu lors du scan nmap, le service SSH tourne sur le port 2222. On s’y connecte :

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/Simple%20CTF/assets/Untitled10.png)

On trouve un fichier `user.txt` ansi qu’un autre user.

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/Simple%20CTF/assets/Untitled11.png)

Ici, nous commençons l’escalade des privilèges : `sudo -l`. Nous pouvons voir que `mitch` peut utiliser `vim` avec `sudo`, on a donc la possibilité de modifier des fichiers.

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/Simple%20CTF/assets/Untitled12.png)

Nous pouvons accéder à /etc/passwd avec sudo :

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/Simple%20CTF/assets/Untitled13.png)

Il suffit de créé un mot de passe avec cette commande sur ton pc local :  `openssl passwd test` .

On remplace le X par le mot de passe généré : 

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/Simple%20CTF/assets/Untitled14.png)

Enfin on se connecte avec le user root :

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/Simple%20CTF/assets/Untitled15.png)

est de sécurité des applications web en identifiant les vulnérabilités telles que les injections SQL, les XSS (Cross-Site Scripting) et les CSRF (Cross-Site Request Forgery).
