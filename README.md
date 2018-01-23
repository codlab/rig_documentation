# Documentation de RIG


# Installation

## System

As experienced with recent versions, we will focus mainly on using Ubuntu 16.04 / 16.10 since, even without the security updates available now, it is far more easier to install the drivers and tools.

If you decide to install Ubuntu 17+, you can experience boot loop after NVidia/AMD drivers. You will then to change your `tty` to access the console "mode" only (and sufficient).

`ctrl+alt+f1` for instance
`ctrl+alt+f7` make you back to the GUI

## Drivers

For NVidia drivers : [DRIVERS_NVIDIA.md](DRIVERS_NVIDIA.md)

For AMD drivers : Please provide documentation

## Miner

If you did not do it earlier, reboot your rig
We won't focus on Overclocking here, just test the post-driver installation
You'll need any miner you like <3. We will focus here on miners with monitoring APIs

### Claymore's

TODO documentation for Claymore

### Ethminer

TODO documentation for Ethminer

### CCMiner

The doc is available [here](INSTALL_CCMINER.md)

# Virtual screen for your GPUs

For NVidia : [VIRTUAL_SCREEN_NVIDIA.md](VIRTUAL_SCREEN_NVIDIA.md)

# Autostart the miner

## Create the miner script

We will now create the script to start mining for you. Nothing complicated here since you should already have the miner installed.

Create a file in the mining folder and put in it the command :

```
cd ~/mining
```

```
nano mine.sh
```

And write, for instance ethminer - note that you must change the values in this file to represent your own wallet, miner name, etc...

```bash

#!/bin/bash

ETHMINER=/usr/bin/ethminer
ETH_POOL=http://etc-eu1.nanopool.org:18888
ETH_ADDRESS=0xYOUMUSTPUTYOURWALLETADDRESSHERE
ETH_RIG_NAME=anameforyourrig
ETH_MAIL=youremail@some.domain
API_PORT=3333

$ETHMINER -F $ETH_POOL/$ETH_ADDRESS/$ETH_RIG_NAME/$ETH_MAIL -U --api-port $API_PORT
```

save and quit the file. And make it executable :

```
chmod +x mine.sh
```
## Testing it

```
./mine.sh
```

You should see your miner starting, if everything is fine and run smoothly, you can proceed to the automation.

Copy the miner script into a more appropriate place :

```
sudo cp ~/mining/mine.sh /usr/local/bin/
```

## Start the miner in a service

### Creating the service

We will create a simple service for now :

```
sudo nano /etc/systemd/system/miner.service
```

```
[Unit]
Description=Miner service
After=syslog.target network.target nss-lookup.target

[Service]
Type=simple
WorkingDir=/usr/local
ExecStart=/usr/local/bin/mine.sh
Restart=always
RestartSec=5

[Install]
WantedBy=default.target
```

### Starting the service

```
# enabling the service
sudo systemctl enable miner.service

# starting it !
sudo systemctl start miner.service
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
