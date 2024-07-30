# Layer 2 Explained

## Abstract

Here you may find a simplified info pertaining to specification level of Metis L2 work algorithms.

This piece consists of following sections:\
1\. Common questions — the most frequent questions simply explained;\
2\. Structure — describes the system in its parts\
3\. Algorithm — step-by-step guide on what happens when the user submits a transaction on Andromeda\
4\. Special scenarios — how the system behaves during uncommon cases\
5\. Diagrams — the visual representation of the system on specification level;\
Flowchart shows the overall behavior, while the Activity diagram goes into detail of every single entity included in the system;

## 1. Common questions

Answering the sharpest questions we receive. Check the sections below for more in-depth details.

1. **What is Metis L2?**\
   Metis is an Ethereum Layer 2 leveraging innovative technologies such as Optimistic Rollup, ZKP, Decentralized Sequencers, off-chain storages, etc. for empowering sustainable Web3 economy.
2. **Why is Metis better than other L2s?**\
   Tech-wise, Metis is designed in a way that prevents single-point-of-failure issues. That means when you turn off some part of the system, the entire structure will not stop or lose its security. This way Metis solves a problem that other L2 solutions suffer from (see Arbitrum going down in the winter of 2022 because of such issues).
3. **How does Metis maintain a constant uptime?**\
   The system is designed in a way that any malfunctioning actor gets charged and swapped out through the constant cycles of rotation. It is accomplished with the Peer Network which includes Sequencers, Verifiers and Consensus (PoS) layer.\
   After the next major upgrade, there will be no more downtime at all, and the maintenance schedule will be controlled by the community through the governance protocol.
4. **What is the Peer Network?**\
   It is a network of decentralized actors (nodes) that serve the Metis system. Anyone can become a part of the Peer Network just by setting up a node on their computer. The participants of the Peer Network receive revenue with a portion of the fees from all the transactions that go through the Peer Network.
5. **How does Metis achieve cheaper transactions?**\
   Metis utilizes the Merkle Tree State and Batch Roots (MTSR/MTTBR). In an oversimplified analogy, you can think about MTSR or MTTBR as a bank cheque that you can withdraw the money from. Let's say you want to give someone a big sum of money: in a more efficient way you would give a bank cheque instead of a bag of cash.\
   Metis does the same: instead of posting huge chunks of transaction data to Layer 1, Metis utilizes MTSR/MTTBR to make it way more efficient while still utilizing the security of Ethereum.
6. **What is Merkle Tree State Root / Merkle Tree Transaction Batch Root (MTSR/MTTBR)?**\
   It is a hashing algorithm which is aimed to identify the inconsistencies between the nodes (or participants of the Peer Network in our case). Merkle tree is created by dividing data into many pieces, which are then hashed repeatedly to form the Merkle Tree Root. You can efficiently verify if something has gone wrong with a piece of data.&#x20;
