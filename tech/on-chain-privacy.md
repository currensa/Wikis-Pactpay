# On-Chain Privacy

From the very beginning, blockchains have leaned on one core property to make a distributed ledger trustworthy: traceability. Every account, every transfer, every balance is visible to anyone who cares to look. This openness is what proves assets aren't forged out of thin air — it reveals the sender, the receiver, and the amount behind every transaction. Most of the time, that's exactly what you want.

But not always. Sometimes privacy isn't optional: a business doesn't want competitors reading its payroll off a public ledger, a trader doesn't want a large order front-run the moment it lands on-chain, a person simply doesn't want their entire net worth searchable by anyone with a wallet address. In a private transaction, none of the usual details — sender, receiver, amount — should be visible to outside eyes. Most blockchains aren't built for this; they're traceable by design. A handful of chains, Zcash chief among them, take the opposite approach and bake full privacy into the protocol itself. This article is mainly about adding privacy on top of traceable, Ethereum-like blockchains, but to set the stage, let's first look at how native privacy chains pull off the trick.

## Native Privacy Chains

Zcash was the first blockchain to make this real. It uses zk-SNARKs — a form of zero-knowledge proof — to support "shielded" transactions in which the sender, the receiver, and the amount are all encrypted on-chain. Instead of publishing those details, the sender publishes a cryptographic proof that the transaction is valid: the funds exist, they haven't already been spent, and the books still balance, all without revealing who is involved or how much is moving. Validators check the proof, not the data, so the network stays fully verifiable while the transaction itself stays opaque.

Newer privacy chains push the idea further. Aleo and Aztec extend zero-knowledge proofs beyond simple value transfers to entire private smart contracts, letting arbitrary programs run on hidden inputs and outputs while still proving to the network that every execution followed the rules.

In privacy-native blockchains, strong transaction and data privacy is achievable, but building a rich, production-grade dApp ecosystem is significantly harder than on transparent chains like Ethereum.

## Build Privacy on Traceable Native Blockchains

Today, a mature and rich dApp ecosystem can only really be found on traceable native blockchains like Ethereum. So from a product perspective, the more practical path is to look for solutions that layer privacy on top of transparent, transaction-like operations. Native traceability means anyone can read off the sender, the receiver, and the amount directly from a transaction. To break that, two things need to happen: cut off the link between sender and receiver, and hide the amount.

It sounds a bit like "having your cake and eating it too." The short answer is: yes, you can, just not as completely as on Zcash.

Start with the first problem: cutting off the relationship between sender and receiver. We can't change how native transactions work, so the first step is splitting one transfer into two. Alice deposits her assets with a third party, and later Bob withdraws assets from that same third party. To keep things self-custodied, the "third party" is typically a smart contract that's unowned and unupgradeable, so no one — not even its deployer — can interfere with it. From there, the mechanics are simple:
- A deposit into the smart contract, with the eventual receiver hidden.
- A withdrawal from the smart contract, with the original deposit it traces back to hidden.

Once deposits and withdrawals are decoupled like this, a withdrawal can no longer be matched back to a specific deposit.

The second problem is the amount. The deposit and withdrawal are still native, on-chain transactions, so their amounts can't be hidden outright — and if the amount stays visible, an observer can simply match a deposit and a withdrawal of the same size and reconstruct the sender-receiver link anyway. This is where the trick gets elegant: instead of mapping deposits to withdrawals one-to-one, the system maps many deposits to many withdrawals. In other words:
- Someone makes N deposits, which later authorize M withdrawals. Outside observers only see the smart contract receive N transactions — they have no way of knowing how many withdrawals those deposits will eventually fund, let alone who will make them.
- Someone withdraws from the smart contract. Outside observers only see the contract send a token to one address, knowing only that the token is backed by the pooled history of every deposit ever made by every depositor, not by any single one of them.

Sounds almost magical, right? The piece that makes it all work is zero-knowledge proofs attached to every deposit and withdrawal. They sever the link between deposits and withdrawals, hide the amounts, and — most importantly — prove that everything still adds up.

<!--***-->

A zero-knowledge proof lets someone convince the contract of a fact — "I made one of these N deposits, and I'm entitled to withdraw this amount" — without revealing which deposit it was, what the amount actually is, or any other identifying detail. The contract checks the proof against a public verification key and either accepts or rejects it; it never sees the secret inputs that produced it, only a mathematical guarantee that they were valid. That's the whole trick: a public, fully transparent blockchain can still host private transactions, because the chain is no longer being asked to trust the data — it's being asked to trust a proof.

We've deliberately stayed at the surface here and skipped how these proofs are actually constructed. If you're curious about what's underneath — the circuits, the commitments, the cryptography that makes all of this verifiable — stay tuned for our next article, where we'll go deeper into the implementation.
