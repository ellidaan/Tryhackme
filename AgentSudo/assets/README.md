Commençons par faire un nmap. On peut voir les ports 21, 22 et 80 ouverts.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/7519d85a-065b-470e-9c96-70f14244e8bf/7b517c17-c61b-4a1d-89e6-1fb0304f4aed/Untitled.png)

Sur le site web, d’après ce que je comprends, il faut changer dans l’en-tête de la requête le user-agent, comme indiqué dans l’aide où il faut mettre C.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/7519d85a-065b-470e-9c96-70f14244e8bf/7b517c17-c61b-4a1d-89e6-1fb0304f4aed/Untitled.png)

Pour changer la requête, nous utiliserons Burp Suite afin d’intercepter celle-ci.

Voici à quoi ressemble la requête avant le changement :

less

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/7519d85a-065b-470e-9c96-70f14244e8bf/7b517c17-c61b-4a1d-89e6-1fb0304f4aed/Untitled.png)

Après le changement :

less

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/7519d85a-065b-470e-9c96-70f14244e8bf/cdd5e487-a77c-4d62-8519-6fb61844f462/Untitled.png)

Nous arrivons sur cette page où nous trouvons un nom d’utilisateur : chris

less

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/7519d85a-065b-470e-9c96-70f14244e8bf/81997625-7339-4a1f-bbda-a88a3c6e2c48/Untitled.png)

Nous lançons une attaque par force brute sur le mot de passe FTP à l’aide de Hydra

less

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/7519d85a-065b-470e-9c96-70f14244e8bf/a05ce114-f1ce-4d97-bb80-afb8c7e4f737/Untitled.png)

Nous nous connectons donc au serveur FTP avec le nom d'utilisateur : chris et le mot de passe crystal. Nous pouvons voir trois fichiers que nous allons télécharger sur notre machine locale avec la commande mget (pour télécharger tous les fichiers).

less

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/7519d85a-065b-470e-9c96-70f14244e8bf/9b06dab8-a705-4eb0-a8d3-b190c781e63d/Untitled.png)

Le fichier nous indique que le mot de passe est caché quelque part dans l’une des photos :

less

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/7519d85a-065b-470e-9c96-70f14244e8bf/8d9aced9-15f3-482c-88c9-5a5fc7913033/Untitled.png)

Nous utilisons exiftool pour examiner les photos données. Dans cutie.png, on remarque un problème :

Attention : Avertissement concernant la lecture des métadonnées. Nous pouvons donc imaginer qu’il y a un problème au niveau des métadonnées.

less

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/7519d85a-065b-470e-9c96-70f14244e8bf/1e82759f-4542-406e-9967-9da791603d0f/Untitled.png)

Nous pouvons utiliser binwalk. Binwalk est un outil qui permet de rechercher dans des images binaires des fichiers intégrés et du code exécutable. Effectivement, nous voyons bien un fichier .zip.

Pour extraire un fichier d’une image : binwalk -e cutie.png

Le fichier .zip est malheureusement bloqué par un mot de passe.

Nous utiliserons John the Ripper pour cracker le code : zip2john 8702.zip > hash.txt

john hash.txt

Voici ce que nous trouvons dans ce fichier .zip :

QXJlYTUx semble être hors du commun, cela semble encodé. J’utilise https://hashes.com/en/decrypt/hash pour décoder cela, et nous trouvons Area51 qui est le mot de passe de steg.
<aside>
💡 `Steghide` est en effet un outil de stéganographie numérique disponible sur Linux, ainsi que sur d'autres plates-formes comme Windows. Steghide permet de cacher des données dans des fichiers multimédias tels que des images ou des fichiers audio.
</aside>

Nous cherchons donc un moyen d'extraire les données de la deuxième photo grâce à steghide, et nous trouvons ceci : steghide extract -sf <NomDuFichier> un mot de passe nous sera demandé.

Nous voyons qu'un fichier .txt a été trouvé :

Nous nous connectons en SSH avec james@<ip> et le mot de passe hackerrules!

Nous commençons l’escalade des privilèges avec sudo -l. Nous trouvons (ALL, !root) /bin/bash, ce qui semble assez bizarre.

Nous faisons donc une recherche pour voir s'il existe un exploit pour cela :

Il suffit d’exécuter cette commande : sudo -u#-1 /bin/bash