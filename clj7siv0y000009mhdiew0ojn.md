---
title: "Polygon PoS Is Becoming A zkEVM Validium (Explained)"
seoDescription: "Learn what Polygon's new proposal for upgrading the Polygon PoS chain into a zkEVM validium means"
datePublished: Thu Jun 22 2023 23:44:42 GMT+0000 (Coordinated Universal Time)
cuid: clj7siv0y000009mhdiew0ojn
slug: polygon-pos-is-becoming-a-zkevm-validium-explained
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1687393273169/e8319724-b37a-4be9-a91f-de57dfe5387a.png
tags: blockchain, cryptocurrency, web3, polygon, zkevm

---

Polygon recently announced a roadmap of improvements to their products to become "the value layer of the internet".

The [first announcement](https://polygon.technology/blog/polygon-2-0-polygon-pos-zk-layer-2) in the roadmap is a proposal to upgrade the Polygon PoS (proof-of-stake) chain we know and love to become a zkEVM Validium.

I must admit... when I read that, I thought *"wtf is a zkEVM Validium?"*!

If you thought something similar, you're in the right place. I've done the research so you don't have to, and in this post, we're going to cover:

* What's changing about Polygon PoS?
    
* What are Validiums? Why is Polygon PoS becoming one?
    
* What about Polygon zkEVM? What's the difference between PoS and that?
    

Let's do this!🫡👇

## Recap: Sidechains, rollups, and scaling

As a quick refresher, let's quickly explore why any of these scaling solutions exist in the first place. *You can safely skip this part if you're already a pro.*

On Ethereum, users pay [fees](https://ethereum.org/en/developers/docs/gas/#priority-fee) that validator nodes take as an incentive to include users' transactions in new blocks. Over time, Ethereum has reached "[certain capability limitations](https://ethereum.org/en/developers/docs/scaling/#:~:text=certain%20capacity%20limitations)".

As the demand to write information to Ethereum, such as the desire to mint NFTs or transfer ETH rose, the fee required to have your transaction included in a block became very expensive *(sometimes hundreds of dollars per transaction).*

This has led to many different scaling solutions developed by talented groups of people across the world, each with a unique way of accomplishing more scalability on Ethereum; including companies like [Polygon](https://polygon.technology/), [Optimism](https://www.optimism.io/), and [Arbitrum](https://arbitrum.io/).

These companies created implementations of [sidechains](https://ethereum.org/en/developers/docs/scaling/sidechains/), [rollups](https://ethereum.org/en/developers/docs/scaling/#rollups), [plasma](https://ethereum.org/en/developers/docs/scaling/plasma/) chains, and more, to address the scalability problem that the Ethereum mainnet faces, and enable users to more easily create and experience decentralized applications.

You can learn more in-depth about each of these solutions in my previous blog post:

%[https://blog.jarrodwatts.com/zkevms-and-the-race-to-scale-ethereum] 

So that's why Polygon PoS was created initially, and more recently, Polygon zkEVM was also released. So why is Polygon PoS changing?

## What's changing about Polygon PoS?

Polygon PoS (proof-of-stake) is, in its current state*,* a [sidechain](https://ethereum.org/en/developers/docs/scaling/sidechains/).

The main benefit of a sidechain is increased scalability; higher transactions per second and significantly lower gas fees per transaction.

Since Polygon PoS is EVM-compatible, you can perform the exact same development process you would if you were developing for Ethereum, and deploy to Polygon PoS instead, immediately granting your users a massively improved experience when interacting with your smart contracts, with no extra effort.

The downside to sidechains is that because they don't post back state changes or transaction data to Ethereum, they therefore don't inherit all of Ethereum's security properties; essentially making the tradeoff of more scalability for less security; as outlined in Ethereum's [scalability trilemma](https://ethereum.org/en/roadmap/vision/#:~:text=The%20challenge%20of%20decentralized%20scaling).

Polygon PoS powers the most popular brand web3 efforts in the world, like Reddit, Stripe, Starbucks, Bulgari, and more; giving it a powerful ecosystem and strong incentive for developers to launch their projects using this network.

However, Polygon's broader vision with Polygon 2.0 is to use the latest innovations in [zero-knowledge proofs](https://ethereum.org/en/zero-knowledge-proofs/) to power all of their scaling solutions to Ethereum, and Polygon PoS in its current state is secured by its own validators; ***not*** by ZK proofs.

That's what's going to change; the [proposal](https://polygon.technology/blog/polygon-2-0-polygon-pos-zk-layer-2) suggests Polygon PoS should upgrade to use ZK proofs to provide even more scalability and stronger security to the chain.

With this upgrade, Polygon PoS would become a **zkEVM validium**:

* **ZK**: Utilizes zero-knowledge proofs to prove the result of transactions.
    
* **EVM**: It's Ethereum Virtual Machine (EVM) compatible, meaning it works out of the box with existing developer tools and user-facing apps such as wallets.
    
* **Validium**: This is a bit more complex; we'll explore this below.
    

## What are Validiums?

By using zero-knowledge proofs instead of purely relying on validators to execute transactions, Polygon PoS is becoming a [validium](https://ethereum.org/en/developers/docs/scaling/validium/), and will no longer be a sidechain.

The proposal means that Polygon PoS will use zero-knowledge proofs to guarantee and verify the validity of batches of transactions, which are both executed and stored off-chain; leading to lead massive improvements in scalability.

According to [Ethereum's documentation](https://ethereum.org/en/developers/docs/scaling/validium/#settlement), validiums can process ~9,000 transactions, or more, per second; compared to Ethereum's roughly ~15 transactions per second.

Validiums are different from rollups and sidechains because they ***only*** post the validity proof (containing verified proof of the result of the transactions) back to Ethereum, **not** the actual **transaction data** of the executed transactions.

The way this works is by having a verifier smart contract deployed to Ethereum, which the validium submits validity proofs to. The validity proofs are zero-knowledge proofs that contain the result of the transactions, but not the data within them.

The verifier smart contract determines if the validity proof is valid, and if it's not, the batch submitted from the Validium is rejected and not stored on Ethereum.

The tradeoff to this process is that Validiums utilize an off-chain data availability model; transaction data is not stored on Ethereum, so the validators of the network must attest to data availability; which comes with its own set of [potential risks](https://ethereum.org/en/developers/docs/scaling/validium/#data-availability).

### Why is this better for Polygon PoS?

Compared to sidechains, validiums are better in two key aspects:

1. Scalability: The amount to which rollups and sidechains can scale is limited by the data bandwidth on Ethereum Mainnet. Since validiums reduce the amount of data Ethereum has to process, they [greatly extend throughput on Ethereum](https://ethereum.org/en/developers/docs/scaling/validium/#off-chain-data-storage).
    
2. Security: The use of validity proofs gives validiums higher security guarantees than sidechains.
    

Typically, the downside to validiums is that they are not EVM-compatible, making them only suitable for simple applications like transferring tokens or minting NFTs, due to the [considerable overhead of proving EVM instructions in a zero-knowledge proof circuit](https://ethereum.org/en/developers/docs/scaling/validium/#validiums-and-evm-compatibility).

Over the past eighteen months, Polygon Labs has built "[the fastest ZK proving system in the industry and launched the only EVM-equivalent zkEVM on mainnet](https://polygon.technology/blog/polygon-2-0-polygon-pos-zk-layer-2#:~:text=Over%20the%20past%20eighteen%20months%2C%20Polygon%20Labs%20has%20released%20the%20fastest%20ZK%20proving%20system%20in%20the%20industry%20and%20launched%20the%20only%20EVM%2Dequivalent%20zkEVM%20on%20mainnet)" to overcome this obstacle, meaning that a validium doesn't have to come with this limitation.

Creating an EVM-compatible validium is the best of both worlds; as it is flexible enough to perform all EVM operations and use all existing tools and applications in the EVM world, while also providing greater scalability and security to Polygon PoS; which has a massive community already.

Using zero-knowledge proofs for this process is the cherry on top; by proving the transactions upfront, it means there are no other limitations such as a challenge period that come with other scaling solutions.

## What about Polygon zkEVM?

You might be asking, *didn't Polygon just release a zkEVM? What happened to that?*

Polygon PoS and Polygon zkEVM currently co-exist as two separate chains, and they will continue to be separate chains moving forward; although Polygon 2.0's future announcements will include improved unity between the two.

The key difference here is that Polygon zkEVM is a zero-knowledge **rollup**, whereas Polygon PoS will become a zero-knowledge **validium**.

* Polygon zkEVM *does* post transaction data back to Ethereum mainnet. Meaning it will be comparatively less scalable, and more secure, making it suitable for projects such as high-value DeFi applications.
    

* Polygon PoS (when upgraded) *does* ***not*** *post* transaction data back to Ethereum. Meaning it will be comparatively more scalable, and less secure, making it suitable for projects that have high transaction volume and require low transaction fees, e.g. gaming, social, and micro DeFi.
    

![](https://assets-global.website-files.com/637e2b6d602973ea0941d482/6491cb67a15313f153883a39_Blog%20cover%20(99).png align="left")

[*Link to image* ↗](https://polygon.technology/blog/polygon-2-0-polygon-pos-zk-layer-2)

## Wrapping Up

TLDR: the big words are intimidating, but the proposed change to Polygon PoS will provide improvements to both the scalability and security of the chain.

Polygon's zkEVM will coexist, and be more suited to apps that require higher levels of security. Whereas Polygon PoS will be more suited towards apps demanding higher throughput; such as social apps built on [Lens Protocol](https://www.lens.xyz/).

It's clear to see the investments Polygon has made in zero-knowledge technology are powering massive innovations throughout its product suite, and I'm excited to see how this technology can be used more broadly.

[Follow me on Twitter](https://twitter.com/jarrodWattsDev) for more updates like this one!