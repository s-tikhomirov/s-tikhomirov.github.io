---
layout: post
title: SilentWhispers&#58; routing in credit networks
published: true
---

As I stated [previously](/concurrency-privacy-payment-channel-networks/), payment channel networks (PCNs) are cool.
One of their distinguishing features, which separates them from the underlying layer-1 blockchains, is the importance of routing.
A sender of a layer-1 transaction only assumes that that a  miner will receive it in a reasonable amount of time.
Any transaction with a sufficient fee will be confirmed.
Its random route through the gossip network is not important.

The semantics of routing in PCNs is more complex.
For a payment to reach the recipient, a series of consequent channels must be atomically rebalanced.
How does the sender (or the receiver, or anyone, really) find a suitable path to route the payment?

I'm planning to direct my research towards PCNs, mainly Lightning (but also Raiden and others).
I'm currently doing some background reading to familiarize myself with the state of the art in the area.
In this post, I summarize and discuss a paper called "[SilentWhispers: Enforcing security and privacy in decentralized credit networks](https://eprint.iacr.org/2016/1054)" by Giulio Malavolta et al.


# Introduction to credit networks

The paper starts, unsurprisingly, with the problem definition.
The authors mention that credit networks that "model transitive trust (or credit) between users" have seen an increase in popularity, with Ripple and Stellar as the primary examples.
Nevertheless, their architecture raises privacy concerns, as all information (both the topology and the link values) is public and stored in a ledger (aka blockchain).
The authors aim at overcoming the issue by designing a privacy preserving routing algorithm for credit networks.

Let's stress this crucial point: a credit network is just a way to formalize a credit-like relationships between users.
This relationship doesn't even have to be strictly monetary: the related work section contains references to applications of credit networks to reputation systems and spam filters.[[^1]]
Bitcoin, on the other hand, doesn't simply _record_ your entitlement to something valuable -- it _is_ the ~~danger~~ valuable thing itself.
The Satoshi's key insight -- using proof-of-work to make modifying the ledger difficult -- allowed him to create the first digital _bearer asset_.
I won't deny the fact that I'm biased towards trustless systems and suspicious of everything that involves trust ("don't trust -- verify").
But ignoring existing research on not-so-decentralized networks is counterproductive.
There is a high chance it contains clever ideas which, perhaps after some tweaking, can be applied to decentralized systems.
So let's dive in.

[^1]: Turns out, many interesting ideas were thought to be applicable to combating spam -- including Adam Back's hashcash, one of the precursors to Bitcoin -- until Google came along and trained its machine learning on everybody's email content.

So let's separate two issues: how to _suggest_ a path and how to _enforce_ a payment along it (or "guarantee atomicity", or "solve double-spending" -- these are all basically re-formulations of the same problem).
For a centralized system, conflicts are resolved by a central server or multiple servers running some BFT-style consensus.
Lightning relies on Bitcoin for conflict resolution.
Initially, I expected the authors to just focus on routing.
But they in fact aim at something bigger (emphasis mine):

> we present SilentWhispers, the first distributed, privacy-preserving credit network that does not require any ledger to **protect the integrity of transactions** <...> even in the presence of distrustful users and **malicious neighbors**, whose misbehavior <...> is detected and such users can be **held accountable**.

This does look like as an attempt to also propose an enforcement mechanism without a common ledger.
Which to me sounds a bit like an oxymoron: either someone keeps track of who owns how much to whom (a centralized model), or everybody does this (an open blockchain).
Do credit networks and SilentWhispers in particular offer a third way? [[^2]]

