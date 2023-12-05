---
title: "Create A Blockchain Indexer with Chain Indexer Framework"
seoTitle: "How To Create A Blockchain Indexer Using Chain Indexer Framework"
seoDescription: "Learn how to create your own blockchain indexer that stores block data inside a Kafka instance running in a Docker container using Chain Indexer Framework"
datePublished: Tue Dec 05 2023 12:55:40 GMT+0000 (Coordinated Universal Time)
cuid: clpscflkx000a06jlbxrdfjwi
slug: create-a-blockchain-indexer-with-chain-indexer-framework
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1701780908555/822882ee-7a7c-40d4-8f31-105c7c292e3d.png
tags: docker, blockchain, kafka, web3, indexing

---

This guide will show you how to build your own indexer for an EVM-compatible blockchain such as Ethereum, Polygon Proof of Stake, and more!

By the end, you'll have a [Docker](https://www.docker.com/) container running [Kafka](https://kafka.apache.org/) that captures all blockchain events that you specify, along with references for how to transform and consume this data inside applications!

Let's do this!

## How It Works

The architecture of the Chain Indexer Framework can be broken up into three parts:

1. **Block Producers**: Scan the blockchain and publish raw block data into Kafka.
    
2. **Transformers**: Shape the raw data from Kafka into meaningful events.
    
3. **Consumers**: Read the transformed data for various use cases such as from a frontend application or an API endpoint.
    

We're going to first set up our Kafka instance inside a Docker container, then run one script written in TypeScript for each of the three components.

### Architecture Diagram

The producer reads data from the specified blockchain and publishes it into our Kafka instance running in a Docker container. At the same time, it also keeps the most recent blocks inside Mongo DB in case of [reorgs](https://www.alchemy.com/overviews/what-is-a-reorg).

Once it's stored in Kafka, we're able to transform it into more meaningful data, and also make it available for any consumer(s) to read; such as applications.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1701780140067/92ebbb2f-f336-4363-aece-24de1e8fbc28.png align="center")

## Setting Up the Backend Infrastructure

Before we create each of the three services (producer, transformer, consumer), we need to set up the infrastructure required to connect to the blockchain and store data. In this section, we'll set up the following services:

1. An RPC to connect to the blockchain
    
2. A Docker container running Kafka to store raw data
    
3. A Mongo database for storing the most recent blocks.
    

Let's first create a new directory to contain all of the work we're about to do:

```bash
# Create our base directory
mkdir chain-indexer-framework-guide

# Change into this new directory
cd chain-indexer-framework-guide

# Open in VS Code
code .
```

### Creating the Docker Compose File

