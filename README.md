# Documentation de RIG Nvidia Distributions standards


# Prérequis

- drivers nvidia
- cuda
- binaires du mineur & fonctionnel
- circusd (installé via pip3 de python)
- xorg

# Installation NVIDIA

L'installation de NVIDIA en passant par le fichier cuda*.run :
```
#installation du driver graphic uniquement - règle des soucis de compilation sans source kernel
sudo ./NVIDIA-Linux-x86_64-*.run

# ne choisir que l'installation du toolkit nvidia
sudo ./cuda-sapropreversion.run
```

Ne pas oublier de
## Erreur pour cause de serveur X lancé ?

Il vous suffit de stopper le service lightdm

```
sudo service lightdm stop
```

## Erreur de version GCC

Si un message du type "Error: unsupported compiler: X.X.X. Use --override to override this check.", il vous faut installer la version 4.9.0 par exemple :

```
#installation de gcc et g++
sudo apt-get install gcc-4.9 g++-4.9
```


Puis changer les alternatives pour utiliser par défaut la version 4.9
```
# effacer les alternatives pré/-existantes
sudo update-alternatives --remove-all gcc
sudo update-alternatives --remove-all g++

# mettre par défaut la version 4.9
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-4.9 10
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-4.9 10
```
sudo update-alternatives --remove-all gcc

# Compilation d'un mineur

Ici je prend l'exemple de ccminer qui n'est pas très difficile à compiler manuellement.

Exemple avec le repo https://github.com/krnlx/ccminer-skunk-krnlx

## Prérequis

Comme indiqué, il faut git, autoconf ainsi que les dépendances des bibliothèques openssl et curl. Par exemple via :
```
sudo apt-get install git autoconf libssl-dev libcurl4-openssl-dev
```

Une fois fait, il est possible de faire la compilation

```
git clone https://github.com/krnlx/ccminer-skunk-krnlx.git
cd ccminer-skunk-krnlx
./autoconf
./configure
make
```

Si chacune de ces étapes s'est déroulée sans erreur, le binaire ccminer devrait être disponible dans le dossier actuel.

Pour tester la compilation, vous pouvez utiliser :

```
#exemple sur skunkhash & la pool aikapool, choisissez en fonction de votre pool évidemment
./ccminer -a skunk -o stratum+tcp://stratum.aikapool.com:7939 -u unuser.unrig -p unmotdepasse --api-bind=0.0.0.0:4068
```

Notez la présence du api-bind=0.0.0.0:4068 pour pouvoir utiliser des utilitaires de monitoring réseau !

# Ecrans virtuels pour chaque GPU

## Initial

Pour modifier la configuration actuelle xorg.conf

```bash
sudo nvidia-xconfig -a --allow-empty-initial-configuration --cool-bits=28 --use-display-device="DFP-0" --connected-monitor="DFP-0"
```

## Vérification de la config

En éditant le fichier /etc/X11/xorg.conf, vérifier la présence de :
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "GeForce GTX 1060 3GB"
    BusID          "PCI:1:0:0"
    Option         "Coolbits" "28"
EndSection
```

A noter qu'il faut autant de bloc Section "Device" que de gpu (Identifier "DeviceX"). (idem concernant Screen)
L'option "Coolbits" va permettre de règler les GPU avec les outils nvidia par la suite

# Installation de Circus si absent

Circus est un programme de gestion de processus comme peut l'être systemd par exemple. Il possède l'avantage d'être extrêmement léger et simple de configuration.

L'installation de se faisant par le python packet manager (en fonction des OS, ici Ubuntu):
```
#python < 3
sudo apt-get install python-pip

#python3+
sudo apt-get install python3-pip
```

Puis l'installation de circus :
```
sudo pip install circus
```

Pour l'instant, Circus ne démarre pas automatiquement, dans les étapes suivantes, un fichier de configuration pour systemd sera écrit ainsi que tout le processus de démarrage du mineur choisi.

# Démarrage automatique du mineur

En créant & éditant le fichier /etc/circus/circus.ini :
```
[circus]
check_delay = 5
endpoint = tcp://127.0.0.1:5555
pubsub_endpoint = tcp://127.0.0.1:5556
statsd = true

#création du bloc du watcher de processus
[watcher:miner]
working_dir = /home/someuser/
cmd = /home/someuser/mine.sh
numprocesses = 1
autostart = true
send_hup = true

# création d'un log de sortie standard
stdout_stream.class = FileStream
stdout_stream.filename = /var/log/miner.output.log
stdout_stream.max_bytes = 10737418
stdout_stream.backup_count = 1

