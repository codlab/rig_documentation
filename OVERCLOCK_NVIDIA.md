# NVidia Overclocking

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
