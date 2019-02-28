---
layout: post
title: Concurrency and privacy with payment-channel networks (paper summary)
published: true
---

Payment channel networks (PCNs) are a promising technology to overcome scalability challenges of open blockchains by moving the majority of transactions off-chain.
At the time of writing, the only PCN working in production on at least some scale is the Lightning network (LN) for Bitcoin.
I haven't yet written the planned second and other parts of the series on how LN works (see [part 1](/how-lightning-works-part-1) and stay tuned).
Today I present you the summary of a paper by Giulio Malavolta et al. entitled "[Concurrency and Privacy with Payment-Channel Networks](https://eprint.iacr.org/2017/820)" ([CCS 2017](https://ccs2017.sigsac.org/agenda.html)).
It describes and formalizes the privacy and security of PCNs, and propose two PCN constructions which improve over LN, exploring the inherent privacy vs concurrency trade-off.

# Background on PCNs

A PCN works as follows.
Alice and Bob lock some coins into a multisig address.
If they both agree, they can withdraw their shares according to the latest channel state (closing the channel).
If one party goes off-line, the remaining party can unilaterally close the channel after a timeout.
They can also negotiate and sign a new state reflecting a new distribution of the coins.
The key challenge is to invalidate the old state, such that neither Alice nor Bob could violate the agreement by closing the channel with outdated balances.

Lightning network solves this problem with hash-time-lock contracts (HTLC).
Coins in the channel can be withdrawn by Bob if he provides a preimage of a hash, or by Alice after a timeout.
The hash-lock part is what crucially enables atomicity in multi-hop payments.
For Charlie to get a payment from Alice, he generates a secret value and sends its hash to Alice.
Alice finds a route to Charlie, say, through Bob.
With HTLCs, either both parts of the payment happen (if Charlie reveals the secret preimage to claim the money from Bob, Bob uses the same preimage to claim the money from Alice), or neither do.


# A formal model for PCNs

In order to reason about PCNs and their properties, the authors introduce a formal model.
A PCN is represented by a directed graph with weighted edges and nodes.
The weight of a node represent the routing fee this node requires.
The weight of an edge represents the amount of coins which can move between the two edges (in one direction).
The model only considers unidirectional channels; the authors claim that "the proposed algorithms can be easily extended to support bidirectional channels".

The authors use the Universal composability (UC) framework to formalize the PCN protocol.
The key idea behind UC is to prove properties of a cryptographic protocol in three simple steps:

1. define the ideal functionality F -- a black box that magically does all that we prescribe it to;
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
Other users are only aware of the initial state of the channel, and assume it may route up to 5 BTC in each direction.
But in reality, a 5 BTC payment routed from Alice to Bob will fail, because there are only 3 BTC in the channel which can move in the direction from Alice to Bob.

Moreover, one can imagine a situation (illustrated in Figure 5 in the paper) where two payments are routed in opposite directions through a diamond-like set of channels, such that each of the payments individually could go through, but they both block each others' progress, a-la the [dining philosophers problem](https://en.wikipedia.org/wiki/Dining_philosophers_problem), and both fail after a timeout as a result.

Turns out, it's impossible to have concurrency and privacy at the same time.
In a PCN (as it is defined in the paper), "if we are providing non-blocking progress, [...] then it is impossible to provide serializability".

So, if a single construction cannot solve all our problems, the authors propose two.


# Fulgor: choose privacy

The first proposed construction -- Fulgor -- optimizes for privacy.
The key insight is the following: in the HTLC construction, let's get rid of the common secret preimage while maintaining atomicity.
This can be done by using _distinct but related_ hashlocks for each hop.

The _sender_ generates n secrets, for every hop in the route: _x1_, _x2_, etc.
Then, the last hop is hash-locked with _H(x\_n)_, the penultimate one uses _H(x\_n XOR x\_(n-1))_, and so on (for some cryptographic hash function H).
It _also_ provides all the intermediate nodes with zero-knowledge proofs that the values have been generated correctly.
The sender provides the recipient _and all intermediary nodes_ with their respective hashes.
The receiver gets both the final hash and the final preimage, and checks that the former is indeed the hash of the latter.
The preimage allows the receiver to pull funds from the (n-1)-th node.
The (n-1)-th node checks that the zero-knowledge proof is correct, and if that is the case, XORs the received preimage with what it got from the sender.
This allows the (n-1)-th node to pull funds from (n-2)-th node, and so on.
If at some point the zero-knowledge proof doesn't check out, the payment is aborted (this indicates that an intermediary is trying to cheat by pulling funds before the funds get pulled from it).

To put is shortly, the key difference between Fulgor and LN-style HTLCs is that Fulgor uses seemingly unrelated preimages along the path, with nodes proving the correctness of the construction to each other in zero knowledge.

Some substantial differences compared to the current LN implementations include the following:
* the receiver chooses the path (instead of the sender in LN);
* the sender generates random secrets (instead of the receiver in the LN);
* the hashed secrets are sent to _all_ intermediary nodes, not only between the sender and the receiver (which immediately raises the question of communication complexity).

# Rayo: choose concurrency

Rayo optimizes for concurrency: if two payments are blocking each other, one should succeed.
Intuitively, we need a way to resolve conflicts: how to choose between two conflicting payments?
The solution is as follows: suppose every payment has a unique identifier.
Than, in case of a conflict, just fulfill the payment with the smaller identifier!
Obviously, we this is where we break privacy again, re-introducing a single number to identify a multi-hop payment.
Moreover, can an adversary can "mine" payment identifiers by changing some non-essential data in the payment request until the identifier is small enough?..

# Performance analysis

I like papers where authors not only theorize but actually implement and measure their proposed construction.
In this paper, this is the case: using the API of the LND implementation of the Lightning network and a ZK-Boo library for zero-knowledge proofs, the authors implemented the construction described above and measured its performance.
For Fulgor, generating proofs takes around 3 seconds on Intel Core i7 with 2 GB RAM.
Overall,

> a payment with 10 intermediate users takes less than 5 seconds and require a communication overhead of 17 MB at each intermediate user.

Not negligible, I would say.

For Rayo, in additional to the overhead outlines above, the nodes must maintain a list of the current in-flight payments.
As per the authors,

>The management of these data structures requires a fairly small computation overhead.

Specifically,

> a payment involving a path of length _k_ incurs _O(ck)_ message complexity, where _c_ is bounded by the total number of concurrent payments.

In conclusion, the evaluation results demonstrate that

>it is feasible to deploy Fulgor and Rayo in practice and [they] can scale to cater a growing number of users.

Unfortunately, the code is not open-sourced (at least, I couldn't find it).


# Further reading: Anonymous multi-hop networks for blockchain scalability and interoperability

A [more recent paper](https://eprint.iacr.org/2018/472) ([NDSS 2019](https://www.ndss-symposium.org/ndss-program/ndss-symposium-2019-program/)) by the (nearly) identical team addresses another problem with PCNs.
I'll be brief here as I haven't dug [[^1]] deep into it yet.

The authors introduce the so-called "wormhole attack".
If one party controls two nodes which are on the same path, it can not only learn that fact, potentially violating someone's privacy.
The adversary can transfer the secret preimage off the protocol, making the node(s) in between think the payment was aborted.
In the meantime, the adversary gets more fees than initially planned.

[^1]: Digged? Diggen? Oh, English is hard.

The authors propose a construction to solve this problem -- anonymous multi-hop locks (AMHL). [[^2]]
Moreover, they also show how to implement scriptless scripts for PCNs for even better anonymity.
The key idea is that we can encode additional constraints in the signatures themselves, such that only the party in possession of some secret can generate them, but for an external observer these signatures are no different than any other.
In particular, it should be possible to open and close payment channels without the whole world knowing that this was indeed a channel, not a usual on-chain transfer.

[^2]: Which at first glance sounds kinda like the Fulgor construction described in the first paper. But at least it's more efficient now. Love how the first paper is mentioned in the Related work section of the second one: "Their approach, however, imposes an overhead of around 5 MB for the nodes in the network, thus hindering its deployability." Wait, I thought it was "feasible to deploy Fulgor and Rayo in practice"...

But that's a story for another time.

# My conclusion and questions for PCNs

Let me outline some questions which popped up in my mind while I was reading the papers.

* Is updating the model from unidirectional to bidirectional channels really trivial? I'm a bit skeptical as there is a crucial distinction between a bidirectional link and a pair of independent unidirectional links.
* Can we combine Fulgor and Rayo in one PCN so that the users could chose concurrency or privacy depending on their needs?
* What are the trade-offs in channel monitoring? Can we define an analogous model and explore the trade-off, say, between timeout and performance (long timeouts allow "slow" users to react but degrade the overall network throughput)?
* Routing in the proposed model, just as in LN, assumes all nodes have knowledge of the global topology, which obviously doesn't scale. Which scaling approaches can we employ here? Can Tor teach us valuable lessons? Or maybe there is something to learn from Ripple and credit networks? Will LN (and potentially other PCNs) have its "block size debate" along the lines of "store full network graph and route independently" vs "store only a subgraph and delegate parts of routing to semi-trusted parties"? A related issue: even with the global view of the topology, both LN and Fulgor / Rayo assume knowledge of the initial channel state only. Can it also be a problem?
* In the model, the fee seems to be the inherent property of the node, there is no mechanism for fee estimation. In the real world systems, what would fee dynamics look like? I remember the discussions in early 2018, after Bitcoin fees had hit the historic peak of $50, that many wallets used poor fee estimation and made their users drastically overpay (or, on the other extreme, underpay and have their transactions never confirmed). How would PCN nodes estimate fees? By the way, the claim in the Fulgor / Rayo paper that "the transaction used to open a payment channel can contain user-defined data so that each user can embed their own payment fee" doesn't look realistic: the reference links to OP_RETURN documentation, and OP_RETURN is an opcode used to embed data into a Bitcoin output while _making it provably unspendable_, which is definitely not what we want from a channel opening transaction, if the parties want to close it eventually.
* PCNs as defined in the papers and implemented in LN are decentralized and symmetric, while real-world payments are not. The vast majority of people receive a relatively large payment once a month from their employer and constantly spend the money in multiple smaller payments. Can PCNs optimize for this real-world scenario? Will it lead to centralization?

And here is the conclusion: PCNs are cool. Study PCNs.

