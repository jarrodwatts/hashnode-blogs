---
title: "The Ultimate Guide to Building on Polygon zkEVM"
seoTitle: "The ULTIMATE Guide to Building on Polygon zkEVM"
seoDescription: "Learn how to create & deploy smart contracts and decentralized applications on the Polygon zkEVM by creating an NFT collection smart contract"
datePublished: Mon Mar 27 2023 21:24:06 GMT+0000 (Coordinated Universal Time)
cuid: clfrc6xnn000109l24swuh6z1
slug: the-ultimate-guide-to-building-on-polygon-zkevm
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1679895546481/8842582c-7ff5-47a4-a5d4-cbe1cd02bdb2.png
tags: ethereum, web3, smart-contracts, thirdweb, polygon-zkevm

---

In this guide, I'll show you how to create a full-stack web3 application on the [Polygon zkEVM](https://wiki.polygon.technology/docs/zkEVM/). By the end of the guide, we'll cover how to:

* Set up our wallet to connect to the Polygon zkEVM
    
* Create and deploy a smart contract to the Polygon zkEVM
    
* Build a React application to connect and interact with the smart contract
    

Let's get started!

## What is the Polygon zkEVM?

If you're not already familiar, Polygon zkEVM is an EVM-compatible zero-knowledge [rollup](https://ethereum.org/en/developers/docs/scaling/#rollups). This means it acts as a scaling solution to the [Ethereum](https://ethereum.org/en/developers/docs/) blockchain; improving its scalability by enabling more transactions per second and significantly cheaper [gas fees](https://ethereum.org/en/developers/docs/gas/).

