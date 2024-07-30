# Layer 2 Explained

## Abstract

Here you may find a simplified info pertaining to specification level of Metis L2 work algorithms.

This piece consists of following sections:\
1\. Common questions — the most frequent questions simply explained;\
2\. Structure — describes the system in its parts;

3\. Algorithm — step-by-step guide on what happens when the user submits a transaction on Andromeda;

4\. Special scenarios — how the system behaves during uncommon cases;

5\. Diagrams — the visual representation of the system on specification level;\
Flowchart shows the overall behavior, while the Activity diagram goes into detail of every single entity included in the system;

## 1. Common questions

Answering the sharpest questions we receive. Check the sections below for more in-depth details.

1. What is Metis?

Metis is an Ethereum Layer2 leveraging innovative technologies such as Optimistic Rollup, ZKP, Decentralized Sequencers, offchain storages, etc. for empowering sustainable Web3 economy.

2. Why is Metis better than other L2s?\
   Tech-wise, Metis is designed in a way that prevents single-point-of-failure issues. That means when you turn off some part of the system, the entire structure will not stop or lose its security. This way Metis solves a problem that other L2 solutions suffer from (see Arbitrum going down in the winter of 2022 because of such issues).

Also, Metis maintains the lowest fees amongst other Layer 2s, which are constant so you don't have to care about gas price fluctuations. For now, the gas prices are being fixed by the developer team, but in the future it will be controlled by the community through the governance protocol.

3. How does Metis maintain a constant uptime?

The system is designed in a way that any malfunctioning actor gets charged and swapped out through the constant cycles of rotation. It is accomplished with the Peer Network which includes Sequencers, Verifiers and Consensus (PoS) layer.\
After the next major upgrade, there will be no more downtime at all, and the maintenance schedule will be controlled by the community through the governance protocol.

4. What is the Peer Network?\
   It is a network of decentralized actors (nodes) that serve the Metis system. Anyone can become a part of the Peer Network just by setting up a node on their computer. The participants of the Peer Network receive revenue with a portion of the fees from all the transactions that go through the Peer Network.
5. How does Metis achieve cheaper transactions?\
   Metis utilizes the Merkle Tree State and Batch Roots (MTSR/MTTBR).\
   In an oversimplified analogy, you can think about MTSR or MTTBR as a bank cheque that you can withdraw the money from. Let's say you want to give someone a big sum of money: in a more efficient way you would give a bank cheque instead of a bag of cash.\
   Metis does the same: instead of posting huge chunks of transaction data to Layer 1, Metis utilizes MTSR/MTTBR to make it way more efficient while still utilizing the security of Ethereum.
6. What is Merkle Tree State Root / Merkle Tree Transaction Batch Root (MTSR/MTTBR)?\
   It is a hashing algorithm which is aimed to identify the inconsistencies between the nodes (or participants of the Peer Network in our case). Merkle tree is created by dividing data into many pieces, which are then hashed repeatedly to form the Merkle Tree Root. You can efficiently verify if something has gone wrong with a piece of data.&#x20;
