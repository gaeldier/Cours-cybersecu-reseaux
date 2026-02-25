// oN va changer toutes les images, les formats ne sont pas bons, respecter la premiere occurance 


### Types d'attaques
- Couche 1 : man-in-the-middle, splitter, rubberducky, gateway 

- Couche 2 : man-in-the-middle -> modification de table ARP, spamming apr, détournement arp  ![alt text](<res/Pasted image 20260124103504.png>)
	- Couche 3 : Session : Déchiffrement, cookies volés 
	- Couche 4: utilisateur -> plus d'attaques faisables, donc plus de protections disponibles
### Rappels : 
![alt text](<res/Pasted image 20260131083349.png>)


### L'anssi :
Donne les normes et certifications pour trouver des bons partenaires et architectures sécurisées. 

#### Aucun produit americain certifié ANSSI, normal

Teamviewer est pourri niveau sécurité : Il copie/colle le presse papier, redirige certaines données etc.. 

## Captures de trames 

**Wireshark(1), TCPDump(2)** : Pour du débug, Audit/analyse de sécurité

(1) : Outil graphique, utilise la lib libpcap/winpcap -> permet de rejouer les captures effectuées via tcpdump, *Permet de faire du débug, capture de traffic et possibilité de voler des données

(2) : Outil non graphique mais présent presque tout le temps sur Linux, utilise libpcap

`tcpdump -ni <interface> `
Juste -i donnera les noms de machines propres

**Petit rappel** TCP : Numéro de séquence = quantité de données transmises 

TCP garantie la fiabilité contrairement a udp, grâce au numéro de sequence et acquittement

![alt text](<res/Pasted image 20260131091824.png>)(Fonctionnement TCP syn ack)

SACK (Selective acknolegement)

option tcp qjui permet de demander la réemission de certains paquets uniquement.
![[Pasted image 20260131092748.png]]
ack : dit qu'il a tout reçu jusqu'à 1401, SACK RE veut dire qu'il a reçu de 2301 à LE :4101. 
*Il doit donc récuperer les paquets entre 1401 et 2301.



Man expliqué de tcpdump : 
![alt text](<res/Pasted image 20260131093751.png>)

Quelques exemples : 

- Capture de paquets en provenance de l'adresse 192.168.1.1 a Dest 91.212.116.102 sur port 80 sir eth1
 
Rappel : On peut utiliser tcpdump -ni wlp3s0 [source|dst|host|] 

Réponse : tcpdump -ni eth1 src 192.168.1.1 or dest 91.212.116.102 and port 80

Déroulement d'une connexion puis à un site internet : 

1) DHCP : demande une ip, une gateway, un ou plusieurs dns
2) j'ai tappé google.fr (émission de données), le DNS regarde la table de routage pour arriver à notre adresse, regarde notre table ARP pour trouver l'adresse MAC de la gateway


###  **FTP** :
Envoyer et recevoir/partager des fichiers en réseau
![alt text](<res/Pasted image 20260131103148.png>)
![alt text](<res/Pasted image 20260131103201.png>)

**Mode passif** : client vient se connecter pour récuperer de la donnée
Il calcule le port en faisant `a*256+b` 


Récap visuel : 
![alt text](<res/Pasted image 20260131103433.png>)


**Mode actif** : Vient se connecter des deux côtés
Le client se connecte et demande le mode actif, sur quel port et ip le serveur pourra se connecter
(Deprectated)

Le stormshield remplace l'ip privée envoyée par le serveur par une ip publique et ouvre dynamiquement les filtrages pendant une certaine durée


#### TP : 
wireshark : filtrage vers ip.addr `==`192.168.1.169
Pour créer une connexion fille, ftp s'est mis en mode passif étendu
ip.addr `==`192.168.1.169 abd 192.168.1.174

On retrouve la première connexion, partie handshake tcp puis que des paquets couche 4



# Cours 3 :


TP: Retrouver les erreurs 


Filtrer sur wireshark et regarder les problèmes : 

PB 1 : Refus de mode passif
PB 2 : pendant une minute, le serveur essaie de se connecter en actif mais n'y arrive pas
La connection sur le port **21** fonctionne, mais aucun paquet n'est arrivé 
PB final : dans le firewall, il est parametré pour drop les paquets du port 20

On aurait dû traquer le passage du paquet, de machine en machine pour voir où est-ce qu'il bloque.

Dernière capture : appel tel

On récupère le format brut, filtrages avec audacity et on récupère toute la conversation


## Chiffrement/Cryptage

Rendre un message illisible sauf pour la personne ciblée qui possède la clé de déchiffrement
Cryptage : anglissisme donc les gens qui disent ce mot sont un peu concon
**Ne pas confondre avec le hash**

