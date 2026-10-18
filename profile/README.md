## Great Script Restoration: Tapleaf 0xc2

The Great Script Restoration (GSR) defines Tapleaf 0xc2, the second Taproot leaf version.  It restores the script capabilities disabled in Bitcoin 0.3.1, adds transaction introspection and signature verification over script-built messages, and limits the validation cost of every 0xc2 script with an explicit computational budget.  Legacy, segwit v0 and Tapleaf 0xc0 scripts are unchanged.

In ["A Bitcoin Scripting Proposal BIP Quartet"](https://groups.google.com/g/bitcoindev/c/GisTcPb8Jco/m/8znWcWwKAQAJ), Rusty Russell and Julian Moik proposed restoring Script to its original power while keeping it Script: a varops budget first, then the restored opcodes, introspection and new primitives on top.  The quartet has since grown into five documents, proposed for activation together as a single soft fork.

This organization collects the specifications, the implementation, the benchmarks behind the budget, and proof-of-concepts built on Tapleaf 0xc2.

### Specifications

| Document | What it does |
|---|---|
| [BIP 440: Varops Budget For Script Runtime Constraint](https://github.com/jmoik/bips/blob/gsr-full/bip-0440.mediawiki) | Generalizes BIP 342's sigops budget to every operation: a transaction-wide budget funded by weight, from which each executed opcode pays a cost composed from a fixed set of primitives. |
| [BIP 441: Restoration of disabled script (Tapleaf 0xC2)](https://github.com/jmoik/bips/blob/gsr-full/bip-0441.mediawiki) | Defines the leaf version, re-enables the fifteen opcodes disabled in 0.3.1, makes numbers arbitrary-length unsigned integers and raises the stack limits. |
| [OP_TX](https://github.com/jmoik/bips/blob/gsr-full/bip-unknown-optx.md) (draft) | Transaction introspection: pushes fields of the spending transaction, its spent outputs and the Taproot execution context. |
| [Extended Primitives](https://github.com/jmoik/bips/blob/gsr-full/bip-unknown-extended-primitives.md) (draft) | OP_CHECKSIGFROMSTACK, OP_TWEAKADD and OP_BYTEREV: signatures over script-built messages and Taproot outputs built in script. |
| [Reusable Macros](https://github.com/jmoik/bips/blob/gsr-full/bip-unknown-reusable-macros.md) (draft) | OP_MACRO and OP_CALLMACRO: declare a repeated instruction sequence once and reference it, unrolled before execution. |

BIP 440 and BIP 441 are also published in [bitcoin/bips](https://github.com/bitcoin/bips); the links above point to the latest revisions.

### Implementation

- **Bitcoin Core prototype:** [bitcoin-gsr/bitcoin](https://github.com/bitcoin-gsr/bitcoin) implements all five documents under one deployment, with unit, functional and fuzz tests.  It is a prototype, not an activation or deployment.
- **Test vectors:** JSON vectors in [`bip-0441/`](https://github.com/jmoik/bips/tree/gsr-full/bip-0441) match the prototype, and an independent evaluator written from the BIP texts alone reproduces every vector.
- **Benchmarks:** [bitcoin-gsr/varopsData](https://github.com/bitcoin-gsr/varopsData) holds the `bench_varops` results and the methodology the BIP 440 costs are fitted from.

### Proof-of-concepts

- **Zero-knowledge proof verifiers:** [gsr-stark-verifier](https://github.com/ekrembal/gsr-stark-verifier) verifies a RISC Zero succinct receipt and a recursive Stwo proof bundle, each in one standard Taproot spend mined on activated regtest.  It includes a covenant that only a proof can spend, reading its outputs with OP_TX, and a privacy rollup settled by the RISC Zero verifier.
- **Groth16 bridge:** [coins-gsr-v1](https://github.com/jmoik/coins-gsr-v1) verifies SP1 proofs, wrapped as Groth16, with a pure-GSR Groth16 verifier built from restored arithmetic and macros.
- **In the specifications:** state-carrying covenants, rebindable signatures and key delegation are worked through in the [Extended Primitives examples](https://github.com/jmoik/bips/blob/gsr-full/bip-unknown-extended-primitives.md).

All proof-of-concepts are research code and have not been audited.

### Discussion

- bitcoindev: ["A Bitcoin Scripting Proposal BIP Quartet"](https://groups.google.com/g/bitcoindev/c/GisTcPb8Jco/m/8znWcWwKAQAJ)
- Delving Bitcoin: ["Benchmarking Bitcoin Script Evaluation for the Varops Budget (Great Script Restoration)"](https://delvingbitcoin.org/t/benchmarking-bitcoin-script-evaluation-for-the-varops-budget-great-script-restoration/2094)
- Overview of the five documents: TODO.

### Help wanted

- **Benchmarks on more hardware.**  Running `bench_varops` on machines not yet covered can raise the final costs; see [varopsData](https://github.com/bitcoin-gsr/varopsData).
- **Review** of the specifications and the prototype.
