# **Bounty Hacker**

lien du CTF: https://tryhackme.com/r/room/cowboyhacker

Commençons par faire un scan avec nmap. Nous pouvons voir qu’un site tourne sur le port 80 et que le service ftp tourne sur le port 22 et qu’on peut se connecter en tant qu'Anonymous.

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/BountyHacker/assets/Untitled.png)

Nous rentrons l’URL du site web, nous ne trouverons rien de particulier sur ce site :

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/BountyHacker/assets/Untitled2.png)

Nous nous connectons en ftp en tant que Anonymous, nous remarquons deux fichiers que nous exporterons sur notre PC local avec `get <nomDuFichier>`.

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/BountyHacker/assets/Untitled3.png)

Voici ce qu’il y a dans task.txt : le nom de l'utilisateur est `lin`.

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/BountyHacker/assets/Untitled4.png)

Voici ce qu’il y a dans locks.txt qui est une liste de mots de passe :

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/BountyHacker/assets/Untitled5.png)

Nous pouvons donc essayer de nous connecter en SSH. Pour cela, il faut trouver le mot de passe à l’aide d’hydra.

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/BountyHacker/assets/Untitled6.png)

Nous nous connectons donc en SSH :

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/BountyHacker/assets/Untitled7.png)

C’est ici que nous commençons l’escalade des privilèges. Nous remarquons que `tar` peut être utilisé à l’aide de `sudo`.

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/BountyHacker/assets/Untitled8.png)

On va donc se reiseigne si il existe pas une faille sur GTFOBins.

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/BountyHacker/assets/Untitled9.png)

Grâce à cette faille, nous pouvons augmenter les privilèges et être root sur la machine :

![Untitled](https://github.com/ellidaan/Tryhackme/blob/main/BountyHacker/assets/Untitled10.png)
