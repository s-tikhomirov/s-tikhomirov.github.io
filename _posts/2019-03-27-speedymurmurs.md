---
layout: post
title: SpeedyMurmurs&#58; applying friend-to-friend routing to payments
published: true
---

In a [previous post](/silentwhispers), I discussed SilentWhispers -- a routing algorithm for credit networks.
Today I'll dive into a follow-up paper by a partially intersecting group of authors, entitled "[Settling payments fast and private: decentralized routing for path-based transactions](https://arxiv.org/abs/1709.05748)", which presents a routing algorithm called SpeedyMurmurs.

# Routing, revisited

The [SilentWhispers paper](https://eprint.iacr.org/2016/1054) (2016) was mostly dealing with credit networks (and was itself a continuation of the work of some of the co-authors on this subject).
The introduction of the SpeedyMurmurs paper (2017) sets up the stage in the blockchain world.
The authors introduce the notion of path-based transaction (PBT) network, which unifies credit networks such as Ripple and Stellar with L2 solutions on top of open blockchains (Lightning, Raiden).
They then define the three mechanisms that comprise a PBT network:

* routing;
* payment;
* accountability.

In all PBT networks, payments are executed in an atomic series of elementary operations.
Credit networks use a (somewhat) centralized entity for ensuring atomicity (even "real-world" courts maybe).
Lightning and Raiden rely on the base layer.
The problem of routing, however, is orthogonal to the implementation of enforcement, so it makes sense to unify the credit-like networks under an umbrella term PBT.

Speaking of the routing mechanism specifically, the authors list four dimensions to measure how well it performs:

* effectiveness (share of successfully completed transactions);
* efficiency (delays and overhead);
* scalability (how the system responds to the growth of the number of nodes, links, and transactions).

On top of that, the authors argue that routing must not compromise users' privacy.

Ripple and Stellar base routing decisions based on the information stored in a public blockchain.
A number of obviously centralized approaches are mentioned as well, but these are not interesting (with a trusted third party we can do anything).
A more noteworthy proposal, which I haven't heard of before, is [Flare](https://bitfury.com/content/downloads/whitepaper_flare_an_approach_to_routing_in_lightning_network_7_7_2016.pdf) -- a 2016 BitFury-designed routing proposal for Lightning which [didn't eventually go into production](https://bitcoin.stackexchange.com/q/85356/31712) (though it does go into my reading list).

SilentWhispers, as the authors modestly note, is

> the most promising approach in regard to privacy

Let me just briefly remind you the key ideas:

* a number of well-known, highly-connected nodes called landmarks periodically run a breadth-first search and create a spanning tree over all nodes;
* payments are routed via a landmark;
* landmarks calculate path capacities using a multi-party computation, which conceals individual nodes and their link capacities;
* if the path capacity via the first landmark is low, the sender tries another one.

However, it suffers from a number of drawbacks:

1. the spanning tree has to  be re-computed once every epoch, including the parts which have not changed;
1. all paths go through a landmark, even if both the sender and the receiver are in the same sub-tree;
1. as all nodes along the path must send shares of their local link capacity to all landmarks, the number of messages grows quadratically;
1. the protocol doesn't handle concurrency.

SpeedyMurmurs aim to solve these problems by abandoning the landmark routing and using another approach called _embedding-based routing_.


# Embedding-based routing

What are embeddings, exactly?
I was looking for a definition (a sentence starting with "An embedding is...") but didn't find one.
Instead, the notion is introduced as follows:

> Embeddings rely on assigning coordinates to nodes <...> and having nodes forward packets based on the distances between coordinates known to that node and a destination coordinate.

For the proposed routing algorithm, _greedy_ embeddings are used.
Greedy embeddings assign coordinates based on the position of the node in the spanning tree.
Then a distance function is defined on pairs of coordinates with the following essential quality: for every (sender, destination) pair, the sender has a neighbor which is closer to the destination than the sender itself.
This means that a greedy algorithm -- forwarding a message to the neighbor which is the closest to the destination -- will always find a path (and not get stuck in a local minimum).

Consider prefix embeddings -- greedy embeddings where a coordinate of a node contains the coordinate of its parent as a prefix.
Imagine a binary tree with three levels.
The root gets an empty string as its coordinate.
The two nodes on the first level get "0" and "1".
Their children get, rather unsurprisingly, "00", "01", "10", and "11".
If we want to send a message from "00" to "11", the shortest path follows the tree up to the root and back to the leaf: 00 -- 0 -- "" -- 1 -- 11.
If the sender and the receiver are in the same sub-tree, we don't have to go all the way up to the root: 00 -- 0 -- 01.
There might also be "shortcuts" -- links between nodes which do not belong to the tree.
If a suitable shortcut exists, a routing algorithm should choose it.

On each step, the next node to forward the message to is chosen greedily among all neighbors (includes shortcuts).
The distance function is defined as d(u,v) = |u| + |v| - 2CPL(u,v), where |u| is the length of the path from u to the root (equivalently, the length of its coordinate vector), and CPL is the common prefix length of the two coordinates.
The formula basically says:

1. go from u to the root;
1. go from the root to v;
1. oh, you didn't actually have to traverse the common part of these paths (twice).

It's not the only way to construct embedding-based routing, but follows the general recipe:

1. construct a spanning tree;
1. assign a pre-defined coordinate to the root;
1. let each node derive its coordinate from its parent's coordinate;
1. define a suitable distance function between coordinates.

This is all well and good, but what about privacy?
In a routing scheme described above, every intermediary node knows where the message is going.
A privacy-preserving protocol called VOUTE comes to the rescue.

VOUTE uses _anonymous return addresses_, which allow intermediary nodes to choose the neighbor closest to the destination without revealing its coordinate.
Sounds somewhat like homomorphic encryption which supports distance calculation (or comparison, at least) on encrypted coordinates.
See the [VOUTE paper](https://arxiv.org/abs/1601.06119) (and [a shorter version](https://www.freehaven.net/anonbib/cache/roos16anonymous.pdf)) for more detail.

By the way, did you notice how we now talk about messages and not transactions?


# Friend-to-friend networks

Embedding-based routing was initially conceived for anonymous messaging in _friend-to-friend_ (F2F) networks.
In F2F, links are established and maintained consciously, relying on off-protocol trust relations.
This differs from P2P networks like BitTorrent, where a user connects to whichever peers provide the highest bandwidth (I don't care where I get my file from as long as the checksum is correct).
In anonymous messaging, maximizing bandwidth is not as important as not letting your data fall into untrusted hands.

F2F lies in between data-based (BitTorrent) and value-based (Lightning) P2P networks.
F2F links are more semantically charged compared to those in filesharing networks, but, contrary to PBT networks, are **undirected** and **unweighted**.
Undirected means that messages can be directly transmitted from Alice to Bob if and only if they can be directly transmitted from Bob to Alice.
Unweighted means that

>transmitting a message does not affect the ability of the link to transmit future messages

(I think this is a rather deep observation highlighting the crucial difference between digital representations of _information_ and _value_.)

In order to adapt routing algorithms from F2F to PBT, we have to account for two crucial features of credit links:

* asymmetry: links from Alice to Bob and from Bob to Alice generally differ in capacity;
* weights: all links are weighted, and weight changes must be accounted for during graph re-balancing.


# SpeedyMurmurs

SpeedyMurmurs adapt VOUTE -- a greedy embedding-based routing with anonymous return addresses for F2F messaging -- to the PBT model.
The protocol operates in weighted graph model and distinguishes between **unidirectional** and **bidirectional** links.
Alice and Bob are said to share a bidirectional link, if they share two links with positive weights in opposite directions.
I'm a bit skeptical on whether this model accurately reflects the reality of PCNs like Lightning, where the following four types of relationship between Alice and Bob have distinct qualities [[^1]]:

1. sharing _no_ channel;
1. sharing an open channel with all capacity _on one side_;
1. sharing a _pair_ of channels with their full capacities on opposite sides;
1. sharing a channel with non-zero capacity _on both sides_.

(Formalizing the properties of these states may also be interesting!)

[^1]: Cases 3 and 4, though they both enable payments in both directions, differ, at least, in that they require, respectively, two or one on-chain transactions to redeem the balances on layer-1.

We assume, as in SilentWhispers, that there is a set of well-connected and well-known nodes called **landmarks**.
Each landmark defines its own spanning tree.
For each tree, each nodes is assigned a coordinate based on its parent's coordinate.
A payment is split into random chunks, and each chunk is sent along the path within a different tree.

The protocol consists of three algorithms:

* _setRoutes_ creates spanning trees and assigns coordinates to nodes;
* _setCred_ reacts to a change in a link capacity;
* _routePay_ discovers a suitable path for the requested transaction.


## Setting the routes

The authors modify the VOUTE's tree creation algorithm by splitting it into two phases.
During the first phase, the original algorithm runs, considering only bidirectional links.
Then, if any nodes are left outside the tree, they "attach" to it with their unidirectional links.
Note that the algorithm described in the paper (Algorithm 1, page 7) assumes a central coordinator which maintains a queue of nodes not yet in the tree.
In a distributed scenario, the authors acknowledge,

> starting the second phase is tricky

The nodes that are not yet in the tree are not sure whether they should wait for an invitation from a node with a bidirectional link, or just be satisfied with a unidirectional one.
But the problem can be circumvented by choosing a proper timeout, after which a node assumes the second phase has started.

## Setting the credit

The key problem is how to make the network react to changes in link capacities.
The authors suggest the following algorithm.
The network reacts to one of the following events:

* a new unidirectional link: as one of the node is not yet part of the tree, it chooses the other as the parent;
* a now non-zero bidirectional link: if one of the nodes has only a unidirectional link to its current parent, it should choose the newly connected node with a bidirectional link as a new parent instead; this leads to the tree replacing unidirectional links with bidirectional ones whenever possible, leading to higher potential throughput;
* removed link: as one of the two nodes in question is a child of another, the child selects a new parent.

Every time a node changes its parent, all its neighbors are notified, they then also choose a new parent and a corresponding coordinate.

Note that setCred doesn't react to changes in capacities of existing links!

## Routing the payment

RoutePay discovers the path between a sender and a receiver capable of transferring the required amount.
To improve anonymity, the sender splits the payment in random chunks and sends them along paths in different trees.
This allows to avoid a costly multi-party computation of SilentWhispers and also gives a passive attacker less information on the lower band of the payment.

The routing accounts for weighted links but doesn't actually do much to optimize for this new model.
Routing fails if there is no neighbor with a coordinate closer to the receiver _and_ sufficient available credit.
There may be a path with sufficient credit which temporarily goes "the wrong way", but _greedy_ routing wouldn't consider it.
Can we make routing a bit less greedy to account for some combination of the qualities "being close to the destination" and "having sufficient capacity"?
Another open question worth investigating!

## Privacy analysis

The authors dedicate a separate sub-section to privacy guarantees of SpeedyMurmurs.
Most of the privacy properties follow from proofs in the VOUTE paper, but one assumption regarding value privacy looks suspicious:

> we say that a PBT network achieves value privacy, if the adversary cannot determine the value c <...>, if the adversary is not sitting in **any** of the involved routing paths.

A rather weak assumption, isn't it?
In credit networks with an external identity systems, where establishing Sybil nodes requires social engineering attacks at scale, this might be reasonable.
But for a PCN over an open blockchain, where a resourceful attacker can easily launch a well-connected, well funded node and route everyone's payments while spying on them...
This just doesn't seem right.
Note that the reason why the sender splits the value into random chunks supposed to be a countermeasure against this attack.
The authors note:

> when the adversary corrupts some of the paths <...> we cannot prevent the adversary from estimating c.

Indeed, the adversary at least learns the lower bound for the total value, and as the value is shared uniformly, and the number of landmarks is known, the total value may be estimated as L * c_i, where L is the number of landmarks.

# Evaluation

The authors identified multiple "axes" along which routing algorithms for P2P networks may differ:

* routing: landmark-based, greedy embedding-based, or "tree-only";
* stabilization method: periodic or on-demand;
* assignment of credit on paths: multi-party computation or random;
* landmark selection: highest degree or random;

and five metrics: fraction of successful transactions, delay, messages sent per transaction path length, path length, and messages related to stabilization per epoch.

SilentWhispers are landmark-based, with periodic stabilization, and multi-party computation.
SpeedyMurmurs are greedy embedding-based, with on-demand stabilization, and random credit assignment.
Both use highest-degree nodes as landmarks.

Using the GTNA graph analysis framework and the dataset of Ripple transactions, the authors compared multiple combinations of the parameters listed above.
Unsurprisingly, Ford-Fulkenson (a generic max-flow algorithm) "exhibited prohibitive delays".
More interestingly, SpeedyMurmurs performed better than SilentWhispers across all metrics in the "static scenario" (without stabilization).
In a dynamic scenario, during "normal operation", SpeedyMurmurs were also superior, but during "intervals of frequent change" the opposite was the case.
All algorithms except for Ford-Fulkenson showed success ratio substantially lower than 100%, none of them higher than 91%.

I fully agree with the authors in that

> users might not be willing to accept a failure rate of 10%

Lots of work lies ahead before we make payment networks appealing to the general public (or, more realistically, at least to developers who would create payment-network-based applications targeting the general public).


# Summary and questions

SpeedyMurmurs are an interesting proposal, but I can't get over the feeling that we can't just apply relatively minor tweaks to algorithms from _data transfer_ networks and apply them to _value transfer_.
We definitely can and should borrow ideas from existing research, but routing algorithms for P2P messaging may need more radical modifications to be useful in a PBT setting, especially in PCNs like Lightning and Raiden.

Another faucet of the same issue: most algorithms in the paper are introduced in a "centralized" manner (assuming there is a central coordinator who runs the protocol).
Then a paragraph follows, starting with something along the lines of: "the distributed version of the algorithm is basically the same, but nodes additionally do this and that".
I'm not convinced that patching centralized algorithms in such a manner does not introduce vulnerabilities or substantial inefficiencies.
I'd prefer algorithms to be introduced in a decentralized setting directly.

The same applies to the details of the graph model.
How well does modeling a bidirectional link as a pair of independent unidirectional ones reflect the reality (of networks being implemented in practice, as they are the most useful things to model)?
Do differences between stateless (Lightning, Bitcoin-style) vs stateful (Raiden, Ethereum-style) models play a role here?

Moreover, as I'm undoubtedly spoiled by the importance of the Bitcoin's incentive layer, every time I see a protocol description containing a phrase like "every node does X and notifies all its neighbors", certain questions immediately start popping up in my head:

* what if it doesn't?
* why would it want to?
* what if it does it but incorrectly?
* what if it sends equivocating messages?
* what if an attacker launches 100x more nodes than the network currently contains?

[Und so weiter, und so weiter](https://youtu.be/hiq1lturAao?t=91)...

Yet, in the end, isn't it always the same question -- and always the same answer?
