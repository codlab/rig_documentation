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

# Overclocking

## NVidia

Head to [NVIDIA](OVERCLOCK_NVIDIA.md)

# Pull requests

Pull requests are accepted. The repository will soon embed an app to monitor RIGs in the network.


# License

This repo is under the MIT license.
