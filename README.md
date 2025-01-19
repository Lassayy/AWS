# Procédure du TP : Création d'une infrastructure AWS pour PrestaShop

Dans ce TP, nous allons créer une infrastructure complète pour héberger le CMS PrestaShop, avec les éléments suivants :

- **Création d'une instance EC2** pour héberger le CMS.
- **Création d'un RDS** pour la base de données.
- **Création d'un S3** pour le stockage et la mise en cache des fichiers.

## Étape 1 : Création d'un EC2 sur la console AWS

1. **Accéder au service EC2 :**
   - Dans la barre de recherche, tapez **EC2**.
   - Cliquez sur **"Lancer une instance"**.

   ![Image Alt](https://github.com/Lassayy/AWS/blob/2f632437d3a33de538ea3bacd8d8159af8a98b42/EC2.png)

2. **Configurer l'instance EC2 :**
   - **Nom et balises :** "srv-prestashop".
   - **Images d'applications et de systèmes d'exploitation (Amazon Machine Image) :** Ubuntu.
   - **Type d'instance :** `t2.micro`.

3. **Paire de clés (connexion) :**
   - Cette étape est optionnelle, mais recommandée si vous souhaitez vous connecter en SSH à votre instance EC2 sans passer par la console web.
   - Choisissez **"Créer une paire de clés"**.
   - **Type de paire de clés :** RSA.
   - **Format de fichier de clé privée :** `.pem`.
   - **Stocker la clé privée :**
     - Placez le fichier `.pem` dans le répertoire `/home` de votre WSL (Windows Subsystem for Linux).
     - Appliquez les bons droits au fichier avec la commande :
       ```bash
       chmod 400 NOMDELACLE.pem
       ```
   - **Connexion à l'instance EC2 :**
     - Utilisez la commande suivante pour vous connecter une fois l'EC2 créé :
       ```bash
       ssh -i NOMDELACLE.pem ubuntu@IP_EC2
       ```

4. **Configuration des réseaux :**
   - Cochez la case **Autoriser le trafic HTTP depuis Internet**.
   - Cliquez sur **Lancer l'instance**.

## Étape 2 : Configuration de l'instance EC2

1. **Mise à jour du système :**
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

2. **Installation d'Apache2 :**
   ```bash
   sudo apt install apache2 -y
   ```

3. **Installation des dépendances PHP :**
   ```bash
   sudo apt install php libapache2-mod-php php-xml php-zip php-curl php-gd php-mysql php-intl php-mbstring -y
   ```

4. **Redémarrage d'Apache :**
   ```bash
   sudo systemctl restart apache2
   sudo systemctl start apache2
   sudo systemctl enable apache2
   ```

5. **Vérification des modules PHP :**
   ```bash
   php -m | grep -E 'xml|zip'
   ```

6. **Installation de Composer :**
   ```bash
   curl -sS https://getcomposer.org/installer | php
   sudo mv composer.phar /usr/local/bin/composer
   ```

7. **Installation de zip et unzip :**
   ```bash
   sudo apt install zip unzip -y
   ```

8. **Configuration du répertoire web :**
   ```bash
   cd /var/www/html
   sudo rm index.html
   ```

## Étape 3 : Installation de PrestaShop

1. **Téléchargement de PrestaShop :**
   - Assurez-vous d'être dans le répertoire `/var/www/html`.
   - Lancez la commande :
     ```bash
     sudo wget "https://assets.prestashop3.com/dst/edition/corporate/8.2.0/prestashop_edition_basic_version_8.2.0.zip?token=9c3095b0a0" -O prestashop_8.2.0.zip
     ```

2. **Décompression de prestashop_8.2.0.zip :**
   ```bash
   sudo unzip prestashop_8.2.0.zip
   ```

3. **Suppression du fichier prestashop_8.2.0.zip :**
   ```bash
   sudo rm prestashop_8.2.0.zip
   ```
   
4. **Décompression de prestashop.zip :** (Cliquez sur A quand vous verrez un message apparaître)
   ```bash
   sudo unzip prestashop.zip
   ```
   
5. **Suppression du fichier prestashop.zip :** 
   ```bash
   sudo rm prestashop.zip
   ```
   
6. **Permissions :**
   ```bash
   sudo chown -R www-data:www-data /var/www/html
   sudo chmod -R 755 /var/www/html
   ```

7. **Activation du module rewrite :**
   ```bash
   sudo a2enmod rewrite
   sudo systemctl restart apache2
   ```

## Étape 4 : Création d'une instance RDS pour héberger la base de données du CMS

1. **Accéder au service RDS :**
   - Tapez **RDS** dans la barre de recherche sur la console AWS.

   ![Image Alt](https://github.com/Lassayy/AWS/blob/f2047b9eb1a475a6cf53c136b69dd5bef7fbc9c0/rds.png)

2. **Configurer l'instance RDS :**
   - **Choisir une méthode de création de bases de données :** Création standard.
   - **Options de moteur :** MySQL.
   - **Modèles :** Offre Gratuite.
   - **Identifiant d'instance de base de données :** `database-prestashop`.
   - **Identifiant principal :** `admin`.
   - **Mot de passe principal :** `Azerty123!`.

3. **Connectivité :**
   - Sélectionnez **Se connecter à une ressource de calcul EC2**.
   - Choisissez votre instance EC2 dans la liste déroulante.

4. **Finalisation :**
   - Faites défiler jusqu'en bas et cliquez sur **Créer une base de données**.
   - Attendez que la création soit terminée et vérifiez que le statut est "Disponible" avant de passer à la suite.


## Étape 5 : Aller sur l'installation assistant pour configurer le CMS

1. **Récupérez l'adresse DNS IPv4 publique de votre instance EC2 :**
   - Allez dans le **Résumé de l'instance** de votre EC2 et copiez l'adresse DNS IPv4 publique.

   ![Image Alt](https://github.com/Lassayy/AWS/blob/dd04fff31b4397eb31610077f6bf94441c453f04/DNS%20IPv4%20public.png)

2. **Accédez à l'installation PrestaShop :**
   - Tapez dans un navigateur internet :
     ```
     http://DNS_IPv4_public/install
     ```
   - Exemple :
     ```
     http://ec2-54-91-65-108.compute-1.amazonaws.com/install
     ```

3. **Installation de PrestaShop :**
   - Vous arriverez sur l'assistant d'installation de PrestaShop.

     ![Image Alt](https://github.com/Lassayy/AWS/blob/78a2ad0e98f632d4345dafc53f0b4b72c906209d/assistantprestashop.png)

4. **Configuration initiale :**
   - Choisissez la langue (ex. : français).
   - Cochez **"J'accepte les termes et conditions du contrat ci-dessus"**.
   - Configurez les paramètres comme bon vous semble.

     ![Image Alt](https://github.com/Lassayy/AWS/blob/72569e3a1a13e2c50bc9cdd7a26ddbee13068a6a/information.png)

5. **Connexion à la base de données :**
   - Renseignez les informations de votre instance RDS créée à l'étape 4.
   - **Adresse du serveur de la base :** Récupérez le **point de terminaison** dans l'onglet **Connectivité et sécurité** de votre instance RDS.

     ![Image Alt](https://github.com/Lassayy/AWS/blob/b74bbe1e31dc1668dd032805de5a32022e704d20/Assistant%20d'installation.png)

6. **Finalisation :**
   - Cliquez sur **"Essayer de créer la base de données automatiquement"**.
   - Une fois le message **"Base de données créée"** affiché, cliquez sur **Suivant**.
   - Attendez la fin de l'installation de la boutique.
  
7. **Supprimez le dossier `install` :**
   - Une fois l'installation terminée, vous devez supprimer le dossier `install` de votre EC2 dans `/var/www/html` :
     ```bash
     sudo rm -rf /var/www/html/install
     ```

8. **Accédez à l'administration de votre boutique :**
   - Cliquez sur **"Gérer votre boutique"** pour procéder à la configuration de celle-ci.

     ![Image Alt](https://github.com/Lassayy/AWS/blob/ffd1b2a263889d123fdaf61f5913d621ebaeb07a/black-office.png)

   - En cliquant sur "Gérer votre boutique", l'URL va automatiquement pointer sur le serveur dans `/var/www/html/admin`. Toutefois, il est possible que sur votre serveur, le dossier `/admin` soit renommé automatiquement, par exemple en `admin346gmsyozgvxdmpqjq9`.
   - Vous devez donc adapter votre URL pour accéder à l'administration du prestashop comme suit :
     ```
     http://ec2-54-91-65-108.compute-1.amazonaws.com/admin346gmsyozgvxdmpqjq9
## Étape 6 : Mise en cache sur un bucket S3

### Activer les options 3C sur PrestaShop
1. **Accédez à l'interface d'administration :**
   - Allez dans **Paramètres avancés > Performances**.
   - Dans la section **"CCC (Concaténation, Compression et mise en Cache)"** :
     - Activez les options en mettant les cases sur **Oui**.

   ![Image Alt](https://github.com/Lassayy/AWS/blob/47c8135cdd0f23a97ae8054d0fa0c7b97813e5dc/ccc.png)

2. **Cliquez sur Enregistrer** après avoir activé ces options.

---

### Créer une archive pour le stockage sur un S3
1. **Connectez-vous à votre instance EC2** et allez dans le répertoire `/var/www/html` :
   ```bash
   cd /var/www/html
   ```

2. **Créer une archive des fichiers importants de PrestaShop :**
   ```bash
   sudo zip -r prestashop_full_backup.zip img upload download themes var/cache cache
   ```

3. **Vérifiez l'existence de l'archive :**
   - Vous devriez voir un fichier nommé `prestashop_full_backup.zip` dans le répertoire `/var/www/html`.

4. **Téléchargez le fichier ZIP en local sur votre PC :**
   - Ouvrez un navigateur et entrez l'URL suivante en l'adaptant à votre serveur :
     ```
     http://ec2-54-91-65-108.compute-1.amazonaws.com/prestashop_full_backup.zip
     ```
   - Un fichier va se télécharger. Décompressez-le sur votre ordinateur.

---

### Création d'un bucket S3
1. **Accédez au service S3 :**
   - Dans la barre de recherche de la console AWS, tapez **S3** et cliquez sur **Créer un compartiment**.

   ![Image Alt](https://github.com/Lassayy/AWS/blob/1f2191e35afe6ac74ea2459d134b70b2aecf8bab/s3.png)

2. **Configurer le bucket :**
   - **Nom du compartiment :** `s3-prestashop`.
   - **Paramètres de blocage de l'accès public pour ce compartiment :**
     - Décochez toutes les cases pour autoriser l'accès public si nécessaire.

3. **Créer le compartiment :**
   - Faites défiler jusqu'en bas et cliquez sur **Créer un compartiment**.

4. **Téléchargez les fichiers dans le bucket :**
   - Accédez à votre bucket S3 fraîchement créé.
   - Cliquez sur **Charger** et ajoutez un dossier.
   - Sélectionnez votre dossier décompressé `prestashop_full_backup`.
   - Cliquez sur **Charger** et attendez la fin du chargement.
  
   ![Image Alt](https://github.com/Lassayy/AWS/blob/ef29d2355b71dec2d0bc28db3ac5f7c4d39d5626/chargement%20cache%20reussis.png)

---
