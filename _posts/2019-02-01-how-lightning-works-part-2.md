---
layout: post
title: How the Lightning network works - Part 1
published: false
---

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