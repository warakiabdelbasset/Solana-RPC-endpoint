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
4. Create a `.env` file so you can test locally
``` Ruby
FIGMENT_TOKEN=YOUR_RPC_TOKEN
FIGMENT_URL=https://solana--mainnet.datahub.figment.io/
#FIGMENT_URL=https://solana--devnet.datahub.figment.io/
PORT=8080
``` 

5. Alright, create an `index.js` file with the following code:
I’ll explain those lines right now: First of all we call the config method of ‘dotenv’ so we inject all of our environment variables in the process.env object. Then we creaste a proxy middleware that’s going to point to the figment url, and pass it’s token as an Authorization header on every request, finally we bootstrap our app with express and start running it on the given port.
``` Ruby
require('dotenv').config();
const express = require('express');
const { createProxyMiddleware } = require('http-proxy-middleware');
const proxyMiddleware = createProxyMiddleware({
  target: process.env.FIGMENT_URL,
  changeOrigin: true,
  headers: {
    Authorization: process.env.FIGMENT_TOKEN,
  },
});
const app = express();
app.use('/', proxyMiddleware);
app.listen(process.env.PORT || 8080);
```
6. Modify `package.json` and add the following nodes:
``` Ruby
{
  "scripts": {
    "start": "node index.js"
  },
  "engines": {
    "node": ">= 14.0.0"
  },
}
```
Alright, you can try your rpc endpoint now. Run `yarn start` and you should see the following message on your terminal:
``` Ruby
Proxy created: /  -> https://solana--mainnet.datahub.figment.io/
```
Cool, go to `http:localhost:8080/health` and you should see an “OK” response back from DataHub’s servers.


Alright, so now you have an agnostic docker container that can be uploaded into any cloud provider. But how do you upload to GCP? Well, this isn’t the easiest thing in the world, you need to install `gcloud` in your terminal, then you need to `build` your container image, then you need to `run` it to test that it’s fine, after that, you’ll need to tag it and upload it into your account’s Google Cloud Registry, then, go to Google Cloud Registry and simply deploy it to Google Cloud Run.
The first time this process will fail at the end because of missing environment variables, and that’s fine, just go to your Google Cloud Run service, find your container and “Edit and Deploy” a new Revision.









