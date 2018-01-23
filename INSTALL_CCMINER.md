# CCMiner Installation

# Simple installation

The first step is to locate the required ccminer release. It is up to you, the crypto you want to mine etc...

## Download

### Prerequisite

```
export CCMINER=ccminer.uneversion.unecrypto

mkdir -p ~/mining/$CCMINER
cd ~/mining/$CCMINER
```

### Tar

```
wget <http/s://server/.../.../<name of ccminer file>.tar.gz

tar -xzvf <name of ccminer file>
```

### Zip

```
wget <http/s://server/.../.../<name of ccminer file>.zip

unzip <name of ccminer file>
```

### Final step

You have now a folder of binary of ccminer directly available to you in the current `~/mining/$CCMINER` folder.

Locate the ccminer's binary and copy it to your usr binary folder :
```
sudo cp ~/mining/$CCMINER/..../ccminer /usr/bin/
```

If you wanto use multiple mining ccminer versions, you can also, copy it by specifying the new file name when copied, for instance `ccminer.skunk`


## Miner compilation

In this example, we will compile the miner ourself. It should not be difficult and quite straightforward.

Example with the fork : https://github.com/krnlx/ccminer-skunk-krnlx

### Prerequisite

```
export CCMINER=ccminer.uneversion.unecrypto

mkdir -p ~/mining/$CCMINER
cd ~/mining/$CCMINER
```

Install the mandatory libraries and packages :

```
sudo apt-get install git autoconf libssl-dev libcurl4-openssl-dev
```

### Compiling

```
git clone https://github.com/krnlx/ccminer-skunk-krnlx.git
cd ccminer-skunk-krnlx
./autoconf
./configure
make
```

If every steps were successful, the miner binary should be in the folder. To test the compilation you can use :

```
#test on skunkhash & aikapool, choose according to your pool
./ccminer -a skunk -o stratum+tcp://stratum.aikapool.com:7939 -u auser.arig -p apassword --api-bind=0.0.0.0:4068
```
We use the api-bind to specifiy a monitoring output available to devices on the network.
