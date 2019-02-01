---
layout: post
title: How the Lightning network works - Part 1
published: true
---

This the first pois in a series about the Lightning network.

I am definitely not the first person to do a write-up on how this cool new tech works.
I'm doing it anyway, with the main purpose being to summarize my own understanding.
Hope it may be helpful for you as well.


# Why payment channels

I'm targeting an advanced audience here, so I won't start with "on October 31st, 2008, an anonymous character under a pseudonym Satoshi Nakamoto ...".
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
Payment channels let us buy scalability by giving up one of the (less important) Bitcoin's features -- non-interactivity.
I can publish my Bitcoin address on my website and go offline for a year, and people will be able to give me money regardless.
(You say, it violates the "no address reuse" security advice? Then I'll write a script which will deterministically derive new addresses for every payment from my root public key.)
In payment channels, the payer and the payee must constantly interact, and going offline for longer than a predefined timeout can cause loss of money.
For me, this seems like a reasonable trade-off: most of us are online 24/7 anyway (this very phrase -- "going online" -- is reminiscent of the [1990-s publications](https://www.youtube.com/watch?v=95-yZ-31j9A) about this new thing called The Internet).
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
Alice sends Bob a transaction with two outputs: 100 BTC to Alice and 0 BTC to Bob.
Then, she send him another transaction: 99 BTC to herself and 1 BTC to him.
At some point, Alice notifies Bob that she is done, or simply goes offline, and Bob submits the latest transaction to the blockchain.
He can technically submit any transaction, but why do so, if the last one gives him the most money?
This mechanism is called replace-by-incentive.

An obvious drawback of this construction is that, you guessed it, it is one-way only.
Bob can not give anything back to Alice: how can he be sure that she won't send an older transaction to the blockchain, assigning herself an higher balance?

To understand how Lightning solves this problem, some background is needed.

[^1]: Yes, I write this word without a diæresis! I also like to live dangerously.


# How Bitcoin transactions actually work 

Learning about Bitcoin is often described as "going down the rabbit hole".
The more you learn, the more you realize how much you don't yet understand.

Take transactions.
A most natural model is the one of account balances.
Alice has 3 BTC, Bob had 7 BTC, Alice sent 1 BTC to Bob -- now they have 2 and 8 BTC, respectively.
This is what most wallets and block explorers show, but this is not what actually happens.

What happens is that bitcoins are "locked" in unspent transaction outputs (UTXOs).
"Alice has 3 BTC" means that there are UTXOs in the UTXO database (derived from the main chain), with values summing up to 3 BTC, such that Alice's private key has the right to spend them.
But...

This also is not what _actually_ happens (feel that rabbit hole?).

What actually happens is that each UTXO has a script associated with it.
A UTXO can be spent if and only if a spender provides an input which when given to the script evaluates to True.
For instance, a script may say: check that the signature matches this public key.
If I have the private key, I can submit a valid signature and spend my coins.
Scripts can be more complex.
Consider multisig.
A multisig is a script which makes a UTXO spendable by Alice and Bob _together_.
Do we add it to Alice's or Bob's balance? Both? None? Enjoy watching that "balances" abstraction fall apart!

We can add even more conditions.
The two relevant types of restrictions are timelocks and hashlocks.

Timelocks define the time after which a UTXO can be spent.
There are four types of timelocks, varying across two dimensions: relative vs absolute and per-transaction vs per-UTXO.
Lightning uses per-UTXO timelocks, both relative ("can be spent only after 10 blocks") and absolute ("can be spent only after block 600000") [[^2]].

[^2]: Denoted by the totally obvious opcode names OP_CHECKLOCKTIMEVERIFY and OP_CHECKSEQUENCEVERIFY. I'll let you guess which one is absolute and which one is relative. Read [the docs](https://en.bitcoin.it/wiki/Script#Locktime) to check: if you guessed correctly, today is your lucky day.

More interestingly, most scripts are not even visible on-chain.
With pay-to-script-hash (P2SH), the sender specifies the _hash_ of the script which must be satisfied by the receiver.
The receiver-turned-sender, in the next transaction, submits the script _itself_, and the inputs which make it evaluate to True.
This is the type of scripts used by the Lightning network, to which we turn next. [[^3]]

[^3]: Ok, ok, what Lightning ___actually___ uses are pay-to-witness-script-hash (P2WSH) scripts, which are basically the same as P2SH but with Segwit, which allows parties to sign transactions spending outputs of not-yet-confirmed transactions without the fear that those subtly changing their signatures, remaining valid, but invalidating transactions dependent on them.


# Lightning

The Lightning network was introduced somewhere in the Middle ages (2015?).
If you search for "Lightning network whitepaper", as every post-2017-boom blockchain enthusiast is used to doing, you will arrive at [this document](https://lightning.network/lightning-network-paper.pdf) by Poon and Dryja, dated January 2016.
I spent about a month in late 2017 wrapping my head around it (and gave [a talk in Russian](https://www.youtube.com/watch?v=MjVtTPXVpC0) on the subject shortly thereafter).
The idea is brilliant, but, sorry to bring this up, the paper is poorly written.
At 59 pages, its epigraph could well have been the [famous quote](https://knowyourmeme.com/memes/if-i-had-more-time-i-would-have-written-a-shorter-letter) by Winston Churchill: "If I had more time, I would have written a shorter whitepaper".

A group of developers decided to implement Lightning.
They started with deriving a specification, which is called [BOLT: Basics of Lightning Technology](https://github.com/lightningnetwork/lightning-rfc/).
At the time of this writing, there are three major BOLT implementations: [lnd](https://github.com/lightningnetwork/lnd), [c-lightning](https://github.com/ElementsProject/lightning), and [Eclair](https://github.com/ACINQ/eclair).
BOLT is not precisely compatible with the initial whitepaper, which is in many regards outdated. [[^4]]
Another problem with BOLT is that it is a specification, not an introduction.
It assumes that the reader is familiar with _what_ Lightning is, and provides detailed instructions on _how_ to implement it.
BOLT, consisting of 11 Chapters (naturally, starting with #0), lacks the Chapter #-1: a general technical overview.
I'll try to fill the gap.

[^4]: For instance, it dedicates many pages to possible solutions of the malleability problem, which was eventually solved with Segwit and doesn't deserve that much attention now.

In the grand scheme of things, the key insight of Lightning is the way to invalidate old state.
Alice commits funds in the channel with Bob by putting them in a multisig outputs. [[^5]]
Alice and Bob pay each other by creating a pair of commitment transactions (Why a pair? Hold on, I will explain later).
Each commitment transaction spends the same output (from the funding transaction), but creates different outputs.
The key question is: how to prevent parties from sending to the blockchain any commitment transaction but the latest one?

Economics comes to the rescue, again!
As in Bitcoin itself, we can not technically _prevent_ a group of miners from trying to create a fork with a double-spend, but we can make it so _expensive_ that few will even try.
After another payment to Bob, Alice _may_ try to broadcast an earlier commitment transaction, where she has more money.
But in this case, and this is the Lightning's secret sauce, Bob can take _all_ the money from the channel.

This is done using a clever combination of hashlocks and timelocks, which I will describe it in detail in the next post of this series.

I'll leave you with the Lightning theme song, which, believe it or not, is also [part of the official specification](https://github.com/lightningnetwork/lightning-rfc/blob/master/00-introduction.md#theme-song):

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/edItjMHez48" frameborder="0" allow="encrypted-media" allowfullscreen></iframe>

Stay tuned!


[^5]: In the Lightning paper, channels were thought to be dual-funded; BOLT-Lightning only describes funding by one party (who initiates the channel opening), though dual-funded channels are at least [proposed](https://github.com/lightningnetwork/lightning-rfc/pull/184).

