# zkEVMs and the Race to Scale Ethereum

In the past week, the race to launch an EVM-compatible ZK rollup has been heating up:

* [Polygon announced the launch date for their zkEVM mainnet](https://twitter.com/0xPolygon/status/1625529122561597440)
    
* [zkSync launched their zkEVM mainnet]!(https://twitter.com/zksync/status/1626235292268240902) *(just two days later!)*
    

Why's this happening? WTF is a zkEVM? How do I use one?

Let's dive into it.

## Wait, what's wrong with Ethereum?

Ethereum Mainnet is able to process roughly **15 transactions** per second. We all know what this means; huge gas prices and long finality times, leading to a grim user experience.

Depending on how deep down the rabbit hole you are at this point, you may already be familiar with solutions to address this; sidechains, layer 2s, rollups... *all that good stuff*.

These were all built because Ethereum is *currently* too popular for what it is capable of handling. Despite this, the Ethereum Foundation has agreed they are not going to sacrifice security/decentralization to improve scalability.

This balance between decentralization, security, and scalability is known as the [scalability trilemma](https://ethereum.org/en/upgrades/vision/). The long-term vision of Ethereum is to achieve all three of these qualities.

![blockchain scalability trilemma](https://cdn.hashnode.com/res/hashnode/image/upload/v1676680857126/2bb5ac1f-96e2-4c75-9ece-67fe7afc286b.png align="center")

## Why should we improve scalability?

[Scalability](https://ethereum.org/en/developers/docs/scaling/) refers to how well Ethereum can handle the demand for transactions to be processed. If more transactions can be processed faster, two things happen:

1. [Gas prices](https://ethereum.org/en/developers/docs/gas/#top) go down
    
2. [Finality](https://ethereum.org/en/developers/docs/consensus-mechanisms/pos/#finality) *(how fast your transactions are finalized)* gets faster
    

So, if we're not going to sacrifice decentralization, how do we make Ethereum more scalable? There are multiple different solutions that are either already being used today, in the research/testing phase, or recently shipped to mainnet 😉.

Let's explore some of the existing solutions first, why they have problems of their own, and then dive into ZK EVMs as the potential "final boss" of scaling solutions.

## What's been built to address this so far?

Most solutions built to address the scalability of Ethereum usually come with some kind of sacrifice in the other two qualities in the scalability trilemma.

*"it scales better... buuuuuut &lt;insert downside here&gt;",* is usually how it goes*.*

Let's take a closer look at each of them.

### Sidechains

A sidechain is a completely separate blockchain from Ethereum, with different histories, roadmaps, and even [consensus algorithms](https://ethereum.org/en/developers/docs/scaling/sidechains/#consensus-algorithms).

In order for a side-chain to actually *be* a side-chain, it is required to have a two-way [bridge](https://ethereum.org/en/bridges/) connecting it with Ethereum Mainnet, where data can be transferred between the two chains.

[Polygon's PoS](https://polygon.technology/solutions/polygon-pos) ([proof-of-stake](https://ethereum.org/en/developers/docs/consensus-mechanisms/pos/)) is the most popular example of a sidechain.

Polygon PoS boasts a ~10,000x lower gas fee per transaction, and ~450x increase in transactions per second than Ethereum. Polygon PoS is awesome. Some of the biggest brands in the world have chosen Polygon as their partner; Adidas, Meta, Stripe, Reddit, and more.

![Partners of Polygon](https://pbs.twimg.com/media/FpLekSCXwAMbZec?format=jpg&name=medium align="left")

However, sidechains sacrifice *some* measure of decentralization or security to achieve this higher throughput.

You can dive deeper into the risk assessment of scaling solutions on sites such as [L2BEAT](https://l2beat.com/scaling/risk). For example, if we take a look at Polygon PoS, we can see this:

![Polygon PoS risk assessment L2BEAT](https://cdn.hashnode.com/res/hashnode/image/upload/v1675941834613/af699227-f18e-4940-a37a-bb1cca4362ad.png align="center")

In addition, despite these sacrifices, Polygon PoS has had struggles with scalability several times in the past.

For example, in 2022, a play-to-earn game called Sunflower Farmers sent gas prices skyrocketing after the game took up over 40% of the network's gas fees.

%[https://twitter.com/PhABCD/status/1478613172487602179] 

More recently, transactions were failing from users' wallets due to incorrect gas estimations, as a result of the high demand on Polygon PoS.

%[https://twitter.com/moo9000/status/1626195644573425665] 

## Rollups

Rollups are different from sidechains in the sense that they're not "separate" from Ethereum. Instead, they publish transaction results onto Ethereum mainnet; allowing them to derive the full benefits of Ethereum's security in the process.

Rollups work by allowing operators to bundle multiple off-chain transactions together outside the main [EVM](https://ethereum.org/en/developers/docs/evm/), and then submit them all together to Ethereum; achieving 10 to 100 times improvements in terms of scalability.

On a technical level, these batches are submitted to a "rollup" smart contract that is stored on Ethereum mainnet; which keeps track of the rollup's state.

There are two kinds of rollups that exist today:

1. Optimistic rollups
    
2. Zero Knowledge (ZK) Rollups
    

A [bridge](https://ethereum.org/en/developers/docs/bridges/) allows you to transfer funds or data between Ethereum mainnet and a rollup.

### Optimistic Rollups

Optimistic rollups such as [Optimism](https://www.optimism.io/) and [Arbitrum One](https://arbitrum.io/) are called "optimistic" because they **assume** the transactions that they roll up are valid **by default**.

They don't post any proof that the transactions are valid to Ethereum. Instead, transactions are "innocent until proven guilty", which includes a fraud-proving scheme that enables the detection of invalid transactions.

A "challenge period" is available for parties to contest the results of a rollup transaction by submitting a [**fraud-proof**](https://ethereum.org/en/glossary/#fraud-proof) that can see whether or not a fraudulent transaction took place in that rollup.

If one is detected, the transactions are re-executed and the state of the rollup is updated. The party who submits the fraudulent transactions incur a penalty, creating an incentive mechanism within the ecosystem.

*Okay, this sounds great. What's the downside?*

Because of this challenge period, (usually around 7 days), you **must wait** until the challenge period is over before you can perform this withdrawal to Ethereum mainnet over the bridge.

There's also *some* level of reliance that at least *one* honest person is checking for fraudulent transactions to challenge the state of the rollup.

### ZK Rollups

Alright, we're almost at the juicy part.

ZK rollups, unlike optimistic rollups, **do** post [validity proofs](https://ethereum.org/en/glossary/#validity-proof) to prove the correctness of the changes they post to Ethereum mainnet.

These validity proofs are "zero-knowledge proofs"; meaning the details of the statement that the rollup provides can be proven as true, without revealing the actual contents of that statement, using either [zk-SNARKs](https://arxiv.org/abs/2202.06877) or [zk-STARKs](https://eprint.iacr.org/2018/046).

[Learn more about how validity proofs work in zk-rollups](https://ethereum.org/en/developers/docs/scaling/zk-rollups/#validity-proofs-in-zk-rollups).

This removes the requirement for a challenge period, as the transactions are provably true as soon as the rollup contract verifies the validity proof. This means you can withdraw funds from ZK rollups to Ethereum mainnet without waiting!

*Awesome, but what's the catch?*

Unlike the other solutions we've talked about so far, ZK Rollups are **not** compatible with the EVM. This means you can't just point your Solidity contract at a ZK Rollup and ship it, which you *can* do with all of the other solutions we've described.

Whereas, with other solutions, you can simply change the URL of your deployment script and deploy the exact same code to Ethereum, Polygon PoS, Optimism, Arbitrum, etc.

That's the catch. You **cannot** do this with ZK Rollups... <mark>Until now!</mark>

**Enter, zkEVMs**.

## What is a zkEVM?

The main reason ZK rollups are not utilized is that they have not been EVM-compatible in the past, making it extremely difficult to actually build anything on them.

If I've done a good job explaining things so far, the name "zkEVM" is hopefully self-explanatory to you... **A zkEVM is a zero-knowledge rollup, that *is* EVM compatible.**

zkEVMs enable all the benefits of a ZK Rollup, with the same developer experience of building on any other EVM chain like Polygon or Ethereum. Combining the best of both worlds, and potentially providing an answer to the scalability trilemma.

This technology unlocks all existing web3 dapps to simply change their deployment scripts to a different RPC URL and ship directly to the zkEVM, exactly the same way they do now.

zkEVMs handle the batching of transaction execution and post cryptographic proof that the result of those transactions is correct to Ethereum mainnet, all while maintaining EVM compatibility.

This means that every smart contract on Ethereum or Polygon PoS can now also be deployed to a zkEVM. Drastically improving the scalability of the smart contracts and the dapps that use them.

*"In the medium to long term, ZK rollups will win out in all use cases as ZK-SNARK technology improves."* - [Vitalik Buterin](https://vitalik.ca/general/2021/01/05/rollup.html)

### How do I build on a ZK EVM?

With all of the hype and releases of ZK EVMs, how do you actually build on one?

At [thirdweb](https://thirdweb.com/) *(where I currently work)*, we just shipped support for *any* EVM chain, including all the zkEVMs such as Polygon zkEVM, zkSync, Scroll and more.

Consider checking out the below guide I helped review by my awesome colleague and friend @[Avneesh Agarwal](@avneesh0612), which shows you how to:

* Add a ZK EVM network to your wallet
    
* Bridge funds from Ethereum to the ZK Rollup
    
* Deploy your Solidity smart contract to a ZK EVM
    

%[https://blog.thirdweb.com/guides/how-to-deploy-a-smart-contract-to-polyon-zkevm-testnet/] 

Or check out this 3-minute video to shipping an NFT collection on Polygon's zkEVM:

%[https://twitter.com/jarrodWattsDev/status/1627520959854022656] 

## Wrapping Up

That's it! I've tried my best to condense the past few weeks of reading about Ethereum scaling solutions into a 5 minute read for you :)

What do you think? Are zkEVMs the holy grail for scaling Ethereum? Or are we just getting started? Let me know!

If you enjoyed this article, there'll be many more just like it coming soon. Consider following me to get notified when those are released!

%[https://hashnode.com/@JarrodWatts]