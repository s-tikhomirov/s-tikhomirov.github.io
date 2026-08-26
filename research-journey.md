---
layout: plain
title: Research journey
permalink: /research-journey/
---

# My research journey

## Before 2016: Vulnerability detection

In 2013, I got my Masters degree in applied mathematics and systems programming from the Moscow State University ([Faculty of Computational Mathematics and Cybernetics](https://cs.msu.ru/en)).

In 2013–2016, I was a full-time information security analyst at [SmartDec](https://smartdec.net/).
My tasks included aggregating information about best practices in various programming languages and formalizing dangerous coding patters for our vulnerability detection tool.

I fell into the Bitcoin rabbit hole in late 2013.
In my spare time throughout 2014–2016, I wrote for a popular Russian-language cryptocurrency website [Bitnovosti](https://bitnovosti.io/author/aab5420/), and helped film a [documentary](https://youtu.be/xJYwh9lw4CM) about cryptocurrency adoption in Europe.

## 2016–2017: Bugs in Ethereum contracts

In 2016, I started a PhD program at the University of Luxembourg.
My first research topic was the security of Solidity smart contracts.
The main results of that period were Findel and Smartcheck.

[Findel](/assets/papers/findel.pdf) is a functional domain-specific language (DSL) for financial contracts on top of Solidity.
The key idea is to think of a contract as a tree-like structure of elementary operations.
The leaves correspond to monetary sums, and the nodes reflect the conditions under which the payments are made.
The benefit of a functional DSL, compared to a Turing-complete language, is that it's easier to analyze and write securely.

[Smartcheck](/assets/papers/smartcheck.pdf) was among the first papers on automated security analysis for Solidity code.
We proposed a comprehensive classification of bugs in Solidity contracts known at the time, including the infamous re-entrancy vulnerability that destroyed The DAO in 2016.
We developed a tool that detects said vulnerabilities, and tested it on a large set of real-world contracts.

In 2017 I started [Basic Block Radio](/podcast/) to explain this work in Russian.

## 2018: P2P-level deanonymization in Bitcoin and friends

In 2018, I studied the P2P layer of Bitcoin and privacy-focused cryptocurrencies (Zcash, Dash, and Monero).
The research question was: what information can a well-connected adversary extract from the P2P layer?
In [the resulting paper](/assets/papers/deanonymization-and-linkability.pdf), we described a method by which an attacker can cluster transaction that had originated from the same node based solely on their P2P propagation patterns.
We successfully clustered our own transactions using patched node software running on geographically distributed servers.

## 2019–2022: Lightning Network's security and privacy

In 2019, I became interested in scaling blockchains with second-layer protocols and payment channel networks in particular.
I decided to focus on the Lightning Network – the major L2 effort in the Bitcoin ecosystem.
During this time, I studied two somewhat related issues: probing and jamming.

Balance probing allows for estimating a remote channel balance by sending unsolicited fake payments.
This behavior should not be possible but is hard to discourage, as failed payment attempts are free.
We [introduced](/assets/papers/lightning-probing-parallel-channels.pdf) a mathematical model to quantify the amount of information an attacker learns, and applied it to the previously unstudied case of parallel channels.

Channel jamming is a denial-of-service attack where an adversary blocks victim's channels by initiating payments but not finalizing them.
Similar to jamming, the absence of fees for failed payments make attack costs trivial.
We [proposed](/assets/papers/unjamming-lightning.pdf) a new fee scheme that includes upfront unconditional fees, and measured its effectiveness in a simulation.

I also [contributed](https://github.com/lnbook/lnbook/issues/400) to a [chapter on security and privacy](https://github.com/lnbook/lnbook/blob/develop/16_security_privacy_ln.asciidoc) for "[Mastering the Lightning Network](https://www.oreilly.com/library/view/mastering-the-lightning/9781492054856/)".

## IFT and Logos

TBD.

{% comment %}
Smart contract [specification](https://lip.logos.co/messaging/core/raw/rln-contract.html) for spam-resistant P2P messaging with Rate-Limiting Nullifiers (RLN)
{% endcomment %}