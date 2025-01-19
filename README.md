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

2. **Décompression de PrestaShop :**
   ```bash
   sudo unzip prestashop_8.2.0.zip
   ```

3. **Suppression du fichier zip :**
   ```bash
   sudo rm prestashop_8.2.0.zip
   ```

4. **Permissions :**
   ```bash
   sudo chown -R www-data:www-data /var/www/html
   sudo chmod -R 755 /var/www/html
   ```

5. **Activation du module rewrite :**
   ```bash
   sudo a2enmod rewrite
   sudo systemctl restart apache2
   ```

## Étape 4 : Création d'une archive de sauvegarde

1. **Créer une archive des fichiers importants de PrestaShop :**
   ```bash
   sudo zip -r prestashop_full_backup.zip img upload download themes var/cache cache
   ```

2. **Vérifiez l'existence de l'archive :**
   - Vous devriez voir un fichier nommé `prestashop_full_backup.zip` dans le répertoire `/var/www/html`.
   - Conservez ce fichier pour une utilisation ultérieure lors de la configuration du S3.
