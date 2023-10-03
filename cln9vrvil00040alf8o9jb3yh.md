---
title: "Build Your Own Layer 2 Blockchain (using Polygon CDK)"
seoTitle: "How To Build A Layer 2 Blockchain (with ZK Proofs)"
seoDescription: "Learn How To Create Your Own Layer 2 Blockchain That Utilizes Zero Knowledge Proofs Via the Polygon CDK And Build Your Own dApp On The Custom Chain."
datePublished: Tue Oct 03 2023 05:30:03 GMT+0000 (Coordinated Universal Time)
cuid: cln9vrvil00040alf8o9jb3yh
slug: build-your-own-layer-2-blockchain-using-polygon-cdk
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1696308863457/1f67ab07-681e-475c-a5bf-f1cb8f4bb08e.png
tags: blockchain, web3, polygon, zero-knowledge-proofs, zkevm

---

In this guide, I'll show you how you can create and operate your very own blockchain. More specifically, we'll walk through the process of creating a "ZK [Validium](https://ethereum.org/en/developers/docs/scaling/validium/)". A layer 2 chain that utilizes zero-knowledge proofs to guarantee and verify the validity of batches of transactions.

If you're unfamiliar with ZK proofs or Validiums, I have also written blogs previously that will help you understand these topics in more depth, linked below:

