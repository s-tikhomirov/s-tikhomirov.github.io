---
layout: post
title: Swiss Blockchain Winter School
published: false
---

CHANGE PUBLICHED TO FALSE TO PUSH AS DRAFT

Here are my notes from the talks at the [Swiss blockchain winter school](https://blockchainschool.epfl.ch/) which took place on 11-15 February 2019 in Interlaken, Switzerland.

# Day 1

## Prateek Saxena (Zilliqa) – Better Consensus in the Bitcoin Model

After some welcome talks, the school started with an overview of the Bitcoin consensus and proposals on how to improve it.
One can look at Bitcoin (and probably any blockchain) as a replicated state machine.
The key objective is to agree on the total ordering of transactions.
Total ordering is equivalent to solving double-spending (the first of the conflicting transactions is valid).
Bitcoin is operating under a number of assumptions: a trusted genesis block, and a synchronous network. [[^1]]
Synchrony is interesting: while not explicitly mentioned in the original whitepaper, it is essential that a new block is propagated through the network with a bounded delay (bounded by the block time, probably).
The goals we want to achieve are:

* safety (a.k.a. "nothing bad happens": miners do agree on the same order of blocks and don't change the decision);
* liveness (a.k.a. "something good eventually happens"): valid blocks do get confirmed sooner or later;
* fairness: the number of blocks a miner produces is proportional to its share of mining power.

[^1]: Read my notes from the Israeli winter school, where there was a more elaborate discussion on types of synchrony assumptions and their applicability in blockchains.

Bitcoin is fundamentally limited by the propagation delay (i.e., the speed of light).
An optimal confirmation latency should be on the order of O(d), where d is the propagation latency, but it's unreasonable to hope for anything faster.
In real-world networks, the global propagation delay is around 1-2 seconds, as tested in a geo-distributed EC2 setup.

Is Bitcoin optimally utilizing the available resources given these fundamental limitations?
Not really.
The speaker proposes a protocol called [OHIE](https://arxiv.org/abs/1811.12628).
The key idea as formulated in the abstract is as follows:

> OHIE composes as many parallel instances of Bitcoin’s original (and simple) backbone protocol as needed to achieve near-optimal throughput (i.e., utilizing within a constant factor of the available bandwidth).

When reading such papers, I often remember a phrase my math teacher in high school used to say: "Contrary to all other subjects, in mathematics, words have _meanings_."
So when I stumble upon yet another "let's just run multiple chains in parallel", I'm puzzled: what does the word "parallel" here even mean?
Well, let's find out.

[TODO: more from OHIE paper]



Can we do even better?
One possible way would be to combine the good old Byzantine agreement (BA) protocols, such as PBFT.
Their major drawback is that they require fixed membership (contrary to open blockchains, where all can participate without asking for permission) and relatively poor scalability.
Can we take the best of both worlds?
Yes: we use proof-of-work to choose a committee and then run a BA algorithm.

One implementation of this approach, presented in this talk, is Zilliqa.
[TODO: elaborate on Zilliqa]





## Vincent Botbol (Nomadic Labs / Tezos) – Tezos, an Overview of a Self-amending Crypto-ledger

## Giulia Fanti (Carnegie Mellon University) – Compounding of Wealth in Proof-of-Stake Cryptocurrencies

## Sergey Gorbunov – Algorand: Distributed Ledger and Digital Signatures for Consensus







