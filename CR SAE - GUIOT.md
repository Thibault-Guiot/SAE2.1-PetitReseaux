# <p align="center"><u><b>Compte rendu de la SAE21 - Petit Réseaux</b></u></p>

## <u><b>Sommaire :</b></u>
<ul>
<li>Phase d'initalisation</li>
<li>Phase d'Adressage</li>
<li>Phase de Configuration</li>
<li>Conclusion</li>
</ul>

## <li><u><b>Phase d'initalisation :</b></u></li>
#### Dans un premier temps, j'ai pris connaissance du sujet, analyser les différentes demande, analyser les différentes configurations qu'il faudra faire..
#### Et surtout prendre connaissance de la maquette, qu'il faudra configurer :
<img src="Image SAE/Maquette.PNG">

<br>
<br>
<br>

## <li><u><b>Phase d'Adressage :</b></u></li>
#### Pour commencer le parcours de cette SAE, il m'a fallu réaliser un plan d'adressage, pour les différents réseaux (Interne, DMZ, Opérateur, ...)
<br>

### <u><b><i>- Plan d'Adressage du réseau interne :</i></b></u>
<img src="Image SAE/RInterne.PNG">

```
Le plan d'adressage à été réalisé comme cela, car il est configurer en DHCP.. Sous l'image ci-dessus, les couleurs attribuent l'adressage.
```
<br>

### <u><b><i>- Plan d'Adressage du DMZ :</i></b></u>
<img src="Image SAE/DMZ.PNG">

```
Le plan d'adressage du DMZ à été réalisé à l'aide d'adresse publique (200.0.0.0/24).
- Le serveur Web à été configurer en .80, en référence au protocole HTTP
- Le serveur DNS configuré en .53, référence à l'utilisation du port 53 par le protocole DNS.
```
<br>

### <u><b><i>- Plan d'Adressage du Réseau Opérateur :</i></b></u>
<img src="Image SAE/ROpérateur.PNG">

```
L'adressage du réseau opérateur, imposé d'être éffectué en '/30', soit 2 machines par sous-réseaux.
```
<br>

### <u><b><i>- Plan d'Adressage des Sites Délocalisés :</i></b></u>
<img src="Image SAE/SDéloc.PNG">

```
Le plan d'adressage ci-dessus est effectué de façon à suivre la suite logique de la configuration de mon réseau interne.
```

<br>
<br>
<br>

## <li><u><b>Phase de Configuration :</b></u></li>

### <u><b><i>- Configuration du Réseaux Interne :</i></b></u>
<ul>

#### <li>Pour commencer le réseaux doit être configurer en DHCP, pour se faire j'ai réalisé cette configuration via le routeur encadré en noir ci-dessous :
<img src="Image SAE/RInterne2.png"></li>

<br>

#### <li>Le réseaux interne doit être configurer avec 3 sous-réseaux :
<ol>
    <li>Commerce en : 192.168.10.0/24</li>
    <li>Technique en : 192.168.20.0/24</li>
    <li>Administratif en : 192.168.30.0/24</li>
</ol></li>
<br>

#### <li>Dans un premier temps les VLANs ont du être configurés, via le switch dans le menu "Config" :
<img src="Image SAE/VLAN.png">

Puis être attribuer à une interface, voir ci-dessous :
<img src="Image SAE/VLAN2.png"></li>

<br>

#### <li>Configuration du routeur en DHCP :
<img src="Image SAE/DHCP1.png">

Après avoir mis les machines en configuration DHCP:
<img src="Image SAE/DHCP2.png">

 Réaliser les commandes suivantes sur le routeur (dans notre cas celui entouré en noir plus haut) :
- Router(config)#ip dhcp pool Commercial
- Router(dhcp-config)#network 192.168.10.0 255.255.255.0
- Router(dhcp-config)#default-router 192.168.0.254

<br>

Cette configuration permet aux différentes machines de communiquer entre elles:
- Les commerciaux communiquent entre eux
- Les techniciens communiquent entre eux.
- Les Administratifs communiquent entre eux
</li>
<br>

#### <li>Configurer sur les sous interfaces du réseaux, grâce à des ACL :
<img src="Image SAE/ACL1.png">

Pour répondre à toutes les tâches que doit réalisé notre Réseau Interne, il faut que les techniciens communiquent avec tout le monde (MAIS SEULEMENT LES TECHNICIENS QUI COMMUNIQUENT)

<br>

A l'aide des commandes suivantes :
- routeur(config)#ip access-list extended Tech-Comm
- routeur(config-ext-nacl)#permit ip 192.168.20.0 0.0.0.255 192.168.10.0 0.0.0.255
- routeur(config-ext-nacl)#permit icmp any 192.168.20.0 0.0.0.255 echo-reply
- routeur(config-ext-nacl)#exit
<br>

Cette configuration permet aux techniciens de se connecter aux machines des commerciaux, et des administratifs en cas de problème réseau.

#### <li>Configuration du Web Interne :
Pour répondre à la dernière partie de notre réseau interne, le Web Interne, celui-ci doit être accessible par tous.
Pour ce faire, je lui ai créer un VLAN à part des autres, et ajouter une ACL permettant à tout le monde de se connecter :
<img src="Image SAE/VlanWebInterne.png">
<img src="Image SAE/ACLWeb.png">

