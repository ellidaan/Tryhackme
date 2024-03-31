Commençons par faire un nmap. On peut voir les ports 21, 22 et 80 ouverts.

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/AgentSudo/assets/Untitled.png)

Sur le site web, d’après ce que je comprends, il faut changer dans l’en-tête de la requête le user-agent, comme indiqué dans l’aide où il faut mettre C.

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/AgentSudo/assets/Untitled1.png)

Pour changer la requête, nous utiliserons Burp Suite afin d’intercepter celle-ci.

Voici à quoi ressemble la requête avant le changement :

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/AgentSudo/assets/Untitled2.png)

Après le changement :

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/AgentSudo/assets/Untitled3.png)

Nous arrivons sur cette page où nous trouvons un nom d’utilisateur : chris

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/AgentSudo/assets/Untitled4.png)

Nous lançons une attaque par force brute sur le mot de passe FTP à l’aide de Hydra

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/AgentSudo/assets/Untitled5.png)

Nous nous connectons donc au serveur FTP avec le nom d'utilisateur : chris et le mot de passe crystal. Nous pouvons voir trois fichiers que nous allons télécharger sur notre machine locale avec la commande mget (pour télécharger tous les fichiers).

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/AgentSudo/assets/Untitled6.png)

Le fichier nous indique que le mot de passe est caché quelque part dans l’une des photos :

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/AgentSudo/assets/Untitled7.png)

Nous utilisons exiftool pour examiner les photos données. Dans cutie.png, on remarque un problème :

Attention : Avertissement concernant la lecture des métadonnées. Nous pouvons donc imaginer qu’il y a un problème au niveau des métadonnées.

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/AgentSudo/assets/Untitled8.png)

Nous pouvons utiliser binwalk. Binwalk est un outil qui permet de rechercher dans des images binaires des fichiers intégrés et du code exécutable. Effectivement, nous voyons bien un fichier .zip.

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/AgentSudo/assets/Untitled9.png)

Pour extraire un fichier d’une image : binwalk -e cutie.png
![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/AgentSudo/assets/Untitled10.png)

Le fichier .zip est malheureusement bloqué par un mot de passe.
![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/AgentSudo/assets/Untitled11.png)

Nous utiliserons John the Ripper pour cracker le code : zip2john 8702.zip > hash.txt

john hash.txt

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/AgentSudo/assets/Untitled12.png)

Voici ce que nous trouvons dans ce fichier .zip :

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/AgentSudo/assets/Untitled13.png)

QXJlYTUx semble être hors du commun, cela semble encodé. J’utilise https://hashes.com/en/decrypt/hash pour décoder cela, et nous trouvons Area51 qui est le mot de passe de steg.
<aside>
💡 `Steghide` est en effet un outil de stéganographie numérique disponible sur Linux, ainsi que sur d'autres plates-formes comme Windows. Steghide permet de cacher des données dans des fichiers multimédias tels que des images ou des fichiers audio.
</aside>

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/AgentSudo/assets/Untitled14.png)

Nous cherchons donc un moyen d'extraire les données de la deuxième photo grâce à steghide, et nous trouvons ceci : steghide extract -sf <NomDuFichier> un mot de passe nous sera demandé.

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/AgentSudo/assets/Untitled15.png)

Nous voyons qu'un fichier .txt a été trouvé :

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/AgentSudo/assets/Untitled16.png)

Nous nous connectons en SSH avec james@<ip> et le mot de passe hackerrules!

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/AgentSudo/assets/Untitled17.png)

Nous commençons l’escalade des privilèges avec sudo -l. Nous trouvons (ALL, !root) /bin/bash, ce qui semble assez bizarre.

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/AgentSudo/assets/Untitled18.png)

Nous faisons donc une recherche pour voir s'il existe un exploit pour cela :

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/AgentSudo/assets/Untitled19.png)

Il suffit d’exécuter cette commande : sudo -u#-1 /bin/bash

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/AgentSudo/assets/Untitled20.png)
