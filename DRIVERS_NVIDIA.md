# NVIDIA Drivers Installation

Open a shell into your Ubuntu/Debian/etc... distribution

# Prerequisite

We will create a workspace from where every sources will be put into place :

```
#no cd ~ since assuming you followed instructions :p

mkdir mining
cd mining
```

# Build tools

```
sudo apt-get install build-essential
```

# Download

```
#this one is deprecated but some times, it helped the later
#this one won't install the CUDA toolkit, only the Graphic Driver
wget http://fr.download.nvidia.com/XFree86/Linux-x86_64/384.111/NVIDIA-Linux-x86_64-384.111.run

wget https://developer.nvidia.com/compute/cuda/9.1/Prod/local_installers/cuda_9.1.85_387.26_linux
```

Finally check the file integrity :

```
#if you downloaded the deprecated
md5sum NVIDIA-Linux-x86_64-384.111.run
md5sum cuda_9.1.85_387.26_linux
```

It should be
`0428addcf69a21a955e4caf4c90d0c68  NVIDIA-Linux-x86_64-384.111.run`
`67a5c3933109507df6b68f80650b4b4a  cuda_9.1.85_387.26_linux`

# Install

On Ubuntu, you will have to stop the current X,

```
sudo service lightdm stop
```

Then start the installation :

```
# follow the instruction for installing the driver
chmod +x ./NVIDIA-Linux-x86_64-384.111.run
sudo ./NVIDIA-Linux-x86_64-384.111.run

#follow the below instructions when dealing with cuda (with or not the driver)
chmod +x cuda_9.1.85_387.26_linux
sudo ./cuda_9.1.85_387.26_linux
```

## License Agreement

- You can read the whole NVIDIA Software License Agreement (recommended)
- spam `space` key
- press `q`

Accept it by typing `accept` then `enter`

## Drivers

Accept the installation of the drivers (`y`). You can install the OpenGL libraries (I did not test if cuda/opencl for Nvidia cards do need some features from them, accept them by default, it won't hurt)

## Multiple GPUs

This is no by default, but if you will have a AMD/NVidia, you know what to put.

## CUDA Toolkit

Accept it and set the default location to `/usr/local/cuda-9.1` (since it is the default one, simply press `enter`). You need to create the symbolic link on some distributions, use `y`.

And you don't need the examples (you can use them if you want to look into the cuda kernels <3)

## Installation

The installation will now start, it should be OK. In case of any issues, please post your log in a new issues in order to improve this doc !

## Post Installation

```
echo 'export PATH=/usr/local/cuda-9.1/bin:$PATH' >> ~/.bashrc
echo 'export LD_LIBRARY_PATH=/usr/local/cuda-9.1/lib64:$LD_LIBRARY_PATH' >> ~/.bashrc

source ~/.bashrc

cd ~/mining
```

You can now head back to the main [README.md](README.md)

## Troubleshoot

- in case of driver installation failed, you may want check if downgrading the kernel fix the issues (related to the 4.10+ bugs with headers)

- check the logs for possible UEFI incompatibilities !

## Deprecated info

Here are some infos (in french) which will be removed in a future version :

### Erreur de version GCC

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
