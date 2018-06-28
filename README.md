# Docker Ethereum Testnet

- Deploy a private Ethereum testnet cluster with netstats using Docker

**Additional Features**

- Manage running containers using the Portainer GUI.
- Interact with your private blockchain using Etherwallet (account interface) and Ethersapp (contract interface)

## Deploy the Ethereum Testnet

```
docker-compose up -d
```

or running in terminal as process with loggin for all containers

```
docker-compose up --build
```

This will build the images and start the containers for:

- 1 Ethereum Bootstrapped container (acts as a primary node for the other nodes to connect)
- 1 Ethereum "Miner" container (which connects to the bootstrapped container on launch)
- 1 Netstats container (with a Web UI to view activity in the cluster) To access the Netstats Web UI: `http://localhost:3000`
- 1 Addons container which gives you access to Ethersapp and MyEtherWallet. These are accessible via localhost:8080/ethersapp and localhost:8080/etherwallet

Note: you can remove the addons container by removing the last entry in the docker-compose.yml file.

### Test accounts

There are 20 pre-funded accounts included.
Keystore Files can be found in ./files/keystore
Connect myetherwallet to the private testnet and obtain the private key for use in metamask if desired

To unlock an account for lets say Truffle migrations
use the docker command below to go into the geth container and run unlock and get first address that now is unlocked

```
web3.personal.unlockAccount(web3.personal.listAccounts[0],"1234",15000);
web3.personal.listAccounts[0];
```

---

## Interact with geth via commandline

```
docker exec -it bootstrap geth attach ipc://root/.ethereum/devchain/geth.ipc
```

## Docker cluster management

You can manage the containers via the command line or using portainer.

Use the following Docker commands to deploy Portainer:

```
docker volume create portainer_data

docker run -d -p 9001:9001 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer
```

Access the Portainer Dashboard via http://localhost:9001

## Cleanup Docker (containers and images)

```
docker ps -aq | xargs docker rm -f
docker images -aq | xargs docker rmi -f
```