7. Does Metis still provide Ethereum Layer 1 security by using the Merkle Tree algorithm?\
   Yes, you can practically check whether your transaction is included into a MTSR/MTTBR data batch (that's also how Metis solves the data availability problem), and it can not be maliciously altered without your private keys. Ethereum Layer 1 processes the transaction data, Metis instead utilizes Ethereum Layer 1 to process MTSR/MTTBR batches which were derived from the transaction data.
8. What will happen with my money if I send a transaction and the blockchain gets stopped?\
   Your money will either successfully reach the desired destination address or you will have to resend the transaction. No money will be lost.
9. Where does Metis store the transaction data?

Metis stores the transaction data in Memolabs storage, which is based on blockchain technology to provide safe, efficient and large-scale decentralized cloud storage services.

10. If Memolabs goes down, will the system still operate?

Yes, in this case the Verifier downloads the transaction data from the blockchain. If the blockchain is unavailable, then Verifier requests Sequencer to post the transaction data on L1 and then downloads it from there. You can check these cases in diagrams or algorithm section below for more details.

2\. Structure

The entire structure of Metis Layer 2 is designed around several algo loops (sections 2, 4, 7, 8, 12) which are designated to mitigate the potential damage and filter the possible malfunctions of decentralized actors and/or other outer ill-wishers.

There are 8 distinguished actors participating in the system (Governance Protocol is a part of L1 / Smart contract, but it handles a number of functions that makes it a separate entity):

1. User sends the transactions;
2. Sequencer Node (it’s a part of a decentralized network with rotations etc) is responsible for correcting the blockchain, propagating the blocks through the Peer Network;
3. Metis Blockchain is an entity held by Decentralized Sequencer;
4. Verifier (decentralized entity) is the counterpart of Sequencer, mostly responsible to keep an eye on Sequencer to not provide false/invalid data;
5. L1 Smart Contracts — the set of smart contracts that handle the security of the system, solves the disputes between Sequencer and Verifier;
6. Memolabs (decentralized entity) stores the tx data;
7. The Governance Protocol is responsible for anything related to the efficiency of the system.

## 3. Algorithm

Here we describe the algorithm of executing transactions on Metis Layer 2. The start of the algorithm is at section 1, the desirable end is at section 12.A.3.A.1.&#x20;

1\. User submits a tx to sequencer;

2.Transactions are sent to the consensus layer, which then submits the transactions to the current sequencer.

2\. The current sequencer produces the blocks for Metis Blockchain;\
2.1 The current sequencer propagates the blocks through the Peer Network; Other sequencers in the peer network verify the received blocks to ensure they are signed by the correct sequencer

In case if Sequencer stop the block propagation through the network:

2.1.1 Metis Blockchain stops since it doesn't receive the blocks from Sequencer&#x20;

2.1.2 User gets notified that the tx has failed. Blockchain will resume after the rotation of Sequencer;\
2.1.3 Sequencer may post the relatively fake batch (fake MTSR / MTTBR) which would signal that Sequencer forged it, because Sequencer can not post a batch without computing it from tx data, which it could not receive in this case in principle;

In case if the Sequencer does submit the fake batch:

2.1.3.A.1 Malfunctioning Sequencer may get kicked by L1 / Smart Contract (by the proposal from community);\
2.1.3.A.2 Verifier requests Sequencer to post tx data on Smart Contract which may prove Sequencer’s malfunction. Sequencer may get kicked and rotated;

2.1.3.A.3 System enters insecure tx state for data availability request time window till next rotation;

2.1.3.A.4 Sequencer posts the tx data on Smart Contract;

2.1.3.A.5 L1 / Smartcontract computes a batch from newly posted tx data by Sequencer;\
2.1.3.A.6 L1 / Smartcontract compares new MTSR and old MTSR;

In case if new MTSR doesn't match the old MTSR:

2.1.3.A.6.A.1 Malfunctioning Sequencer may get kicked and/or slashed by L1 / Smartcontract;

2.1.3.A.6.A.2 The fake tx data that Sequencer submitted gets marked invalid by L1 / Smart Contract;

2.1.3.A.6.A.3 Sequencer gets rotated by Consensus Layer. The insecure tx state will end once Verifier will agree that tx data submitted to L1 / Smartcontract by Sequencer is valid;

2.1.3.A.6.A.4 Sequencer gets rotated by Consensus Layer. User has to resend the transaction;

2.1.3.A.6.A.5 Blockchain gets re-enabled.

In case if new MTSR matches the old MTSR:

2.1.3.A.6.B.1 The system gets out of insecure tx state;

2.1.3.A.6.B.2 Sequencer gets rotated by Consensus Layer. User has to resend the transaction;

2.1.3.A.6.B.3 Blockchain gets re-enabled.&#x20;

In case if the Sequencer does NOT submit the tx data:

2.1.3.B.1 Verifier can not request the tx data. Only Sequencer may get kicked and rotated. In this case, the community (can be Sequencers/Verifiers) submits the proposal to Governance protocol to punish only Sequencer;\
2.1.3.B.2 Malfunctioning Sequencer may get kicked by L1 / Smartcontract if proposal passed;\
2.1.3.B.3 Sequencer gets Consensus Layer;&#x20;

2.1.3.B.4 The blockchain gets re-enabled. User has to resend the transaction.

3\. User gets confirmation of tx from Metis Blockchain;

4\. Sequencer checks if it is selected by the Consensus Layer for current rotation:

In case if Sequencer is the current (selected by Consensus Layer for current rotation) Sequencer:\
4.A.1 Sequencer downloads the tx data from Consensus Layer;

In case if Sequencer is not the current Sequencer:\
4.B.1 It knows it is not the current sequencer, and will continue to synchronize data and verify the correctness of the blocks.  That means that the next Sequencer is already rotated and working.

5\. The submitter obtains finalized blocks from the sequencer network, submits the transaction data to Memolabs, and submits metadata and state root to L1. Validators retrieve transaction data from L1 and Memolabs.

5.1 Memolabs provides tx data;

6\. Consensus Layer computes the data batch which consists of Merkle Tree State Roots (MTSR) and Merkle Tree Transaction Batch Root (MTTBR);

7\. Batch Submitter submits the batch of data to the L1 / Smart Contract (MTSR and MTTBR);

8\. Batch Submitter downloads Sequencers's MTSR and/or state roots it sent in the batch to L1 / Smart Contract;\
8.1 Batch Submitter downloads tx data from Metis Blockchain;\
8.2 Batch Submitter computes MTSR and/or state roots from tx data;\
8.3 Batch Submitter compares its own MTSR/MTTBR with the Sequencer’s MTSR/MTTBR;

In case Batch Submitter’s MTSR/MTTBR roots matches the Sequencer’s MTSR/MTTBR:\
8.3.A Nothing happens.

9\. Verifier downloads the tx data from Memolabs;

In case if Memlabs is unavailable:

9.A.1 Verifier restore data from snapshot;

In case if Metis Blockchain is unavailable:

9.B.1 Verifier requests Sequencer to provide the tx data to L1 / Smart Contract;\
9.B.2 Verifier downloads tx data from L1 / Smart Contract.

10\. Verifier computes its own MTTBR from tx data it got from Memolabs / Mets blockchain / L1 / Smart Contract;

11\. Verifier downloads Sequencer’s MTTBR from L1 / Smart Contract;

12\. Verifier compares its own MTTBR with the MTTBR submitted to L1 / Smartcontract by Sequencer:\
In case Verifier’s MTTBR = Sequencer’s MTTBR:

12.A.1 Verifier computes its own MTSR from tx data it received from Memolabs;

12.A.2 Verifier Downloads Sequencer’s MTSR from L1 / Smart Contract submitted by Sequencer;

12.A.3 Verifier compares Verifiers MTSR with Sequencer’s MTSR;

In case Verifier’s MTSR = Sequencer’s MTSR:

12.A.3.A.1 Nothing happens, the end.

In case Verifier’s MTSR =/= Sequencer’s MTSR:

12.A.3.B.1 Verifier submits the tx data (it got from Memolabs) to L1 / Smart Contract and marks them challengeable;

12.A.3.B.2 Verifier can submit fraud proof (which only validates the MTSR, which has already been checked by Verifier in this case). Fraud proof process will proceed just as the regular Optimistic Rollup hereafter.

In case Verifier’s MTTBR =/= Sequencer’s MTTBR:

12.B.1 Verifier requests Sequencer via fee payment to post Sequencer’s transaction data on L1 / Smart Contract. Sequencer will have a time window (in # of blocks) to respond and is responsible for the resulting gas fee. Both Sequencer and Verifier lose money in this process;

In case if Verifier is spamming the data requests to Sequencer:

12.B.1.A.1 Sequencer sends proposal to Governance protocol to ban Verifier and get compensated;

12.B.1.A.2 Sequencer can get compensated.

12.B.1.A.3 Verifier gets banned from requesting Sequencer. No rotation needed since other Verifiers work asynchronically. Sequencer will send the data to L1 / Smart Contract by request even in this case.

12.B.2 System enters insecure tx state for data availability request time window till next rotation;

12.B.3 Sequencer sends transaction data again to L1 / Smart Contract;

12.B.4 L1 / Smartcontract computes its new MTSR requested by the Verifier;

12.B.5 L1 / Smartcontract compares new MTSR with old MTSR originally submitted by the Sequencer:

In case Verifier’s requested new MTSR = Sequencer’s original MTSR:

12.B.5.A.1 System gets out of insecure tx state;

12.B.5.A.2 Verifier downloads transaction data from L1 / Smart Contract to continue the process as above;

12.B.5.A.3 Verifier sends proposal to Governance Protocol to kick the malfunctioning Sequencer because of tx data discrepancy;

12.B.5.A.4 If the proposal passes, Sequencer gets kicked;\
12.B.5.A.5 If the proposal passes, Governance Protocol will compensate Verifier;

12.B.5.A.6 If the current Sequencer is the one who malfunctioned, then gets rotated by L1 / Smart Contract (even if 3000 tx limit wasn’t fulfilled), if not, then nothing happens;

12.B.5.A.7 The Verifier can submit a Fraud Proof. The Fraud proofing process will proceed just as a regular Optimistic Rollup hereafter.

In case if requested by Verifier new MTSR =/= Sequencer’s original MTSR or data is invalid or Sequencer did not manage to provide data to L1 / Smart Contract:

12.B.5.B.1 Sequencer gets slashed by L1 / Smartcontract;\
12.B.5.B.2 Sequencer gets kicked without proposal by L1 / Smartcontract;\
12.B.5.B.3 L1 / Smartcontract will compensate Verifier;

12.B.5.B.4 Sequencer’s original MTSR and MTTBR are marked invalid (by L1 / Smart Contract) and open to be resubmitted by the next Sequencer. Sequencer’s state roots from this point will be deleted (from L1 / Smart Contract) and resubmitted by the next Sequencer, a rollback happens.

12.B.5.B.5 If the current Sequencer is the one who malfunctioned, then gets rotated by L1 / Smart Contract (even if 3000 tx limit wasn’t fulfilled);\
12.B.5.B.6 System gets out of insecure tx state.

## 4. Special scenarios

Describing the extraordinary cases and how the Metis system handles them.

1. What is the “Insecure transaction state of the system”?\
   It is a special state when the system stops in some sense, because the security of transactions after the problematic batch can not be guaranteed. System enters insecure tx state for data availability request time window till next rotation. During this time window, if Sequencer provides the valid data, the system will get out of an insecure transaction state.\
   For now, the duration of data availability request time window is 24 hours;
2. What if the Sequencers unite to stop the system?\
   The system will only halt if more than 1/3 of Sequencers stop producing blocks for the Metis blockchain. In the event of a single node failure, it will rotate to the next sequencer after a 1-minute timeout period. The community has proposed governance measures to penalize problematic or malicious sequencers. For more details, see [this](https://snapshot.org/#/metislayer2.eth/proposal/0xabeba18fbb141585744dece6361f74749a0292de9e1f88ba3196bd763c44cd6d) page.
3. What if the Sequencer forged both transaction data and the batch?\
   Nothing to worry about, a sequencer that alters transactions will not be able to pass the verification by other sequencers.
4. What if Sequencer sends forged data to Memolabs and L1 / Smart Contract?\
   The Sequencer is not authorized to perform this action.

## 5. Diagrams

Visual representation of the Metis Layer 2 system from 2 different angles.\
Note: please use the steps from the algorithm section below to follow the arrows on the diagrams. Also the Lucidchart requires registration or you can sign in with your Google account.

Flowchart diagram works great if you want to see the entire system without overflow of details:

\
