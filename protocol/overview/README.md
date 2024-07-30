# Overview

<figure><img src="../../.gitbook/assets/Metis Sequencer Abstracted Structure_.drawio.png" alt=""><figcaption><p>Abstracted diagram of Decentralized Sequencer architecture, <a href="https://drive.google.com/file/d/14bQChT5v_hDrViw1my1_v7CP5X1DgLrY/view?usp=sharing">link</a></p></figcaption></figure>

In this section we are going to shortly describe the overall system overview on which the decentralized sequencer pool was built upon.

We have launched a Decentralized Sequencer Pool to achieve full decentralization in Layer 2 networks and avoid the single-point failures derived from centralized sequencers. This is a key step towards making our L2 network fully decentralized. It works with our existing decentralized P2P network to enable smooth and secure sequencer transitions, and the removal of faulty or malicious nodes. This ensures the network's long-term stability and creates a continuous, stable, and community-driven model.

To achieve that, we utilize such technologies:

* [Tendermint consensus](https://docs.tendermint.com/v0.34/introduction/what-is-tendermint.html) developed developed by Cosmos
* [Threshold Signature Scheme](https://github.com/bnb-chain/tss-lib) (TSS)&#x20;
* Multi Party Computation (MPC)
* [Libp2p](https://libp2p.io/)
* L2 Geth and others

Simplified transaction flow overview:

1. A user initiates a transaction.
2. The transaction is sent to Sequencer nodes in the network.
3. The Sequencers receive the transaction, and produce a block for it if it’s a valid tx.
4. Multi-party computation (MPC) nodes combine the blocks and send them to the Ethereum main chain. This completes the transaction.

A comprehensive flowchart can be found [here](https://drive.google.com/drive/folders/1tKS3bk-fWICfm38h2wqtX3SZoB26P-\_U).

To apply for decentralized sequencer operator, you will need 20,000 Metis tokens locked in the node. More info [here](https://sequencer.metis.io/).
