# Privacy for DeFi

In [On-Chain Privacy](./on-chain-privacy.md), we explored how public blockchains expose transactions, balances, and the addresses behind them. That visibility extends to DeFi. Stake through Lido, lend through Aave, or swap on Uniswap, and anyone can inspect the activity tied to your address. The protocols work as intended, but you may not want the world to see your portfolio or follow your next move.

## Privacy Through UTXOs

One way to build private DeFi is to use a shielded UTXO system. Users move assets into the system, transact privately within it, and move assets back out when they need to. The private operations can work well inside that environment, but interacting with ERC-20 tokens, native gas tokens, and existing public protocols usually requires a way to shield and unshield assets.

Privacy-focused chains such as Aleo offer another route: build applications within an ecosystem designed for private execution. That approach can provide deeper privacy, but it also means building or connecting to a separate ecosystem. What if users could keep using the DeFi protocols already available on transparent chains?

## Connecting Privacy to Existing DeFi

Imagine you want to stake ETH through Lido without linking the staking transaction to your wallet. On Ethereum, the transaction itself is public: observers can see the assets, amounts, and contracts involved. Hiding the connection to your wallet therefore takes more than sending funds from a fresh address.

The [previous article](./on-chain-privacy.md) describes how a privacy pool can use zero-knowledge proofs to break the direct link between deposits and withdrawals. We can extend that idea to DeFi in two steps:

1. A user deposits assets into the privacy pool. A later withdrawal can be authorized without revealing which deposit funded it.
2. In a single transaction, the pool sends assets to a DeFi protocol and receives the resulting assets back into the pool.

That second step is the key integration. A user could withdraw to a new address and interact with DeFi afterward, but the public transactions from that address would form a trail the user must manage. An atomic interaction lets the protocol handle the withdrawal, DeFi action, and return to the pool together. The user does not have to expose a new address between those steps.

An observer can still see the DeFi interaction, including its public amounts and the protocol involved. What the observer should not be able to read directly is which earlier deposit funded it or which user owns the assets returned to the pool. Privacy depends on the pool's design and activity: distinctive amounts, timing, or other public clues may still narrow the possibilities. The aim is to make DeFi usable without turning every portfolio decision into a permanent, easily followed trail.
