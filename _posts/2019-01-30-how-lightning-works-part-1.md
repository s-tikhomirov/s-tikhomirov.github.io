---
layout: post
title: How the Lightning network works - Part 1
published: false
---

CHANGE PUBLICHED TO FALSE TO PUSH AS DRAFT

This the first pois in a series about the Lightning network.

I am definitely not the first person to do a write-up on how this cool new tech works.
I'm doing it anyway, with the main purpose being to summarize my own understanding.
Hope it may be helpful for you as well.


# Why payment channels

I'm targeting an advanced audience here, so I won't start with "on October 31st, 2008, an anonymous character under a pseudonym Satochi Nakamoto ...".
I assume general knowledge of what Bitcoin is, how it works, and why is it important.

Now, if you haven't been living under a rock since 2015, you've heard about Bitcoin's scalability problems.
Obviously, if we require all (full) nodes to process all transactions, the network doesn't get any faster by adding more nodes (contrary to, say, BitTorrent).
We can tweak some parameters like the block size limit and win 10--100x (though it's not worth the effort, in my opinion).
We can use a DAG-like data structure instead of a linear chain along with clever rules replacing the heaviest chain with the heaviest sub-tree, and squeeze maybe another 10x.

But it is not enough for a global payment system!
Bitcoin is often compared to Visa which does tens of thousands transactions per second (Bitcoin does three).
But Visa is not the limit!
We want autonomous cars paying to each other for traffic data, delivery drones charging customers for every second of their flight, online magazines publishing articles which cost 2 cents each to read...
Bitcoin is a natively digital, inherently neutral payment system.
How do we scale it a million x?

There is no free lunch: to achieve our ambitious goal, we have to make trade-offs.
I think, payment channels is the best way to do so.
Basically, payment channels let us buy scalable payments by giving up one of the (less important) Bitcoin's features -- non-interactivity.
I can publish my Bitcoin address on my website and go offline for a year, and people will be able to give me money regardless.
(You say, it violates the "no address reuse" security advice? Then I'll write a script which will deterministically derive new addresses for every payment from my root public key.)
In payment channels, the payer and the payee must constantly interact, and going offline for longer than a pre-defined timeout can cause loss of money.
For me, this seems like a reasonable trade-off: most of us are online 24/7 anyway (this very phrase -- "going online" -- is reminiscent of the 1990-s publications about this new thing called The Internet).
Moreover, we can use cryptography to assign third parties to be online for us (degrees of privacy vary).

But I'm getting ahead of myself.


# Payment channels: initial idea

The idea of a payment channel dates back to ancient history (like, 2011?).
Most probably, you've already heard the general description: Alice and Bob exchange signed transactions but don't put them on the blockchain until they decide to end their economic interaction, at which point they do.
Wow, we just did a trillion transactions off-chain with only the final balance hitting the chain.
Is this all?
No, as the devil is in the details.

Its first, naive [[^1]] iteration was the one-way payment channel.
Imagine two whales named Alice and Bob.
Alice sends Bob a transaction with two outputs: 100 BTC to Alice and 0 BTC to Bob (the worst trade deal in the history of trade deals, maybe ever).
Then, she send him another transaction: 99 BTC to herself and 1 BTC to him.
At some point, Alice notifies Bob that she is done, or simply goes offline, and Bob submits the latest transaction to the blockchain.
He can technically submit any transaction, but why do so, if the last one gives him the most money?
This mechanism is called replace-by-incentive.

An obvious drawback of this construction is that, you guessed it, it is one-way only.
Bob can not give anything back to Alice: how can she be sure that he won't send an older transaction to the blockchain, assigning himself a higher balance?
This problem can be defined as old state invalidation, and Lightning solves it.
Let me tell you how.
But first, some background is needed.

[^1]: Yes, I write this word without a diæresis! I also like to live dangerously.


# How Bitcoin transactions actually work 

Learning about Bitcoin is often described with the phrase "going down the rabbit hole": the more you learn, the more you realize how much you don't yet understand.
As any complex subject, Bitcoin can be described at different levels of abstraction.
Take transactions.
A most natural model for transaction is the following: Alice has 3 BTC in her account, Bob had 7 BTC, Alice sent 1 BTC to Bob -- now they have 2 and 8 BTC, respectively.
This is what most wallets and block explorers show, but this is not what actually happens.

What actually happens is that Bitcoin are "locked" in unspent transaction outputs (UTXOs).
"Alice has 3 BTC" means that there are UTXOs in the UTXO database (derived from the main chain), with values summing up to 3 BTC, such that Alice's private key has the right to spend them.
But...

This also is not what _actually_ happens (feel that rabbit hole?).

What actually happens is that each UTXO has a script associated with it.
A UTXO can be spent if and only if a spender provides an input which when given to the script evaluates to True.
For instance, a script may say: check that the signature matches this public key.
If I have the private key, I can submit a valid signature and spend my coins.
Scripts can be more complex.
Take multisig, for example.
A multisig is a script which makes a UTXO spendable by Alice and Bob _together_.
Do we add it to Alice's or Bob's balance? Both? None? Enjoy watching that "balances" abstraction fall apart!

We can add conditions on top.
The two relevant types of restrictions are timelocks and hashlocks.

Timelocks define the time after which a UTXO can be spent.
There are four types of timelocks, varying across two dimensions: relative vs absolute and per-transaction vs per-UTXO.
Lightning uses per-UTXO timelocks, both relative (can be spent only after 10 blocks) and absolute (can be spent only after block 600000) [[^2]].

[^2]: Denoted by the totally obvious opcode names OP_CHECKLOCKTIMEVERIFY and OP_CHECKSEQUENCEVERIFY. I'll let you guess which one is absolute and which one is relative. Read [the docs](https://en.bitcoin.it/wiki/Script#Locktime) to check: if you guessed correctly, today is your lucky day.

Even more interestingly, scripts are not always visible on-chain.
With pay-to-script-hash (P2SH), the sender specifies the _hash_ of the script which must be satisfied by the receiver.
The receiver-turned-sender, in the next transaction, submits the script _itself_, and the inputs which make it evaluate to True.
This is the type of scripts used  by the Lightning network, to which we turn next. [[^3]]

[^3]: Ok, ok, what Lightning ___actually___ uses are pay-to-witness-script-hash (P2WSH) scripts, which are basically the same as P2SH but with Segwit, which allows parties to sign transactions spending outputs of not-yet-confirmed transactions without the fear that those subtly changing their signatures, remaining valid, but invalidating transactions dependent on them.


# Lightning

The Lightning network, hereinafter abbreviated as LN, was introduced somewhere in the Middle ages (2015?).
If you search for "Lightning network whitepaper", as every post-2017-boom blockchain enthusiast is used to doing, you will arrive at [this document](https://lightning.network/lightning-network-paper.pdf) by Poon and Dryja, dated January 2016.
I spent about a month in late 2017 wrapping my head around this document (and gave [a talk in Russian](https://www.youtube.com/watch?v=MjVtTPXVpC0) on the subject shortly thereafter).
The idea is brilliant, but, sorry to bring this up, the paper is poorly written.
At 59 pages, its epigraph could well have been the [famous quote](https://knowyourmeme.com/memes/if-i-had-more-time-i-would-have-written-a-shorter-letter) by Winston Churchill: "If I had more time, I would have written a shorter whitepaper".

Somewhere in 2016, a group of developers decided to implement Lightning.
They started with deriving a specification, which is called [BOLT: Basics of Lightning Technology](https://github.com/lightningnetwork/lightning-rfc/).
At the time of this writing, there are three major BOLT-compatible implementations: lnd, c-lightning, and Eclair.
BOLT is not precisely compatible with the initial whitepaper, which is in many regards outdated. [[^4]]
Another problem with BOLT is that it is a specification, not an introduction.
It definitely assumes that the reader is familiar with _what_ Lightning is, and provides detailed instructions on _how_ to implement it.
BOLT, consisting of 11 Chapters (naturally, starting with #0), lacks the Chapter #-1: a general technical overview.
I'll try to fill the gap.

[^4]: For instance, it dedicates many pages to possible solutions of the malleability problem, which was eventually solved with Segwit and doesn't deserve that much attention now.

## The general flow

The original LN paper described the protocol in two stages:

1. A bi-directional payment channel between two parties;
1. A way to connect the channels into a network.

In the modern LN (BOLT-LN), there is no inherent distinction between payments going from Alice to Bob and somebody else's payments being routed through their channel.
So instead of talking about a single channel, I will describe a network right away.

So what do Alice and Bob do, if they want to pay to each other?

1. Alice opens a channel to an LN node.
1. Bob opens a channel to an LN node.
1. Bob generates an invoice and sends it to Alice out-of-band.
1. Alice creates a route from herself to Bob.
1. Alice sends a payment: channel balances along the route are re-balances such that Bob gets the payment (modulo fees).

TODO: continue












# Header one

Lorem ipsum. [Link](example.com).


Image (with link to Flickr page):

1024 x 768:

[![](https://farm5.staticflickr.com/4249/34060581973_37b9bc7bc8_b.jpg)](https://farm5.staticflickr.com/4249/34060581973_37b9bc7bc8_b.jpg)


YouTube video:

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/7Pq-S557XQU" frameborder="0" allow="encrypted-media" allowfullscreen></iframe>

Citation:

> Foo bar.


## Sub-header

List:
* one two

Ordered list:
1. one
1. two (sic!)

# Header two

One * or _ is italic, two of either is bold.
**This** is italic, and _this_ is bold.