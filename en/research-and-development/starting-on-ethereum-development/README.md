Working on an Ethereum network requires a different mindset, but also requires a whole new set of tools to develop with. To aid new developers, a summary of tools and how to use them is made. 
This page will act as a summary in helping to start off developing. First of, this will describe how two ways to start a local blockchain and why/when you should use one of the ways. Next, this guide will go into detail on how to write smart contract scripts. Finally, this guide will tell you a way how to deploy your script to (your local) blockchain. 
It is necessary to at least install [NodeJS](https://nodejs.org/) beforehand. 
# Setting up a blockchain
Setting up a local blockchain can be done in two ways. The reason to take one option or the other is whether you want to develop or test using Ethereum Mist, or that your prefer a command prompt environment. 
## GinacheCLI (before: testrpc)

The first way is using TestRPC. TestRPC creates a temporary blockchain that can be interacted with using Truffle consoles (see "Deploying Contracts" later). TestRPC is installed with an npm package using the following command: 
`npm install -g ethereumjs-testrpc`

To run TestRPC, use `testrpc` as a command. In order to maintain consistancy in accounts in deployment, it is advised to use a seed. This is done by using the testrpc command with the seed tag and provided the seed. To do so, use `testrpc -s "<seed>"`, replacing `<seed>` with the wanted seed string. 

TestRPC simulates mining, meaning no action is required to handle new transactions and that transactions can be dealt with instantly. 

## Geth
Another way of creating a local blockchain is by using Go Ethereum (Geth). Geth is a consistant blockchain implementation that maintains the data, rather than deleting it after closing. For long term testing of a script, Geth is ideal. 

Geth can be run in two ways: on your own machine, or via Docker containers. [Jastam](https://github.com/jastam) put together a docker container which runs Geth locally. 

### Running via Docker

Usually, this is the preferred way of getting started with your own (development) blockchain. One the site of docker, you can learn how to install and use docker. It is advised to use a volume when using docker, to keep the data in the container consistent. Docker volumes can be created via `docker volume create [VOLUME]`, where `[VOLUME]` is the name of the volume (will be used later). 

Install the image of the node via `docker image pull jastam/geth`. The advised way of running this image is `docker run --name [NAME] --port 8545:8545 --port 8546:8546 --restart unless-stopped --volume [VOLUME] jastam/geth`, where `[NAME]` is the name of the container and `[VOLUME]` is the name of the volume, as created before. 

#### General information

The chain mine every 5 seconds. The node with come with one (default unlocked) account and a great supply of ether. The address of this account is 0x88e94a4b7bfc62a38d300d98ce1c09f30fb75e3e. The chain id of the docker image is 3177. 

You can reach the node via websockets on port 8546 and via http on port 8545. Websockets are advised in Dapps, but certain development tools (mainly Truffle) can only interact via http. 

### Running on local computer

[Geth can be installed by going to their website](https://geth.ethereum.org/downloads/). Geth can be started using the `geth` command, but without options, it will start using the main network. In order to use a local network, you can use the following attributes:

`rpc`: required for json interaction, with for example Mist. On a local network, never leave home without it, but then again, never use it with the main network. Example use: `--rpc`

`dataDir`: this allows you to save blockchain data in another directory, allowing you to make more than one blockchain or allow you to run a local blockchain next to the main network data. Example: `--dataDir "C:\Blockchain"` 

`networkid`: this tag defines the id of the network. Main network is on network id 1, for example. By defining one, you can use multiple networks on one device, or maintain a truly private network. Example use: `--networkid 42`

`dev`: not compatible in combination with `testnet`. This tag creates a private, local blockchain with an integrated developeraccount, without password (Mist will still ask for passwords, but you can leave it blank). Using `dev` will make transactions instant and will not require mining. 

`testnet`: not compatible in combination with `dev`. This tag will start a local Ropsten-like copy, but with no real connection to Ropsten or data from it, meaning that this network will emulate the use of a real chain (rather than instant networks like TestRPC or the `dev` tag as described above) without actually using the chain. 

In total, this would leave you with the following command: `geth --testnet --datadir "C:\blockchain --networkid 42 --rpc console"`. The `console` command allows geth to run with a console that interacts with the blockchain. This one is not necessary, but if you want to interact with the blockchain immediately, use it.  

### Mining

WIP

# Writing smart contracts
Smart contracts are written in a Javascript-based language called Solidity. If you want more advise, starting out and common practice in Solidity, head over to the Solidity FAQ page (WIP) on this wiki. 

Ethereum Mist has an IDE (integrated development environment) ready for use called Remix, but most developers would prefer a different IDE. If so, than [Visual Studio Code](https://code.visualstudio.com/) (VSC) has lots of support for Solidity script development. If you use VSC, you should head to the extentions in the left side of the screen (`Ctr + Shift + X`) and download the Solidity extension by Juan Blanco: an absolute must-have for Solidity development. 

The structure of contracts and necessary files will be described at the _Deploying Contract_ section of the page. 

# Deploying contracts

Of course it is good to understand how to write scripts, but it's useful to know how to deploy those to the blockchain too. Deploying to the blockchain is called migrating. (minor detail, but looking into options for deployment, you can get confused if all you can find is migration)

## Truffle

The first option to migrate scripts is to use Truffle. Truffle can be installed using the command `npm install -g truffle`. 

In order to start using truffle, you should make a dedicated folder to a Solidity project. In that folder, use the command `truffle init` to start building the structure as for Truffle. This will generate the folders `contracts` and `migrations`, for their respective uses, alongside the `truffle.js` and `truffle-config.js` files. With the latter, either remove the `truffle.js` on Windows platforms or remove `truffle-config.js` on Linux and Mac platforms. 

In the `contracts` folder, you can start making some contracts. Contracts are made in `.sol` files, with a name equal to their contract name. A contract named `ForusCoin` should have the filename `ForusCoin.sol`. Solidity files should only contain one contract. If you want to interact or use different contracts, you can put them in separate files and import them before the contract declaration using `import { ForusCoin } from './ForusCoin.sol'`. 

After you have written some contracts, you can start to compile them. If they compile, it means that there are no issues with the syntax of the code and that it will actually run. You can compile by using the `truffle compile` command. This will generate a new folder: `build`. In this folder, the compiled code can be found alongside some necessary json or byte codes. This will come in handy later on.

Before you can migrate the contract, you have to tell truffle what and how it should deploy. To do so, you first have to write a migration script in the `migrations` folder. In this folder, you can see `1_initial_migration.js`. You can basically copy/paste this file, rename it to `2_<your_filename>.js` and change every reference to `Migrations` to your contract name, so for example `ForusCoin`. 

Next, you can use the `truffle migrate --reset` command to deploy your contract. The `--reset` is to to make sure not to check if the contract exists in the blockchain. Note that this command automatically calls `truffle compile`. However, make sure that your contract does *not* have a `.json` build file. Otherwise, it may decide not to update it, resulting in strange behaviour when using the contract. 

After migrating, you can use a generated ABI to tell other applications how to interact with the contract. Without this, there is no way of telling an application what your contract does. When compiling, truffle will generate this ABI in the build `.json` file. When using web3 to interact with the contract, you might need a bytecode to interact with the script too. This is generated in the same file. 

After this, your contract is deployed on your local network and you can interact with it. However, updating the script will not cause an existing script to change, but instead 'uploads' a new contract, meaning that you should stop using the old contract address and instead use a new address for it. 

##### todo
geth flags
permissioned quorum
cryptozombies
etherstats
block explorer