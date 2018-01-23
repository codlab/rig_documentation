# Ethminer Installation

# Simple installation

The first step is to locate the required ethminer release. It is up to you, the crypto you want to mine etc...

## Download

### Prerequisite

```
export ETHMINER=ethminerminer.version.crypto

mkdir -p ~/mining/$ETHMINER
cd ~/mining/$ETHMINER
```

### Tar

```
wget <http/s://server/.../.../<name of ethminer file>.tar.gz

tar -xzvf <name of ethminer file>
```

### Zip

```
wget <http/s://server/.../.../<name of ethminer file>.zip

unzip <name of ethminer file>
```

### Final step

You have now a folder of binary of ethminer directly available to you in the current `~/mining/$ETHMINER` folder.

Locate the ethminer's binary and copy it to your usr binary folder :
```
sudo cp ~/mining/$ETHMINER/..../ethminer /usr/bin/
```

If you wanto use multiple mining ccminer versions, you can also, copy it by specifying the new file name when copied, for instance `ethminer.ethash`

You can now head back to the main [README.md](README.md)


## Miner compilation

In this example, we will compile the miner ourself. It should not be difficult and quite straightforward.

Example with the fork : https://github.com/ethereum-mining/ethminer

### Prerequisite

```
export ETHMINER=ethminer.verions.crypto

mkdir -p ~/mining/$ETHMINER
cd ~/mining/$ETHMINER
```

Install the mandatory libraries and packages :

```
sudo apt-get install git cmake libssl-dev libcurl4-openssl-dev
```

### Compiling

```
git clone https://github.com/ethereum-mining/ethminer.git
cd ethminer
mkdir build; cd build
cmake .. -DETHASHCUDA=ON -DETHASHCL=ON

make
```

If every steps were successful, the miner binary should be in the folder.

You can now head back to the main [README.md](README.md)

## Troubleshoot

### When compiling, I get on error with CURL / SSL

If you have something like
```
libcurl was built with SSL disabled, https: not supported!
```

You must use the `--system-curl`

You should then have

```
cmake .. -DETHASHCUDA=ON -DETHASHCL=ON --system-curl
```