#création d'un log de sortie erreur
stderr_stream.class = FileStream
stderr_stream.filename = /var/log/miner.error.log
stderr_stream.max_bytes = 10737418
stderr_stream.backup_count = 1
```

A noter que la ligne
```
cmd = /home/someuser/mine.sh
```

fait référence à un script mine.sh dans le dossier racine de l'utilisateur someuser.
A modifier en fonction.

Si votre script n'est qu'un onelinecode. e.g.
```
ethminer -F https://somesite:8888/address/rig_name -U
```

ou reprendre l'exemple plus haut avec ccminer. Tout est fonction de votre mineur !

Vous pouvez utiliser la notation
```
cmd = ethminer
args = -F https://somesite:8888/address/rig_name -U
```

En fonction de vos binaires, vous aurez peut-être besoin de passer des variables d'environnement à circus pour démarrer les processus dans de bonnes conditions.

Dans ces cas-là, créez un bloc "env" et placez le au-dessus du bloc "watcher". Faites attention à donner le même nom de bloc. Dans l'exemple précédent, notez le "watcher:ethminer". On pourrait avoir alors :

```
[env:ethminer]
LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64/:$LD_LIBRARY_PATH

[watcher:ethminer]
...
...
```

A ce niveau, il ne reste qu'à s'assurer que circusd démarre au boot. En utilisant systemctl, s'assurer de la présence d'un script circusd.service dans le dossier /etc/systemd/system/

Qui doit contenir :

```
[Unit]
Description=Circus process manager
After=syslog.target network.target nss-lookup.target

[Service]
Type=simple
ExecReload=/usr/local/bin/circusctl reload
ExecStart=/usr/local/bin/circusd /etc/circus/circus.ini
Restart=always
RestartSec=5

[Install]
WantedBy=default.target
```


# Script d'overclocking

L'overclocking minimal présenté ici consiste à modifier certaines valeurs via les outils standards fournis par Nvidia.

Une version simple est :

```
#!/bin/bash

MEM=600
GRAPHICS=200

sudo /usr/bin/nvidia-settings -c :0 -a [gpu:0]/GPUMemoryTransferRateOffset[3]=$MEM
sudo /usr/bin/nvidia-settings -c :0 -a [gpu:1]/GPUMemoryTransferRateOffset[3]=$MEM
sudo /usr/bin/nvidia-settings -c :0 -a [gpu:2]/GPUMemoryTransferRateOffset[3]=$MEM
sudo /usr/bin/nvidia-settings -c :0 -a [gpu:3]/GPUMemoryTransferRateOffset[3]=$MEM

sudo /usr/bin/nvidia-settings -c :0 -a [gpu:0]/GPUGraphicsClockOffset[3]=$GRAPHICS
sudo /usr/bin/nvidia-settings -c :0 -a [gpu:1]/GPUGraphicsClockOffset[3]=$GRAPHICS
sudo /usr/bin/nvidia-settings -c :0 -a [gpu:2]/GPUGraphicsClockOffset[3]=$GRAPHICS
sudo /usr/bin/nvidia-settings -c :0 -a [gpu:3]/GPUGraphicsClockOffset[3]=$GRAPHICS
```

## Installation

```
nano ~/oc.sh
#vi/etc...
#coller le script au dessus, modifier en fonction des gpus présents

chmod +x ~/oc.sh
```

Et pour finir, il faut aussi indiquer au système que l'utilisation de "sudo" sur l'outil /usr/bin/nvidia-settings n'a pas besoin de se faire avec mot de passe pour l'utilisateur actuel :
```
sudo visudo
```

Et mettre en fin de fichier  (someuser est dans l'exemple le nom de l'utilisateur, à remplacer donc):
```
someuser ALL=(ALL) NOPASSWD: /usr/bin/nvidia-settings
```

## Tester ?

En lançant deux shells, vérifier à la fois l'état du mineur & l'execution du script.
En reprenant l'exemple de la configuration du dessus (Démarrage automatique) :
```
tail -f /var/log/miner.error.log
```
Attendre de voir le hashrate "actuel" des GPUs.
Puis dans l'autre shell
```
~/oc.sh
```

Une évolution du hashrate vous indiquera la bonne exécution ou l'échec de l'overclock avec le script. A noter aussi que des valeurs trop importantes ou incorrects nécessiteront un redémarrage.


## Démarrage automatique

L'overclock n'est pas statique. Il est nécessaire de l'effectuer à chaque démarrage du rig. Pour se faire & pour des soucis de démarrage via systemctl ou circus, il est recommandé de :
- mettre le démarrage de l'utilisateur courant par défaut sans mot de passe
- créer un script de démarrage

Pour se faire
```
nano .config/autostart/oc.desktop
#vi/etc...
```

Puis mettre
```
[Desktop Entry]
Name=OverClock
GenericName=Program
Comment=start overclocking

Exec=/home/someuser/oc.sh
Terminal=false
#ou true en fonction de l'envie de debug
Type=Application
Icon=icon.png
Categories=Network;
#TODO mettre une catégorie convenable
StartupNotify=false
```


# Evolutions

Toutes les pull requests & issues seront acceptées volontier. Ce dépôt sera aussi surement l'ébauche pour un outil de diagnostic sur réseau local des rigs du réseau.


# License

Ce dépôt est sous license MIT.
