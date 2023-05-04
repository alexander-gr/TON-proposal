# An open-source TON-ETL

## Abstract

The TON blockchain ecosystem has recently undergone significant growth and evolution, with the community building hundreds of custom applications across various domains [[1]](https://ton.app/). In this proposal, we conducted a study of existing applications with a focus on on-chain analytics and identified the main drawbacks of these applications.

After conducting thorough analysis, we propose a solution for **indexing and storing TON blockchain data with an open-source indexer application and ETL pipeline** built using the latest **industry best practices**. We strongly believe that the combination of the optimal tech stack and open-source will result in an enhanced product that better meets the needs of the community in terms of on-chain data analytics and applications utilizing transaction data.

Our goal is to enable faster and more efficient access to blockchain data, making it suitable for on-chain data analysts, TON developers, risk managers, etc. This will promote transparency and reliability, thereby driving innovation and growth within the TON community.

## Background

At present, there are a total of 16 live projects in the TON ecosystem that are specifically focused on analytics. These projects provide various features that cater to different use cases, making it crucial to have a comprehensive understanding of each one in order to identify their respective strengths and weaknesses. In the following section, we will provide a brief overview of each of these projects.

* **TONscan** [[2]](https://tonscan.org/) is a web explorer application that provides information about TON addresses, domains, and transactions. Unlike other applications, TONscan does not own its own data storage. Instead, it relies on external APIs such as TONcenter [[4]](https://toncenter.com/), TONWhales [[7]](https://tonwhales.com/), TON API [[6]](https://tonapi.io/), and others to access blockchain data [[3]](https://github.com/catchain/tonscan). While this approach allows TONscan to provide up-to-date and reliable information, it also means that the application's functionality is limited to the features supported by these external APIs.

* **TONcenter** [[4]](https://toncenter.com/) is an API service that enables HTTP access to the TON blockchain. TONcenter utilizes its own indexer [[5]](https://github.com/toncenter/ton-indexer), which uses rabbitMQ and PostgreSQ. This makes it easy for external developers to build explorer tools on top of TONcenter API.

* **TON API** [[6]](https://tonapi.io/) provides a REST API to access TON blockchain data. With TON API, developers can retrieve information about transactions, traces, accounts, and more. TON API is a part of the TonKeeper project and comes with closed source code. However, the API provides a convenient way for developers to interact with the TON blockchain.

* **TonWhales** [[7]](https://tonwhales.com/) provides detailed information about wallets, staking, and mining activities on the TON network. While it primarily focuses on analyzing whale addresses, TonWhales also offers other features to users. The platform open-sources several tools for developers [[8]](https://github.com/tonwhales), but its indexer code is not publicly available.

* **3xpl TON Explorer** [[9]](https://3xpl.com/ton) is a block explorer service that provides data for 11 different blockchain networks, including the TON blockchain. It offers a wide range of features, including explorers, data dumps, APIs, and more. According to the product description, 3xpl uses PHP as its programming language and ClickHouse as its database. While 3xpl provides access to some parts of its codebase [[10]](https://github.com/3xplcom), certain components such as the ETL pipeline and API backend are not publicly available.

* **Explorer-toncoin** [[11]](https://explorer.toncoin.org/) is a technical block explorer that provides detailed information about the TON blockchain. TON.cx [[12]](https://ton.cx/) is a user interface built on top of Explorer-toncoin, designed to make the data more accessible to users. Unfortunately, neither platform provides open source code for their tools.

* **M3TA Analytics** [[13]](https://m3talab.io/reports/ton-telegram-open-network) is a collection of dashboards created by M3TA labs, a web3 data analytics platform that leverages no-code tools and external data providers. While M3TA Analytics provides valuable insights into the TON blockchain, the platform's source code is not open sourced.

* **dTon** [[14]](https://dton.io) is a block explorer and GraphQL API service that offers access to TON blockchain data. While dTon does provide some open source tools for developers, unfortunately its indexer code is not available for public access.

* **Ton.sh** [[15]](https://ton.sh) is an experimental toolkit for TON that includes an address explorer. Unfortunately, there is no information available about the availability of its source code.

* **TON Observer** [[16]](https://tonobserver.com/explorer) is a block explorer that is focused on providing information about TON addresses. The explorer does not provide any information about its code or source availability.

* **TON monitoring** [[17]](https://tonmon.xyz/) is a grafana dashboard that provides basic blockchain metrics. It uses a local lite TON client without a local database. This makes it easier to set up and use, however, it may not provide as comprehensive data as other explorers that use a full node or database. No open-source code is provided for the TON monitoring tool.

* **TON Scan Mobile** [[18]](https://ton.app/explorers/tonscanmobile) and TenereExplorer [[19]](https://ton.app/explorers/tenereexplorer) are android and IOS explorer applications. The source code of these applications is closed and not available for the public.

* **TON NFT tools** [[20]](https://explorer.tonnft.tools/). The project is officially closed though the nft explorer is still live.

* **TONexpl** [[21]](https://tonexp.org/) is a NFT focused explorer. No public code available. 

## Relevance

So there are a lot of projects in the TON ecosystem providing various features for different use cases. However, after conducting a thorough analysis of these projects, we see **a few fundamental drawbacks** that hinder their performance and limit their capabilities.

First, most of the projects [[3]](https://github.com/catchain/tonscan) [[4]](https://toncenter.com/) [[5]](https://github.com/toncenter/ton-indexer) [[6]](https://tonapi.io/) [[7]](https://tonwhales.com/) [[8]](https://github.com/tonwhales) [[9]](https://3xpl.com/ton) [[10]](https://github.com/3xplcom) [[11]](https://explorer.toncoin.org/) [[12]](https://ton.cx/) [[13]](https://m3talab.io/reports/ton-telegram-open-network) [[15]](https://ton.sh) are not open sourced, though the open source is crucial when talking about blockchain analytics.

Open source provides many advantages. The success of Defi Llama [[22]](https://defillama.com/) and Dune Analytics [[23]](https://dune.com/home) highlights **the power of community-driven development**. DefiLlama built a community-driven analytical tool, with contributors from hundreds of protocols, to stay up-to-date with emerging across different blockchain DeFi apps. Similarly, Dune Analytics owes its success to its community-driven development model, enabling quick reactions to rapidly changing market conditions through community-built dashboards.

Open source also allows for easy **customization and transparency**. For example, the blockchain-ETL [[24]](https://github.com/blockchain-etl/ethereum-etl) has become a standard because it's easy for developers to clone and modify the code according to their own needs without creating an ETL pipeline from scratch. **Trust and verifiability** are also important, as open source allows anyone to inspect the code for vulnerabilities or malicious code, ensuring that the code is reliable and secure.

Secondly, some projects use non-optimal tech stacks [[2]](https://tonscan.org/) or do not utilize internal databases [[6]](https://tonapi.io/) [[11]](https://explorer.toncoin.org/). **App performance** is crucial when it comes to analytics on top of extracted data. Most on-chain analytics use cases require analyzing a large amount of data and performing mathematical and logical operations over the dataset. 

Column-oriented databases like Clickhouse [[25]](https://clickhouse.com/) are the best fit for **on-chain analysts' needs** due to data reading specifics, compression, and internal database architecture [[26]](https://clickhouse.com/docs/en/concepts/why-clickhouse-is-so-fast). It is also important to consider efficiency and low latency for some **business use cases** that require minimal data delay. In such cases, a standalone database may not be efficient enough, so it is important to support it with tools like Kafka [[27]](https://www.confluent.io/lp/apache-kafka/) to ensure optimal performance.

In conclusion, the lack of open source options and non-optimal technological stack in existing blockchain indexers provide opportunities for improvement. Community-driven development and the use of an optimal technology stack can greatly enhance the functionality and performance of the indexer, ensuring low latency and high efficiency for various business use cases. By addressing these issues, we can improve **the quality of blockchain analytics** and make it more **accessible to developers and users**.

## Proposal & Scope

The proposed project aims to develop an **indexer for TON blockchain and ETL pipeline** using open source code and the industry's best-practice technological stack. The indexer will run with a database, which will expose the API for external developers plus Grafana dashboards for a demo.

The project consists of three parts:

* **The indexer**

    The indexer is a piece of software that connects to the TON node and pulls the transaction data. The indexer will be written in python and could be deployed as a standalone service making it easy for other developers to use the indexer for their own needs.

* **ETL pipeline**

    ETL (extract-transform-load) pipeline gets raw data from the indexer, processes the data and stores the data in a usable format. Since that data is ready for analytical queries and API requests. Several other views will be built on top of the data in order to speed up and simplify usage. They include precomputed aggregates for analysts (e.g. amount of active addresses for each timeframe, etc). In order to achieve robustness and efficiency kafka [[27]](https://www.confluent.io/lp/apache-kafka/) and clickhouse [[25]](https://clickhouse.com/) are utilized. The proposed stack is able to guarantee data consistency and stability.

* **API**

    API service allows to query data for community needs. The API part includes Grafana dashboards using the same API endpoint to provide a demo and dashboards for analysts. The API service will provide external developers with information about blocks, transactions, addresses, and on-chain metrics

To simplify deployment and future usage, all services will be packed into Docker, with a ready-to-use docker-compose file and deployment documentation provided.

## Funding

The research has been started already. To develop an indexer and ETL pipeline we are requesting the support of the TON community. Funds would be spent on development, implementation and deployment of the indexer and ETL pipeline.

**Amount**: 42000 TON, could be funded in batches.

**Recipient**: EQDMpGzikdp6CCUOnbCUb79leb4pbkKE8P2yZGkt2Ym3jvMr

**ETA soft date**: 5 months

**ETA hard date**: 9 months

## WhoAmI

An experienced data analyst lead/data engineer. I’ve been working mostly with Ethereum blockchain data for 6+ years building and using tools for analyzing transaction activity and addresses labeling. Besides that I’ve been working on tokenomics modeling and researching applied reputation models.

Please see more [info here](https://prism-shrine-0bd.notion.site/ALEXANDER-GRABLEVSKI-67d1ffe30d1c437f89e58a38aac8b557)

## Further work

Once the project is live there’s a way to **allow access to the database to a wide range of audience** building a service similar to Dune analytics [[23]](https://dune.com/home). That would provide them with an easy access to the database and also help visualize and analyze on-chain data without setting up a copy of the indexer and ETL pipeline. This service would facilitate **the growth of a community** of analysts, investors, and dApp developers, enabling a **community-driven development** model for analytical dashboards.

To achieve this goal, additional development of a frontend app and an enhanced API endpoint would be necessary, which may be the subject of a future proposal. 

## Appendix - Sources

[[1] ton.app](https://ton.app/)

[[2] tonscan](https://tonscan.org/)

[[3] github.com/catchain/tonscan](https://github.com/catchain/tonscan )

[[4] toncenter](https://toncenter.com/)

[[5] github.com/toncenter/ton-indexer ](https://github.com/toncenter/ton-indexer)

[[6] tonapi](https://tonapi.io/) 

[[7] tonwhales](https://tonwhales.com/)

[[8] github.com/tonwhales](https://github.com/tonwhales) 

[[9] 3xpl.com/ton](https://3xpl.com/ton)

[[10] github.com/3xplcom](https://github.com/3xplcom)

[[11] explorer.toncoin](https://explorer.toncoin.org/)

[[12] ton.cx](https://ton.cx/)

[[13] m3talab.io/reports/ton-telegram-open-network](https://m3talab.io/reports/ton-telegram-open-network)

[[14] dton.io](https://dton.io) 

[[15] ton.sh](https://ton.sh)

[[16] tonobserver](https://tonobserver.com/explorer)

[[17] tonmon](https://tonmon.xyz/)

[[18] tonscanmobile](https://ton.app/explorers/tonscanmobile)

[[19] ton.app/explorers/tenereexplorer](https://ton.app/explorers/tenereexplorer)

[[20] tonnft](https://explorer.tonnft.tools/)

[[21] tonexp](https://tonexp.org/)

[[22] defillama.com](https://defillama.com/)

[[23] dune.com](https://dune.com/home)

[[24] github.com/blockchain-etl](https://github.com/blockchain-etl/ethereum-etl)

[[25] clickhouse](https://clickhouse.com/)

[[26] why-clickhouse-is-so-fast](https://clickhouse.com/docs/en/concepts/why-clickhouse-is-so-fast)

[[27] apache-kafka](https://www.confluent.io/lp/apache-kafka/)
