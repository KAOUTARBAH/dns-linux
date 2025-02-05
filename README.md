# dns-linux

- Configure la carte réseau de ta machine virtuelle en "Réseau Interne"
- Mettre en place un serveur DNS sur Linux avec Bind9
- Ce serveur fait autorité sur la zone wilders.lan
- Créer un enregistrement de type A et CNAME
- Test la bon fonctionnement du serveur y compris depuis un client sur le réseau local
- Poste une procédure au format markdown permettant pas à pas d'obtenir cette configuration ainsi que les tests associés

## Étapes de configuration

### 1. Configuration de la carte réseau en "Réseau Interne"
1. Arrêtez la VM.
2. Ouvrez les paramètres de la VM dans VirtualBox .
3. Allez dans **Réseau > Adaptateur 1**.
4. Sélectionnez **Réseau Interne** comme mode d’attachement.
5. Redémarrez la VM.

### 2. Installer Bind9
- Exécutez les commandes suivantes sur la VM (serveur DNS):
  ```bash
  sudo apt update
  sudo apt install bind9 bind9utils bind9-doc -y

- Vérifiez l'état du service Bind9 :
  ```bash
  sudo systemctl status bind9

Assurez-vous qu'il est actif et en cours d'exécution.
![active-dns](https://github.com/KAOUTARBAH/dns-linux/blob/main/images/active-dns.png)

