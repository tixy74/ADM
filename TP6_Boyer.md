# TP5: Réseau et Sécurité

## BOYER Charles - 3IRC

---

Ce TP a été effectué sur une machine virtuelle, sous Ubuntu Server

---

### Exercice 1 : Adressage IP (rappels)

L'adresse IP est **172.16.0.0 /23**. Nous allons la décopuer en sous-réseau en utilisant la technique VLSM.

1. Sous Réseau 1: 38 machines

    * Adresse Réseau: _172.16.0.64 /26_
    * Adresse de Broadcast: _172.16.0.127 /26_
    * Plage: _172.16.0.65 /26_ - _172.16.0.126 /26_

2. Sous Réseau 2: 33 machines

    * Adresse Réseau: _172.16.1.64 /26_
    * Adresse de Broadcast: _172.16.1.127 /26_
    * Plage: _172.16.1.65 /26_ - _172.16.1.126 /26_

3. Sous Réseau 3: 52 machines

    * Adresse Réseau: _172.16.0.0 /26_
    * Adresse de Broadcast: _172.16.0.63 /26_
    * Plage: _172.16.0.1 /26_ - _172.16.0.62 /26_

4. Sous Réseau 4: 35 machines

    * Adresse Réseau: _172.16.0.192 /26_
    * Adresse de Broadcast: _172.16.0.255 /26_
    * Plage: _172.16.0.193 /26_ - _172.16.0.254 /26_

5. Sous Réseau 5: 34 machines

    * Adresse Réseau: _172.16.1.0 /26_
    * Adresse de Broadcast: _172.16.1.63 /26_
    * Plage: _172.16.1.1 /26_ - _172.16.1.62 /26_

6. Sous Réseau 6: 37 machines

    * Adresse Réseau: _172.16.0.128 /26_
    * Adresse de Broadcast: _172.16.0.191 /26_
    * Plage: _172.16.0.129 /26_ - _172.16.0.190 /26_

7. Sous Réseau 7: 25 machines

    * Adresse Réseau: _172.16.1.128 /27_
    * Adresse de Broadcast: _172.16.1.159 /27_
    * Plage: _172.16.1.129 /27_ - _172.16.1.158 /27_

---

### Exercice 2: Préparation de l'environnement

Tout d'abord, nous allons cloner la machine virtuelle. Pour cela:

* Clique droit sur la machine virtuelle -> Cloner -> Suivant
* Clone intégral -> Cloner

1. Mise en place de l'environnement de communication entre les machines:

    **Sur la machine serveur:**
    * Configuration
    * Menu Réseau
    * Adapter 1:
      * Mode d'accès réseau: Accès par pont
    * Adapter 2:
      * Mode d'accès réseau: Réseau interne (Pour que le serveur puisse communiquer avec le client)
      * Cocher: Activer l'interface réseau

    **Sur la machine client:**
    * Configuration
    * Menu Réseau
    * Adapter 1:
      * Mode d'accès réseau: Réseau interne (Pour que le client puisse communiquer avec le serveur)
      * Cocher: Activer l'interface réseau

2. Vérification de la configuration

    ```bash
    > ip a #Pour lister les interfaces réseaux de la machine jusqu'à la couche réseau
    ```

    On peux voir une interface _lo_. Cette interface est l'interface de loopback et est utilisé pour les communications intra-machine.

---

### Exercice 3 : Installation du serveur DHCP

1. Installation du paquet

    ```bash
    > sudo apt install isc-dhcp-server
    > systemctl status isc-dhcp-server # Le Deamon n'est pas activé car il n'a pas d'adresse IP à distribuer
    ```

2. Attribution d'une adresse IP statique

    ```bash
    > sudo netplan generate # Pour créer les fichiers de configuration
    > sudo nano /etc/netplan/00-installer-config.yaml #Le fichier de configuration
    # Placer dans le fichier (Attention ce sont des espaces, pas de tab):
        network:
         version: 2
         renderer: networkd
         ethernets:
          enp0s8:
           addresses:
            - 192.168.100.1/24
    > sudo netplan try # Pour tester la configuration
    > sudo netplan apply # Pour appliquer la configuration
    > sudo systemctl restart systemd-networkd # Pour redémarrer le service réseau
    > reboot
    > ip a # Pour voir si l'adresse IP est celle configuré.
    ```

3. Configuration du serveur DHCP

    ```bash
    > cp /etc/dhcp/dhcpd.conf /etc/dhcp/dhcpd.conf.bak # Backup de la conf
    > sudo nano /etc/dhcp/dhcpd.conf
    # Placer dans le fichier:
        default-lease-time 120;
        max-lease-time 600;
        authoritative; #DHCP officiel pour notre réseau
        option broadcast-address 192.168.100.255; #informe les clients de l'adresse de broadcast
        option domain-name "tpadmin.local"; #tous les hôtes qui se connectent au réseau auront ce nom de domaine

        subnet 192.168.100.0 netmask 255.255.255.0 { #configuration du sous-réseau 192.168.100.0
            range 192.168.100.100 192.168.100.240; #pool d'adresses IP attribuables
            option routers 192.168.100.1; #le serveur sert de passerelle par défaut
            option domain-name-servers 192.168.100.1; #le serveur sert aussi de serveur DNS
        }
    ```

    Les deux premières lignes concernent la durée minimal et maximal du bail.

4. Configuration de l'interface d'écoute (côté serveur)

    ```bash
    > sudo nano /etc/default/isc-dhcp-server
    # Ajouter dans le fichier
        INTERFACESv4="enp0s8"
    ```

5. Validation de la configuration

    ```bash
    > dhcpd -t
    > sudo systemctl restart isc-dhcp-server
    > sudo systemctl status isc-dhcp-server # Pour afficher si il est fonctionnel
    ```

