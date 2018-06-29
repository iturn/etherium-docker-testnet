FROM ethereum/client-go:stable

RUN apk add --update git bash nodejs nodejs-npm perl

RUN cd /root &&\
    git clone https://github.com/cubedro/eth-net-intelligence-api &&\
    cd eth-net-intelligence-api &&\
    npm install &&\
    npm install -g pm2

ADD ./files/password     /root/files/password
ADD ./files/genesis.json /root/files/genesis.json
ADD ./files/keystore     /root/.ethereum/devchain/keystore
ADD ./files/timezone     /etc/localtime

ADD ./monitored-geth-client/start.sh /root/start.sh
ADD ./monitored-geth-client/app.json /root/eth-net-intelligence-api/app.json

RUN chmod +x /root/start.sh

ENTRYPOINT /root/start.sh
