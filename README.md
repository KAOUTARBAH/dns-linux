# Configuration d'un serveur DNS sur Ubuntu avec Bind9

- Configure la carte réseau de ta machine virtuelle en "Réseau Interne"
- Mettre en place un serveur DNS sur Linux avec Bind9
- Ce serveur fait autorité sur la zone rank.fr
- Créer un enregistrement de type A et CNAME
- Test la bon fonctionnement du serveur y compris depuis un client sur le réseau local
- Poste une procédure au format markdown permettant pas à pas d'obtenir cette configuration ainsi que les tests associés

## Étapes de configuration

### Étape 1. Configuration de la carte réseau en "Réseau Interne"
1. Arrêtez la VM.
2. Ouvrez les paramètres de la VM dans VirtualBox .
3. Allez dans **Réseau > Adaptateur 1**.
4. Sélectionnez **Réseau Interne** comme mode d’attachement.
5. Redémarrez la VM.

### Étape 2. Installer Bind9
- Exécutez les commandes suivantes sur la VM (serveur DNS):
  ```bash
  sudo apt update
  sudo apt install bind9 bind9utils bind9-doc -y

- Vérifiez l'état du service Bind9 :
  ```bash
  sudo systemctl status bind9

Assurez-vous qu'il est actif et en cours d'exécution.
![active-dns](https://github.com/KAOUTARBAH/dns-linux/blob/main/images/active-dns.png)

### Étape 3 : Configuration de BIND9 pour la zone `rank.fr`

1. Ouvrez le fichier de configuration principal de BIND9 pour y ajouter la zone `rank.fr` :
   ```bash
   sudo nano /etc/bind/named.conf.local

2. Ajoutez la configuration de la zone rank.fr comme suit :
    ```bash
    zone "rank.fr" {
        type master;
        file "/etc/bind/db.rank.fr";
    };

### Étape 4 : Créer un enregistrement de type A et CNAME

1. Créez le fichier de zone pour rank.fr :
    ```bash
    sudo cp /etc/bind/db.local /etc/bind/db.rank.fr
    
2. Modifiez le fichier de zone :
    ```bash
    sudo nano /etc/bind/db.rank.fr

3. Modifiez-le pour qu'il ressemble à ceci (avec des exemples d'enregistrements A et CNAME) :

    ```text
    ;
    ; BIND data file for the rank.fr domain
    ;
    $TTL    604800
    @       IN      SOA     ns1.rank.fr. admin.rank.fr. (
                            2025020501 ; Serial
                            604800     ; Refresh
                            86400      ; Retry
                            2419200    ; Expire
                            604800 )   ; Negative Cache TTL

    ; Serveurs de noms
    @       IN      NS      ns1.rank.fr.

    ; Enregistrements A
    ns1     IN      A       172.20.0.3
    www     IN      A       172.20.0.20

    ; Enregistrements CNAME
    mail    IN      CNAME   www.rank.fr.

4. Vérification de la syntaxe
Avant de recharger la configuration, il est important de vérifier que le fichier de zone est correctement formaté.
    ```bash
    sudo named-checkzone rank.fr /etc/bind/db.rank.fr
![checkzone](https://github.com/KAOUTARBAH/dns-linux/blob/main/images/checkzone.png)

5. Recharger BIND9
    ```bash
    sudo systemctl reload bind9

#### Configurer le serveur DNS pour qu'il soit accessible
- Modifier le fichier /etc/resolv.conf pour que le serveur DNS de la machine soit utilisé :
    ```bash
    sudo nano /etc/resolv.conf

- Ajouter la ligne suivante :
    ```bash
    nameserver 172.20.0.3

### Étape 5 : Tests de la configuration
#### Tester la résolution DNS localement
- Sur la machine où BIND9 est installé, vérifiez que les enregistrements sont correctement résolus.
- Tester l'enregistrement A pour rank.fr :
    ```bash
    dig @localhost rank.fr
![test-enregA](https://github.com/KAOUTARBAH/dns-linux/blob/main/images/test-enregA.png)

- Tester l'enregistrement CNAME pour www.rank.fr :
    ```bash
    dig @localhost www.rank.fr
![test-enregCNAME](https://github.com/KAOUTARBAH/dns-linux/blob/main/images/test-enregCNAME.png)

#### Tester depuis un autre client sur le même réseau local
- Sur une autre machine configurée avec l'adresse IP 172.20.0.3 comme serveur DNS (vous pouvez modifier /etc/resolv.conf ou la configuration DHCP de cette machine), effectuez les tests suivants :

- Tester l'enregistrement A :
    ```bash
    dig @172.20.0.3 rank.fr
![test-enregA-clt](https://github.com/KAOUTARBAH/dns-linux/blob/main/images/test-enregA-clt.png)

- Tester l'enregistrement CNAME :
    ```bash
    dig @172.20.0.3 www.rank.fr
![test-enregCNAME-clt](https://github.com/KAOUTARBAH/dns-linux/blob/main/images/test-enregCNAME-clt.png)