6. Préparation du client

    **Sur la machine client**
    * Configuration
    * Menu Réseau
    * Decocher : Activer la carte Réseau
    * Allumer la machine virtuel

    ```bash
    > hostnamectl set-hostname client
    > hostname # Pour vérifier
    > sudo nano /etc/cloud/cloud.cfg.d/99_hostname.cfg # Pour éviter que le hostname ne change
    # Ajouter:
        preserve_hostname: true
    ```

7. Manipulation des logs

    **Sur la machine serveur:**

    ```bash
    > tail -f /var/log/syslog
    ```

    * DHCPDISCOVER: Message avertissant qu'une adresse MAC est présente dans l'interface enp0s8
    * DHCPOFFER: Message proposant une adresse IP libre pour l'adresse MAC découverte
    * DHCPREQUEST: Message du client pour une adresse IP libre
    * DHCPACK: Message d'acquisition de l'adresse IP pour le client

    **Sur la machine client (Après activation de la carte réseau):**

    ```bash
    > ip a # Pour vérifier qu'une adresse de la plage est bien attribuer
    ```

8. Le fichier ``` /var/lib/dhcp/dhcpd.leases ``` permet d'obtenir les informations des clients, où une adresse a été attribué par le serveur (date d'attribution, hostname...). La commande ``` dhcp-lease-list ``` permet d'afficher aussi des informations sur les clients, dans le terminal.

9. Vérification via ``` ping ```

    **Sur la machine serveur:**

    ```bash
    > ping 192.168.100.100 # Les paquets s'envoient
    ```

    **Sur la machine client (Après activation de la carte réseau):**

    ```bash
    > ping 192.168.100.1 # Les paquets s'envoient
    ```

10. Attribution d'une IP statique au client

    **Sur la machine serveur:**

    ```bash
    > sudo nano /etc/dhcp/dhcpd.conf
    # Ajouter dans le fichier (AVANT LE SUBNET):
        deny unknown-clients; #empêche l'attribution d'une adresse IP à une station dont l'adresse MAC est inconnue du serveur
        host client1 {
            hardware ethernet XX:XX:XX:XX:XX:XX; #remplacer par l'adresse MAC
            fixed-address 192.168.100.20;
        }
    ```

    **Sur la machine client:**

    ```bash
    > sudo dhclient -v #Pour libérer l'adresse
    ```

---

### Exercice 4 : Donner un accès à Internet au client

1. Autoriser l'IP Forwading

    **Sur la machine serveur:**

    ```bash
    > sudo nano /etc/sysctl.conf
    # Décommenter net.ipv4.ip_forward=1
    > sudo sysctl -p /etc/sysctl.conf # Pour prendreen compte les changements
    ```

2. Autorisation de traduction d'adresse source

    **Sur la machine serveur:**

    ```bash
    > sudo iptables --table nat --append POSTROUTING --out-interface enp0s3 -j MASQUERADE
    > ping 1.1.1.1 # Pour vérifier (sur le client)
    ```

---

### Exercice 5 : Installation du serveur DNS

1. Installation des paquets et vérification de l'installation

    ```bash
    > sudo apt install bind9
    > systemctl status bind9 #Pour vérifier que le service est actif
    ```

2. Configuration du service

    ```bash
    > sudo nano /etc/bind/name.conf.options
    # Décommenter dans le fichier et ajouter
        forwarders {
            1.1.1.1;
            8.8.8.8;
        }
    > systemctl restart bind9 #Pour prendre en compte la configuration
    > systemctl status bind9 #Pour vérifier que le service est actif
    ```

3. Test de la configuration

    **Sur la machine client:**

    ```bash
    > ping www.google.fr
    ```

4. Le navigateur en mode terminal

    **Sur la machine client:**

    ```bash
    > sudo apt install lynx
    > lynx wikipedia.fr
    ```

---

### Exercice 6 : Configuration du serveur DNS pour la zone tpadmin.local

1. Création du DNS privé : tpadmin.local

    ```bash
    > sudo nano /etc/bind/named.conf.local
    # Ajouter dans le fichier:
        zone "tpadmin.local" {
            type master; #c'est un serveur maître
            file "/etc/bind/db.tpadmin.local"; #lien vers le fichier de définition de zone
        };
    ```

2. Configuration du DNS privé

    ```bash
    > sudo cp /etc/bind/db.local /etc/bind/db.tpadmin.local
    > sudo nano /etc/bind/db.tpadmin.local
    # Remplacer dans le fichier (celui d'en bas):
        localhost par tpadmin.local. #NE PAS OUBLIER LE . A LA FIN
        127.0.0.1 par 192.168.100.1
    ```

3. Configuration de la zone inverse

    ```bash
    > sudo nano /etc/bind/named.conf.local
    # Ajouter dans le fichier:
        zone "100.168.192.in-addr.arpa" {
            type master;
            file "/etc/bind/db.192.168.100";
        };
    > sudo cp /etc/bind/db.127 /etc/bind/db.192.168.100
    # Remplacer la dernière ligne:
        0.0.1 par 100.168.192
        localhost par tpadmin.local.
    ```

4. Vérification de la configuration

    ```bash
    > cd /etc/bind/
    > named-checkconf named.conf.local
    > named-checkzone tpadmin.local /etc/bind/db.tpadmin.local
    > named-checkzone 100.168.192.in-addr.arpa /etc/bind/db.192.168.100
    ```

5. Validation de la configuration

    ```bash
    > sudo systemctl restart bind9
    > sudo systemctl status bind9
    ```

---

### Exercice 7 : Installation d'un serveur web

Voir fiche protocole 1ère année de BTS

---

### Exercice 8 : Installation d'un serveur de temps

Voir fiche protocole 2ème année de BTS

