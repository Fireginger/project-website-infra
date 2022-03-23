# Projet Infra B1 Ynov

**Notre projet d infra nous c'était de créer et d'héberger un site comparateur de crypto-monnaies en ligne.**

**Ce projet a été réalisé par** :

- EVEILLARD Thomas
- EYMAS Eliott
- RIVRY Théo
- RUEMELI Quentin

## Sommaire

- [Projet Infra](#projet-infra)
  - [Sommaire](#sommaire)
  - [Installation](#installation)
    - [Apache](#apache)
  - [Schéma](#schéma)
  - [Utilisation de la solution](#utilisation-de-la-solution)
  - [Monitoring](#monitoring)
  - [Backup](#backup)


## Installation

**Voici une documentation d'installation complète pour notre projet** :

Pour commencer, nous avons loué une machine virtuelle sur OVH, donc toute notre installation c'est directement faite sur cette machine.

Donc, avant toute installation il faut paramétrer la machine virtuelle avec toutes les initialisations :
- **Créer un mot de passe pour le compte root** : 
```bash
sudo passwd root
```
- **Vérifier si la machine est bien à jour** :
```bash
sudo apt-get update
```
```bash
sudo apt upgrade
```
- **Créer un compte utilisateur** :
```bash
adduser [identifiant]
```
- **L'ajouter au sudoers pour qu'il ai les droits sudo** :
```bash
usermod -aG sudo [identifiant]
```
Quand ces paramétrages sont fait on va pouvoir commencer à installer notre service web.

# Apache

Maintenant nous allons passer à l'installation de notre service web **Apache** :

Installation du paquet :
```bash
sudo apt install apache2
```
Vérification du service apache2 :
```bash
sudo systemctl status apache2
```
Ouverture du firewall pour les ports de Apache :
```bash
sudo ufw allow 'Apache Full'
```
'Apache full' permet d'ouvrir directement les ports 80 et 443, les ports correspondants a HTTP et HTTPS.

Il faut maintenant configurer une racine web pour Apache, ce qui va nous permettre des mettre des fichiers HTML et CSS pour notre site internet :

La racine web on va la mettre à ```/var/www/cryptocomp.com``` et c'est ici qu'on va mettre nos fichiers html.
Pour cela on va venir modifier les fichiers de configurations de apache2 pour y changer la racine web.

Pour les modifications soit prises en compte, il nous suffit juste de redemarrer le service :
```bash
sudo systemctl restart apache2
```

Quand tous cela est fait, l'installation de Apache2 est enfin terminé.

Quand l'installation de Apache2 est terminé, on va avoir besoin d'installer d'autres choses tels que Certbot, qui va nous permettre d'obtenir un certificat https pour notre site internet.

Pour cela, on va d'abord avoir besoin d'installer snapd :
```bash
sudo apt update

sudo apt install snapd
```
Ensuite pour les commandes d'après on a besoin de passer en utilisateurs root :
```bash
su root
```
Quand on est en root on fais les commandes suivantes :
```bash
apt update

apt install snapd
```
Après on repasse en utilisateur normal et on fais :
```bash
sudo snap install core
```
Et notre snapd est installé. Quand snapd est installé on peut repasser à l'installation de certbot :
```bash
sudo apt-get remove certbot
```
Cette commande sert à enlever le certbot auto, ensuite :
```bash
sudo snap install --classic certbot
```
```bash
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```
On fait cette commande pour s'assurer que certbot peut se lancer, et enfin on fait cette commande :
```bash
sudo certbot --apache
```
Et on choisit sur quelle site internet on veut notre certificat HTTPS.

## Schéma

**Voici un schéma avec les machines impliqués dans notre projet** :

![schéma projet](./pics/schéma_projet.PNG)


## Utilisation de la solution

Pour utiliser la solution il suffit juste de se rendre sur le site internet : https://cryptocomp.ovh/

Ce qui nous amène directement sur notre site internet.


## Monitoring

Pour faire du monitoring, nous avons utilisé, sur les conseils de Léo, Netdata. Pour installer Netdata, il faut rentrer la commande :
```bash
curl https://my-netdata.io/kickstart.sh > /tmp/netdata-kickstart.sh && sh /tmp/netdata-kickstart.sh
```

Lorsqu'on a fait cette commande, pour accéder au panneau de controle de Netdata, il nous suffit juste d'écrire sur notre navigateur :
**https://[notre_site_internet]:19999**
et on accède à Netdata.

Après, avec Netdata on peut configurer des alertes pour que lorsque la machine va planter, une alerte est envoyée avant que ça arrive pour nous avertir.
Dans notre cas, nous avons parametré une alerte sur un serveur discord, pour cela, il suffit juste d'aller dans le fichier de conf de Netdata grâce à la commande : 
```bash
sudo nano /etc/netdata/netdata.conf
```
Et dans ce fichier de conf, on va voir un paragraphe consacré a discord où on peut y attacher un webhook. On a juste à créer un webhook sur le serveur discord qu'on veut et mettre le lien dans le fichier de conf.

Avec Netdata, les principales fonctions qui sont monitorisés sont :

- Le CPU de la machine
- La RAM utilisée
- Le nombre de requêtes et de connexions au service web Apache
- La mémoire de la machine

Ce sont les fonctions qui ont besoin d'être monitorisé pour que le serveur web et la machine virtuelle marche bien.


## Backup

Pour le projet, nous pouvions mettre en place une backup pour les fichiers et les base de données:

Dans notre cas, nous n'avions pas de base de données donc nous n'avions pas besoin de faire de backup des bases de données, et nous n'avons pas mise en place de backup automatique, nous avons juste fais une sauvegarde des fichiers de confs suivants :

- **fichier de conf de Apache2**
- **fichier de conf de Netdata**
- **les fichiers html et css reliés a notre serveur**

On a fait une backup de ces fichiers parce que ce sont les principaux fichiers où on à fait beacoup de modifications et tous ces fichiers ont été sauvegardés dans le cas où la machine a besoin d'être réinitialiser pour une raison, tous ces fichiers ont été sauvegardés pour que la machine soit reparametrée plus facilement.
