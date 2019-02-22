---
layout: post
title: Concurrency and Privacy with Payment-Channel Networks (paper summary)
published: false
---

Payment channels are a promising technology to overcome scalability challenges of open blockchains by moving the majority of transactions off-chain.
The most promising (and, at the time of writing, the only one working in production on at least some scale) is the Lightning network (LN) for Bitcoin.
I haven't yet written the planned second and other parts of the series on how LN works (see part 1 and stay tuned).
Today I present you a summary of a paper by Giulio Malavolta et al. entitled "[Concurrency and Privacy with Payment-Channel Networks](https://eprint.iacr.org/2017/820)" ([CCS 2017](https://ccs2017.sigsac.org/agenda.html)).
It describes and formalizes the privacy and security of payment channel networks (PCNs), and propose two PCN constructions which improve over LN, exploring the inherent privacy vs concurrency trade-off.

# Background on PCNs

A payment channel network works as follows.
Alice and Bob lock some coins into an address they both control via multisig.
If they both agree, they can withdraw their shares according to the latest channel state (closing the channel).
If one party goes off-line, the remaining party can unilaterally close the channel after a timemout.
They then also negotiate and sign a new state reflecting a new distribution of the coins.
The key challenge is to invalidate the old state, such that neither Alice nor Bob could violate the agreement by closing the channel with earlier balances.

Lightning network solves this problem with hash-time-lock contracts (HTLC).
Coins in the channel can be withdrawn by Bob if he provides a preimage of a hash, or by Alice after a timeout.
The hash-lock part is what crucially enables atomicity in multi-hop payments.
For Charlie to get a payment from Alice, he generates a secret value and sends its hash to Alice.
Alice finds a route to Charlie, say, through Bob.
With HTLCs, either both parts of the payment happen (if Charlie reveals the secret preimage to claim the money from Bob, Bob uses the same preimage to claim the money from Alice), or neither do.


# A formal model for PCNs

I order to reason about PCNs and their properties, the authors introduce a formal model.
A PCN is represented by a directed graph with weighted edges and nodes.
The weight of a node represent the routing fee this node requires.
The weight of an edge represents the amount of coins which can move between the two edges (in one direction).
The model only considers unidirectional channels; the authors claim that "the proposed algorithms can be easily extended to support bidirectional channels" but this statement seems somewhat optimistic to me: there is a crucial distinction between a bidirectional link and a pair of independent unidirectional links.

The authors use the Universal composability (UC) framework to formalize the PCN protocol.
The key idea behind UC is to prove properties of a cryptographic protocol in three simple steps:

1. define the an ideal functionality F -- a trusted black box that magically does all that we prescribe it to;
1. prove that F has the properties we are interested in (usually related to security or privacy);
1. show that a real-world execution, under given cryptographic assumptions, is computationally indistinguishable from F.

The authors define the ideal functionality that the PCN must provide.
Nodes can open channels (_openChannel_), close channels (_closeChannel_), and _pay_ through a chain of existing channels, if their capacity permits.
The required properties are defined:

* balance security: honest parties must not lose money;
* serializability: for every concurrent execution of _pay_ operations there exists an equivalent sequential execution;
* value privacy: corrupted nodes outside the path get no information about the payment;
* relationship anonymity: corrupted intermediaries can not distinguish between two simultaneous _pay_ operations with at least one honest node along the path.

The PCN, as defined, has all these properties, and the real-world execution (as proven in the Appendix) is indistinguishable from F.


# Choosing between concurrency and privacy

The authors identify the two key challenges which stem from the HTLC construction described above.
First, the parts of one payment are trivially linkable.
Consider a path: Alice -- Bob -- Charlie -- Dave.
Alice and Dave are benign users, while Bob and Charlie are colluding.
If Bob and Charlie see the same hash value in payments being routed through them, they are sure these are parts of the same payment.

The second problem is concurrency.
For a payment to be routed through a payment channel, the channel must have enough capacity _in this direction_.
Say, Alice and Bob open a channel and initially contribute 5 BTC each (#reckless!).
Then, Alice pays 2 BTC to Bob, and the new balances are: Alice -- 3, Bob -- 7.
Other users are only aware of the initial state of the channel, and assume it may route up  to 5 BTC in each direction.
But in reality, a 5 BTC payment routed from Alice to Bob will fail, because there are  only 3 BTC in the channel which can move in the direction from Alice to Bob.

Moreover, one can imagine a situation (illustrated in Figure 5 in the paper) where two payments are routed in opposite directions through a diamond-like set of channels, such that each of the payments individually could go through, but they both block each others' progress, a-la the [dining philosophers problem](https://en.wikipedia.org/wiki/Dining_philosophers_problem), and both fail after a timeout as a result.

Turns out, it's impossible to have concurrency and privacy at the same time.
In a PCN (as it is defined in the paper), "if we are providing non-blocking progress, [...] then it is impossible to provide serializability".
Consequently, if no one construction can solve all our problems, the authors propose two.


# Fulgor: chose privacy

The first proposed construction -- Fulgor -- optimizes for privacy.
The key insight is the following: in the HTLC construction, let's get rid of the common secret preimage while maintaining atomicity.
This can be done by using distinct but related hashlocks for each hop.

The _sender_ generates n secrets, for every hop in the route: _x1_, _x2_, etc.
Then, the last hop is hash-locked with _H(xn)_, the penultimate one uses _H(xn XOR x(n-1))_, and so on (for some cryptographic hash function H).
The sender provides the recipient _and all intermediary nodes_ with their respective hashes.

[TODO: figure out]

This differs substantially from the current LN implementations:
* the receiver chooses the path (instead of the sender in LN);
* the sender generates random secrets (instead of the receiver in the LN);
* the hashed secrets are sent to all intermediary nodes, not only between the sender and the receiver (which immediately raises question of communication complexity).

# Rayo: chose concurrency


# Bonus track: Anonymous multi-hop networks for blockchain scalability and interoperability


# My conclusion

# Questions for PCNs

Let me outline some questions which popped up in my mind while I was reading the papers.

* To which extent is what the authors propose compatible with / applicable to the Lightning network? I've been realizing lately that for me to get excited about a design requires it not only to be elegant and actually solving the problem it is trying to solve, but to be realistically deployable and potentially widely used in the real world. Can we actually build Fulgor or Rayo? (The paper describes benchmarking but I couldn't find a link to  any open-sourced code...)
* The authors have done some work on credit networks and Ripple in particular. How are they different from PCNs like LN? Which lessons can we draw from that area?
* Is updating the model from unidirectional to bidirectional channels so trivial as the authors try to make it seem?
* Routing in the proposed model, just as in LN, assumes all nodes have knowledge of the global topology, which obviously doesn't scale. Which scaling approaches can we employ here? Can Tor teach us valuable lessons? Will LN (and potentially other PCNs) have its "block size debate" along the lines of "store full network graph and route independently" vs "store only a subgraph and delegate parts of routing to semi-trusted parties, a-la Bitcoin light nodes"? A related issue: even with the global view of the topology, both LN and Fulgor / Rayo model assume knowledge of the initial channel state only. Can it also be a problem?
* Definition 1 describes a PCN as a graph where nodes are "Bitcoin accounts". But there is no such thing as an account in Bitcoin, only release scripts (or hashes thereof). Can we leverage P2SH / P2WKH fro channel, maybe encode some channel-management logic in Bitcoin script?
* In the model, the fee seems to be the inherent property of the node, there is no mechanism for fee estimation. In  real world systems, what would fee dynamics look like? I remember discussions in early 2018, after Bitcoin fees hit the historic peak of $50, that many wallets used poor fee estimation and made their users drastically overpay (or, on the other extreme, underpay and have their transactions never confirmed). How would PCN nodes estimate fees? By the way, the claim in the paper that "the transaction used to open a payment channel can contain user-defined data so that each user can embed their own payment fee" doesn't look realistic: the reference leads to OP_RETURN documentation, and OP_RETURN is an opcode used to embed data into a Bitcoin output while _making it provably unspendable_, which is definitely not what we want from a channel opening transaction, if the parties want to close it eventually.
* PCNs ad defined in these papers and implemented in LN are inherently decentralized and symmetric, while real-world payments are not. I receive a relatively large payment once a month from my employer and constantly spend the money in multiple smaller payments. Can PCNs optimize for this real-world scenario? Will it lead to centralization?
* Can we combine Fulgor and Rayo in one PCN so that the users could chose concurrency or privacy depending on their needs?
* After Definition 2 "Lower bound": "all results and claims in this paper assume that in any PCN execution, there does not exist a channel in which both its users are byzantine" -- how realistic is this assumption?
* What are the trade-offs in channel monitoring? Can we define an analogous model and explore the trade-off, say, between timeout and performance (long timeouts allow "slow" users to react but degrade the overall network throughput)?