[^2]: There is a third way -- using zero-knowledge proofs to guarantee the correctness of all state transitions without revealing the state itself, a-la [Coda](https://codaprotocol.com/), but that's a story for another time.

Before going further, I'll just mention a somewhat peculiar line of reasoning from the introduction:

> Bitcoin <...> is limited to transactions where both transacting parties agree on a common currency. Credit networks, instead, smoothly enable cross-currency transactions in any user specified currency (including Bitcoin)

Bitcoin is "single-currency" because it actually enforces things (and can only control its internal value units -- bitcoins).
For transactions in other units of account, parties can simply agree on the exchange rate out-of-band.
In credit networks, while you can extend a Bitcoin-denominated credit line to me, I can just default on my debt.
Of course, my reputation would be ruined, but I won't get shot for this, rather, as Brad Pitt's character said in "Inglourious Basterds", "[chewed out](https://youtu.be/ggiUtXIVp8g?t=70)".



# The routing problem

Graphs are a natural instrument to formalize the routing problem.
Let nodes be users of the networks, and let edges be credit relationships between them.
A link between Alice and Bob with a weight of 3 units means that Alice can transfer 3 more units towards Bob.
If there is no direct link between Alice and Bob, they initiate an atomic re-balancing of a chain of somebody else's links to make a payment.
The problem is to find a suitable route for that.

Generic algorithms for calculating the maximum flow through a graph with directed weighted edges scale poorly.
To solve this problem, we embrace a trade-off.
Instead of finding the guaranteed best answer, we aim at an approximate solution, which is good enough for most cases, but is much more efficient.
This resembles Bitcoin's breakthrough approach to distributed consensus: guarantee only probabilistic finality to allow open membership.

In credit networks, we make a compromise by using a two-tier system.
Some nodes -- called **landmarks** -- implement special functions and are assumed to be well-known to all participants.
Landmark routing works as follows: each landmark runs a breadth-first search (BFS), creating a spanning tree for the network graph.
Actually, it runs the algorithm twice: for incoming and outgoing links.
This results in every user knowing their parent in the path to and from a landmark.
The path for a payment is then composed as follows: sender -- ... -- landmark -- ... -- receiver.

There are at least two problems with this approach.
First, it finds the "shortest" path by the number of hops, without taking capacity into account.
This may lead to payments failing if the value of even a single link along the "shortest" path is too low (in that case, we should try a path through another landmark).
Second, all payments are routed through landmarks, even if both the sender and the recipient are in the same sub-tree.
Third, we must trust the landmarks to route all payments and not violate users' privacy.

The authors formulate the properties a credit network should have:

* integrity (aka atomicity)
* serializability: for each concurrent execution there exists a sequential execution with the same effect (why is this important, by the way?)
* accountability (double spend attempts are detected)
* value / link privacy (an adversary can't determine the transaction value / link capacity between honest users)
* sender / receiver privacy (an adversary can't determine the sender / receiver in a transaction between honest users)

How do SilentWhispers achieve this?


# SilentWhispers

First of all, let's list our assumptions:

* the time moves in epochs (which implies that users have roughly synchronized clocks);
* each node has an identity;
* the identities of landmarks are "well-known" to everybody.

## Computing credit along a path

So how do we compute the minimum capacity across all links in the path?
A simple approach would go like this: every node in the path sends its link capacity to the landmark.
The landmark then calculates the minimum and notifies the sender whether the payment is possible.
This obviously violates privacy.
A slightly more sophisticated approach is to compute the minimum step-by-step.
Let's say, we have a path Alice -- Bob -- Charlie -- Dave -- ... .
Bob already knows what the available capacity (AB) between him and Alice is.
He communicates (AB) to Charlie.
Charlie calculates the minimum of (AB) and (BC) and communicates that to Dave, and so on.
But, as the authors node,

> It is easy to see, however, that such a protocol leaks all the intermediate values.

But there is a better idea.

## Secret sharing and multi-party computation

SilentWhispers use a multi-party computation to compute the available credit for a given path.
The landmarks play the role of the computation parties.
Each user (except for the sender?) along the path creates a _share_ of the link's value.[[^4]]
The number of shares is equal to the number of landmarks.
After receiving all the shares, the landmarks run a multi-party computation (of the _min()_ function) and obtain a _share_ of the result.
Each landmark knows a _share_ of the result but not the actual value, which can only be re-constructed by combining a (parameterized) subset of the shares.
Each landmark then sends its share to the sender[[^5]], who re-constructs the value and sees what capacity the path actually has.

[^4]: Shamir secret sharing is an algorithm to divide a value into n shares, such that any k of them allow to re-construct the value, but any combination of fewer than k shares reveals no information about the secret. How is it even possible? Think planes in a 3D space: 3 non-parallel planes intersect at a single point; any 2 of them intersect at an infinite number of points. Alternatively, think polynomials and what defines the number of their roots.

[^5]: By the way, what happens if landmarks collude and compute the value by themselves as well?

But how do the landmarks make sure that the share they receive do come from nodes along the same proposed path?
One could just sign the messages with long-term keys (as long as we have an implicit identity systems), but that would reveal the members of the path.
Instead, SilentWhispers use chained signatures: each node generates one-time keys for each payment and shares them with the neighbors only.
Looking from the other side, upon receiving fresh keys from two neighbors, a node signs the tuple, adding its own fresh key.
If the consecutive signatures along the path match up, everything is OK.

There is only one issue left to address...

## Dispute resolution

What if Alice and Bob disagree on the current value of the link between them?

> The two end-points establish the current value of the link by signing it <...> both users log the transaction and sign the new link value. All signatures <...> contain a timestamp to avoid rollback attacks. By inspecting these signatures, a judge can determine the correct value of the link.

A judge?!
Ah, I forgot, we are in a credit network with external enforcement, so appealing to a judge does make sense here.

But what if nodes lie about timestamps?
Who defines the _true_ timestamp anyway?

The paper basically says: given a trusted timestamping service, we can order transactions.
_Of course_ we can, but the system becomes blatantly centralized!
This _is_ the exact problem Bitcoin solves.
Open-source archaeologists even [point out](https://twitter.com/francispouliot_/status/1106028853032611843) that Satoshi used another term for what has since become knows as "blockchain" -- "**time**chain".
Let me just cite the first page of the [Holy Scripture](https://bitcoin.org/bitcoin.pdf) (emphasis mine):

>  we propose a solution to the double-spending problem using a peer-to-peer distributed **timestamp server** to generate computational proof of the chronological order of transactions

Sorry, but claiming that a credit network "does not require any ledger to protect the integrity of transactions" while assuming the presence of a trusted timestamping service is... a bit misleading.
If we trust an external entity with timestamps, why just not let it run the whole protocol?


# Universal composability and Evaluation

I won't go into the UC framework and the proofs that the implementation does indeed correspond to the ideal functionality, given assumptions on the underlying primitives.
I'll briefly mention, however, the results of the evaluation of the actual implementation of the protocol.
(I applaud authors who not only suggest protocols but actually implement and measure them!)

The implementation was tested based on the transaction set of Ripple until December 2015.
In the case with 7 landmarks and paths of length 10, where all landmarks are honest (but curious), computing a minimum credit along a path takes 1.3 seconds, which seems reasonable (though not lightning fast, pun intended).
If we consider malicious landmarks, we have to employ another type of multi-party computation which can tolerate that at a rather high cost: with 3 out of 7 landmarks corrupted, the computation of the minimum credit in a path takes 86 (!) seconds.
Nevertheless,

> the extension to malicious landmarks is <...> not worth implementing in practice since landmarks have no incentive in misbehaving as discussed in Section III-A.

The aforementioned section defined the security model and states that, in the case of misbehaving,

> landmarks would lose customers and (possibly) go out of business.

It's fascinating to see how many if not all security problems in decentralized systems eventually boil down to identity.
Note that the above incentive works only if a landmark can't create multiple identities out of thin air.
Otherwise, it can "burn" its reputation until it's profitable and then start from scratch with a "clean" one.
What prevents actors in real systems from doing so?
There are only two ways to make identity creation hard:

* a central entity _defines_ what counts as an identity (permissioned "blockchains", IP addresses, bank accounts): you have to be approved to take part in the system, and the system administrator limits the number of identities per one person or company;
* creating an identity is _costly_ (proof-of-work or _maybe_ some flavor of proof-of-stake).

I can't prove there is no third option, but I don't currently see any.
In my view, reputation without proof-of-work is only possible in a centralized system, as someone has to manage identities.


# Lessons for decentralized PCNs

Can any of the above be useful for decentralized payment channel networks?

In Lightning, no matter what people in certain regions of crypto-Twitter say, there are no _protocol-defined_ landmarks (aka hubs).
But high-value nodes seem to naturally appear in peer-to-peer networks.
Indeed, it would be strange to expect all participants of an open system to engage with it with the same intensity, even given equal initial conditions.
In BitTorrent, there are semi-professional seeders with terabyte hard drives running 24/7, and occasional leechers with no upload bandwidth.
On forums, 90% of users [lurk](https://en.wikipedia.org/wiki/Lurker), 9% comment, and only 1% start new topics (the [90-9-1 rule](https://en.wikipedia.org/wiki/1%25_rule_(Internet_culture))).
A similar pattern is emerging in Lightning: anyone can set up a node, and there is no obligation to maintain a permanent identity.
But professional players (who have reputation to capitalize on) willingly maintain a permanent identity and accumulate reputation by providing high quality service (e.g., [ACINQ](https://acinq.co/node)).

Does this lead to centralization?
Maybe, but as long as this is inevitable, we (the researchers who value decentralization) should embrace it and try to come up with ways to improve decentralized systems so that they provide high quality service while discouraging monopolies.

Long story short, the ideas related to landmark routing, possibly with additional cryptography such as MPC for privacy protection, may indeed be helpful for improving Lightning routing.

Stay tuned for the summary and discussion on [SpeedyMurmurs](https://arxiv.org/abs/1709.05748) -- a proposal which improves upon SilentWhispers along multiple dimensions, such as splitting payment values into random chunks for better privacy protection and using what is known as embedding-based routing for finding shorter paths compared to landmark routing.