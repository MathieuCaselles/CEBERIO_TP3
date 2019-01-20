
**4. Configuration  Réseau d&#39;une machine en CentOS**

Pour prouver que j&#39;ai internet depuis la vm, je télécharge la partie des «  Bind utilities » qui me servira après pour utiliser la commande DIG. Je tape donc sudo yum install bind-utils et je tape mon mdp. La partie se télécharge sans problème, j&#39;ai donc internet

Je ping l&#39;hôte et la vm pour voir si ils communiquent. Ils communiquent comme on peut le voir plus tard dans la partie 5.

Pour afficher la table de routage, on tape ip route.

Nous obtenons ceci. ![alt text](https://github.com/PierreYnov/CEBERIO_TP3/blob/master/1.png)


La route default a été mise en place par un DHCP. La route connectée (scope link) a été ajoutée automatiquement par le noyau (proto kernel) lors de la configuration de l&#39;adresse IP sur l&#39;interface enp0s3.

Il y a une seconde route sur l&#39;interface enp0s8 connecté entre 192.168.127.0 et 192.168.127.10

**5. Faire joujou avec quelques commandes**

Ping hôte à la vm

![alt text](https://github.com/PierreYnov/CEBERIO_TP3/blob/master/2.png)

Ping vm à l'hôte

 ![alt text](https://github.com/PierreYnov/CEBERIO_TP3/blob/master/3.png)

Afficher table routage hôte sur PowerShell , on tape route print -4

![alt text](https://github.com/PierreYnov/CEBERIO_TP3/blob/master/4.png)

Pour la vm on tape ip route

![alt text](https://github.com/PierreYnov/CEBERIO_TP3/blob/master/5.png)

**Curl**

Je tape sur la vm «  curl google.com «  qui fait une requête http et affiche l&#39;http de google.com

**Dig**

Je tape dig google.com et on me donne l&#39;ip 216.58.209.238

![](https://github.com/PierreYnov/CEBERIO_TP3/blob/master/gif_ip.gif)





#
# II. Notion de ports et SSH

## 1. Exploration des ports locaux

Je tape ss -4lnp

![alt text](https://github.com/PierreYnov/CEBERIO_TP3/blob/master/6.png)

Je vois que «  sshd » ecoute sur le port 22

## 2. SSH

Je lance putty et je mets l&#39;ip de ma vm 192.168.127.10 , je laisse en ssh par défaut et je lance. Je tape mon login et mon mot de passe et je suis enfin connecté en ssh

![alt text](https://github.com/PierreYnov/CEBERIO_TP3/blob/master/7.png)

## 3. Firewall

**A. SSH**  :

Le ssh est bien ouvert sur le port 2222

![alt text](https://github.com/PierreYnov/CEBERIO_TP3/blob/master/8.png)

Je tente de me connecter mais ça ne répond pas

J&#39;autorise avec le firewall la connexion sur le port 2222 en tapant firewall-cmd --add-port=2222/tcp –permanent

Je réessaie avec putty (car j&#39;ai Windows 8.1 et le ssh ne fonctionne pas sur le PowerShell) et cette fois ci, j&#39;arrive à me connecter

**B. netcat**

Je tape sur la console de ma vm : ncat –l –p 5454

Sur PowerShell je tape ncat 192.168.127.10 5454

Je tape sur le PowerShell et je vois bien le mot écrit sur la console de la vm, ça fonctionne à merveille.

# III. Routage statique

## 1. Préparation des hôtes (vos PCs)

Je me mets dans le réseau 12 sur Windows

![alt text](https://github.com/PierreYnov/CEBERIO_TP3/blob/master/9.png)

Je modifie mon reseau host only en mode pc1

![alt text](https://github.com/PierreYnov/CEBERIO_TP3/blob/master/10.png)

### Check



Je ping le PC 2 matthieu et je suis bien répondu

![alt text](https://github.com/PierreYnov/CEBERIO_TP3/blob/master/11.png)

Pour changer l&#39;ip dans CentOS je tape nano ** /etc/sysconfig/network-scripts/ifcfg-enp0s8**

![alt text](https://github.com/PierreYnov/CEBERIO_TP3/blob/master/12.png)

J&#39;arrive ici et je modifie l&#39;ip en 192.168.101.10 et je quitte en sauvegardant

Je ping ma vm1 a mon pc1 et ça répond correctement

![alt text](https://github.com/PierreYnov/CEBERIO_TP3/blob/master/13.png)

Matthieu se ping de vm2 a pc2 et ça fonctionne aussi

### Activation du routage sur les PCs

#### Windows

Je vais dans le regedit et à la direction HKEY\_LOCAL\_MACHINE\SYSTEM\CurrentControlSet\ Services\Tcpip\Parameters\IPEnableRouter et je mets la valeur a 1

![alt text](https://github.com/PierreYnov/CEBERIO_TP3/blob/master/14.png)

Je vais ensuite mettre en automatique Routage a accès distant dans services.msc et je redémarre mon pc.

Le service ne réussira pas à se lancer après 1h30 de tentative de fix le problème, matthieu en w10 aura son service qui marchera.

Je continue donc en faisant comme si ça marche et en simulant. ( on en a parlé discord )

## 2. Configuration du routage

### PC1

Je tape dans le powershell route 192.168.102.10/24 mask 255.255.255.0 192.168.112.2

### PC2

Matthieu fait l&#39;opération inverse soit route add 192.168.101.1/24 mask 255.255.255.0 192.168.112.1

Le cmd répond : OK !

Matthieu réussit donc à me ping

### VM2

Matthieu tape donc
![alt text](https://github.com/PierreYnov/CEBERIO_TP3/blob/master/15.png)

Les ping en theorie sont donc censé marcher puisque nous avons ajouté les routes pour qu&#39;ils le puissent

## 3. Configuration des noms de domaine

Pour changer le nom de domaine je tape nano /etc/hosts

Et j&#39;ajoute les ip et je met comme hostname vm et domaine tp3.b1

Je fais de meme pour le reste

Sur windows je vais dans C:\Windows\System32\drivers\etc et j&#39;ajoute l&#39;ip le hostname pc et domaine tp3.b1

Je fais de même pour le reste et ajoute à chaque fois les 4

![alt text](https://github.com/PierreYnov/CEBERIO_TP3/blob/master/16.png)
Cela fonctionne donc les fichiers hosts sont donc bien configurés
