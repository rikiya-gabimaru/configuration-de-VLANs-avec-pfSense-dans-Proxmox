## Configuration de VLANs avec pfSense dans Proxmox

## Sommaire
- [Licence](https://github.com/rikiya-gabimaru/configuration-de-VLANs-avec-pfSense-dans-Proxmox/blob/main/README.md#licence-)
- [Description du projet](https://github.com/rikiya-gabimaru/configuration-de-VLANs-avec-pfSense-dans-Proxmox/blob/main/README.md#description-du-projet-)
- [Outils du lab](https://github.com/rikiya-gabimaru/configuration-de-VLANs-avec-pfSense-dans-Proxmox/blob/main/README.md#outils-du-lab-)
- [Étape par étape](https://github.com/rikiya-gabimaru/configuration-de-VLANs-avec-pfSense-dans-Proxmox/blob/main/README.md#%C3%A9tape-par-%C3%A9tape-)

## Licence :
- Cette documentation est sous licence [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/deed.fr)

## Description du projet :
Cette procédure a pour objet de simuler le comportement d'un commutateur réseau configurer avec le standard IEEE 802.1Q dans l'environnement virtualisé qu'offre Proxmox.

## Outils du lab :

[Proxmox (8.2.7)](https://www.proxmox.com/)  
[pfSense (2.7.2)](https://www.pfsense.org/)  
[Open vSwitch (3.1.0)](https://www.openvswitch.org/)  

## Étape par étape : 

**1 - (sur le node) Installer Open vSwitch :**

`apt install openvswitch-switch`

**2 - (sur le node) Dans le menu System/Network créer l'interface VLAN (un vmbr qui fera office de switch) :**

- Appuyer sur le bouton `Create`
- Choisir `OVS Bridge`
- Nommer le bridge et éventuellement le commenter
- Appuyer sur `Create`

<p align="center">
  <img src="https://github.com/user-attachments/assets/91de2ad1-d1f8-4c7f-8603-4f5bf0efce7b">
</p>


**3 - (sur le node) Dans le menu System/Network créer les VLANs (ce qui permettra de "taguer" les trames des machines) :**

- Appuyer sur `Create`
- Choisir `OVS InPort`
- Nommer par le nom du vlan (exemple vlan100)
- Choisir l'OVS Bridge correspondant (celui créé à l'étape 1)
- Préciser le `VLAN Tag`
- Appuyer sur `Create`
- Répéter pour tous les VLANs à créer
- Appuyer sur le bouton "Apply Configuration"

<p align="center">
  <img src="https://github.com/user-attachments/assets/95ceb8ca-26cf-4f5c-86c8-312eb46d5219">
</p>

**4 - (sous le node) Configurer la VM pfSense :**

- Cliquer sur la VM pfSense
- Cliquer sur `Hardware`
- Cliquer sur le bouton `Add`
- Cliquer sur `Network Device` pour ajouter la 3ème interface
- Choisir l'OVS Bridge qui a été créé pour faire office de switch

#### (ce comportement simulera le fait de connecter physiquement la 3ème carte réseau de la VM pfSense au switch dans lequel on aurait configuré les VLANs)
<p align="center">
  <img src="https://github.com/user-attachments/assets/08c7d2d5-9e8c-4767-9724-1d9f4f032b8d">
</p>

**5 - Configurer pfSense :**

- Dans `Interfaces/Assignements/VLANs`
- Cliquer sur le bouton `+ Add`
- Dans `Parent Interfaces` choisir l'interface (OVS Bridge faisant office de switch L3)
- Préciser le `VLAN Tag`
- Préciser une description si souhaité

- Retourner dans `Interface Assignements`
- Cliquer sur le bouton `+ Add`
- Cliquer sur le vlan avec le bon TAG
- Cliquer sur le nom de l'interface `OPTx`
	- pour activer l'interface
	- pour lui donner un nom évocateur (qui apparaitra dans le shell)
	- préciser le type de configuration IP
	- configurer l'adresse de l'interface virtuelle du vlan
	- préciser le masque
	- appuyer sur le bouton `Save`
	- appuyer sur `Apply Changes`

**6 - Configurer les règles de flux par interface VLAN :**

- Dans Firewall/Rules/
- Choisir les interfaces sur lesquelles créer des règles
- Configurer le pare-feu en fonction de votre politique de sécurité

**7 - (sous le node) Faire correspondre les VMs à intégrer dans les VLANs à leur VLAN correspondant :**

- Cliquer sur la VM
- Cliquer sur `Hardware`
- Cliquer sur le bouton `Add`
- Cliquer sur `Network Device` pour ajouter leur interface réseau
- Choisir l'OVS Bridge qui a été créé pour faire office de switch
- Préciser le `VLAN Tag` dans lequel il faut intéfrer la machine
- Cliquer sur le bouton `+ Add`

<p align="center">
  <img src="https://github.com/user-attachments/assets/a9b46d82-1943-4e21-a682-76765ef086e6">
</p>

##
<sub><sup>
01010100 01101111 01101111 00100000 01101100 01100001 01110100 01100101 00100000 00100001
</sup></sub>