7. **Does Metis still provide Ethereum Layer 1 security by using the Merkle Tree algorithm?**\
   Yes, you can practically check whether your transaction is included into a MTSR/MTTBR data batch (that's also how Metis solves the data availability problem), and it can not be maliciously altered without your private keys. Ethereum Layer 1 processes the transaction data, Metis instead utilizes Ethereum Layer 1 to process MTSR/MTTBR batches which were derived from the transaction data.
8. **What will happen with my money if I send a transaction and the blockchain gets stopped?**\
   Your money will either successfully reach the desired destination address or you will have to resend the transaction. No money will be lost.
9. **Where does Metis store the transaction data?**\
   Metis stores the transaction data in [Memolabs](https://www.memolabs.org/) storage, which is based on blockchain technology to provide safe, efficient and large-scale decentralized cloud storage services.
10. **If Memolabs goes down, will the system still operate?**\
    Yes, in this case the Verifier downloads the transaction data from the blockchain. If the blockchain is unavailable, then Verifier requests Sequencer to post the transaction data on L1 and then downloads it from there. You can check these cases in diagrams or algorithm section below for more details.

## 2. Structure

The entire structure of Metis Layer 2 is designed around several algo loops (sections 2, 4, 7, 8, 12) which are designated to mitigate the potential damage and filter the possible malfunctions of decentralized actors and/or other outer ill-wishers.

There are 8 distinguished actors participating in the system (Governance Protocol is a part of L1 / Smart contract, but it handles a number of functions that makes it a separate entity):

1. User sends the transactions;
2. Sequencer Node (it’s a part of a decentralized network with rotations etc) is responsible for correcting the blockchain, propagating the blocks through the Peer Network;
3. Metis Blockchain is an entity held by Decentralized Sequencer;
4. Verifier (decentralized entity) is the counterpart of Sequencer, mostly responsible to keep an eye on Sequencer to not provide false/invalid data;
5. L1 Smart Contracts — the set of smart contracts that handle the security of the system, solves the disputes between Sequencer and Verifier;
6. Memolabs (decentralized entity) stores the transaction data;
7. The Governance Protocol is responsible for anything related to the efficiency of the system.

## 3. Algorithm

Here we describe the algorithm of executing transactions on Metis Layer 2. The start of the algorithm is at section 1, the desirable end is at section **11.3.1**&#x20;

1. **Transaction Submission**
   * User submits a transaction (tx) to the sequencer
2. **Consensus Layer Interaction**
   * Transactions are sent to the consensus layer, which then submits them to the current sequencer
   * The current sequencer produces blocks for the Metis Blockchain
3. **Block Propagation**
   * The current sequencer propagates blocks through the Peer Network. Other sequencers verify these blocks to ensure they are signed correctly
   * **If the sequencer stops block propagation:**
     * 3.1 Metis Blockchain stops receiving blocks from the sequencer
     * 3.2 User is notified of the transaction failure. Blockchain will resume after sequencer rotation
     * 3.3 The sequencer may post a fake batch (fake MTSR/MTTBR) to signal it cannot post a batch without computing it from tx data
       * **If the sequencer submits a fake batch:**
         * 3.3.1 Malfunctioning sequencer may get kicked by L1/Smart Contract (via community proposal)
         * 3.3.2 Verifier requests sequencer to post tx data on Smart Contract to prove malfunction. Sequencer may get kicked and rotated
         * 3.3.3 System enters an insecure tx state until the next rotation
         * 3.3.4 Sequencer posts the tx data on Smart Contract
         * 3.3.5 L1/Smart Contract computes a batch from newly posted tx data by the sequencer
         * 3.3.6 L1/Smart Contract compares new MTSR and old MTSR
           * **If new MTSR doesn't match the old MTSR:**
             * 3.3.6.1 Malfunctioning sequencer gets kicked and/or slashed by L1/Smart Contract
             * 3.3.6.2 Fake tx data submitted by the sequencer is marked invalid by L1/Smart Contract
             * 3.3.6.3 Sequencer is rotated by the Consensus Layer. User resends the transaction
             * 3.3.6.4 Blockchain is re-enabled
           * **If new MTSR matches the old MTSR:**
             * 3.3.6.1 System exits insecure tx state
             * 3.3.6.2 Sequencer is rotated by the Consensus Layer. User resends the transaction
             * 3.3.6.3 Blockchain is re-enabled
       * **If the sequencer does NOT submit tx data:**
         * 3.3.7 Verifier cannot request the tx data. Only sequencer may get kicked and rotated. Community submits a proposal to the Governance protocol to punish the sequencer
         * 3.3.8 Malfunctioning sequencer gets kicked by L1/Smart Contract if the proposal passes
         * 3.3.9 Sequencer is rotated by the Consensus Layer
         * 3.3.10 Blockchain is re-enabled. User resends the transaction
4. **Transaction Confirmation**
   * User receives confirmation of tx from Metis Blockchain
5. **Sequencer Check**
   * The sequencer checks if it is selected by the Consensus Layer for the current rotation
     * **If the sequencer is the current one:**
       * 5.1 Sequencer downloads the tx data from the Consensus Layer
     * **If the sequencer is not the current one:**
       * 5.2 It synchronizes data and verifies block correctness. The next sequencer is already rotated and working
6. **Block Finalization**
   * Submitter obtains finalized blocks from the sequencer network, submits the transaction data to Memolabs, and submits metadata and state root to L1. Validators retrieve transaction data from L1 and Memolabs
7. **Data Batch Computation**
   * Consensus Layer computes the data batch consisting of Merkle Tree State Roots (MTSR) and Merkle Tree Transaction Batch Root (MTTBR)
8. **Batch Submission**
   * Batch Submitter submits the batch of data to L1/Smart Contract (MTSR and MTTBR)
   * Batch Submitter downloads sequencer’s MTSR and/or state roots sent in the batch to L1/Smart Contract
   * Batch Submitter downloads tx data from Metis Blockchain
   * Batch Submitter computes MTSR and/or state roots from tx data
   * Batch Submitter compares its own MTSR/MTTBR with the sequencer’s MTSR/MTTBR
     * **If Batch Submitter’s MTSR/MTTBR matches the sequencer’s:**
       * 8.3 Nothing happens
9. **Verifier Data Handling**
   * Verifier downloads the tx data from Memolabs
     * **If Memolabs is unavailable:**
       * 9.1 Verifier restores data from a snapshot
     * **If Metis Blockchain is unavailable:**
       * 9.2 Verifier requests sequencer to provide tx data to L1/Smart Contract
       * 9.3 Verifier downloads tx data from L1/Smart Contract
10. **Verifier MTTBR Computation**
    * Verifier computes its own MTTBR from tx data obtained from Memolabs/Metis Blockchain/L1/Smart Contract
11. **Verifier MTTBR Comparison**
    * Verifier downloads sequencer’s MTTBR from L1/Smart Contract
    * Verifier compares its own MTTBR with the MTTBR submitted by the sequencer to L1/Smart Contract
      * **If Verifier’s MTTBR matches sequencer’s:**
        * 11.1 Verifier computes its own MTSR from tx data received from Memolabs
        * 11.2 Verifier downloads sequencer’s MTSR from L1/Smart Contract
        * 11.3 Verifier compares its own MTSR with the sequencer’s MTSR
          * **If Verifier’s MTSR matches sequencer’s:**
            * 11.3.1 Nothing happens, end
          * **If Verifier’s MTSR does not match sequencer’s:**
            * 11.4 Verifier submits the tx data to L1/Smart Contract and marks it as challengeable
            * 11.5 Verifier can submit a fraud proof to validate the MTSR
      * **If Verifier’s MTTBR does not match sequencer’s:**
        * 11.6 Verifier requests sequencer (via fee payment) to post sequencer’s transaction data on L1/Smart Contract. Sequencer must respond within a time window and is responsible for the resulting gas fee. Both lose money in this process
          * **If Verifier spams data requests:**
            * 11.6.1 Sequencer sends a proposal to the Governance protocol to ban Verifier and get compensated
            * 11.6.2 Verifier gets banned from requesting sequencer
            * 11.6.3 No rotation needed since other Verifiers work asynchronously
            * 11.6.4 Sequencer will send the data to L1/Smart Contract by request
        * 11.7 System enters an insecure tx state until the next rotation
        * 11.8 Sequencer sends transaction data again to L1/Smart Contract
        * 11.9 L1/Smart Contract computes its new MTSR requested by the Verifier
        * 11.10 L1/Smart Contract compares new MTSR with the original MTSR submitted by the sequencer
          * **If Verifier’s requested new MTSR matches the sequencer’s original MTSR:**
            * 11.10.1 System exits insecure tx state
            * 11.10.2 Verifier downloads transaction data from L1/Smart Contract
            * 11.10.3 Verifier sends a proposal to Governance Protocol to kick the malfunctioning sequencer
              * **If the proposal passes:**
                * 11.10.3.1 Sequencer gets kicked
                * 11.10.3.2 Governance Protocol compensates Verifier
                * 11.10.3.3 Malfunctioning sequencer gets rotated by L1/Smart Contract
                * 11.10.3.4 Verifier can submit a fraud proof
          * **If Verifier’s requested new MTSR does not match the sequencer’s original MTSR or data is invalid:**
            * 11.11 Sequencer gets slashed by L1/Smart Contract
            * 11.12 Sequencer gets kicked without a proposal
            * 11.13 L1/Smart Contract compensates Verifier
            * 11.14 Sequencer’s original MTSR and MTTBR are marked invalid by L1/Smart Contract and open to be resubmitted by the next sequencer
            * 11.15 If the malfunctioning sequencer is the current one, it gets rotated by L1/Smart Contract
            * 11.16 System exits insecure tx state

## 4. Special scenarios

Describing the extraordinary cases and how the Metis system handles them.

1. **What is the “Insecure transaction state of the system”?**\
   It is a special state when the system stops in some sense, because the security of transactions after the problematic batch can not be guaranteed. System enters insecure tx state for data availability request time window till next rotation. During this time window, if Sequencer provides the valid data, the system will get out of an insecure transaction state.\
   For now, the duration of data availability request time window is 24 hours;
2. **What if the Sequencers unite to stop the system?**\
   The system will only halt if more than 1/3 of Sequencers stop producing blocks for the Metis blockchain. In the event of a single node failure, it will rotate to the next sequencer after a 1-minute timeout period. The community has proposed governance measures to penalize problematic or malicious sequencers. For more details, see [this](https://snapshot.org/#/metislayer2.eth/proposal/0xabeba18fbb141585744dece6361f74749a0292de9e1f88ba3196bd763c44cd6d) page.
3. **What if the Sequencer forged both transaction data and the batch?**\
   Nothing to worry about, a sequencer that alters transactions will not be able to pass the verification by other sequencers.
4. **What if Sequencer sends forged data to Memolabs and L1 / Smart Contract?**\
   The Sequencer is not authorized to perform this action.

## 5. Diagrams

Visual representation of the Metis Layer 2 system from 2 different angles.

{% file src="../.gitbook/assets/Transaction_Flowchart.png" %}

{% file src="../.gitbook/assets/Simple_transaction_flowchart.png" %}

{% file src="../.gitbook/assets/Deposit_flowchart.png" %}
