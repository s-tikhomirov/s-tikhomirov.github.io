---
layout: page
title: About
---

Hi, I'm Sergei!

I am a researcher interested in security, privacy, and scalability of blockchains and related technologies.

Since September 2023, I am a protocol research engineer at [Waku](https://waku.org/).
I work on incentivization for P2P communication protocols.
Waku is part of [Institute of Free Technology](https://free.technology/) (IFT),
perhaps better known as supporters of [Status](https://status.app/), a decentralized messaging app.

[My CV (updated 2025)](/assets/Tikhomirov-CV.pdf).

The multi-faceted design space of blockchain technologies has fascinated me since 2013.
I have contributed to peer-reviewed papers on a range of topics that include:

- vulnerability detection in Ethereum smart contracts;
- P2P-level transaction clustering in Bitcoin, Monero, and Zcash;
- denial-of-service and privacy attacks in the Lightning Network.

I did my PhD at the University of Luxembourg ([CryptoLUX group](https://cryptolux.org/index.php/Home)), where I defended [my thesis](https://hdl.handle.net/10993/44424) ([presentation video](https://youtu.be/Rf5r8hyZJnQ)) in 2020.
I later worked as a postdoctoral researcher at [Chaincode Labs](https://chaincode.com/).
I also co-host Basic Block Radio – a deeply technical blockchain podcast in Russian with [170+ episodes](https://basicblockradio.com/all-episodes/) so far.

# Short bio in third person (updated 2025)

Sergei is a blockchain researcher and protocol engineer specializing in security, privacy, and scalability.
His work encompasses a broad range of topics within the blockchain space, including smart contract security, P2P network privacy, and protocol design.
Currently, he is developing incentivized communication protocols at Waku, leveraging libp2p and zero-knowledge technologies.
Sergei's previous experience includes contributions to Bitcoin and Lightning at Chaincode Labs 
and doctoral research at the University of Luxembourg, resulting in peer-reviewed publications on Ethereum security and network attacks.
He maintains a close watch on the progress of rollup and zero-knowledge solutions, viewing them as essential for blockchain scaling while upholding decentralization.
Furthermore, he co-hosts Basic Block Radio, a technical podcast exploring topics like rollups, ZK, and MEV.

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

[Findel](https://orbilu.uni.lu/handle/10993/30975) is a functional domain-specific language (DSL) for financial contracts on top of Solidity.
The key idea is to think of a contract as a tree-like structure of elementary operations.
The leaves correspond to monetary sums, and the nodes reflect the conditions under which the payments are made.
The benefit of a functional DSL, compared to a Turing-complete language, is that it's easier to analyze and write securely.

[Smartcheck](http://hdl.handle.net/10993/35862) was among the first papers on automated security analysis for Solidity code.
We proposed a comprehensive classification of bugs in Solidity contracts known at the time, including the infamous re-entrancy vulnerability that destroyed The DAO in 2016.
We developed a tool that detects said vulnerabilities, and tested it on a large set of real-world contracts.

## 2018: P2P-level deanonymization in Bitcoin and friends

In 2018, I studied the P2P layer of Bitcoin and privacy-focused cryptocurrencies (Zcash, Dash, and Monero).
The research question was: what information can a well-connected adversary extract from the P2P layer?
In [the resulting paper](https://orbilu.uni.lu/handle/10993/39724), we described a method by which an attacker can cluster transaction that had originated from the same node based solely on their P2P propagation patterns.
We successfully clustered our own transactions using patched node software running on geographically distributed servers.

## 2019–2022: Lightning Network's security and privacy

In 2019, I became interested in scaling blockchains with second-layer protocols and payment channel networks in particular.
I decided to focus on the Lightning Network – the major L2 effort in the Bitcoin ecosystem.
During this time, I studied two somewhat related issues: probing and jamming.

Balance probing allows for estimating a remote channel balance by sending unsolicited fake payments.
This behavior should not be possible but is hard to discourage, as failed payment attempts are free.
We [introduced](https://eprint.iacr.org/2021/384) a mathematical model to quantify the amount of information an attacker learns, and applied it to the previously unstudied case of parallel channels.

Channel jamming is a denial-of-service attack where an adversary blocks victim's channels by initiating payments but not finalizing them.
Similar to jamming, the absence of fees for failed payments make attack costs trivial.
We [proposed](https://eprint.iacr.org/2022/1454) a new fee scheme that includes upfront unconditional fees, and measured its effectiveness in a simulation.

I also [contributed](https://github.com/lnbook/lnbook/issues/400) to a [chapter on security and privacy](https://github.com/lnbook/lnbook/blob/develop/16_security_privacy_ln.asciidoc) for "[Mastering the Lightning Network](https://www.oreilly.com/library/view/mastering-the-lightning/9781492054856/)".


# Publications

My publications with citation counts etc are listed on [Google Scholar](https://scholar.google.com/citations?user=8w9I9uUAAAAJ&hl=en).
My talks and conference presentations are on [my YouTube channel](https://www.youtube.com/channel/UCfo-qSso2IhRvuJj3AUEwBA/).

## 2024

* H. Cornelius, **S. Tikhomirov**, A. Revuelta, S. P. Vivier, A. Challani. [The Waku Network as Infrastructure for dApps](/assets/papers/waku-poster-paper.pdf). Presented at [DLT 2024](https://dlt2024.di.unito.it/) on 15 May 2024.
* A. Revuelta, **S. Tikhomirov**, A. Challani, H. Cornelius, S. P. Vivier. [Message Latency in Waku Relay with Rate Limiting Nullifiers](/assets/papers/waku-latency.pdf). To be presented at [IEEE DAPPS 2024](https://ieeedapps.com/) in July 2024.

## 2022

* C. Shikhelman, **S. Tikhomirov**. [Unjamming Lightning: A Systematic Approach](https://eprint.iacr.org/2022/1454).

## 2021

* A. Biryukov, G. Naumenko, **S. Tikhomirov**. [Analysis and Probing of Parallel Channels in the Lightning Network](https://eprint.iacr.org/2021/384). Presented at the [Financial Cryptography and Data Security 2022](https://fc22.ifca.ai/) on 3 May 2022 ([pre-recorded video](https://www.youtube.com/watch?v=1dDC2VYTVdw), [slides](https://docs.google.com/presentation/d/1m0MH7RqlYmqVCrXWrML7zL6cKVfo9uWYCtVhFSVEA7Y/edit?usp=sharing), [thread](https://twitter.com/serg_tikhomirov/status/1478107536670830598)).

* R. Pickhardt, **S. Tikhomirov**, A. Biryukov, M. Nowostawski. [Security and Privacy of Lightning Network Payments with Uncertain Channel Balances](https://arxiv.org/abs/2103.08576).

## 2020

* S. Tikhomirov. [Security and Privacy of Blockchain Protocols and Applications](https://hdl.handle.net/10993/44424) (doctoral thesis). Defended on 17 September 2020 ([slides](https://docs.google.com/presentation/d/1olqh-w25ONJcn069Zedm0_n-4A8jm-zJXdUd7DCGNaY/edit?usp=sharing), [video](https://youtu.be/Rf5r8hyZJnQ)).

* **S. Tikhomirov**, R. Pickhardt, A. Biryukov, M. Nowostawski. [Probing Channel Balances in the Lightning Network](https://arxiv.org/abs/2004.00333).

* **S. Tikhomirov**, P. Moreno-Sanchez, M. Maffei. [A Quantitative Analysis of Security, Anonymity and Scalability for the Lightning Network](https://eprint.iacr.org/2020/303). Presented at the [IEEE Security and Privacy on the Blockchain workshop](https://ieeesb.org/) (affiliated with EuroS&P) on 7 September 2020 ([video](https://www.youtube.com/watch?v=hslL3aNm8Vg)).

## 2019

* A. Biruykov, **S. Tikhomirov**. [Deanonymization and linkability of cryptocurrency transactions based on network analysis](http://hdl.handle.net/10993/39724) ([PDF](/assets/papers/deanonymization-and-linkability.pdf), [slides](/assets/papers/deanonymization-and-linkability-slides.pdf), [video](https://www.youtube.com/watch?v=XXO3FBqwwO8)). Presented at the [4th IEEE European Symposium on Security and Privacy (EuroS&P)](https://www.ieee-security.org/TC/EuroSP2019/) on 17 June 2019.

* A. Biruykov, **S. Tikhomirov**. [Security and Privacy of Mobile Wallet Users in Bitcoin, Dash, Monero, and Zcash](http://hdl.handle.net/10993/39729
) ([PDF](/assets/papers/mobile-wallets.pdf)). In [Pervasive and Mobile Computing](https://www.sciencedirect.com/science/article/pii/S1574119218307181), special issue on blockchain technologies.

* A. Biruykov, **S. Tikhomirov**. [Transaction Clustering Using Network Traffic Analysis for Bitcoin and Derived Blockchains](http://hdl.handle.net/10993/39728) ([PDF](/assets/papers/transaction-clustering.pdf), [slides](/assets/papers/transaction-clustering-slides.pdf)). Presented at the [2nd Workshop on Cryptocurrencies and Blockchains for Distributed Systems (CryBlock)](http://www.cryblock.org/) on 29 April 2019.

## 2018
* **S. Tikhomirov**, E. Voskresenskaya, I. Ivanitskiy, R. Takhaviev, E. Marchenko and Y. Aleksandrov. [SmartCheck: Static Analysis of Ethereum Smart Contracts](http://hdl.handle.net/10993/35862) ([PDF](/assets/papers/smartcheck.pdf), [slides](https://www.slideshare.net/SergeiTikhomirov/smartcheck-static-analysis-of-ethereum-smart-contracts), [video](https://www.youtube.com/watch?v=FBMI6VAESWo)). Presented at the [1st International Workshop on Emerging Trends in Software Engineering for Blockchain](http://www.agilegroup.eu/wetseb2018/) on 27 May 2018.

* A. Biruykov, D. Khovratovich, **S. Tikhomirov**. [Privacy-preserving KYC on Ethereum](http://hdl.handle.net/10993/35915) ([PDF](/assets/papers/kyc-blockchain.pdf), [slides](https://www.slideshare.net/SergeiTikhomirov/privacy-preserving-kyc-on-ethereum), [video](https://www.youtube.com/watch?v=-pw_v1DQTyc)). Presented at the [1st ERCIM Blockchain Workshop](https://easychair.org/cfp/ERCIMBlockchain2018) on 9 May 2018.

## 2017
* **S. Tikhomirov**. [Ethereum: State of knowledge and research perspectives](https://hdl.handle.net/10993/32468) ([PDF](/assets/papers/ethereum-sok.pdf), [slides](https://www.slideshare.net/SergeiTikhomirov/ethereum-state-of-knowledge-and-research-perspectives), [video](https://youtu.be/Mvp9Rz4c3MY)). Presented at [the 10th International Symposium on Foundations & Practice of Security](http://fps2017.loria.fr/) on 24 October 2017.

* A. Biruykov, D. Khovratovich, **S. Tikhomirov**. [Findel: Secure Derivative Contracts for Ethereum](https://hdl.handle.net/10993/30975) ([PDF](/assets/papers/findel.pdf), [slides](https://www.slideshare.net/SergeiTikhomirov/findel-secure-derivative-contracts-for-ethereum), [video](https://youtu.be/D4sa9U2HXMQ)). Presented at the [1st Workshop on Trusted Smart Contracts](http://fc17.ifca.ai/wtsc/index.html) on 7 April 2017.

## 2016
* **С. Тихомиров**, Я. Александров, Е. Марченко, Л. Сафин. Поиск закладок в программном обеспечении (**S. Tikhomirov**, Y. Alexandrov, E. Marchenko, L. Safin. Finding undocumented features in programs). «Защита информации. Инсайд» №3, 2016 ([abstract](http://www.inside-zi.ru/pages/3_2016/20.html))


# Media and conference appearances

## English

* 2025-08-08: [Proof-of-concept Waku incentivization](https://www.youtube.com/watch?v=PCRZ68Ht44M) (IFT Town Hall)
* 2025-06-12: [Waku Service Marketplace](https://watch.protocol.berlin/65a90bf47932ebe436ba9351/watch?session=68541b2c90bd41297b2b604a) (Protocol Berg v2, Berlin) - [slides](/assets/slides/Waku_Service_Marketplace_Protocol_Berg_2025-06-12_Slides.pdf)
* 2025-06-11: Waku: decentralized and privacy-preserving communication for Web3 applications (d/acc Discovery Day, Berlin) - [slides](/assets/slides/Waku_dacc_day_Berlin_2025-06-11_slides.pdf), [video (timecode 5:16:01)](https://x.com/dacc_kitchen/status/1932720358173651022)
* 2024-06-04: Waku: a generalized P2P communication protocol with ZK-based rate limiting (ETH Belgrade Talk and Workshop)
* 2024-05-24: [Secure communications with Waku](https://www.youtube.com/watch?v=n-N43givo6g) (ETHBerlin04 Workshop)
* 2024-05-22: [How R&D can solve critical privacy challenges](https://www.youtube.com/watch?v=AWgwuwmMTcM) (Web3 Privacy Now meetup, c-base, Berlin)
* 2022-11-23: [Clara and Sergei – solving Lightning jamming](https://podcast.chaincode.com/2022/11/23/clara-sergei-lightning-jamming.html) (The Chaincode Podcast)
* 2022-06-21: [The Lightning Network Will Checkmate the World - Sergei Tikhomirov](https://bitcointv.com/w/rqQCeEjnhtYmDHoH6hfbrs) (Connect The World)
* 2022-02-16: [Sergei Tikhomirov and Lightning privacy](https://podcast.chaincode.com/2022/02/17/sergei-tikhomirov.html) (The Chaincode Podcast)
* 2021-07-09: [Sergei Tikhomirov on Lightning Network Privacy](https://www.monerotalk.live/sergei-tikhomirov-on-lightning-network-privacy) (Monero Talk)
* 2020-04-21: [Researchers Surface Privacy Vulnerabilities in Bitcoin Lightning Network Payments](https://www.coindesk.com/researchers-surface-privacy-vulnerabilities-in-bitcoin-lightning-network-payments) (Coindesk)
* 2020-04-17: [Researchers Highlight Privacy Issues With Lightning Network](https://cointelegraph.com/news/researchers-highlight-privacy-issues-with-lightning-network) (Cointelegraph)
* 2020-04-16: [Wallet balances on Bitcoin's Lightning Network aren't private, new report says](https://decrypt.co/25800/wallet-balances-on-bitcoins-lightning-network-arent-private-new-report-says) (Decrypt)
* 2018-04-27: [The Bitcoin boom and blockchain breakthrough](/images/bitcoin-boom-snt-report-2017.jpg) (in [SnT](https://wwwen.uni.lu/snt) [Annual report 2017](https://www.uni.lu/content/download/108158/1280308/version/1/file/SNT-Annual-Report-2017-web-interactive.pdf))
* 2017-05-15: [Uni.lu: SnT Team Wins Big at Hackathon](https://wwwen.uni.lu/snt/news_events/snt_team_wins_big_at_hackathon)

## Russian

* 2023-11-14: [Подкаст "Между скобок" — Блокчейн как распределённая система](https://www.youtube.com/watch?v=KtrDKuj1hWU)
* 2022-06-29: [YouTube-канал Forklog о новостях Lightning Network](https://www.youtube.com/watch?v=ZljIU4G2WnI)
* 2022-05-19: [YouTube-канал Crypto Lodes — Безопасность и приватность Биткоина](https://youtu.be/acKNimO7bKw)
* 2021-10-04: [YouTube-канал Forklog — Биткоин и Lightning Network: прогноз успеха и приватность](https://youtu.be/OyHCOxaRg0U)
* 2021-11-18: [Подкаст "После прочтения" о книге "Карта культурных различий"](https://youtu.be/f4LG3tWvNVg)
* 2021-03-22: [YouTube-канал Forklog — Биткоин на максималках — онлайн-конференция](https://youtu.be/URbMDmM9094?t=11329)
* 2021-03-04: [Вастрик-клуб AMA — Всё, что вы хотели знать о криптовалютах, но боялись спросить](https://www.youtube.com/watch?v=qES9S1QnM_Y)
* 2020-05-26: [Подкаст "IT Way", выпуск 26 — Что такое блокчейн?](https://www.youtube.com/watch?v=STPV_hUkrXc)
* 2020-05-10: [Халвинг биткоина: цена против технологии — онлайн-конференция ForkLog](https://www.youtube.com/live/oOUWFpYMugw?feature=shared&t=11073)
* 2020-04-23: [YouTube-канал Forklog — Поясни за крипту / Сообщество крипторазработчиков и медиа](https://www.youtube.com/watch?v=EXXqc5DhHo0)
* 2020-04-21: [Подкаст "SDCast", выпуск 115](https://sdcast.ksdaemon.ru/2020/04/sdcast-115/)
* 2020-03-15: [Подкаст "DevZen", выпуск 279](https://devzen.ru/episode-0279/)
* 2019-12-13: [Подкаст "Иммигранткаст", о криптовалютах и жизни в Люксембурге](https://www.spreaker.com/user/immigrantcast/icast-ep-056-sergey-tikhomirov-luxemburg)
* 2019-10-26: [YouTube-канал Zavodil, о блокчейн-конференциях](https://www.youtube.com/watch?v=aC3fHp5uQd4)
* 2019-10-22: [Подкаст "После прочтения", о книге "От нуля к единице"](https://anchor.fm/afterreadingpodcast/episodes/01-e7tad8)
* 2019-04-11: [Блог "Подкасты наступают", о подкасте "Базовый Блок"](https://medium.com/@podcasts.prevail/%D1%81%D0%B5%D1%80%D0%B3%D0%B5%D0%B9-%D1%82%D0%B8%D1%85%D0%BE%D0%BC%D0%B8%D1%80%D0%BE%D0%B2-%D0%B8%D0%B2%D0%B0%D0%BD-%D0%B8%D0%B2%D0%B0%D0%BD%D0%B8%D1%86%D0%BA%D0%B8%D0%B9-%D0%B1%D0%B0%D0%B7%D0%BE%D0%B2%D1%8B%D0%B9-%D0%B1%D0%BB%D0%BE%D0%BA-%D0%B1%D0%BB%D0%BE%D0%BA%D1%87%D0%B5%D0%B9%D0%BD-%D0%B1%D0%B5%D0%B7-%D0%B1%D1%83%D0%BB%D1%88%D0%B8%D1%82%D0%B0-c1b6ea7b7999)
* 2018-05-11: [brdt.pro, выпуск 2, о Monero](https://www.youtube.com/watch?v=ACIPnQfl1Zs)
* 2018-03-12: [brdt.pro, выпуск 1, о Ripple](https://www.youtube.com/watch?v=1EbjrADSSwc)
* 2018-01-15: [Подкаст "DevZen", выпуск 174](http://devzen.ru/episode-0174/)
* 2017-12-06: [Журнал "Популярная механика", декабрь 2017, о будущем блокчейн-технологий](https://www.popmech.ru/technologies/397902-ethereum-platforma-dlya-blokcheyn-sistem-i-eyo-sozdatel-vitalik-buterin/)
* 2017-10-01: [Подкаст "Pro Bitcoin", выпуск 62](http://podcast.probitcoin.ru/e/%D0%B2%D1%8B%D0%BF%D1%83%D1%81%D0%BA-62%D1%81%D0%BF%D0%B5%D1%86%D0%B3%D0%BE%D1%81%D1%82%D1%8C%D1%81%D0%B5%D1%80%D0%B3%D0%B5%D0%B9/)
* 2016-12-18: [Подкаст "DevZen", выпуск 123](http://devzen.ru/episode-0123/)
