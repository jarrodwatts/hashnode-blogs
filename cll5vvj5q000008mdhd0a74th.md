---
title: "A Deep Dive: How Polygon zkEVM Proves Batches of Transactions"
seoTitle: "How Does Polygon zkEVM Work?"
seoDescription: "Polygon zkEVM Is An EVM-Compatible ZK-Rollup. Learn How Polygon zkEVM Works Under The Hood By Following The Life-Cycle Of A Transaction End-to-End."
datePublished: Fri Aug 11 2023 01:02:25 GMT+0000 (Coordinated Universal Time)
cuid: cll5vvj5q000008mdhd0a74th
slug: how-polygon-zkevm-proves-batches-of-transactions
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1691636973745/d8469b29-0e66-48f3-a5f7-bd29bb2f6192.png
tags: blockchain, web3, polygon, zero-knowledge-proofs, zkevm

---

In my [**previous post**](https://blog.jarrodwatts.com/how-zk-proofs-and-zkevms-work), we discussed how ZK proofs work, and how the different non-interactive ZK proofs are utilized in ZK-EVMs such as Polygon zkEVM.

*But how exactly does that work?* What information gets sent back to Ethereum? And how does this enable Polygon zkEVM to inherit the security of Ethereum?

In this post, we'll take a closer look at exactly what is happening under the hood, covering how transactions:

1. Get submitted to Polygon zkEVM.
    
2. Are executed almost instantly.
    
3. Are batched together using data encryption methods.
    
4. Get sequenced and sent to Ethereum L1.
    
5. Achieve consolidated finality on L1 with the power of ZK proofs.
    

Let's do this!

## Submitting Transactions

As we explored in the [previous post](https://blog.jarrodwatts.com/how-zk-proofs-and-zkevms-work), users are constantly submitting their L2 transactions to the trusted sequencer's node via JSON-RPC interface *(typically through wallets like MetaMask).*

This allows the Polygon zkEVM to look and feel exactly like Ethereum from the user's perspective when interacting with dApps. Example below:

%[https://twitter.com/jarrodWattsDev/status/1686304245539274753] 

From the user's perspective, the transactions go through almost instantly, allowing them to continue using dApps immediately after submitting the transaction. This powerful UX benefit is possible because the state of the zkEVM is updated without any information being sent to Ethereum (L1) at first.

However, if users want to **bridge funds** from L2 (zkEVM) to L1 (Ethereum), essentially performing a withdrawal, the full transaction life-cycle needs to be complete. This requires the PolygonZkEVM smart contract to:

1. Receive the batches from the sequencer (know what transactions to prove).
    
2. Receive the validity proof from the aggregator (prove the transactions).
    

[*See the flow of data in a zkEVM diagram*](https://blog.jarrodwatts.com/how-zk-proofs-and-zkevms-work#heading-flow-of-data-in-a-zkevm)*.*

From the user's POV at this point, the simplified flow looks like this:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691634316635/334f6935-6d98-492f-aa58-625e463863c9.png align="center")

## Executing Transactions

After being submitted, the transactions are stored in a pending transactions pool, where they await the sequencer's selection for either execution or discard.

The sequencer makes a few checks to see if it can discard a transaction, based on:

1. If the sender has enough funds to cover the transaction.
    
2. If the smart contract called exists and has valid/correct bytecode.
    
3. If the transaction isn't a duplicate.
    
4. If the transaction isn't a "[double-spend](https://en.wikipedia.org/wiki/Double-spending)", to ensure the sender's funds haven't already been spent in another transaction.
    

Once the transaction is considered to be valid, the sequencer updates the Polygon zkEVM state, at which point the user experiences the transaction going through almost instantly.

If we modify our diagram to explore under the hood, here's how it looks:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691634760659/8eb90795-de23-49de-8a21-e61df871b92a.png align="center")

At this point in time, the user continues to interact with the state of the L2. Every step *after* this is related to posting transaction data back to Ethereum L1; which is only relevant to the user if they want to bridge funds *back* to Ethereum.

## Batching Transactions

After being added to the L2 state, the transaction gets broadcast to all other zkEVM nodes on the network and is ready to be **batched** up with other transactions.

To batch transactions together, they are concatenated into a single set of bytes in binary form. On the `PolygonZkEVM` smart contract, a Solidity [`struct`](https://docs.soliditylang.org/en/v0.8.14/structure-of-a-contract.html) is defined, named [`BatchData`](https://github.com/0xPolygonHermez/zkevm-contracts/blob/main/contracts/PolygonZkEVM.sol#L27-L41).

Within this struct, a `transactions` field of type `bytes` is defined, that contains the encoded batch of transactions concatenated together.

```solidity
/**
 * @notice Struct which will be used to call sequenceBatches
 * @param transactions L2 ethereum transactions EIP-155 or pre-EIP-155 with signature:
 * EIP-155: rlp(nonce, gasprice, gasLimit, to, value, data, chainid, 0, 0,) || v || r || s
 * pre-EIP-155: rlp(nonce, gasprice, gasLimit, to, value, data) || v || r || s
 * @param globalExitRoot Global exit root of the batch
 * @param timestamp Sequenced timestamp of the batch
 * @param minForcedTimestamp Minimum timestamp of the force batch data, empty when non forced batch
 */
struct BatchData {
    bytes transactions;
    bytes32 globalExitRoot;
    uint64 timestamp;
    uint64 minForcedTimestamp;
}
```

As you can see in the comments above, the transactions are encoded using "RLP", and can be either EIP-155 or pre-EIP-155 transactions.

* [EIP-155](https://eips.ethereum.org/EIPS/eip-155) was a proposal created in 2016 by Vitalik to prevent replay attacks. It adds a `chainId` value to avoid transactions intended for one chain to also work on other chains.
    
* `rlp` stands for [Recursive-Length Prefix Serialization](https://ethereum.org/en/developers/docs/data-structures-and-encoding/rlp). It's a data serialization method that Ethereum uses to encode the structure of any arbitrarily nested arrays of data into binary.
    

As for the other three fields:

1. `globalExitRoot`: The root of the global exit *(Merkle)* tree that stores information about asset transfers between L1 and L2. [**Learn more**](https://zkevm.polygon.technology/protocol/exit-tree/).
    
2. `timestamp`: The time the batch was created.
    
3. `minForcedTimestamp`: Only relevant if the user is not using the trusted sequencer (helpful for censorship resistance). Typically set to `0`. [Learn more](https://zkevm.polygon.technology/docs/protocol/sequencer-resistance/).
    

To update our diagram, let's zoom out and see what the sequencer is up to:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691635391815/81fab14f-0c72-4481-9acb-f1972605695a.png align="center")

Multiple of these batches get created, according to some size rules that we'll define in the next section as defined on the L1 smart contract. So, let's see what happens with these batches next.

## Sequencing Batches of Transactions

Once we have batches of transactions, they're ready to be "sequenced". To do this, the sequencer calls the `PolygonZkEVM` smart contract's `sequenceBatches` function (on L1) and provides it with multiple batches of transactions.

[This transaction](https://etherscan.io/tx/0x38ab7f8302722566b1262098418b322aa42f3353efbfa9569617c232c5e15ef3) is an example of a `sequenceBatch` transaction on Ethereum mainnet. By inspecting the input data, we can see the `52` batches of transactions that got included in this particular function call:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691570911184/a156aed0-6c8e-4644-ae51-177c0ef2212a.png align="center")

Each batch also contains the `transactions` field that we saw earlier (the concatenated bytes), which concatenates as many RLP encoded transactions as it can fit:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691571009307/3fe17b10-2ab6-4aff-9cbf-491ef000a9a6.png align="center")

The `PolygonZkEVM` smart contract has a constant value called `_MAX_TRANSACTIONS_BYTE_LENGTH` that determines how many transactions can be concatenated together in that field. [Source code](https://github.com/0xPolygonHermez/zkevm-contracts/blob/main/contracts/PolygonZkEVM.sol#L107-L119):

```solidity
// Max transactions bytes that can be added in a single batch
// Max keccaks circuit = (2**23 / 155286) * 44 = 2376
// Bytes per keccak = 136
// Minimum Static keccaks batch = 2
// Max bytes allowed = (2376 - 2) * 136 = 322864 bytes - 1 byte padding
// Rounded to 300000 bytes
// In order to process the transaction, the data is approximately hashed twice for ecrecover:
// 300000 bytes / 2 = 150000 bytes
// Since geth pool currently only accepts at maximum 128kb transactions:
// https://github.com/ethereum/go-ethereum/blob/master/core/txpool/txpool.go#L54
// We will limit this length to be compliant with the geth restrictions since our node will use it
// We let 8kb as a sanity margin
uint256 internal constant _MAX_TRANSACTIONS_BYTE_LENGTH = 120000;
```

Similarly, there is a limit to how many batches can be sent as one transaction too, in a constant variable called `_MAX_VERIFY_BATCHES`. [Source code](https://github.com/0xPolygonHermez/zkevm-contracts/blob/main/contracts/PolygonZkEVM.sol#L128-L130):

```solidity
// Maximum batches that can be verified in one call. It depends on our current metrics
// This should be a protection against someone that tries to generate huge chunk of invalid batches, and we can't prove otherwise before the pending timeout expires
uint64 internal constant _MAX_VERIFY_BATCHES = 1000;
```

These batches are provided into a function called [`sequenceBatches`](https://github.com/0xPolygonHermez/zkevm-contracts/blob/main/contracts/PolygonZkEVM.sol#L484-L487), which accepts:

1. An array of `BatchData` structs called `batches`.
    
2. An address that fees for sequencing batches are sent called `l2Coinbase`.
    

```solidity
function sequenceBatches(
    BatchData[] calldata batches,
    address l2Coinbase
) external ifNotEmergencyState onlyTrustedSequencer {
  ...
}
```

This function iterates over each batch and ensures they are valid, before updating the **virtual state** on the L1 smart contract inside a [mapping](https://docs.soliditylang.org/en/v0.8.7/types.html) called [`sequencedBatches`](https://github.com/0xPolygonHermez/zkevm-contracts/blob/main/contracts/PolygonZkEVM.sol#L184-L186):

```solidity
// Queue of batches that defines the virtual state
// SequenceBatchNum --> SequencedBatchData
mapping(uint64 => SequencedBatchData) public sequencedBatches;
```

In our diagram, here's where we are at:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691635720495/7eaae0a1-10dc-4bce-9b3f-5dc43a3ebe74.png align="center")

### The Three Finality States

Now's a good time to introduce the different states a transaction can be in in the Polygon zkEVM. There are different phases of finality that transactions go through:

1. **Trusted State**: The state is updated on L2. Has not yet reached L1.
    
    * Most of the time, this is what state users interact with.
        
2. **Virtual State**: Batches have been sequenced and data is available on L1.
    
    * At this point, data is available on L1 for anyone to prove, but is not yet proven.
        
3. **Consolidated State**: A ZK proof has been posted on L1.
    
    * At this point, the data is proven and inherits Ethereum's security.
        

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691628675611/a985e7e8-39e3-4ecc-bceb-ade4816eee19.png align="center")

[Learn More ↗](https://zkevm.polygon.technology/docs/protocol/state-management)

So far, we've talked about the process up to trusted and virtual states, so let's jump into the final phase now, where ZK proofs of computational integrity are submitted to L1.

## Aggregating Sequenced Batches

Once all of the sequenced batches have reached L1, the final step is to generate a ZK proof that verifies the validity of these transactions.

Aggregator nodes take the sequenced batches and provide them to the [ZK prover](https://zkevm.polygon.technology/docs/zkProver/overview), which produces a final [SNARK](https://blog.jarrodwatts.com/how-zk-proofs-and-zkevms-work#heading-what-are-zk-snarks) using `fflonk` protocol. (Quick summary below):

%[https://twitter.com/jbaylina/status/1624116186861404188] 

The end result is the aggregator receives a ZK proof that is succinct enough that it can be stored on the Ethereum L1. A simplified diagram of this flow is below:

![Skeletal Overview of zkProver](https://zkevm.polygon.technology/assets/images/fig4-zkProv-arch-337c7dd5cfc9f9c0c2e85024cc269904.png align="left")

Once the aggregator node has the proof, it calls the PolygonZkEVM smart contract's `verifyBatchesTrustedAggregator` function, providing the proof it just received to the function, among other parameters, [source code](https://github.com/0xPolygonHermez/zkevm-contracts/blob/main/contracts/PolygonZkEVM.sol#L700-L716):

```solidity
/**
 * @notice Allows an aggregator to verify multiple batches
 * @param pendingStateNum Init pending state, 0 if consolidated state is used
 * @param initNumBatch Batch which the aggregator starts the verification
 * @param finalNewBatch Last batch aggregator intends to verify
 * @param newLocalExitRoot  New local exit root once the batch is processed
 * @param newStateRoot New State root once the batch is processed
 * @param proof fflonk proof
 */
function verifyBatchesTrustedAggregator(
    uint64 pendingStateNum,
    uint64 initNumBatch,
    uint64 finalNewBatch,
    bytes32 newLocalExitRoot,
    bytes32 newStateRoot,
    bytes calldata proof
) external onlyTrustedAggregator {
  ...
}
```

Let's inspect an example transaction again to see how this looks in the real world.

[This transaction](https://etherscan.io/tx/0xdee359c60e068a4340d5b2d1f9a30cb2108810ecf5f9be843f76c25e999cbda0) comes from the trusted aggregator and calls the `verifyBatchesTrustedAggregator` on the PolygonZkEVM smart contract with the proof:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691632283671/aa05cae7-51f8-416b-8850-ca973286bbc3.png align="center")

*Details on the other parameters can be found* [*here*](https://zkevm.polygon.technology/docs/protocol/transaction-aggregation#aggregating-a-sequence-of-batches)*.*

Within this function, another contract called the `rollupVerifier` has a function [`verifyProof`](https://github.com/0xPolygonHermez/zkevm-contracts/blob/main/contracts/PolygonZkEVM.sol#L1510-L1513C10) that gets called. This function is provided with the proof as well as an `inputSnark`; which is a cryptographic representation of all the L2 transactions of a specific L2 State transition.

```solidity
// Verify proof
if (!rollupVerifier.verifyProof(proof, [inputSnark])) {
    revert InvalidProof();
}
```

If the proof is valid, [various states are updated](https://github.com/0xPolygonHermez/zkevm-contracts/blob/main/contracts/PolygonZkEVM.sol#L726-L737) like the global exit root and the `batchNumToStateRoot` mapping containing the consolidated L2 state roots:

```solidity
// State root mapping
// BatchNum --> state root
mapping(uint64 => bytes32) public batchNumToStateRoot;
```

Posting and verifying this proof on L1, in this example, took ~350K gas:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691636320196/5af00f83-3e40-4e28-bc11-af0250accd0a.png align="center")

With one final update to our diagram, we have achieved a consolidated state on L1!

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691636153930/34b1a6dd-7189-4b72-a8e2-dcc1d9d791da.png align="center")

At this point, the batches of transactions are in the final "consolidated" state, and this is how Polygon zkEVM inherits the security of Ethereum; by posting and proving all transaction data back to Ethereum L1.

## Wrapping Up

When interacting with the Polygon zkEVM from a user perspective, transactions are confirmed almost instantaneously, while simultaneously inheriting the security of Ethereum as this entire process occurs under the hood.

This approach provides the best of both worlds, where users have both low gas fees and fast transaction speeds within seconds and can also bridge funds back to Ethereum within ~1 hour using the power of ZK proofs.

In this blog, we've covered the full life cycle of a transaction from the Polygon zkEVM, all the way through to being proved using zero-knowledge on Ethereum L1.

If you enjoyed this content, consider giving me a follow on [Twitter](https://twitter.com/jarrodWattsDev)!