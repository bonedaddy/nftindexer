# Overview

Proposed architecture for an NFT Indexer service. To ensure scalabilitiy and fault tolerance the system is broken up into multiple different components that are made as small as possible.


# Data Collector

The data collector is an umbrella for multiple different microservices that are responsible for scraping data from the blockchain. This data is primarily composed of NFT contract creation transactions, NFT minting transactions, and NFT transfer transactions. In addition there is the need to capture historical data. For the purpose of this proposal historical data is data from blocks that were mined up to whatever block is the current latest block at the time an NFT Indexer deployment is brought online. For example if NFT Indexer is deployed at block height 1234, historical data would be all data contained in blocks 1 -> 1234.

To handle this the data collector is broken down into two components:

* block listener
* back filler

## Block Listener

The block listener is fairly simple in that it listens for new blocks to be mined. When a new block is mined processing is conducted via a background process that runs concurrently while new blocks are listened for. This ensures that processing a block doesn't block listening for new blocks.

A simple flow chart of high-level functions performed by the block listener is included below

![](./diagrams/exports/BlockListener.png)


## Back Filler

The backfiller is equivalent to the block listener in terms of functionality performed, except it is responsible for checking all blocks mined before an NFT Indexer deployment was brought online for the first time. In addition to this it acts as a verify for the block listener to ensure that all data scraped by the block listener is correct, and that no data is missing.

The verification component of the backfiller should run at a minimum 12 blocks after whatever block the information the block lsitener scraped was contained in in order to handle uncle blocks. This means that if the block listener scraped information for block 1235, the verification aspect of the backfiller would run at a minimum after block 1247 is mined.

..insert diagram..