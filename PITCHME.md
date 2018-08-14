# Blockchain Basics

#### An Introduction to Bitcoin

---

## Disclaimers

- I claim no expertise in this subject. |
- I have been following this space for several years, and I am still a total n00b. |
- The following leaves out a massive amount of details. |
- It is impossible to explain blockchain in 30 minutes. |
- I stole most of this. |

---

## What is Blockchain?

Note:

* Who here has heard of blockchain?
* Who feels like they have a good grasp of what blockchain is?

+++

### A Definition

A distributed, immutable database, where each record is a block of transactions with a link to the previous block, managed by a peer-to-peer network that achieves consensus without the use of a centralized authority.

Note:

* This is by no means a canonical defintion.

+++

### Diverse Topic

- computer science
- P2P networks
- economics
- finance
- game theory
- security
- cryptography
- privacy
- policy and governance

---

## Bitcoin

Note:

* I'll only cover Bitcoin in this talk.
* It's a great starting point for understanding blockchain.
* The blockchain world has exploded in the last few years.

+++

### The World's First Blockchain

Invented by Satoshi Nakamoto in 2008 - [Bitcoin: A Peer-to-Peer Electronic Cash System](https://bitcoin.org/bitcoin.pdf)

Genesis block mined in 2009

Currency market cap currently over 30 billion USD

Many other blockchains have started since then

Note:

* Satoshi is anonymous. Nobody knows who he really is.
* Litecoin
* Ripple
* Ethereum

+++

### Components

- decentralized p2p network
- public transaction ledger
- set of rules for independent transaction validation and currency issuance
- mechanism for reaching global decentralized consensus on the valid blockchain

Source: [Mastering Bitcoin, Chapter 1](https://github.com/bitcoinbook/bitcoinbook/blob/second_edition/ch01.asciidoc)

Note:

* We will touch on all of these points soon.

---

### Sending Money Using Bitcoin

#### A Simple Example

Note:

* Who here has used Bitcoin?
* The following is about as simple as it gets.

+++

Alice and Bob share a meal at a restaurant.

Alice forgot her wallet at home, so Bob pays.

+++

Alice owns some bitcoin and wants to use it to pay back Bob.

+++

Bob downloads a Bitcoin wallet onto his phone.

Bob's wallet generates a new Bitcoin address: `1JrvQWvkSv8g4XTmr4SFgKTeeYdGyo7RwV`

+++?image=assets/mycelium-wallet.png&size=auto 90%

Note:

* This is a screenshot of the Mycelium wallet app on Android.
* The app has also generated a QR code along with the address.

+++

Alice puts this address into her wallet. She sends 0.01 bitcoin to the address.

+++?image=assets/blockchain-wallet.png&size=auto 90%

Note:

* This is the Blockchain.info wallet.

+++

Bob waits a few seconds and sees the transaction appear in his wallet.

Note:

* All of this happened without a bank or a trusted third-party.
* Alice and Bob used totally different wallets made by different companies.
* You might be wondering how this is different from PayPal or Square Cash, other than the esoteric address.
* Let's find out why Bitcoin is different.

---

## Cryptography Basics

Note:

* Before discussing how Bitcoin works, we need to establish some of the underlying cryptography primitives.

+++

### Crypographic Hash Function

A function `H(x)` that maps input data of arbitrary size to output data of a fixed size, such that

* for any given `x` and `y`, `H(x) ≠ H(y)`
* it is quick to compute `H(x)`
* any change to `x` produces an entirely different `H(x)`
* it is infeasible to determine `x` given `H(x)`

Source: [Wikipedia - Cryptographic hash function](https://en.wikipedia.org/wiki/Cryptographic_hash_function)

Note:

* `H(x) ≠ H(y)`, in fact they are wildly different
* Infeasible means it takes a lot of computing power and a long time to find `x` given `H(x)`.
* Bitcoin uses the SHA256 hash function.

+++

### Digital Signature

A way to prove to a recipient that a message

* was created by the sender
* cannot be denied by the sender has having been the creator
* was not altered in transit

Source: [Wikipedia - Digital signature](https://en.wikipedia.org/wiki/Digital_signature)

Note:

* Bitcoin uses Elliptic Curve Digital Signature Algorithm (ECDSA).

+++

#### Sending a signed message

1. Sender generates private key and public key <!-- .element: class="fragment" -->
2. Sender publishes public key <!-- .element: class="fragment" -->
3. Sender signs message with private key to produce signature <!-- .element: class="fragment" -->
4. Sender sends message and signature to receiver. <!-- .element: class="fragment" -->
5. Receiver verifies message using message, signature, and sender's public key <!-- .element: class="fragment" -->

Note:

* Lots of cryptographic protocols built on signatures, included TLS and PGP.

---

## Transactions

Note:

* Is everyone still with me?
* Does anyone have questions?
* Now let's dig into the basics of Bitcoin, beginning with Transactions.

+++

### Building Blocks

+++

#### Transaction Output

Indivisible unit of value that was spent in a transaction

Note:

* The word "indivisible" is important, and will make sense in a minute.
* Transaction Output can be seen as the "coin".

+++

#### Every transaction

* consumes Transaction Outputs from previous transactions
* produces new Transaction Outputs

+++

#### Unspent Transaction Output (UTXO)

The output of a transaction that is available to include in another transaction.

Note:

* This is really important to how Bitcoin transactions work.

+++

#### UTXO Set

- Bitcoin does not have a notion of an account
- User's balance is the sum of the value of all the UTXOs that the user can spend

Note:

* Wallet software provides the "account" and "balance" abstractions on behalf of the user.

+++

[UTXO Set Chart](https://blockchain.info/charts/utxo-count?timespan=all)

+++

### Transaction Structure

Note:

* Let's look at the component parts of a transaction.

+++

#### Inputs

One or more, each consisting of

* Reference to existing Tx Output
* Unlocking script

Note:

* Unlocking script validate's owner's ability to spend Tx Output.
* We'll take a look at what this "script" is shortly.

+++

#### Outputs

One or more, each consisting of

* Amount of bitcoin
* Locking script

Note:

* Locking script defines the conditions that must be met to spend the Tx Output
* Sum of value of outputs must be less than the sum of the inputs. This will be important later.

+++

#### Generating Change

Remember: A Transaction Output is indivisible.

UTXOs must be spent in their entirety.

If sending amount less than UTXO's value, must create a "change" output.

This is usually to another address that you own.

Note:

* This is the same as spending a 20 USD bill on something that is less than 20 USD.

+++

#### Fees

Remember: Sum of value of outputs must be less than the sum of the inputs.

Difference is taken by miner as a fee.

+++?image=https://en.bitcoin.it/w/images/en/6/67/Transaction.png&size=auto 90%

Note:

* A: combining 2 inputs into 1 output, spending two previous outputs
* B: coinbase transaction, created by a miner; will explain this soon
* C: combining 2 inputs into 2 outputs, one of which is change; one input is 100, another is 50, spending 101, change of 49
* D: 1 input, 1 output
* Transactions form a chain of UTXOs being consumed and produced, going all the way back to a coinbase transaction.

---

## Script

Note:

* As a programmer, this is one of the most fascinating parts of Bitcoin.

+++

Programming language used for executing transactions

Similar to Forth

Stack-based language

Note:

* Stack is a first-in-last-out data structure.
* Each instruction is place on the stack, and operations are performed on the top item of the stack.

+++

### Pay-to-Public-Key-Hash Example

![P2PKH Example](https://raw.githubusercontent.com/bitcoinbook/bitcoinbook/second_edition/images/mbc2_0603.png)

Note:

* Remember: Inputs have unlocking script, outputs have locking script.
* To validate a transaction, we concatenate the unlocking script from the user (scriptSig) and the locking script from a UTXO (scriptPubKey).
* Unlocking script contains spender's signature and public key.
* Notice that it does not contain the private key.
* This is unlike a credit card transaction, where you reveal your private key.

+++

### P2PKH Script Execution

Note:

* Let's see the script in action.

+++?image=https://raw.githubusercontent.com/bitcoinbook/bitcoinbook/second_edition/images/mbc2_0605.png&size=auto 90%

Note:

* Each instruction placed on stack, one at a time.
* `<sig>` placed on stack first.
* `<PubK>` placed on stack.
* `DUP` instruction duplicates `<PubK>` and places on the stack.

+++?image=https://raw.githubusercontent.com/bitcoinbook/bitcoinbook/second_edition/images/mbc2_0606.png&size=auto 90%

Note:

* `HASH160` hashes the top item on the stack, which is `<PubK>` and puts result `<PubKHash>` on the stack.
* `<PubKHash>` from the locking script is placed onto the stack.
* `EQUALVERIFY` operation pops two items from the stack and returns `TRUE` if they are equal.
* `CHECKSIG` pops two items from the stack and returns puts `TRUE` on the stack if `<sig>` is valid for `<PubK>`.

+++

### Advanced Transactions

* Pay-to-Script-Hash (P2SH)
* Data Recording (RETURN)
* Timelocks
* Over 70 operations available!

Note:

* P2SH allows multisignature transactions among other things.
* `RETURN` allows users to record small bits of data in the blockchain.
* Script is what makes Bitcoin a "programmable money".
* These advanced transactions are the building blocks of smart contracts and meta currencies.

+++

### Addresses

* Example: `1JrvQWvkSv8g4XTmr4SFgKTeeYdGyo7RwV`
* Derived from public key and not part of transaction data
* Wallets display addresses to users

Note:

* Remember addresses?
* You may have noticed that the above script did not reference a Bitcoin address.

+++

[Famous Pizza Purchase](https://blockchain.info/tx/a1075db55d416d3ca199f55b6084e2115b9345e16c5cf302fc80e9d5fbf5d48d)

---

## P2P Network

Note:

* A very important part of Bitcoin's decentralization is the peer-to-peer network.

+++

* Flat topology
* All peers are equals
* No special nodes

+++

### Broadcasting Transactions

* When sending bitcoin, a wallet broadcasts transaction to connected peers.
* Those peers rebroadcast the transaction to their connected peers.
* Transaction is eventually "heard" by all peers.

+++

### Transaction Pool

- When nodes hear these broadcasted transactions, they validate.
- Valid transactions are added to the "mempool" of confirmed transactions.
- Transactions stay in the mempool until they are added to the blockchain.

+++

[Sam's Bitcoin Node](https://bitnodes.21.co/nodes/216.197.79.74-8333/)

---

## Blocks and the Blockchain

Note:

* Now let's take a look at the data structures that make up the blockchain.

+++

### Block Structure

- Each block is identified by the cryptographic hash of the block data.
- Each block contains the hash of the previous block.

Note:

* This is an oversimplification, but it will work for now.

+++?image=https://raw.githubusercontent.com/bitcoinbook/bitcoinbook/second_edition/images/mbc2_0901.png&size=auto 90%

Note:

* Here we see a visualization of the blockchain.
* Notice the block hash and the pointer to the previous block hash.

+++

Remember: Any change to `x` causes `H(x)` to be completely different.

+++

### Changes Cause Cascading Effects

- If any change is made to a block, it changes the hash of the block. |
- This causes the child block to change, which causes the child block's hash to change. |
- This continues on for any descendant blocks. |

+++

- The more generations of blocks, the more expensive it is to change.
- This is what provides the blockchain's immutability. |

Note:

* It is generally accepted that a transaction is considered final after it is 6 blocks deep in the chain.

+++

[Genesis Block](https://blockchain.info/block/000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f)

Note:

* First Bitcoin block ever mined.

+++

Blockchain Demo - https://anders.com/blockchain/

Note:

* This is a really cool interactive demo of how a blockchain is structured and how changes cascade.

+++

### Merkle Trees

Note:

* Transactions in a block are arranged in a Merkle Tree.

+++?image=https://raw.githubusercontent.com/bitcoinbook/bitcoinbook/second_edition/images/mbc2_0902.png&size=auto 90%

Note:

* Very briefly, a Merkle Tree is a tree where each node contains the hash of the hashes of its children nodes.
* Only the Merkle Root is stored in the block header.
* A change to any transaction has a cascading effect of changing the hashes all the way up the tree, which changes the block hash, which changes the hash of descendant blocks.

---

## Mining

Note:

* Everything up to this point has been done before it one form or another.
* Here is the part that makes Bitcoin and blockchain technology unique and allows it to maintain its decentralization.

+++

- Mining is the mechanism that secures the Bitcoin blockchain.
- Mining is what makes decentralized consensus possible. |
- Mining is what prevent double-spending. |

Note:

* These things are what differentitates Bitcoin from previous attempts at digital currency.

+++

### Consensus Mechanism

- Each miner independently verifies new transactions that are broadcast on the network. |
- Each miner independently chooses new transactions from the transaction pool to put in the next block. |
- Each miner independently verifies new blocks that are claimed by other miners. |

Note:

* Remember the transaction pool from before.

+++

### Competition

- Network has a current "target" number. |
- Each miner constructs a block, puts a number in the block, and calculates the hash for the block. |
- If the hash is less than the target, the miner wins the right to publish the block on the blockchain. |
- If the hash is not less than the target, the miner increments the number and tries again. |

Note:

* The number chosen by the miner is called a "nonce".

+++

### Hashing Power

- Remember: hash functions are one-way. |
- Miners essentially guess the number that will produce a hash below the target. |
- The chance of finding the next block is proportional to the amout of hash power a miner has. |
- The more hash power, the more likely to find the next block. |

Note:

* There is a theoretical attack called the 51% Attack, where a miner can double-spend if they have 51% or more of the hashing power of the network.

+++

### Forks

- When a miner publishes a valid block, then the other miners (most of the time) move on to the next block. |
- It is possible for miners to find a new block at the same time, which causes a fork in the chain. |

Note:

* The vast majority of the time, forks only last for one block.
* Miners abandon one side of the fork for the longest chain.

+++

### Proof-of-Work

- Called Proof-of-Work because it is hard to find the solution, but easy to verify.
- Remember: it is infeasible to find `x` given `H(x)`, but quick to verify `H(x)` given `x`.
- Miners _prove_ that they did the _work_ of finding a nonce that causes the hash of the block to be below target.

+++

### Target Adjustment

- Target value causes each mining round to take 10 minutes on average.
- Adjusted every 2,016 blocks (2 weeks) as network's total hash power changes.
- [Difficulty Chart](https://blockchain.info/charts/difficulty?timespan=all)

+++

### Rewards

- Coinbase transaction: new units of bitcoin minted in every block
- Transaction fees: for every transaction, difference between sum of the inputs and sum of the outputs

Note:

* Rewards incentivize miners to provide hash power to secure the network.
* Transactions with higher fees are usually chosen by miners first.

+++

### Supply of Bitcoin

- Mining reward is halved every 210,000 blocks (~4 years).
- Reward is currently 12.5 bitcoin.
- Until year 2140, when no new bitcoin will be created.
- Maximum supply of 21 million.

---

## Genius of Bitcoin and Blockchain

Bootstrap process

- security of blockchain depends on health of mining ecosystem |
- health of mining ecosystem depends on value of currency |
- value of currency depends on security of block chain |

Source: [Coursera: Bitcoin and Cryptocurrency Technologies](https://www.coursera.org/learn/cryptocurrency)

+++

### Many Other Use Cases

* Proof-of-Existence
* M-of-N transactions, useful for escrows or transactions requiring multiple parties
* Colored coins
* Payment channels, useful for micropayments
* Many, many more

Note:

* Remember: programmable money
* Proof-of-Existence allows you to prove a document existed at a certain time.
* One payment channel use case is video streaming, paying small amounts per minute of video watched.

---

- [Blockchain Resources GitHub Repo](https://github.com/sugarjig/blockchain-resources)
- [#blockchain](https://willowtree.slack.com/messages/C2YCF2UGN) Slack channel

Note:

* I barely scratched the surface of Bitcoin here.
* I haven't even gotten into the details of other blockchains!
* I hope that this interest group can dig into those details in the coming weeks and months.

+++?image=https://raw.githubusercontent.com/bitcoinbook/bitcoinbook/second_edition/images/cover.png&size=auto 90%

Note:

* I highly recommend this book.

---

## Image Sources

- [Bitcoin Wiki - Transaction](https://en.bitcoin.it/wiki/Transaction)
- [Mastering Bitcoin, Chapter 6, scriptSig and scriptPubKey](https://github.com/bitcoinbook/bitcoinbook/blob/second_edition/ch06.asciidoc#scriptSig_and_scriptPubKey)
- [Mastering Bitcoin, Chapter 6, P2PubKHash1](https://github.com/bitcoinbook/bitcoinbook/blob/second_edition/ch06.asciidoc#P2PubKHash1)
- [Mastering Bitcoin, Chapter 6, P2PubKHash2](https://github.com/bitcoinbook/bitcoinbook/blob/second_edition/ch06.asciidoc#P2PubKHash2)
- [Mastering Bitcoin, Chapter 9, Chain of Blocks](https://github.com/bitcoinbook/bitcoinbook/blob/second_edition/ch09.asciidoc#chain_of_blocks)
- [Mastering Bitcoin, Chapter 9, Simple Merkle](https://github.com/bitcoinbook/bitcoinbook/blob/second_edition/ch09.asciidoc#simple_merkle)

---