**Hash**: algo (sha1,md5,aes) donne une empreite numérique d'un fichier ou autre
Si l'empreinte change c'est qu'il a changé

ex : toto = mon algo me donne 4 (4lettres min)
toTo = 5 (4lettres min + 1 lettre maj)

**Il y a deux types de chiffrements : 

- symétrique : Par bloc (Aes,DES) ou par flux-stream cypher(RC4, Vernam)

- asymétrique : sha 


![alt text](<res/Pasted image 20260207105109.png>)

#### Symétrique :
Avantage du sym : 
- Consomme peu de ressources cpu, rapide

Inconvénients : 
- gestion des clés difficilé(une clé / correspondant)
- Échange de la clé 


Chiffrement par bloc : 

Toujours accompagnés d'un mode opératoire, ex: vont déterminer les intéractions entre les blocs
ECB :  Ne jamais l'utiliser, pourri 
![alt text](<res/Pasted image 20260207105552.png>)
-- 

CBC, bien meilleur :
![alt text](<res/Pasted image 20260207105946.png>)

#### Chiffrement asymétrique

Principe de fonctionnement : couple de clés publiques & privées

la clé privée permet de déchiffrer ce qui a été chiffré avec la clé publique et vice-versa.
Clé privée = signer
Clé publique = chiffrer 

étapes : 
![alt text](<res/Pasted image 20260207110605.png>)

Avantages : 
- Gestion des clés plus facile 
- Pas de partage de clé secrète
Inconvénients : 
- Conso de ressource élevées
- Plus lent

#### Chiffrement hybride 

![alt text](<res/Pasted image 20260207110944.png>)
Allie le meilleur des deux mondes. 

On peut tout chiffrer : dossiers, partitions, fichiers, swap, connections
Pourquoi chiffrer ? pour rendre confidentiel 

Partitions : CryptSetup - Veracrypt
empêche les vols de données quand le disque est compromis ou volé par quelqu'un
Veracrypt: montre les volumes cachés et l'utilisation d'un fichier de clé, chiffre les partitions et disques virtuels

CIA : Confidentialité, intégrité, authenticité

#### CryptSetup - LUKS 
Utilitaire crypto dispo dans les distrib lunux. Permet de chiffrer les disques systèmes ainsi que toute autre partition.


#### Cryptomator 
Chiffre vos fichiers sensibles, protège du vol de données même lorsque le sys est monté.
Gratuit open source; fichiers chiffrés de manière indépendante. à la différence des autres, ce n'est pas le volume mais chaque fichier qui est chiffré

**Petit rappel**

![alt text](<res/Pasted image 20260214091947.png>)

### Certificats 

permet de vérifier l'identité d'un site d'une personne ou autre

Standard x509 pour la création derds certifs nulériques
System centralisé base sur une infra à clés publiques ou PKI
![[Pasted image 20260214094836.png]]
Si on certifie la root, on certifie les child aussi.

#### Pour en créer un : 

1) CSR
- Génération d'une clé privée puis d'un CST (certificate signing request)
voir photo:
![alt text](<res/Pasted image 20260214095918.png>)


#### Comment le navigateur sait que le site est valide ?
![alt text](<res/Pasted image 20260214095715.png>)
**Si les deux hashs sont égaux, le certificat est valide.**

## TLS/SSL

Ils ne sont pas vraiment différents.
SSL est devenu TLS à la version 3

à quoi ça sert ? couche 5- session
sert à établir des connexions sécurisées.

Suite cryptographique:
combinaison d'algo d'échages de clés, d'auth, de diffrement et de hash permettant d'établir la sécurité d'une connexion.
![alt text](<res/Pasted image 20260214103612.png>)

### Handshake tls 1.2

étapes :
- client hello , envoyé par le client, il annonce la liste des cohpers qu'il peut utiliser ainsi que diverts infos dont un nb aléatoire
- voir photo
![alt text](<res/Pasted image 20260214103645.png>)

Schéma : Cipher suite AES256-SHA256
![alt text](<res/Pasted image 20260214104448.png>)

Récupérer la pms casse tout. C'est pour ça que cette sécu est cassée.
Si la clé privée est dérobée, toutes les communications capturées précédemmment peuvent-être déchiffrées.

PMS comme solution (perfect forward secrecy) remplace le role du chiffrement asy dans l'échange de la clé (avec diffie hellman, qui permet l'échange de clé où les deux se mettent en accord su un nb premier sans qu"un intermédiaire puisse le dévouvrir même en ayant capturé les échanges)

