# Docker Ethereum Testnet

- Deploy a private Ethereum testnet cluster with netstats using Docker
- Netstats is a monitoring web interface
- Interact with your private blockchain using Etherwallet (account interface) and Ethersapp (contract interface)

## Deploy the Ethereum Testnet

```
docker-compose up -d
```

or running in terminal as process with logging for all containers

```
docker-compose up --build
```

This will build the images and start the containers for:

- 1 Ethereum Bootstrapped (acts as a primary node for the other nodes to connect)
- 1 Ethereum "Miner" (which connects to the bootstrapped container on launch)
- 1 Netstats web UI, access the Netstats Web UI: `http://localhost:3000`
- 1 Addons container which gives you access to Ethersapp and MyEtherWallet. These are accessible via localhost:8080/ethersapp and localhost:8080/etherwallet

### Test accounts

There are 20 pre-funded accounts included.
Keystore Files can be found in ./files/keystore
Connect myetherwallet to the private testnet and obtain the private key for use in metamask if desired

To unlock an account for lets say Truffle migrations or other testing
use the docker command (Interact with geth via commandline) below to go into the geth container and run the commands shown below to unlock and get first address

```
web3.personal.unlockAccount(web3.personal.listAccounts[0],"1234",15000);
web3.personal.listAccounts[0];
```

## Interact with geth via commandline

```
docker exec -it bootstrap geth attach ipc://root/.ethereum/devchain/geth.ipc
```

## Docker cluster management

You can manage the containers via the command line or using portainer.

Use the following Docker commands to deploy Portainer. Access the Portainer Dashboard via http://localhost:9001

```
docker volume create portainer_data
docker run -d -p 9001:9001 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer
```