Now we're here, let's set up our Docker container by creating a [Docker Compose file](https://docs.docker.com/compose/compose-file/03-compose-file/).

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">You will need <a target="_blank" rel="noopener noreferrer nofollow" href="https://docs.docker.com/get-docker/" style="pointer-events: none">Docker</a> installed for this part of the guide. Follow the instructions on the <a target="_blank" rel="noopener noreferrer nofollow" href="https://docs.docker.com/get-docker/" style="pointer-events: none">Get Docker</a> page and verify your installation by running "docker --version" in the terminal.</div>
</div>

The Docker Compose file allows us to define our app's environment. To create one, let's create a `docker-compose.yml` file within this current directory.

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">If you're using <a target="_blank" rel="noopener noreferrer nofollow" href="https://code.visualstudio.com/" style="pointer-events: none">VS Code</a>, it's recommended to install the <a target="_blank" rel="noopener noreferrer nofollow" href="https://code.visualstudio.com/docs/containers/overview" style="pointer-events: none">Docker Extension</a>.</div>
</div>

Within `docker-compose.yml`, set up the following compose file:

```yaml
---
version: "3"
services:
  zookeeper:
    image: confluentinc/cp-zookeeper:7.0.0
    hostname: zookeeper
    container_name: zookeeper
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000

  broker:
    image: confluentinc/cp-kafka:7.0.0
    container_name: broker
    ports:
      - "9092:9092"
    depends_on:
      - zookeeper
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: "zookeeper:2181"
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: PLAINTEXT:PLAINTEXT,PLAINTEXT_INTERNAL:PLAINTEXT
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://localhost:9092,PLAINTEXT_INTERNAL://broker:29092
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
      KAFKA_TRANSACTION_STATE_LOG_MIN_ISR: 1
      KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR: 1
```

We won't cover all of the fields in this guide ([learn more about compose files](https://docs.docker.com/compose/compose-file/)), but this file will allow us to run a [Docker container](https://www.docker.com/resources/what-container/) with [Apache ZooKeeper](https://zookeeper.apache.org/) for maintaining configuration information and [Apache Kafka](https://kafka.apache.org/) to store our raw data.

### Running the Docker Container

Now we've defined our compose file, let's run the Docker container.

Use [Docker Compose](https://docs.docker.com/compose/)'s "[up](https://docs.docker.com/engine/reference/commandline/compose_up/)" command with the `-d` flag to run the container in detached mode.

```bash
docker-compose up -d
```

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text"><strong>First time using Docker</strong>? Ensure you've <a target="_blank" rel="noopener noreferrer nofollow" href="https://stackoverflow.com/questions/40459280/docker-cannot-start-on-windows#:~:text=First%2C%20verify%20that%20Docker%20Desktop%20application%20is%20running.%20If%20not%2C%20launch%20it%3A%20that%20will%20run%20the%20docker%20daemon%20(just%20wait%20few%20minutes)." style="pointer-events: none">finalized the Docker installation</a>.</div>
</div>

Run the `docker ps` command to view the status of all containers:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1701650719097/dd118c51-ce83-4fff-a01e-90b1ceedd16c.png align="center")

You can also run [Docker Desktop](https://www.docker.com/products/docker-desktop/) to see the container running:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1701650301712/832c41bf-9ecc-4a2a-b8d4-aee64313e84b.png align="center")

Now our Kafka instance is up and running, we're ready to store raw data from the blockchain. However, to read blockchain data, we'll need a way of connecting to the chain itself; so let's create a new RPC node next.

### Creating the RPC Node

There are [many solution providers that provide RPC connections](https://ecosystem.polygon.technology/spn/explore/?search=&competency=RPC+Provider&chain=) to blockchains. In this guide, we'll use a popular RPC provider, [Alchemy](https://docs.alchemy.com/) as our RPC provider as they provide a generous free tier.

To get started, head to the [Apps](https://dashboard.alchemy.com/apps) page of your Alchemy Dashboard and click **Create New App**:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1701651722493/297a1525-d9ff-4bd9-bcd0-f0bb8407465b.png align="center")

Select the chain and network you want to read data from and click **Create app**:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1701651818263/c2f79b52-bfc8-4d62-a5b9-3bfd8ca59b31.png align="center")

We'll come back to this later to grab our RPC URL when we write the producer.

### Creating A Mongo DB

Next, let's set up a database to do TODO.

In this guide, we'll use [Mongo DB](https://www.mongodb.com/), as the Chain Indexer Framework's **Producer** has a `mongoUrl` property we can easily plug this database into.

[**Sign up to Mongo DB**](https://www.mongodb.com/) if you haven't already, and deploy a new database.

Below are the options that I chose; but you can customize this to your preferences:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1701653490008/57dbbf12-141f-4f03-a0c8-d929dfa4e180.png align="center")

Once your database is provisioned, complete the setup process.

First, add an authentication method; in this guide, we're using a simple username and password that is auto-generated for us.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1701654085663/f5a76fd8-a1e2-45e9-9f9d-ac1503901a59.png align="center")

Second, configure who can access your database. In this guide, we're simply adding our current machine's IP address. Finally, click **Finish and Close**.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1701654356586/bb65e7f8-8a6c-4d77-af07-880b975d2b38.png align="center")

Finally, the infrastructure setup is complete! 🎉

## Setting up Node.js + TypeScript

Now we're ready to set up the three components of the Chain Indexer Framework.

Set up a simple TypeScript + Node.js project by running the following command:

```bash
npm init -y
```

To add TypeScript, create a `tsconfig.json` file with the following contents:

```json
{
  "compilerOptions": {
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "esModuleInterop": true,
    "target": "ES2020",
    "sourceMap": true,
    "strict": true,
    "outDir": "dist",
    "declaration": true
  },
  "include": ["src/**/*"]
}
```

Set the `package.json` file to the following:

```bash
{
  "name": "producer",
  "version": "1.0.0",
  "type": "module",
  "main": "index.js",
  "scripts": {
    "build": "tsc",
    "start-producer": "npm run build && node --experimental-import-meta-resolve --trace-warnings dist/producer.js",
    "start-transformer": "npm run build && node --experimental-import-meta-resolve --trace-warnings dist/transformer.js",
    "start-consumer": "npm run build && node --experimental-import-meta-resolve --trace-warnings dist/consumer.js"
  },
}
```

Install the required TypeScript dependencies:

```bash
npm install -D typescript @types/node
```

Before installing further dependencies, you'll need to follow the installation instructions of [`node-gyp`](https://github.com/nodejs/node-gyp) as a pre-requisite to installing the [`@maticnetwork/chain-indexer-framework`](https://github.com/0xPolygon/chain-indexer-framework) package. If you're on Windows, this includes installing a current version of Python and the Visual C++ Build Environment in Visual Studio.

Next, install the [`@maticnetwork/chain-indexer-framework`](https://github.com/0xPolygon/chain-indexer-framework) package by running the following:

```bash
npm install @maticnetwork/chain-indexer-framework
```

Finally, create a `src` folder, containing three files:

* `producer.ts`
    
* `transformer.ts`
    
* `consumer.ts`
    

Now we're ready to go. Let's start with the producer.

## Creating the Producer

Inside the `producer.ts` file, we're going to connect to the blockchain you selected in the RPC step and write raw data to Kafka.

Below is the barebones code to start storing raw data in Kafka. Mongo DB is used to store the most recent number of blocks (specified in the `maxReOrgDepth` field) before they become permanent inside Kafka.

```typescript
import { BlockPollerProducer } from "@maticnetwork/chain-indexer-framework/block_producers/block_polling_producer";
import { produce } from "@maticnetwork/chain-indexer-framework/kafka/producer/produce";

const producer = produce<BlockPollerProducer>({
  startBlock: 50689834, // Pick any start block you want
  rpcWsEndpoints: ["<your-alchemy-rpc-url-here>",],
  blockPollingTimeout: 20000,
  topic: "polygon.1442.blocks",
  maxReOrgDepth: 256, // Maximum reorg depth on Polygon is 256
  maxRetries: 5,
  mongoUrl: "mongodb+srv://<your-mongo-username>:<your-mongo-password>@chain-indexer.0ymaemb.mongodb.net/",
  "bootstrap.servers": "localhost:9092",
  "security.protocol": "plaintext",
  type: "blocks:polling",
});

producer.on("blockProducer.fatalError", (error: any) => {
  process.exit(1); //Exiting process on fatal error. Process manager needs to restart the process.
});
```

Importantly, you need to:

* Add your HTTPS Alchemy RPC URL in the `rpcsWsEndpoints` field.
    
* Add your Mongo URL connection string as the `mongoUrl`.
    
* Optionally, configure the other properties to your preferences.
    

When you're ready, you can run the following command to start the producer:

```typescript
npm run start-producer
```

If everything's working as expected, you should begin to see some data appear in Mongo DB, which you can find in the **Database &gt; Collections** section:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1701767515062/410a1c1b-7f53-469f-a569-6db0b7f40bd6.png align="center")

### Adding Logging

The Chain Indexer Framework also comes with logging capabilities to see what's happening behind the scenes. To add it to your producer, first import the Logger:

```typescript
import { Logger } from "@maticnetwork/chain-indexer-framework/logger";
```

And optionally add logs to any events emitted by the producer, for example:

```typescript
producer.on("blockProducer.fatalError", (error: any) => {
  Logger.error(`Block producer exited. ${error.message}`);
  process.exit(1); //Exiting process on fatal error. Process manager needs to restart the process.
});
```

## Transforming & Consuming Data

Now we have raw data available inside our Kafka instance! You're ready to begin transforming and consuming data in your applications.

Although there's a decent chunk of code to get started with these steps, we've already layed the foundation for everything, so it should be relatively simple!

Rather than dumping all the code for you to copy & paste, we've made a few open-source [examples](https://github.com/0xPolygon/chain-indexer-framework/tree/main/examples) of how to setup different kinds of transformers and producers on GitHub linked below.

* [MATIC Transfer Example](https://github.com/0xPolygon/chain-indexer-framework/tree/main/examples/matic_transfer)
    
* [NFT Balance Example](https://github.com/0xPolygon/chain-indexer-framework/tree/main/examples/nft_balancer)