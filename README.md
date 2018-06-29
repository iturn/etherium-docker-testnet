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

## Docker image deployment and registration

To deploy this docker compose in lets say Aws ECS the images must not be build in de compose file but prebuild, pushed to a registry and then referenced in the docker compose file. This can be done automatically via the included python file. Keep in mind that the base folder name in this case etheriumdockertestnet cannot contain \_ or - or it will trip! What is dont via the python script can also be done manually as shown in the manual step below.
build-and-register-docker-images.py. This will create a new compose file like docker-compose.yml-1530263456 that can be pushed to AWS ECS or some other similar service. The newly build images have been referenced in the file automatically

```
python build-and-register-docker-images.py
```

### Manual image deploy and registration

After doing this dont forget to replace the docker compose files build steps with references to the images like
create repos on docker cloud like stijno24/etherium-docker-testnet_bootstrap:latest

check local images

```
docker images
```

tag local images to docker repo

```
docker tag etheriumdockertestnet_bootstrap:latest stijno24/etherium-docker-testnet_bootstrap:latest
docker tag etheriumdockertestnet_ethminer:latest stijno24/etherium-docker-testnet_ethminer:latest
docker tag etheriumdockertestnet_netstats:latest stijno24/etherium-docker-testnet_netstat:latest
docker tag etheriumdockertestnet_addons:latest stijno24/etherium-docker-testnet_addons:latest
```

push images to docker cloud

```
docker push stijno24/etherium-docker-testnet_ethminer:latest
docker push stijno24/etherium-docker-testnet_boostrap:latest
docker push stijno24/etherium-docker-testnet_netstat:latest
docker push stijno24/etherium-docker-testnet_addons:latest
```

edit docker compose file to reference the images pushed to docker cloud

```
services:
bootstrap:
image: stijno24/etherium-docker-testnet_bootstrap:latest
```

### Deploy on a aws EC2

login to ec2 istance
`ssh -i ~/ssh -i /Users/user/keys/etherium-private-blockchain-key.pem ec2-user@ec2-54-154-158-225.eu-west-1.compute.amazonaws.com`

put files in some folder on ecs via come SFTP client giving it the ec2 domain and PEM key file

```
sudo yum update -y
sudo yum install -y docker
sudo usermod -aG docker ec2-user
```

close and open senssion the reload suer perms

```
sudo curl -L https://github.com/docker/compose/releases/download/1.21.2/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

then go to the transferred files where the docker compose file is located

```
docker-compose up --build OR docker-compose up -d
```

### Deploy to aws ECS

login to aws ecs cli
`ecs-cli configure profile --profile-name eth-blockchain-testnet --access-key AAAAAAAAAAAA --secret-key AAAAAAAAAAAA`

set created cluster as default in cli config
`ecs-cli configure --cluster etherium-private-blockchain-1 --region eu-west-1 --default-launch-type EC2`

After setting up aws ecs-cli client run the command below inserting the vi python script generated or compatible compose file. Be sure the memory of the containers isn't more the available in aws ecs

`ecs-cli compose --file docker-compose.yml-1530263456 service up`

check cluster containers status

`cs-cli ps`

### SSH into EC2 Docker host

make sure ssh folder has correct perms

```
sudo chmod 700 ~/.ssh.ssh
sudo chmod 600 ~/.ssh/id_rsa
sudo chmod 600 ~/.ssh/id_rsa.pub
chmod 400 /Users/user/keys/etherium-private-blockchain-key.pem
```

login to the instace
`ssh -i ~/ssh -i /Users/user/keys/etherium-private-blockchain-key.pem ec2-user@1234.compute.amazonaws.com`

show active docker instances
`docker ps`

see logs of a container
docker logs --f

ssh -i ~/ssh -i /Users/user/keys/etherium-private-blockchain-key.pem ec2-user@ec2-54-154-158-225.eu-west-1.compute.amazonaws.com
