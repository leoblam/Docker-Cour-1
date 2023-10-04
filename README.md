# Commandes docker pour la mise en réseau de trois container

Ci dessous voici le procédé pour rendre opérationnel trois containers, les mettre en réseaux, utilisant un volume, un script d'update et d'installation de softs, via une image custom.


### docker pull ubuntu:latest

- Je commence par **pull** ou télécharger la dernière version d'Ubuntu
	> ":*latest*" est facultatif car mis de base par docker si il est manquant
	
### docker run -it --name ContainerForCommit ubuntu:latest

- Ici je crée un container de l'image précédemment pull que je come *"ContainerForCommit"* car ce container ne me servira qu'a configurer une **image** possédant déjà la config désiré. 


### docker cp f:dev/script.sh ContainerForCommit:home/script.sh

- Ici je copie de mon disque local Windows un fichier script.sh contenant les commandes de setup désiré.
Le contenue de mon script.sh est : 
apt update -y && apt upgrade -y && apt install net-tools -y && apt install iputils-ping -y && apt install tree -y && apt install vim -y
> 
> 
> Concrètement le script lance la mise a jour de mon image puis l'installation de 4 softs qui sont :
>  1. **net-tools** : qui me permet de voir la config réseau de mon container.
>  2. **iputils-ping** : qui me permet de tester le communication entre les containers.
>  3. **tree** : qui me permet de gagner en lisibilité lors de consultation de fichiers.
>  4. **vim** : qui est un éditeur de texte.


### docker commit ContainerForCommit UbuntuSetup:041023
- Ici je crée une nouvelle image locale en utilisant mon container *"ContainerForCommit"* ,  Je le nomme ensuite *"UbuntuSetup"* et y ajoute le **TAG** de la date d'aujourd'hui pour y voir clair si j'ai dans le futur plusieurs versions.
		

### docker images
- Ici je crée vérifie juste que mon image est bien crée et opérationnelle


### docker run -tid -p 8080:80 -v SharedVolume:home/sharedvolume --name ct1 UbuntuSetup:041023

### docker run -tid -p 8080:80 -v SharedVolume:home/sharedvolume --name ct2 UbuntuSetup:041023

### docker run -tid -p 8080:80 -v SharedVolume:home/sharedvolume --name ct3 UbuntuSetup:041023
- Ici je crée un nouveau container lié au port **808x** j'ajoute également un **volume** car je veux que mes trois containers partagent un volume nommé **ctx** via l'image précédemment crée *"ubuntuSetup"*

### docker ps -a
- Ici je vérifie que mon container sont bien créer je peux aussi vérifié que l'image est la bonne, que le port est correct et que les noms sont ceux que je voulaient
-

> le -a fait en sorte que même les containers exited soient affichés


### docker network  create nt1
- Ici je crée un nouveau network que je nomme *"nt1"*

### docker network ls
- Ici je vérifie que mon network soit crée et que son nom soit correct

### docker network nt1 ct1
### docker network nt1 ct2
### docker network nt1 ct3
- Ici je lie mon container nommé ctx a mon network nommé nt1
### docker exec -it ct1 ifconfig
- ici j'exécute la commande **ifconfig** tiré du soft **net-tools** précédemment installé dans l'image
>la commande me donne de nombreuses information mais pour la suite j'ai juste besoin de la ligne **inet** sous **eth0** qui me donne l'ip de mon container normalement 172.17.0.x
### docker exec ct2 ping 172.17.0.x
- Ici j'exécute ma commande **ping** tiré du soft **iputils-ping** précédemment installé dans l'image
>ici la commande fais le ping entre ct2 et ct1 via l'ip de ct1 "j'aurais pu ping directement via le nom de mon container"

## commandes, option et raccourcis utile :

 - docker ps -a :me donne tous les container de ma machine
 - ctrl+C : Coupe un process
 - ctrl+D : Coupe un container
- :wq : ferme et enregistre un fichier dans vim
- :q! : ferme un fichier sans l'enregistrer dans vim

  ## commande utiles :
### docker cp F:\DEV\script.sh ct1:home/script.sh
- Ici on **copie** un fichier depuis le repertoire **f:\DEV** de mon windows au repertoire **home** de mon **container** *ct1* via le patern **<container>:<chemin ou je veux le coller>**
