# Setting up Development Environment

## Install Node.js

Install Node.js by your favorite method, or use Node Version Manager by following directions at https://github.com/creationix/nvm

```bash
nvm install v4
```

## Fork and Download Repositories

To develop cryptorescuecore-node:

```bash
cd ~
git clone git@github.com:<yourusername>/cryptorescuecore-node.git
git clone git@github.com:<yourusername>/cryptorescuecore-lib.git
```

To develop cryptorescue or to compile from source:

```bash
git clone git@github.com:<yourusername>/cryptorescue.git
git fetch origin <branchname>:<branchname>
git checkout <branchname>
```
**Note**: See cryptorescue documentation for building cryptorescue on your platform.


## Install Development Dependencies

For Ubuntu:
```bash
sudo apt-get install libzmq3-dev
sudo apt-get install build-essential
```
**Note**: Make sure that libzmq-dev is not installed, it should be removed when installing libzmq3-dev.


For Mac OS X:
```bash
brew install zeromq
```

## Install and Symlink

```bash
cd cryptorescuecore-lib
npm install
cd ../cryptorescuecore-node
npm install
```
**Note**: If you get a message about not being able to download cryptorescue distribution, you'll need to compile cryptorescued from source, and setup your configuration to use that version.


We now will setup symlinks in `cryptorescuecore-node` *(repeat this for any other modules you're planning on developing)*:
```bash
cd node_modules
rm -rf cryptorescuecore-lib
ln -s ~/cryptorescuecore-lib
rm -rf cryptorescued-rpc
ln -s ~/cryptorescued-rpc
```

And if you're compiling or developing cryptorescue:
```bash
cd ../bin
ln -sf ~/cryptorescue/src/cryptorescued
```

## Run Tests

If you do not already have mocha installed:
```bash
npm install mocha -g
```

To run all test suites:
```bash
cd cryptorescuecore-node
npm run regtest
npm run test
```

To run a specific unit test in watch mode:
```bash
mocha -w -R spec test/services/cryptorescued.unit.js
```

To run a specific regtest:
```bash
mocha -R spec regtest/cryptorescued.js
```

## Running a Development Node

To test running the node, you can setup a configuration that will specify development versions of all of the services:

```bash
cd ~
mkdir devnode
cd devnode
mkdir node_modules
touch cryptorescuecore-node.json
touch package.json
```

Edit `cryptorescuecore-node.json` with something similar to:
```json
{
  "network": "livenet",
  "port": 3001,
  "services": [
    "cryptorescued",
    "web",
    "insight-api",
    "insight-ui",
    "<additional_service>"
  ],
  "servicesConfig": {
    "cryptorescued": {
      "spawn": {
        "datadir": "/home/<youruser>/.cryptorescued",
        "exec": "/home/<youruser>/cryptorescue/src/cryptorescued"
      }
    }
  }
}
```

**Note**: To install services [insight-api](https://github.com/cryptorescue-project/insight-api) and [insight-ui](https://github.com/cryptorescue-project/insight-ui) you'll need to clone the repositories locally.

Setup symlinks for all of the services and dependencies:

```bash
cd node_modules
ln -s ~/cryptorescuecore-lib
ln -s ~/cryptorescuecore-node
ln -s ~/insight-api
ln -s ~/insight-ui
```

Make sure that the `<datadir>/cryptorescue.conf` has the necessary settings, for example:
```
server=1
whitelist=127.0.0.1
txindex=1
addressindex=1
timestampindex=1
spentindex=1
zmqpubrawtx=tcp://127.0.0.1:28332
zmqpubhashblock=tcp://127.0.0.1:28332
rpcallowip=127.0.0.1
rpcuser=cryptorescue
rpcpassword=local321
```

From within the `devnode` directory with the configuration file, start the node:
```bash
../cryptorescuecore-node/bin/cryptorescuecore-node start
```
