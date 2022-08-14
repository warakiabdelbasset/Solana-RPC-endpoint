# RPC_SOL
# How to run your own Solana RPC endpoint on Figment’s DataHub

## There’s many reasons why you should run your own RPC endpoints, some of them are:

- Offloading charge from public, rate limited RPC endpoints.
- Maybe you don’t want to mess an NFT drop.
- Too many Metaplex shops use the same RPC endpoint ending up in slower response times.

###### Step by step
Alright so this service is going to be based in nodejs so I suggest you to have both Node.js, Yarn and Docker installed already. There’s plenty of guides on how to do that.

1. Register on DataHub and create your own Solana services account https://datahub.figment.io/services/solana
2. Create a `Dockerfile` to specify deployment steps for our service
``` Ruby
FROM node:alpine

WORKDIR /usr/src/app

COPY package.json .
COPY yarn.lock .

RUN yarn install
COPY . .
CMD [ "yarn", "start" ]
```
3. Run `yarn init` and then add the following dependencies:
``` Ruby
$ yarn add dotenv express http-proxy-middleware
```
