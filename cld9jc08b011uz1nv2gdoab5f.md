# How Account Abstraction Will Change Web3 UX Forever

In this post, we'll explore the issues with UX in the current web3 landscape, and dive deeper into how a proposal known as **account abstraction** provides a potential solution to several of the biggest problems we have today.

The reality is, a poor UX is a significant drawback to building your application in a decentralized manner. While there are many solutions actively being worked on to address the issues, account abstraction is arguably the most promising.

It's an exciting paradigm that will allow anybody to interact with [dapps](https://ethereum.org/en/developers/docs/dapps/); not just web3 enthusiasts, and is coming to reality with the latest Ethereum Improvement Proposal [EIP-4337](https://eips.ethereum.org/EIPS/eip-4337).

Let's dive into it!

## Ethereum Accounts & Transactions Recap

Let's get through the boring stuff first. To understand account abstraction, we first need to understand what an account actually *is* when talking about Ethereum.

### What Are Ethereum Accounts?

Ethereum has two different types of "accounts":

1. Contract Accounts
    
2. Externally Owned Accounts (EOAs)
    

You can think of a contract account as **code** (smart contracts) living on the blockchain that defines how the account behaves, and think of EOAs as a **person** (*although a person could have many EOAs)*.

You probably are already familiar with EOAs. Your MetaMask wallet is an EOA. EOAs are made up of a cryptographic pair of keys: public and private keys that control account activities.

Contract accounts, however, don't have a private key. They're smart contracts that are controlled by the logic of the code within them; they're not controlled by a user.

The key takeaway here is that *code* defines what contract accounts do, and *users* control what EOAs do. This matters because smart contracts have the capability to do anything you can write in code, whereas EOAs can basically just sign transactions.

