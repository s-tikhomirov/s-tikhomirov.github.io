---
layout: post
title: Israel winter school on blockchain
published: true
---

In December 2018, I attended a [Winter school on blockchain and cryptocurrencies](http://ias.huji.ac.il/CSE3) in the Hebrew university of Jerusalem.
This was one of the best events of this kind I have ever attended.
The level of talks was consistently high, and the coffee break discussions were tremendously insightful.
I've been making lots of notes during the lectures, and dedicated a whole [episode of my Russian-language podcast](https://basicblockradio.libsyn.com/-054-scalability-privacy-distributed-systems) to summarizing them.

In this post, I outline some of the ideas presented in the lectures, as well as my own thoughts I've been pondering during and after the event.


# Blockchains and traditional distributed systems

Blockchains and traditional distributed systems are deeply connected.
This may not come as a surprise: after all, aren't blockchains a subset of distributed systems (DS)?
DS is an established scientific field with lots of results (including impossibility results!) obtained during the last four decades.
Nevertheless, people in the blockchain space are trying to re-invent the wheel far too often.

The blockchain universe can be viewed as an "evil twin" of DS, with traditional properties weirdly mutated.
Despite the fact that blockchains operate under different assumptions (open participation, game-theoretic security), the underlying networking remains similar.
In particular, the notions of asynchrony (messages delayed by any finite amount), synchrony (messages delayed by up to delta), and partial synchrony (asynchronous initially, synchronous after the global stabilization time) are still applicable.
Ittai Abraham provides an overview:

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/N_3r-NkBUTk" frameborder="0" allow="encrypted-media" allowfullscreen></iframe>

The famous [ACID properties](https://en.wikipedia.org/wiki/ACID_(computer_science)) are also revisited in the blockchain world (Maurice Herlihy):

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/23xYS0rlrkc" frameborder="0" allow="encrypted-media" allowfullscreen></iframe>

If you want to develop a novel blockchain consensus by merging Nakamoto and traditional BFT, you must be familiar with decades of DS research.
The questions you should ask yourself:

* Does your protocol solve some variant of BFT? If so, why not use an existing one?
* Do you describe it in standard DS terms for easier peer-review?
* How many failures can it stand? Which types of failures? Do you prove security properties based on rigorously defined assumptions, or just do some "if-bad-guys-do-this-we-do-that" hand-waving?
* How do you evaluate your implementation? Do you measure TPS between two VMs on the same server, or between geographically distributed nodes? Do you transmit actual transactions, or dummy 1-byte objects? Do you account for cryptographic operations (transaction validation)? Do you write on disk?

Thinking about traditional DS vs blockchains made me even more pessimistic regarding proof-of-stake, but I'll leave my skepticism for another time.


# Reasoning about atomic swaps with graph theory

Alice has Litecoin, Bob has Bitcoin, Carol has a pink Cadillac, and they want to atomically trade in a circular fashion.
It is possible with hash-locked contracts, but be careful: what if a malicious party aborts?
The protocol must be designed in such a way that no honest party is worse off in the end.
Interestingly, with a little help from graph theory, we can understand when a complex multi-party atomic swap is possible.

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/CRPNkPpbNwA" frameborder="0" allow="encrypted-media" allowfullscreen></iframe>

Spoiler: the directed graph of transactions must be strongly connected.


# Avalanche: I'm still not convinced

Emin Gün Sirer gave a talk on Avalanche (no recording from the school, but here is a similar one):

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/AXrrqtFlGow" frameborder="0" allow="encrypted-media" allowfullscreen></iframe>

As soon as I hear "we'll just poll a subset of nodes", important questions arise:

1. How is the identities of the peers maintained?
1. How are Sybil attacks prevented?

I won't pretend I fully understood the [original Avalanche paper](https://ipfs.io/ipfs/QmUy4jh5mGNZvLkjies1RWM4YuvJh5o2FYopNPVYwrRVGV), but replying to my question Gün said something along the lines of "you can use anything for Sybil protection, even proof-of-work, but we're going to use proof-of-stake".
For me, it sounds like sweeping the problems under the rug and defeating straw-man arguments instead.
Of course, if we have a reliable identity system with Sybil protection, there are established ways to achieve consensus.


# Academia and industry: commercializing research

Israel is known for having a disproportionally large and developed entrepreneurial ecosystem (read the "[Start-up nation](https://www.goodreads.com/book/show/6885191-start-up-nation)" to understand how that came to be).

An interesting event (unfortunately not recorded) entitled "Academia & Blockchain; Researchers Becoming Founders" took place in a local WeWork location and featured Eli Ben-Sasson (Technion, [Zcash](https://z.cash/), [Starkware](https://www.starkware.co/)), Aviv Zohar (Hebrew university, [QED-it](https://qed-it.com/)), Emin Gün Sirer (Cornell, AVA coin), Tal Moran ([Spacemesh](https://spacemesh.io/)).

Some insights (not exact quotes, rather thoughts inspired by what I heard):

* Blockchains are all open-source: don't use the traditional means of protecting IP, capitalize on expertise instead.
* University is not an adversary; may try to claim the IP of employers, but "position is weak" (I suspect this is very country-specific, lawyer's advice required).
* No easy way to do due diligence in the blockchain space; assume a priori that all whitepapers are broken.
* Academic skills applicable in a career as an entrepreneur: clear thinking, "cleaning away unnecessary stuff", explaining complex subjects (to investors, users).
* Skills to being back to academia from the industry: dealing with people, project management, evaluating results.
* Being honest about your weaknesses (also with investors) is good in the long run.
* Running a company implies a longer time horizon (compare a bug in the code for a paper to a bug in a production system).
* A CEO has tons of various concerns (legal, hiring, etc), can't freely float in the "world of ideas".
* Attract academic talent by offering something beyond money, such as writing papers, developing new exciting tech.
* Main reasons why blockchains are not popular yet: regulatory concerns, bad usability. (But also, lack of clear use case?)

Another industrial panel discussion.
The perspectives of Sizhao Yang (Daglabs) seemed the most valuable for me.

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/MmvbpssurZE" frameborder="0" allow="encrypted-media" allowfullscreen></iframe>


# Layer one scaling via DAGs

It's fascinating to see how blockchain scaling fundamentally boils down to physical constants (the speed of light and the size of our planet).
The block interval in Bitcoin (10 minutes) was chosen to be large enough so that validation times are negligible in comparison.
Alternative blockchains showed that it is possible to mechanically reduce this number to 2.5 minutes (Litecoin) or maybe even 1 minute, but we can't get a order of magnitude improvement here.
Maybe a more generalized data structure can help?

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/C8-DPmWXYns" frameborder="0" allow="encrypted-media" allowfullscreen></iframe>

This is all well and good, but the key question is: even if L1 scaling brings us 10-100x improvement, is it worth it?
It's unreasonable to expect Bitcoin to integrate any non-trivial modifications in its fundamental data structure.
Ethereum already uses a modification of the GHOST protocol, but still struggles to scale.
A brand new blockchain, even with 100x more on-chain volume, will unlikely gain traction comparable with the aforementioned top-2.

100x Bitcoin throughput will not enable billions of people to pay for their daily purchases anyway.
On the other hand, L2 solutions promise many orders of magnitude more transactions by moving them off-chain.
DAGs may be interesting academically, but what are their real-world prospects?..


# Concurrency in smart contracts

Miners searching for a block (in a stateful blockchain with smart contracts, read Ethereum) are free to execute transaction in any valid order.
Validators, on the other hand, must replay transactions on a block in exactly the order chosen by its miner.
What if make the miners include into a block header a "schedule", telling the validators with transactions are not conflicting and thus may be replayed in parallel?

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/yrlL--mvaHw" frameborder="0" allow="encrypted-media" allowfullscreen></iframe>

A meta-question again: stateful computation on-chain is really complex; under what conditions do the extra capabilities justify the complexity?



# More cool topics

[Zero-knowledge](https://www.youtube.com/watch?v=YW1_5_pxg-Y), [network-level attacks (BGP etc)](https://www.youtube.com/watch?v=TbDgpYDPSbo), [Bitcoin-NG](https://www.youtube.com/watch?v=TiD36gQM4qQ), [selfish mining](https://www.youtube.com/watch?v=1gUX9ikG1FI), [the DAO](https://www.youtube.com/watch?v=DZRzv6u1uGo)...
The [recordings of all lectures](https://www.youtube.com/playlist?list=PLTn74Qx5mPsR6JMF5F6vYSGQgDabTZaaD) are available, watch them!

Many thanks to the organizers, looking forward for insightful events like this in 2019 and beyond.