Puis intégrer un petit bout d'HTML dans la partie HTTP du server "Web Interne"
<img src="Image SAE/http.png">
<img src="Image SAE/HTTP2.png">
<br></li>
</ul>
<br>

### <u><b><i>- Configuration du Réseaux DMZ :</i></b></u>
<ul>

#### <li>Le réseau DMZ avait pour condition d'être configurer avec un adressage IP publique en 200.0.0.0/24</li>
<br>

#### <li><b>Voici les différentes adresses utilisés :</b>
<ol>
    <li>Routeur DMZ-Réseau Interne : 200.0.0.254/24</li>
    <li>Routeur DMZ-Réseau Opérateur : 200.0.0.2/24</li>
    <li>Serveur WEB : 200.0.0.80/24</li>
    <li>Serveur DNS : 200.0.0.53/24</li>
</ol></li>
<br>

#### <li>J'ai configurer les serveurs afin que chacun est une configuration fonctionnelle :
<img src="Image SAE/DNS.png">
<img src="Image SAE/WEB.png"></li>
<br>

#### <li>Pour la communication avec le réseau interne, j'ai réalisé la configuration à l'aide du NAT :
<img src="Image SAE/NAT.png">

```
La configuration ci-dessus à été réalisé de manière à répondre à notre mission, c'est à dire que tout le monde sauf le réseau Technicien, peut se connecter au DMZ.
```

Pour sa configuration, voici les commandes utilisé :
- R1(config)#int fa0/0.10
- R1(config-if)#ip nat inside
- R1(config-if)#exit
- R1(config)#int fa0/0.30
- R1(config-if)#ip nat inside
- R1(config-if)#exit
- R1(config)#int fa0/0.40
- R1(config-if)#ip nat inside
- R1(config-if)#exit
- R1(config)#int fa0/1
- R1(config-if)#ip nat outside
- R1(config-if)#exit

Puis :
- ip nat inside source list 1 interface FastEthernet0/1 overload
- ip nat inside source list 100 interface FastEthernet0/1 overload
- ip nat inside source list 2 interface FastEthernet0/1 overload
- ip nat inside source list 3 interface FastEthernet0/1 overload
- ip nat inside source list 4 interface FastEthernet0/1 overload

Et pour finir :
- access-list 2 permit 192.168.20.0 0.0.0.255
- access-list 3 permit 192.168.30.0 0.0.0.255
- ip access-list extended NAT
 - permit ip any 200.0.0.0 0.0.0.255
 - permit ip 200.0.0.0 0.0.0.255 any
 - permit icmp any any echo-reply
- access-list 4 permit 192.168.40.0 0.0.0.255
- access-list 1 permit 192.168.10.0 0.0.0.255
</li>
</ul>

<br>
<br>

### <u><b><i>- Configuration du Réseaux Opérateur :</i></b></u>
<ul>

#### <li>Le réseau opérateur avait pour objectif d'être configurer en /30 avec 2 machines par réseau. Pour ce faire voici les adresses utilisées :
- Routeur 0 : 192.168.200.2; 192.168.201.2; 192.168.202.2; 192.168.203.2
- Routeur 1 : 192.168.201.1
- Routeur 2 : 192.168.200.1
- Routeur 3 : 192.168.202.1
- Internet : 192.168.203.1
</li>

#### <li>Pour la communication entre le DMZ et le Réseau Opérateur, j'ai utilisé de l'OSPF, pour réaliser l'interconnexion entre les 2 réseaux :
<img src="Image SAE/OSPF.png">

```
Comme nous pouvons le voir ci-dessus, la configuration OSPF à bien été réaliser ce qui permet maintenant au packet de communiquer du réseau DMZ au Réseau Opérateur
```

Les commandes suivantes ont été utilisés :
- R1(config)#router ospf 100
- R1(config-router)#network 192.168.200.0 0.0.0.3 area 0
- R1(config-router)#network 200.0.0.0 0.0.0.255 area 0
</li>
</ul>

<br>

### <u><b><i>-Configuration des Sites Délocalisés :</i></b></u>
<ul>

#### <li>La partie qui à été pour moi la plus dure à réaliser est celle du VPN, ne l'ayant jamais réalisé et travaillé au par avant, c'est une partie qui ma fait perdre énormement de temps</li>

#### <li>Dans un premier temps, il ma fallut mettre une adresse pour chaque site délocalisé :
<img src="Image SAE/SD1.png">
<img src="Image SAE/SD2.png">

- Le premier est configuré en 192.168.50.0/24
- Le deuxième en 192.168.60.0/24
</li>

#### La seule partie ne marchant pas de ma maquette, je mets la coonfiguration ci-dessous, qui est censé marcher :

#### <li>Partie VPN :
- Voici toutes la configuration :
<img src="Image SAE/VPN1.png">
<img src="Image SAE/VPN2.png">
<img src="Image SAE/VPN3.png">
<img src="Image SAE/VPN4.png">
<img src="Image SAE/VPN5.png">
</li>
</ul>
</ul>

## <li><u><b>Conclusion :</b></u></li>
### Voilà ce qui est censé être la maquette 100% fonctionnelle de ma SAE2.1.
### Ce projet à été vraiment complet, mettant différentes compétences en avant, la plus grosse difficultée que je n'ai pas réussi à résoudre c'est la configuration du VPN.