![contract accounts vs eoas](https://cdn.hashnode.com/res/hashnode/image/upload/v1673397627141/f22cdc2c-7527-45e3-9d7f-291050a19adb.png align="center")

### What Are Ethereum Transactions?

*Alright, so that's* [*accounts*](https://ethereum.org/en/developers/docs/accounts/)*. What about* [*transactions*](https://ethereum.org/en/developers/docs/transactions/)*?*

Every time you want to write information to the blockchain, such as transfer tokens, or mint an NFT, a **transaction** needs to occur. **Transactions** need to be signed by an EOA, and an EOA also has to pay the associated gas fees.

A transaction is initiated *by* an EOA, and can be sent *to* either:

* Another EOA, for example, an EOA transferring ETH to another EOA.
    
* A contract account, for example, minting an NFT from a drop.
    

### How Web3 Works Today: EOAs & A Poor UX

Performing actions on the blockchain today is typically slow and tedious. Every time you want to write new information to the blockchain, you sign a transaction from your EOA to do so.

Once you're familiar with the process, this becomes the standard experience.

**For new users, however, it's a *nightmare*.**

The process of starting from scratch and interacting with a web3 application for the very first time is enough to put anyone off from entering the space, and that's *just* the beginning.

Here's a step-by-step experience that a new user goes through to perform their first action on a decentralized application from a fresh EOA:

![eoa onboarding flow for new users](https://cdn.hashnode.com/res/hashnode/image/upload/v1673398370455/45c866dc-ec62-41e2-9468-f3cfcd07578e.png align="center")

This experience is brutal for any new user, tech-savvy or not. But it doesn't stop there, the initial setup is just the beginning of the user's concerns when using EOAs.

### EOAs Are Extremely Risky

You're likely already familiar with somebody you know losing access to their EOA by either accidentally sharing or losing access to their private key. Some examples:

* [Millions of dollars of cryptocurrency 'lost' after man **dies** with only password](https://www.independent.co.uk/tech/bitcoin-exchange-quadrigacx-password-cryptocurrency-scam-a8763676.html)
    
* [Man owns $321M in bitcoin but he can't access it because he **lost** his password](https://www.cbc.ca/radio/asithappens/as-it-happens-friday-edition-1.5875363/this-man-owns-321m-in-bitcoin-but-he-can-t-access-it-because-he-lost-his-password-1.5875366#:~:text=Back%20in%202011%2C%20he%20produced,wallet%20that%20holds%20his%20bitcoins.)
    
* [Guy locks phone with potentially $6 million in crypto](https://finbold.com/guy-locks-phone-with-potentially-6-million-in-crypto-heres-how-much-the-hacker-recovered/)
    

The level of responsibility you have with traditional EOAs is **dangerously** high.

There's even a saying for it: **"not your keys, not your crypto"**; referring to the fact that if somebody else ever has your private key at *any* point (such as a centralized exchange), they have the power to control your funds; this point has been proven countless times in the past.

The harsh reality is that **private keys are easy to lose and impossible to recover**.

![with great power comes great responsibility gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1673410563457/54b33450-2bbe-47bc-a532-3f8c1aaab3d1.gif align="center")

### EOAs Have Limited Capabilities

As we touched on earlier, EOAs are very limited in their capabilities.

From your EOA, you're usually performing one of two typical actions:

1. Submitting a transaction to transfer tokens to another EOA
    
2. Submitting a transaction that executes a function on a contract account
    

Whoever has the private key can sign messages and initiate *any* transactions the EOA can handle. Knowing the private key of an EOA gives you the power to perform **everything** that an EOA is capable of. It's all or nothing.

### EOAs Will Never Enable Mainstream Adoption

In the real world, losing your credit card doesn't mean you are completely doomed.

There are rules in place that allow you to do things like set payment limits, stop transactions, detect fraud, change funds to a new account, only allow funds to be transferred under certain conditions, etc.

In web3, if you make *one* mistake, your **entire** account is compromised and unrecoverable. EOAs even compared to centralized stores of currency is... 💩.

We've dunked on EOAs enough, let's finally discuss the solution.

## What Is Account Abstraction?

**Account abstraction is the proposal to allow users to use smart contract wallets instead of EOAs.** This completely removes any need for users to use EOAs in order to perform transactions.

*But why? What do contract accounts do that EOAs can't?*

Smart contracts are infinitely more flexible in their capabilities than EOAs. Each smart contract can define different rules and configurations within its code.

Some examples of use cases can be seen below:

| **Use Case** | **EOAs** | Contract Accounts |
| --- | --- | --- |
| Permission controls | Private key grants full access to everything. | Define a list of tiered permission levels. e.g. Require 3 out of 5 signers to approve a transaction. |
| Batch transactions | Each individual action requires a separate signature. | Capable of batching transactions together; e.g. approving a token transfer and transferring a token in the same operation. |
| Account Recovery | Loss or exposure of private key means full loss of control over the wallet. | There is no private key. You can write any arbitrary logic in code that allows you to recover the funds in the wallet. |
| Transaction limits | Any transaction your wallet signs is what occurs. You cannot restrict anything. | Write any logic to control how funds can be transferred. E.g. a function to halt transactions to other addresses while you recover your account. |

These are just a few of the capabilities that contract accounts offer over traditional EOAs. The key thing is; **contract accounts are code**.

This means a*nything* you can write in code is therefore possible in a contract account.

![anything is possible gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1673412340960/df7543a7-743f-4e75-b9ee-3c6c9f9db8dd.gif align="center")

### History of Account Abstraction Proposals

Okay, this sounds great. Why aren't we already doing this today? Before we answer that, let's quickly give a brief overview of the history of account abstraction proposals dating back to 2016, and explore why EIP-4337 is different.

**2016:** [EIP-86](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-86.md) - Proposal allowing users to create "account contracts" that perform any desired signature/nonce checks instead of using the mechanism that is currently hard-coded into transaction processing.

**2020:** [EIP-2938](https://eips.ethereum.org/EIPS/eip-2938) \- Proposal to create a new transaction with type `AA_TX_TYPE`. Transactions of this type are referred to as “AA transactions”.

**2020:** [EIP-3074](https://eips.ethereum.org/EIPS/eip-3074) - Proposal allowing users to delegate control of their EOA to a smart contract. Would allow any EOA to act like a smart contract wallet without deploying a contract.

**None of these proposals have been merged into Ethereum**. They are all currently in the "stagnant" category; meaning they have been inactive for a period of 6 months or greater.

Part of the reason for these proposals not being merged is that they require consensus-layer protocol changes to the Ethereum network.

Until 2021, when [EIP-4337](https://eips.ethereum.org/EIPS/eip-4337) was proposed; account abstraction on Ethereum *without* a consensus layer change required!

### EIP-4337: Account Abstraction Using Alt Mempool

[EIP-4337](https://eips.ethereum.org/EIPS/eip-4337) introduces a *"*pseudo-transaction" object called a `UserOperation`; a structure that describes a transaction to be sent on behalf of a user.

User Operations go into an "alt mempool"; which is essentially a waiting room for storing information on unconfirmed transactions.

Nodes on the Ethereum network can choose to act as a "bundler". Bundlers pick up user operations from the mempool, and package multiple user operations into a single transaction known as a "bundle transaction".

Once they create a bundle transaction, they send it to a global "[singleton](https://eips.ethereum.org/EIPS/eip-2470#:~:text=Some%20contracts%20are%20designed%20to,%2D1820%20and%20EIP%2D2429.)" smart contract known as the "EntryPoint". There is only one EntryPoint smart contract on the entire blockchain. The bundler calls a function on the EntryPoint smart contract called `handleOps`.

This function receives the bundle transaction, and calls a special function on each account: \``` validateUserOp` ``. Each smart contract wallet must implement this function.

\``` validateUserOp` `` should verify the operation’s signature, and pay the fee if the account considers the operation valid, before continuing to execute the operation.

Each smart contract wallet also must implement a second function: expected to be called "`execute`" to actually perform the operation that is sent in by the EntryPoint contract.

A simplified flow of this can be seen below:

![summary of account abstraction in eip-4337](https://cdn.hashnode.com/res/hashnode/image/upload/v1674449123541/87bef380-1733-4feb-bf6c-52edbef6f521.png align="center")

### Why Does This Matter?

Contract accounts are the next evolution of wallets required to provide a much-needed improvement to the UX of web3.

The possibilities are really endless for what this change enables:

* Creating wallets for your users under the hood when they sign up for your app
    
* Session keys for web3 games (allow any X transaction for Y amount of time without the need for signatures on each transaction)
    
* Team wallets to use decentralized applications with tiered permissions
    

A grandma could be collecting NFTs and not even know what the blockchain is. Account abstraction enables everybody to use web3; not just tech enthusiasts.

## Wrapping Up

In this post, we've outlined:

* The fundamental concepts of accounts and transactions on Ethereum.
    
* How EOAs fall short in terms of web3 user experience.
    
* What account abstraction does to solve it and how it works under the hood.
    

Account abstraction is a game changer for web3 and bringing decentralized applications to a mainstream audience.

The power to use smart contracts as your wallet brings endless possibilities and EIP-4337 is the latest proposal of account abstraction that doesn't require a consensus-layer change.

### Thanks For Reading!

I appreciate you making it this far! 🙏

If you enjoyed this content, consider following me on [**Hashnode**](https://hashnode.com/@JarrodWatts) for more!

%[https://hashnode.com/@JarrodWatts]