* [How do ZK Proofs and ZK-EVMs work](https://blog.jarrodwatts.com/how-zk-proofs-and-zkevms-work)?
    
* [What are Validiums](https://blog.jarrodwatts.com/polygon-pos-is-becoming-a-zkevm-validium-explained#heading-what-are-validiums)?
    

By the end of this guide, you'll be operating your very own chain, including a faucet, block explorer, RPC, bridge, and more; powered by [Polygon CDK](https://polygon.technology/polygon-cdk).

As a bonus, I'll also showcase the process of deploying a smart contract to the chain, and finally, creating an application to interact with your smart contracts.

Let's do this!

## What is Polygon CDK?

Polygon Chain Development Kit (CDK) enables you to build your own ZK-powered Layer 2 blockchain that is custom-fitted to your needs. It provides a way for you to easily create and operate your own "app chain" *(application-specific chain)*.

The fastest way to do this is by using an [implementation provider](https://polygon.technology/cdk/implementation-providers); a platform that provides interfaces built on top of the CDK to help you configure and deploy a chain. In this guide, we'll use an IP called [Presto](https://presto.gateway.fm) to easily ship our own chain.

Polygon CDK is already powering a number of companies by providing the infrastructure to launch ZK-powered L2s; including [Canto](https://polygon.technology/blog/canto-to-migrate-to-a-zk-l2-powered-by-polygon-chain-development-kit), [Immutable](https://polygon.technology/blog/immutable-zkevm-testnet-is-live-on-polygon), [Palm](https://medium.com/@palmfdn/palm-foundation-chooses-polygon-technology-for-zkl2-upgrade-empowering-creators-sports-640a1bbd3e1f), [Astar](https://www.theblock.co/post/250505/astar-network-zkevm-ethereum-layer-2-polygon), and [more](https://twitter.com/0xMarcB/status/1707849304658899415).

As part of the [Polygon 2.0](https://polygon.technology/blog/polygon-2-0-protocol-vision-and-architecture) vision, the chains launched via the CDK will be connected via the interop layer, tapping into the existing ecosystems and liquidity available of all the other CDK-powered chains, including Polygon PoS and Polygon zkEVM.

![Polygon CDK architecture](https://assets-global.website-files.com/637e2b6d602973ea0941d482/64ed4d6458993a014207eef2_image3.png align="left")

[Learn More ↗](https://polygon.technology/blog/introducing-polygon-chain-development-kit-launch-zk-l2s-on-demand-to-unlock-unified-liquidity)

## Deploying The Rollup

In this guide, we'll use [Presto](https://presto.gateway.fm/), a [RaaS](https://www.alchemy.com/overviews/rollups-as-a-service-raas) *(rollup-as-a-service)* platform utilizing Polygon CDK under the hood to deploy our chain with infrastructure deployed to AWS.

To get started, head to [Presto](https://presto.gateway.fm/login) and sign up.

![Presto Sign up page](https://cdn.hashnode.com/res/hashnode/image/upload/v1695907802643/b6504f13-8fd2-424f-a9b4-8acccca37092.png align="center")

Once onboarded, Presto will take you to the dashboard page, where you can click "**Add new"** to kickstart the process of configuring and deploying your chain.

At the time of writing, the only available option is to deploy the ZK-Validium. However, CDK will soon support zkEVM rollups too! For now, let's select **"Private zk-Validium"**:

![Presto plan select page](https://cdn.hashnode.com/res/hashnode/image/upload/v1695907721999/ff2ba592-70e5-494f-b3ce-2c6111a2f79e.png align="center")

Next, we need to decide how/where we want to deploy the infrastructure (such as the nodes, prover, aggregator, sequencer, etc.).

In this guide, we'll use the pre-configured option, "Stockholm", which handles all of the deployment steps for us and runs on AWS.

![Presto location select page](https://cdn.hashnode.com/res/hashnode/image/upload/v1695907743305/f951d7a8-20de-4345-b7fb-1ee309a6a662.png align="center")

The deployment process can take up to *a few hours* to fully complete; in the background, all of the infrastructure and compute resources are being deployed to AWS.

### RPC, Bridge, Block Explorer, Faucet, and More

All of the developer tooling required to utilize your rollup is also deployed for you! The RPC, chain ID, bridge, block explorer, and even a faucet are all deployed for you out of the box; making it super simple to get started developing on it.

![Presto RPC section](https://cdn.hashnode.com/res/hashnode/image/upload/v1696286939433/589e7657-5cae-4efc-aff3-c3eaca6fcd99.png align="center")

You can also view the smart contracts that get deployed for data availability (DA), and the rollup smart contract where batches of transactions are sequenced and verified on Ethereum (layer 1).

![Presto diagnostics (smart contracts) section](https://cdn.hashnode.com/res/hashnode/image/upload/v1696286927247/9aa456d7-55dd-49a1-a791-37c7fe3c49d5.png align="center")

That's it for the deployment step!

Now, let's deploy our first smart contract to our newly created validium.

### Deploy a Smart Contract to Your Custom Chain

A big benefit of the CDK is that it allows you to create chains that are [EVM](https://ethereum.org/en/developers/docs/evm/)\-compatible; meaning you can use all of the methods you're already familiar with to develop your smart contracts, such as Hardhat, Solidity, MetaMask, etc.

The Presto dashboard provides you with the chain ID and RPC URL that you can connect your wallet with and deploy smart contracts to. If you've ever deployed to Polygon before, you're likely already equipped with the skills to deploy to your rollup.

In this guide, I'll show you my favourite way to deploy Solidity smart contracts, using [thirdweb](https://thirdweb.com/) from a [Hardhat](https://hardhat.org/) project.

### Create a New Solidity Project

First, let's create a new Solidity project by running the following command from the terminal:

```bash
npx thirdweb@latest create contract
```

This command allows you to configure and instantiate a new project with your preferences of tooling, such as Forge or Hardhat and sets us up with a simple smart contract to deploy. Below are the options I selected for the project:

![npx thirdweb create contract](https://cdn.hashnode.com/res/hashnode/image/upload/v1696288627243/c99f7ece-444a-452b-9bb0-a71de03d4927.png align="center")

Inside the project, you'll find a `Contract.sol` file that you can modify. For this guide, I've written a simple `Greeter` smart contract with the following contents:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.11;

contract Greeter {
    string private greeting;

    constructor(string memory _greeting) {
        greeting = _greeting;
    }

    function greet() public view returns (string memory) {
        return greeting;
    }

    function setGreeting(string memory _greeting) public {
        greeting = _greeting;
    }
}
```

### Deploy the Smart Contract

Let's go ahead and deploy this smart contract to the validium we just created. To do that, let's run the command below from the directory of our smart contract:

```bash
npx thirdweb@latest deploy
```

This will first prompt you to link your device to your thirdweb account, and then open a URL for you to configure the constructor parameters of your smart contract:

![deploy greeter smart contract](https://cdn.hashnode.com/res/hashnode/image/upload/v1696289593341/1ee69c99-9f73-40fc-af83-aaef7e1fdd03.png align="center")

We need to change the chain we're deploying on to be the validium we just deployed. Click on the network name (e.g. `Mumbai`) to open up the network modal and click `Add Custom Network`:

![add custom network](https://cdn.hashnode.com/res/hashnode/image/upload/v1696291870635/3ebd88a0-ab05-4dae-ab10-f90f5069e7d0.png align="center")

This will allow you to add your validium's network details into the thirdweb dashboard. Map up the chain ID, URL and network name to the corresponding fields. In the screenshot below, I've color-coded each field to help you out:

![custom network details section](https://cdn.hashnode.com/res/hashnode/image/upload/v1696292181156/25a72042-0f47-460b-bac8-a452e9b7de1c.png align="center")

Ensure you set your custom network as the chain you'd like to deploy to, which will subsequently prompt your wallet to switch across to this network:

![select your custom network](https://cdn.hashnode.com/res/hashnode/image/upload/v1696292222159/9ebde643-cf7f-426a-b082-07808038eb83.png align="center")

We'll also need some funds on our validium so we can cover the gas fees of deploying our smart contract. On the Presto dashboard, you should see a URL for a **faucet**, where you can send funds to test with to your wallet:

![presto faucet URL section](https://cdn.hashnode.com/res/hashnode/image/upload/v1696292331718/a7b9072f-842e-477d-9f18-826a74875ba7.png align="center")

Open up the faucet website and paste your wallet address into the field like so:

![CDK Faucet page](https://cdn.hashnode.com/res/hashnode/image/upload/v1696292367953/f728c253-608f-45cc-a6cf-6fb357cc324d.png align="center")

Finally, click `Deploy Now` on the thirdweb dashboard to deploy your smart contract:

![Accept contract deployment transaction on thirdweb](https://cdn.hashnode.com/res/hashnode/image/upload/v1696292447182/ca22e498-99e7-4d20-9176-4eebcb227060.png align="center")

That's it! 🥳 We just shipped our smart contract! We can even use our own block explorer to confirm it. Go ahead and copy the address of our smart contract:

![Copy contract address from thirdweb dashbaord](https://cdn.hashnode.com/res/hashnode/image/upload/v1696293705855/539db809-9d1c-4020-b31d-8676309c037f.png align="center")

Open up the block explorer that was deployed for us from the Presto dashboard:

![View block explorer URL presto dashboard](https://cdn.hashnode.com/res/hashnode/image/upload/v1696293762306/54248aed-6c52-4130-a9af-f91796776346.png align="center")

And we can indeed see our contract was deployed successfully! 🚢🎉

![Blockscout contract creation transaction](https://cdn.hashnode.com/res/hashnode/image/upload/v1696293886880/1fcee801-de56-45d0-9c92-5c73d170eaff.png align="center")

## Creating a dApp on your Custom Chain

Now we've deployed our smart contract, let's see how we can create an application that interacts with the smart contract we just deployed.

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">Just want the source code? Check out the <a target="_blank" rel="noopener noreferrer nofollow" href="https://github.com/jarrodwatts/polygon-cdk-demo" style="pointer-events: none">GitHub repo</a>.</div>
</div>

You can use any tools you like to do this, but in this guide, we'll use the [thirdweb React SDK](https://portal.thirdweb.com/react) to easily connect to our custom chain from a front-end environment.

To create a new application with the thirdweb React SDK pre-configured, run the following command, *(from a new directory)*:

```bash
npx thirdweb@latest create app
```

This command allows you to interactively choose the frontend language and framework you prefer. Below are the options I selected for my app:

![npx thirdweb create app](https://cdn.hashnode.com/res/hashnode/image/upload/v1696294675812/8f53f173-95a3-49b7-a6de-218a336ca34c.png align="center")

### Setting Up the thirdweb API Key

To use thirdweb's RPC infrastructure, you'll need to get a thirdweb API key from the [**Settings**](https://thirdweb.com/dashboard/settings) tab of the thirdweb dashboard. Click `Create API Key` to get started (free):

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1696294881782/95690dad-03a7-44ea-9b9d-f2b994672ded.png align="center")

In the root of your project, create a new environment variables file (`.env.local`) and add the following:

```bash
NEXT_PUBLIC_TEMPLATE_CLIENT_ID=your-api-key-client-id-here
```

### Connect to your Custom Chain

Now we've got our API key set, we can connect to our custom chain via the RPC.

Inside the `_app.tsx` file, you can see the active chain is currently set to `ethereum`:

```typescript
// This is the chain your dApp will work on.
const activeChain = "ethereum"; // <------- Let's change this!
```

We need to change this to be set to our own custom network details, which we can do by following the [custom EVM chains](https://portal.thirdweb.com/react/react.thirdwebprovider#custom-evm-chains) section of the thirdweb docs. Let's change `ethereum` to a new object containing our network details, like so:

```typescript
const activeChain = {
  // Chain id from Presto
  chainId: 1571747963,
  // RPC URL from Presto
  rpc: ["https://cranegeode-rpc.eu-north-2.gateway.fm"],
  // Name of your rollup in various forms that you want it to appear
  shortName: "cranegeode",
  slug: "cranegeode",
  chain: "cranegeode",
  name: "Crane Geode",
  // Testnet flag, set this to true!
  testnet: true,
  // Details of the token used for gas on your chain.
  nativeCurrency: {
    name: "Ether",
    symbol: "ETH",
    decimals: 18,
  },
  // Optional, the block explorer for wallets to view transactions.
  explorers: [
    {
      name: "blockscout",
      // Block Explorer, Explorer URL from Presto
      url: "https://cranegeode-blockscout.eu-north-2.gateway.fm/",
      standard: "EIP3091",
    },
  ],
};
```

Now our application can interact with smart contracts deployed to our custom chain!

### Reading & Writing Data on the Smart Contract

Finally, we can demo interacting with the smart contract we deployed. On the homepage, (`index.tsx`), we can use the [`Web3Button`](https://portal.thirdweb.com/react/react.web3button) component to:

1. Prompt users to connect their wallet
    
2. Prompt users to add & connect to our custom chain
    
3. Call a function on our smart contract from the user's wallet.
    

Below is some simple demo code to read and write data from the smart contract:

```typescript
import { Web3Button, useContract, useContractRead } from "@thirdweb-dev/react";

// Your smart contract address (from the thirdweb dashboard)
const contractAddress = "0x33341719456e9d506bcFDbC3afcC5A6882230566";

const Home = () => {
  // Connect to your contract via the contract address
  const { contract } = useContract(contractAddress);
  // Read the current greeting on the smart contract
  const { data, isLoading } = useContractRead(contract, "greet");

  return (
    <>
      {isLoading ? ( <p>Loading...</p> ) : ( <h1>{data}</h1> )}

      <Web3Button
        contractAddress={contractAddress}
        action={(contract) =>contract.call("setGreeting", ["Hey!"])}
      >
        Set Greeting
      </Web3Button>
    </>
  );
};

export default Home;
```

Run `npm run dev` and visit [localhost:3000](http://localhost:3000/) to preview your demo application:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1696296591673/8a7f2713-682e-4390-b1fe-d71927412ee5.gif align="center")

Voilà! 💅You just built a full-stack application on your own layer 2 blockchain.

## Wrapping Up

Polygon CDK allows anyone to launch their own L2 chain, powered by the industry-leading ZK technology of Polygon.

In this guide, we've walked through the process of:

* Launching our own ZK-Validium L2 blockchain
    
* Deploying a smart contract to our own custom chain
    
* Building a dApp to interact with our custom chain smart contracts
    

You can access the full source code of this demo on [GitHub](https://github.com/jarrodwatts/polygon-cdk-demo), and follow me on [Twitter](https://twitter.com/jarrodWattsDev) for more content like this!