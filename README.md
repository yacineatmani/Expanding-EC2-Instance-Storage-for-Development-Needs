# Expanding-EC2-Instance-Storage-for-Development-Needs
Expanding EC2 Instance Storage for Development Needs

### Étapes pour Augmenter l’Espace de Stockage et Accéder à l’Instance EC2

#### **Étape 1 : Identifier et Modifier le Volume Attaché à l’Instance EC2**

1. **Accéder à la Console AWS :**
   - Connectez-vous à la console de gestion AWS.
   - Accédez à la section **EC2**.

2. **Identifier l’Instance et le Volume :**
   - Recherchez l’instance EC2 nommée **xfusion-ec2**.
   - Dans l’onglet **Description** de l’instance, localisez la section **Block devices** pour identifier le volume attaché (par exemple, `vol-xxxxxxxx`).

3. **Modifier le Volume :**
   - Accédez à la section **Volumes** dans le tableau de bord EC2.
   - Sélectionnez le volume identifié et cliquez sur **Actions > Modify Volume**.
   - Changez la **taille** du volume de **8 GiB** à **12 GiB**.
   - Cliquez sur **Modify** et confirmez les modifications.

![Description de l'image]('Screenshot from 2024-08-28 16-43-10.png')

#### **Étape 2 : Étendre la Partition et le Système de Fichiers sur l’Instance**

1. **SSH dans l’Instance EC2 :**
   - Accédez à l'instance EC2 en utilisant la clé privée située sur l’hôte `aws-client` :
     ```bash
     ssh -i /root/xfusion-keypair.pem ec2-user@<EC2_Instance_Public_IP>
     ```
   - Remplacez `<EC2_Instance_Public_IP>` par l’adresse IP publique de l’instance EC2.

2. **Vérifier les Disques et Partitions :**
   - Vérifiez les disques et partitions présents :
     ```bash
     lsblk
     ```
   - Assurez-vous que le volume principal (`/dev/xvda`) est bien de 12 GiB, mais que la partition (`/dev/xvda1`) reste à 8 GiB.

3. **Étendre la Partition :**
   - Utilisez `growpart` pour étendre la partition pour qu’elle utilise tout l’espace disponible du volume :
     ```bash
     sudo growpart /dev/xvda 1
     ```
![Description de l'image]('Screenshot from 2024-08-28 16-50-46.png')

4. **Étendre le Système de Fichiers XFS :**
   - Étendez le système de fichiers XFS pour utiliser la partition étendue :
     ```bash
     sudo xfs_growfs /
     ```

5. **Vérifier la Taille du Système de Fichiers :**
   - Confirmez que la taille du système de fichiers a été étendue à 12 GiB :
     ```bash
     df -h /
     ```

#### **Étape 3 : Accéder à l’Instance via SSH depuis l’Hôte AWS-Client**

1. **Vérifier la Clé et les Permissions sur l’Hôte AWS-Client :**
   - Assurez-vous que la clé se trouve bien à l’emplacement `/root/xfusion-keypair.pem` sur l’hôte `aws-client`.
   - Ajustez les permissions de la clé :
     ```bash
     sudo chmod 400 /root/xfusion-keypair.pem
     ```
![Description de l'image](images/'Screenshot from 2024-08-28 17-00-55.png')

2. **SSH vers l’Instance EC2 :**
   - Depuis l’hôte `aws-client`, utilisez la commande suivante pour accéder à l’instance EC2 :
     ```bash
     ssh -i /root/xfusion-keypair.pem ec2-user@<EC2_Instance_Public_IP>
     ```
