---
layout: post
title: Thoughts on Web3. Part 1
published: true
---

This August, Berlin was the global center of all things decentralized.
Thousands of blockchain enthusiasts gathered for the [Berlin Blockchain Week](https://www.blockchainweek.berlin) – a series of conferences, meetups, and a [hackathon](https://ethberlinzwei.com).
In this post, I'll share my thoughts on Web3, which was the primary topic of the the first major event of the week – Web3 Summit. [[^1]]

[^1]: For Russian speakers: you can [watch](https://www.youtube.com/playlist?list=PLU_YQeud2Sg6gcV6HNg47vUU4dnCD5W0V) and [listen to](https://basicblockradio.com/e086/) our coverage of Berlin Blockchain Week in Basic Block podcast. I also participated in the ETHBerlin Zwei hackathon, helping with [a project around Maker DAO](https://github.com/smartkek/dai-insured).

<img src="../images/2019-09-04-thoughts-on-web3-part-1-funkhaus.jpg" alt="Web3 Summit 2019. Funkhaus, Berlin. 2019-08-19"/>

<p style="text-align:right">"The next Mark Zuckerberg won't start a social network company." – Peter Thiel</p>

# What is Web3?

The "3" in Web3 here refers to the "versions" of the web.
Web 1.0 consisted of static, read-only websites.
Web 2.0 brought the dynamic, Javascript-powered interfaces and, more importantly, user generated content.
Facebook is the primary example of a Web2 service.[[^2]]
Billions of users use it for free to stay in touch, share photos, and much more.
More and more people are feeling upset though.
The major reason for criticism is Facebook's business model.

[^2]: I will Facebook as metaphor for a Web2 service throughout this post, but the same applies to Google and others.


# What is wrong with Web 2.0?

Contrary to radio and television, the Internet is a dual channel.
Users consume data but also report what they've consumed back to the server.
The cost of storing and processing data has been declining rapidly (Moore's law plus economies of scale) – at least compared to the value of the data if processed properly.
This led to the current Facebook's business model: it collects comprehensive user profiles and provides precise targeting to advertisers.

Is it that bad if someone extracts enough value from my clicks to let me connect to friends for free?
Advertising [has been around for centuries](https://commons.wikimedia.org/wiki/File:Woman%27s_Home_Companion_1919_-_Campbell_Vegetable_Soup.png), why are people angry at Facebook?

Say, every morning you drop by the same cafe to grab some coffee.
The barista greets you with a smile: "A double espresso as usual, Mister Smith? We have delicious chocolate cupcakes today, would you like one?"
You smile back and feel glad that you live in such a lovely neighborhood.

Now imagine you bought a coffee machine on Amazon.
A minute later, you see an ad: "Bought a coffee machine? Order our extra-special coffee beans! Best for your favorite morning double espresso!"
Feels a bit creepy, right?
What if the ad greets you by name?

People fear the unknown.
A monster jumping from behind the corner in a horror movie makes you scared for a second, but the character walking around the haunted house anticipating a monster jumping from behind _any_ corner generates lasting suspense.
Nuclear power, which is statistically very safe, is perceived as dangerous because but involves deadly yet invisible radiation.
People also don't like giving up control.
Many people "feel" that [driving](https://en.wikipedia.org/wiki/List_of_countries_by_traffic-related_death_rate) is safer than [flying](https://en.wikipedia.org/wiki/Aviation_safety#Statistics), because they feel in control behind the wheel.

Uncertainty and lack of control affect personal data as well.
Sure, Facebook collects data on me, but _what exactly_? Does it come only from my Facebook usage or from other websites too?
Who has access to the data? How long is it stored and where? What can they understand about me if they analyze it? What if they analyze it in ten years with a hundred times more powerful computer?
Who do they share it with, and what to _those_ parties do?
Can I control what data they collect and what they use it for?

Facebook isn't keen to answer these questions.
The rare PR-department-style announcements start with the obligatory "We value your privacy" only to continue with a huge "**but**", carefully wrapped in layers of unconvincing legalspeak.
Facebook is one of the least trusted brands in the US, at the bottom of the list alongside big banks and The Worst Brand Ever in The Land Of The Free: the Government.

Speaking of banks and the government...

Money is another area of life with similar dynamics.
It's very scary to have your card blocked, especially if you have no cash.
The rationale behind such decisions (is your transaction pattern strange so we block you just in case?) is hidden.
The financial system is complex, controlled by someone else, without an opt-out option....

...until 2009.

Bitcoin showed that it is possible to disrupt the seemingly all-mighty fiat money system.
Understandably, people angry at Facebook started wondering: if Bitcoin found a way to give people a more free and private money, can we use similar technologies to achieve the same goals for data?

Without going into too much technical details, let's unpack what makes Bitcoin work.


# Why Bitcoin works

Bitcoin, famously, is a [rabbit hole](https://bitcoinrabbithole.org/).
As you start thinking about what makes Bitcoin work, every insight opens up new questions.
After some time, you stare into the abyss, asking yourself: what is value? what is energy? what is time?
In this chapter, I'll just focus on three points which I find important for the web3 discussion: what is digital scarcity, value as a special content type, and how this enables a closed reward system in Bitcoin.

## Digital scarcity

Bitcoin is the first implementation of _digital scarcity_ without a trusted party.

Digital information works very well for many purposes, but one characteristic delayed the (proper) digitalization of money for nearly three decades (from David Chaum's [digital cash](https://en.wikipedia.org/wiki/David_Chaum#Digital_cash) in 1982 to Bitcoin in 2008): **bits are not scarce**.
Whatever sequence of bits you give me, I can copy it, diluting the value you aimed at transferring.
A simple but dirty way of solving this problem was to appoint a central party, which we all _trust_ to not let people copy value-representing bits.
(This is called fraud or counterfeiting and is punishable off-protocol, that is, by armed people putting you in a cage.)

Bitcoin is digital scarcity without people with guns.
Where does its value come from?

Well, where does value of anything come from?
It comes from people.

Imagine a universe without humans.
How much is a star worth? How much is an atom of hydrogen worth?
These questions are absurd because value is subjective.
"Something is valuable" literally means "some _people_ find it useful".

People need money – a system of value transfer though time (store of value) and space (medium of exchange).
(Unit of account is the third function of money, but it arguably emerges if the other two work well.)
Bitcoin satisfies the demand for a money system for enough people to be worth around $10k apiece at the time of this writing.
Turns out, to achieve this, it has to be very "inefficient" and make the harder choice at every turn.
But as the type of information Bitcoin handles is so valuable (it is pure _value_ itself), the expenses are worth it.


## Is money just another content type?

I'm a long time fan of Andreas Antonopoulos.
It was his passion and dedication that helped me comprehend the incredible beauty of Bitcoin design.
One of my favorite metaphors by Andreas is "[money as a content type](https://youtu.be/3NqDNetEqmI)".
However, while catchy and indeed applicable in programming contexts, it is not entirely accurate.

Content types like MP3 or JPG are just sets of rules that let a computer _interpret_ a sequence of bits.
You may _try_ to interpret a JPG file as text – this will most likely result in pages of unreadable symbols.
But fundamentally JPG bits are the same as TXT bits and even the same as EXE bits.

But you can't _interpret bitcoin_ as text.

The [Bitcoin whitepaper](https://bitcoin.org/bitcoin.pdf) defines a coin as a chain of digital signatures.
Signatures, of course, can be represented as text and printed in hexadecimal.
But not _every_ coin is a bitcoin.
To be _valid_, a coin must stem from the coinbase of some valid block in the _heaviest chain_, which started from a _particular_ genesis block.

A digital representation of value does not work in vacuum.
It must _relate_ to some agreed upon money system.
In the same way it makes no sense to say "pay me 100" without specifying the currency, it makes no sense to consider a chain of signatures without linking it to _the_ Bitcoin system.
In fiat, we establish this relation (PKI, banking licenses, etc) and _then_ trust that the system won't fail, while it technically can.
I trust that the numbers in my bank account there are, all fiat sins notwithstanding, not part of an outright scam plot.
In Bitcoin, we establish this relation (full nodes, SPV nodes, trusted servers – whatever fits our security model) but no additional trust is required.
The validity of blocks can be verified independently, and the probabilistic uniqueness of history is established with proof-of-work.


## A Soviet porcelain factory

When the Soviet Union [collapsed](https://en.wikipedia.org/wiki/Dissolution_of_the_Soviet_Union), its economic system was completely dysfunctional. [[^3]]
It was not just _inefficient_ as you might expect from a centrally planned economy – it was impossible to perform basic financial tasks.
Instead of money, a porcelain factory would pay their employees in dishes and cups, which they sold for next to nothing on the streets, just to bump into empty shelves in a grocery store. [[^4]]

[^3]: Let me suggest [Collapse of an Empire](https://www.goodreads.com/book/show/21351284-collapse-of-an-empire) by Yegor Gaidar if you're interested in why that happened.
[^4]: People also used all kinds of money substitutes. I remember mid-1990s Russian TV ads with prices in "[у.е.](https://ru.wikipedia.org/wiki/Условная_единица)", an euphemism for "USD converted to rubles at the date of purchase" invented due to a legal ban on price listings in ~~stablecoins~~ foreign currencies.

Bitcoin is a porcelain factory.
It pays salaries to its "employees" with what it produces. [[^5]]
However, this is that single case where this makes sense – the factory literally _produces money_.
The money-like objects Bitcoin spits out every ten minutes on average _turn out_ to be exactly what people generally expect to be paid in.
Therefore it is possible to embed the reward for maintaining the system into the system itself.

[^5]: An interesting question to ponder is who are the employees of Bitcoin? The obvious question is miners, but it is only miners? What about early adopters who invested at $10 per BTC and have the resources to go Bitcoin full time or develop another blockchain? What about Coindesk journalists? Or countless [blockchain podcasters](https://github.com/s-tikhomirov/blockchain-podcasts), myself included? In some indirect way, all people working in the space have been paid, essentially, by a DAO named Bitcoin. Mind = blown. A nice topic for another post.

Regular employees trust their employer to pay them and rely on an _external_ legal system to enforce their job contract if they don't.
Bitcoin miners, on the other hand, are not _expecting_ a salary from anyone.
Their pay is indivisible from their performance.
There is no "boss" who could fail to pay, no company to go bankrupt.
This closed cryptoeconomic loop makes the whole thing working and independent of any external party.

To summarize: value is a special type of information.
Money is a system for transferring value.
Before Bitcoin, digital money required trust.
Satoshi leveraged a very special property of _value_ to embed a reward mechanism into Bitcoin, creating a _closed_ (hence independent) system.
I'd argue that this is the absolutely critical piece of the Bitcoin puzzle.
Let's call these self-sustaining systems with built-in economic motivations – _cryptoeconomic_ systems.


# Why is my Facebook data valuable?

Let's get back to the Web2 / Web3 discussion.

Facebook is one of the largest companies in the world, yet I don't pay anything to use it.
How come?
This is quite a cliché already, but if you're not paying for the product, you are the product.
More precisely, you are a data point which lets Facebook develop better products for its true clients – advertisers.
To attract data point (like you), Facebook maintains all those data centers and hires best developers to lure people into scrolling for hours on end.

Your data has no _immediate_ value.
It is more of a debt instrument: it promises returns, if you derive insights from it and sell them to someone.
The value of Facebook's ad targeting system stems from a number of ingredients:
* the huge amount of data (billions of people use Facebook for their basic daily activities, tracked every split second);
* the monopoly on data (a Facebook's competitor can hire smart engineers, but can't train the algorithms on Facebook's datasets);
* the proprietary nature of the algorithms.

The loop is self-enforcing.
Facebook's algorithms are developed by the best-in-generation engineers and trained on the world's largest dataset.
Facebook can afford to hire the best engineers and run powerful servers because of high revenue, enabled by the best data and algorithms, and so on.

The Web3 question then is: can we break that circle?
Can we build a self-sustained, decentralized _cryptoeconomic_ system on top of _data_ to atone for the [Internet's original sin](https://podcastnotes.org/2019/08/31/andreessen-crypto/)?

I'll try to answer this question in the next post.