![alt text](<res/Pasted image 20260214105042.png>)

## définition :  hash chiffré avec la clé privée = signature

**TLS avec Diffie-Hellman**
![alt text](<res/Pasted image 20260214110904.png>)

Faille TLS HeartBleed (faille de heartbeat (garde une connection ouverte temporaire))
![alt text](<res/Pasted image 20260214110723.png>)

Comment vérifier la solidité de sa configuration TLS ?

SSLabs : test notre serveur ssl sur un site, informe des vulnérabilités et donne les solutions


### SSH :

établir une connection sécurisée vers un shell distant. Crée en 1995

Openssh depuis 1999 par les développeurs d'openbsd.

Protocole sécurisé, chiffré en asymétrique et hybride

Plusieurs cas : 

synthaxe: ssh user@ip

**Authentification ssh:**
- par mdp (plus simple mais sensible au brutforce, obligé de communiquer le mdp)
- par clé ssh (ssh -i <chemin clé privée> user@ip (attention, -i prend toutes les clés donc peut faillir si les authentifications sont limitées en essais & nb de clés (donc rajouter -v pour valider)))

signification du message `are you sure you want to connect blablabla key fingerprint`
Montre que les clés ont changées ou qu'elles n'ont jamais été vérifiées.


Commandes de télé/up : 

Uploader :
scp monfichier user@ip:chemin

Downloader : 
scp user@ip:chemin
-C fait en sorte de compresser la donnée, donc augmente le débit et le nb de fichiers transférés


### SSH 

`ssh -R 9090:127.0.0.1:8000 -p 2222 etudiant@blabla.fr

Local -L : Port d'entrée:ipdest:port-dest

Reverse -R port entrréeDistant:ip-dest:port-dest
- permet de faire suivres les conn qui arrivet sur le docket distante vers l'ip et port en sortie de tunnel ssh local

par ex : ssh -R 6767:127.0.0.1:8000 -p 2222

ssh va lancer un port sur le serveur du prof (6767) et rediriger ce port sur notre loopback au port 8000(par défaut port python)

##### Tunnel dynamique (Anonyme bien mieux)
ssh -D <port qu'on veut ouvrir> -p 2222 etudiant@rmdck.fr 
aller dans le navigateur, param proxy et remplacer le proxy socks par 127.0.0.1

##### ENCORE MIEUX - FoxyProxy

passe direct par ssh mais sur internet et sans parametres.
___

## Pare-feu stateful

permet d'accepter un paquet retour implicitement parce qu'un paquet aller explicite a été envoyé 
![alt text](<res/Pasted image 20260221102827.png>)

Mode IDS : intrusion detection system
- Pas actif
![alt text](<res/Pasted image 20260221103215.png>)
Perlet de détecter des comportements malicieux sans apporter d'actions vis ) vis du flyx remonté.
- agit comme une sonde, alerte l'admin, détecte.

**Mode IPS** : intrusion prevention system
- Actif
![alt text](<res/Pasted image 20260221103239.png>)
Permet de détecter des comportements malicieux et les bloquer activement

Il existe des pare-feu non-dédiés: ceux qui font autre chose que le pare-feu (windows defender, iptables, eset, avast)

Dédié : Stormshield, paloAlto, SonicWall, Fortinet
Infra cool : 
![alt text](<res/Pasted image 20260221103953.png>)
Mélanger les marques, les technos

Avantages d'un pare-feu :

- Mettre tout le monde derrière des règles d'un pare-feu
__ 

Iptables travaille jusqu'à la couche 4
Les vrais pare-feu peuvent gerer minimum jusqu'à couche 7 voir 8

Par couche d'analyse
![alt text](<res/Pasted image 20260221104408.png>)


#### Netfliter (iptables)
- firewall par défaut sur linux 
Permet de : 
- gérer le nat (l3 & >)
- gérer le filtrage au niv réseau l3 et L4 grâce a un system de chaines et tables
- gérer le filtrage au niveau de la trame L2
(Voir schéma du bordel)

IPTables : une des interfaces de management de netfilter permettant de créer drs regles de fiktatge dans les différentes chaines des tables netfilter

- Les chaines et les tables : 
Tables: 
- filter
- nat
- mangle
- raw
- security
Par défaut 
- input 
- output
- forward
- etc..
	Commandes de base : 
![alt text](<res/Pasted image 20260221104921.png>)

Usages de filtrage :
![alt text](<res/Pasted image 20260221105301.png>)

![alt text](<res/Pasted image 20260221110057.png>)

Commande réponse du tp : 

iptables -A OUTPUT -p tcp --sport=22 -j ACCEPT

TP2 ; 