As developers, an important component of the Polygon zkEVM is its [**EVM**](https://ethereum.org/en/developers/docs/evm/) **compatibility**, which means we can use the same tooling we know and love such as [Solidity](https://docs.soliditylang.org/en/v0.8.19/) to build smart contracts and deploy them to the network; something that was previously not possible with ZK rollups.

%[https://blog.jarrodwatts.com/zkevms-and-the-race-to-scale-ethereum] 

Now we've got the basics down, let's dive into the building.

## Setting Up the Polygon zkEVM

The Polygon zkEVM uses a [bridge](https://ethereum.org/en/developers/docs/bridges/) to transfer ETH from Ethereum to the Polygon zkEVM rollup.

In this guide, we'll be using the [testnet](https://ethereum.org/en/developers/docs/networks/#ethereum-testnets) to demonstrate the process, however the process is the same on mainnet, except you are dealing with real funds; I'll provide you with resources for both testnet and mainnet options.

Before we begin the bridging process, you'll need a wallet setup with some test *(or real) ETH loaded in*. I won't cover these topics as I'm assuming you likely already have this setup. Just in case you don't, below are some resources to help you:

1. [Getting started with MetaMask](https://support.metamask.io/hc/en-us/articles/360015489531-Getting-started-with-MetaMask)
    
2. [How to get testnet ETH using a faucet](https://www.alchemy.com/overviews/goerli-faucet)
    

### Bridge Funds to the Polygon zkEVM Network

With a wallet setup and either real ETH or testnet ETH in it, you're ready to begin the bridging process. Head over to the bridge URL below to get started *(pick either testnet or mainnet depending on which one you want)*:

* [Testnet bridge](https://public.zkevm-test.net/login?ref=blog.jarrodwatts.com)
    
* [Mainnet bridge](https://bridge.zkevm-rpc.com/)
    

On the bridge, click the "Add to MetaMask" button to add the network information about the zkEVM to your wallet. Click "Approve" and "Switch Network" as the prompts appear.

![Add polygon zkEVM network to wallet](https://cdn.hashnode.com/res/hashnode/image/upload/v1679874781768/e551a94b-db7d-4758-9227-89b00410b9a2.png align="center")

Next, connect your wallet to the bridge by clicking on the button below:

![connect wallet to Polygon zkEVM bridge](https://cdn.hashnode.com/res/hashnode/image/upload/v1679874892633/ddda7137-e646-4356-a151-583a436676db.png align="center")

Select the Ethereum network as the "From" network; to specify you are bridging funds *from* Ethereum *to* the zkEVM.

![select Ethereum as the "from" network](https://cdn.hashnode.com/res/hashnode/image/upload/v1679874965698/68020d56-3712-4782-b96c-08c60e790d94.png align="center")

Enter the amount of ETH you want to bridge, e.g. `0.005 ETH`, and click "Continue", and "Bridge" in the confirmation step.

![bridge funds from eth to zkevm](https://cdn.hashnode.com/res/hashnode/image/upload/v1679875134642/01f1e76b-bbf2-42a6-b0fe-1697a17741cf.png align="center")

Approve the transaction to initiate the bridging process.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1679875214070/28f4d02a-acbe-45b7-bf40-90e0c3d9670b.png align="center")

Finally, click "Finalise" and approve the transaction to complete the bridging process.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1679884286546/f2812fbb-90d5-4e6c-b523-11ae98b65689.png align="center")

Once completed, you should see the funds available in your wallet:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1679884371279/116bc2f9-ad56-4265-976f-e952711431f2.png align="center")

Nice! Now we've got some funds to use on the zkEVM. Remember you can send funds back to Ethereum through the bridge at any time; one of the awesome benefits of ZK rollups!

## Creating a Smart Contract on Polygon zkEVM

Now we've got some funds setup, we're ready to start building! First, we're going to create a smart contract. In this guide, we'll build a simple [ERC721](https://eips.ethereum.org/EIPS/eip-721) NFT smart contract; but you can create anything you like.

### Creating the Hardhat project

We'll use [Hardhat](https://hardhat.org/) in this guide to keep it simple, but this process will also work for [Foundry/Forge](https://book.getfoundry.sh/forge/) too.

To begin, let's create a new Solidity project by using the [thirdweb CLI](https://portal.thirdweb.com/cli).

Run the following command in your terminal to get started:

```bash
npx thirdweb create contract
```

This command kicks off an interactive set of questions to create your Solidity project with the framework and standards of your choice.

Below, I'm using Hardhat with a basic ERC721 NFT smart contract as a starting point:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1679885174169/e4504d02-596a-4062-8d34-25600f85a721.png align="center")

### Creating the NFT Collection smart contract

If we open this project in our text editor, we can see we have a barebones Hardhat project with a smart contract in the `/contracts/MyContract.sol` file, containing the following code:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@thirdweb-dev/contracts/base/ERC721Base.sol";

contract Contract is ERC721Base {
    constructor(
        string memory _name,
        string memory _symbol,
        address _royaltyRecipient,
        uint128 _royaltyBps
    ) ERC721Base(_name, _symbol, _royaltyRecipient, _royaltyBps) {}
}
```

This is using thirdweb's [ERC721 base contract](https://portal.thirdweb.com/contractkit/base-contracts/erc-721/erc721base) which provides code for us to implement all the features of the ERC721 NFT standard, along with the [ERC721A](https://www.erc721a.org/) optimization to the standard.

Feel free to customize, override, or add any features you want to; or of course, build a completely different smart contract!

## Deploying a Smart Contract to Polygon zkEVM

Now you've created the smart contract, you're ready to ship it to the zkEVM. To do this, we'll use [thirdweb deploy](https://portal.thirdweb.com/deploy).

Run the following command *(from the same directory as your smart contract)* to begin the deployment flow:

```bash
npx thirdweb deploy
```

This command completes all the steps required to prepare our contract:

* Compiles the smart contract using Hardhat
    
* Uploads the smart contract ABI to IPFS.
    
* Opens the [thirdweb dashboard](https://thirdweb.com/dashboard) to deploy from your connected wallet.
    

No private key, RPC URLs, or config required!

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1679885959755/46af549a-5177-46d0-8e2f-bd516da9fbf2.png align="center")

Once this process is complete, open the URL that gets printed at the end of the command to be taken to the thirdweb dashboard.

### Populate the Contract's Constructor Parameters

Connect your wallet to the dashboard, and provide values for any of your smart contract's [constructor parameters](https://www.tutorialspoint.com/solidity/solidity_constructors.htm). If you're following along with the NFT collection contract, the parameters are outlined below:

* **Name**: the name of your smart contract
    
* **Symbol**: Ticker symbol for each token in the contract, e.g. ETH or BAYC
    
* **Royalty Recipient**: Wallet address that receives funds from royalty fees
    
* **Royalty Bps**: What percentage royalty fee to take, e.g. 500 = 5%
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1679886102463/0e42d4af-d152-441f-90f4-bc71d20dc216.png align="center")

### Select the Polygon zkEVM Network

To specify that we want to deploy to the Polygon zkEVM, click "Configure Networks":

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1679886271150/d950b1e4-8161-4e50-9de7-b69a0b90136a.png align="center")

Search for "Polygon zkEVM", and you'll see options for both the Polygon zkEVM Testnet and Polygon zkEVM Mainnet:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1679947568679/2a7040e5-a131-4ffa-8f29-953490cea271.png align="center")

Select the network and click "Add Network" to add the zkEVM as a deployment option. Close the modal and head back to the deploy page, and click "Deploy Now":

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1679886457784/0c94f938-31eb-4ba9-9872-4a16e6a1a1f1.png align="center")

Finally, approve the "Contract Deployment" transaction.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1679886502891/e954470c-0d62-4165-a3d6-69a52981e730.png align="center")

Nice! You just deployed a smart contract to the zkEVM! 🥳 Feel free to explore the dashboard, where you can view and execute all the functionality of your smart contract from the UI.

## Creating A Web3 App on Polygon zkEVM

Now we've shipped our smart contract, let's create an application that can connect to users' wallets, and have them interact with our smart contract.

To do that, we'll again use the [thirdweb CLI](https://portal.thirdweb.com/cli) to create a frontend application with the [thirdweb React SDK](https://portal.thirdweb.com/react) installed.

Run the following command in your terminal to get started:

```bash
npx thirdweb create app
```

This will ask you a series of questions about the framework and languages you want to use to create your front-end application. In this guide, we'll use [Next.js](https://nextjs.org/) and JavaScript; but feel free to use what you're comfortable with!

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1679887174369/20d4fcc5-19bd-43c9-a0a0-15c1858fb03e.png align="center")

Open up the newly created application in your text editor.

### Connect the App to Polygon zkEVM

The first thing we'll need to do is configure the [`ThirdwebProvider`](https://portal.thirdweb.com/react/react.thirdwebprovider) to use the Polygon zkEVM network. We'll use the [`@thirdweb-dev/chains`](https://www.npmjs.com/package/@thirdweb-dev/chains) package to manage this connection for us.

From within your application, run the following command:

```bash
npm install @thirdweb-dev/chains@nightly
```

This package has over 700 chains for us to use and connect to, including the Polygon zkEVM testnet and mainnet.

Next, within the `_app.js` file, *(or* `Index.js` file *for CRA/vite)*, import the `PolygonZkevmTestnet` from the chains package and set it as the [`activeChain`](https://portal.thirdweb.com/react/react.thirdwebprovider#activechain-recommended) prop:

```javascript
import { ThirdwebProvider } from "@thirdweb-dev/react";
// Import the Polygon zkeVM chain and set it as the activeChain
// PolygonZkevmTestnet = testnet
// PolygonZkevmBeta = mainnet
import { PolygonZkevmTestnet, PolygonZkevmBeta } from "@thirdweb-dev/chains";
import "../styles/globals.css";

function MyApp({ Component, pageProps }) {
  return (
    <ThirdwebProvider activeChain={PolygonZkevmTestnet}>
      <Component {...pageProps} />
    </ThirdwebProvider>
  );
}

export default MyApp;
```

This manages our connection to the blockchain with a free [RPC URL](https://portal.thirdweb.com/glossary/rpc) out of the box.

### Connect to the Smart Contract

Head to the home page at `index.js` *(or* `App.js`*)* and connect to the smart contract you deployed using its contract address via the [`useContract`](https://portal.thirdweb.com/react/react.usecontract) hook.

*Hint: you can get the contract address from the thirdweb dashboard.*

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1679887868121/b3a29e15-504d-4065-a14b-affc6c4392c1.png align="center")

Here's how our code looks so far:

```javascript
import { useContract } from "@thirdweb-dev/react";

// Place your smart contract address here!
const contractAddress = "0x66CC8aB6a3bFD1e1510d4ED2115b26E2f13fdcfC";

export default function Home() {
  // Connect to your smart contract with the useContract hook
  const { contract } = useContract(contractAddress);

  return <div>Hello world!</div>;
}
```

To add the capability to connect to users' wallets, we could use the [`ConnectWallet` UI component](https://portal.thirdweb.com/react/react.connectwallet), but we're going to use the [`Web3Button` UI Component](https://portal.thirdweb.com/react/react.web3button) instead, to allow the contract admin to mint an NFT after they've connected.

### Mint An NFT Using the React SDK

The `Web3Button` connects to the user's wallets, switches them to the correct network (polygon zkeVM), and *then* calls some functionality on our smart contract when clicked.

First, import the button component from the `@thirdweb-dev/react` package, then provide the [`contractAddress`](https://portal.thirdweb.com/react/react.web3button#contractaddress-required) and [`action`](https://portal.thirdweb.com/react/react.web3button#action-required) props. The `action` is what occurs when the button is clicked; i.e. where we're going to mint our NFT.

```javascript
import { useContract, Web3Button } from "@thirdweb-dev/react";

const contractAddress = "0x66CC8aB6a3bFD1e1510d4ED2115b26E2f13fdcfC";

export default function Home() {
  const { contract } = useContract(contractAddress);

  return (
    <Web3Button
      contractAddress={contractAddress}
      action={async (contract) => {
        return await contract.erc721.mint({
          name: "My zkEVM NFT",
          description: "I just minted an NFT on the Polygon zkEVM!",
          image: "your-image-url-here",
        });
      }}
    >
      Mint NFT
    </Web3Button>
  );
}
```

Here, we provide the metadata of the NFT we want to mint and use the [`mint`](https://portal.thirdweb.com/typescript/sdk.erc721mintable#mint) function to upload and pin the metadata to IPFS before minting the NFT into the smart contract.

Run your application on your localhost to see the result, using `npm run dev` and visiting [http://localhost:3000/](http://localhost:3000/). Click the button and accept the transaction to mint your first NFT!

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1679892570325/d7c8f501-b26c-42d3-adff-b2713cc9ddd3.png align="center")

On your contract dashboard, you can now see an NFT has been minted into the smart contract with the metadata you provided:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1679892610387/87dcfbf0-3025-4313-83fd-a14ab9d0228d.png align="center")

Awesome 🔥 we now have our very own NFT collection minted onto the polygon zkEVM network.

### Displaying NFTs in the App

Finally, we can use the thirdweb React SDK to read information from our smart contract too. In this example, we'll use the [`useNFT`](https://portal.thirdweb.com/react/react.usenfts) hook to read the metadata of the NFT we just minted, and the [NFT Renderer](https://portal.thirdweb.com/react/react.thirdwebnftmedia) component to display it on the UI.

```javascript
import { ThirdwebNftMedia, useContract, useNFT } from "@thirdweb-dev/react";

const contractAddress = "0x66CC8aB6a3bFD1e1510d4ED2115b26E2f13fdcfC";

export default function Home() {
  const { contract } = useContract(contractAddress);
  const { data: nft, isLoading } = useNFT(contract);

  return isLoading ? (
    <p>Loading...</p>
  ) : (
    <div>
      <ThirdwebNftMedia metadata={nft.metadata} />
      <h2>{nft.metadata.name}</h2>
      <p>{nft.metadata.description}</p>
    </div>
  );
}
```

Using a [ternary operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator), we now display a loading state while the metadata loads from IPFS, and show the metadata of the NFT on the UI once it's loaded:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1679892993891/2d015175-58b4-4f0d-97ce-0fc69d264e0e.png align="center")

## Wrapping Up

That's it! We've covered everything you need to know when it comes to creating a full-stack application built on top of Polygon's new zkEVM; from setting up the network in your wallet to creating an app that allows users to connect their wallets and mint NFTs.