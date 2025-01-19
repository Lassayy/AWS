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
     - Utilisez la commande suivante pour vous connecter :
       ```bash
       ssh -i NOMDELACLE.pem ubuntu@IP_EC2
       ```
