# STATE OF THE MIXERS: SUMMER 2019

A survey of active on-chain mixer efforts for Ethereum, including history, context and benchmarks.

## Outline

* [Summary](https://github.com/tvanepps/State-of-the-Mixers-Summer-2019/blob/master/README.md#summary)
* [Strategic importance of mixers](https://github.com/tvanepps/State-of-the-Mixers-Summer-2019/blob/master/README.md#strategic-importance-of-mixers)
* [BTC coinjoin context / prior art](https://github.com/tvanepps/State-of-the-Mixers-Summer-2019/blob/master/README.md#btc-coinjoin-context--prior-art)
* [Abstract Tradeoffs](https://github.com/tvanepps/State-of-the-Mixers-Summer-2019/blob/master/README.md#abstract-tradeoffs)
    * [SNARKs](https://github.com/tvanepps/State-of-the-Mixers-Summer-2019/blob/master/README.md#snarks)
    * [Ring Signatures](https://github.com/tvanepps/State-of-the-Mixers-Summer-2019/blob/master/README.md#ring-signatures)
* [Mixer List](https://github.com/tvanepps/State-of-the-Mixers-Summer-2019/blob/master/README.md#mixer-list)
    * ⭐ Mixer Benchmarks ⭐
    * [SNARK Based](https://github.com/tvanepps/State-of-the-Mixers-Summer-2019/blob/master/README.md#snark-based)
        * [Miximus](https://github.com/tvanepps/State-of-the-Mixers-Summer-2019/blob/master/README.md#miximus)
        * [Semaphore Mixer (Active)](https://github.com/tvanepps/State-of-the-Mixers-Summer-2019/blob/master/README.md#ethsnarks-miximus)
        * [Hopper (active)](https://github.com/tvanepps/State-of-the-Mixers-Summer-2019/blob/master/README.md#semaphore-mixer)
        * [K0 (active)](https://github.com/tvanepps/State-of-the-Mixers-Summer-2019/blob/master/README.md#k0)
        * [Zeth](https://github.com/tvanepps/State-of-the-Mixers-Summer-2019/blob/master/README.md#zeth)
        * [Tornado Mixer (active)](https://github.com/tvanepps/State-of-the-Mixers-Summer-2019/blob/master/README.md#tornado-mixer)
    * [Ring Signature Based](https://github.com/tvanepps/State-of-the-Mixers-Summer-2019/blob/master/README.md#ring-signature-based)
        * [Heiswap (Active)](https://github.com/tvanepps/State-of-the-Mixers-Summer-2019/blob/master/README.md#heiswap)
        * [Ethereum-RingCT](https://github.com/tvanepps/State-of-the-Mixers-Summer-2019/blob/master/README.md#ethereum-ringct)
        * [RingToken](https://github.com/tvanepps/State-of-the-Mixers-Summer-2019/blob/master/README.md#ringtoken)
        * [SolidBlu1992](https://github.com/tvanepps/State-of-the-Mixers-Summer-2019/blob/master/README.md#solidblu1992)
        * [Mobius](https://github.com/tvanepps/State-of-the-Mixers-Summer-2019/blob/master/README.md#mobius)
        * [Laundromat](https://github.com/tvanepps/State-of-the-Mixers-Summer-2019/blob/master/README.md#laundromat)
    * Others
        * [ShareLock](https://github.com/tvanepps/State-of-the-Mixers-Summer-2019/blob/master/README.md#sharelock)
    * [⭐ Recommendations for continued efforts ⭐](https://github.com/tvanepps/State-of-the-Mixers-Summer-2019/blob/master/README.md#recommendations-for-continued-efforts)
## Summary

This document is meant to serve as a general overview of all mixer efforts currently in development for Ethereum. Requested by Moloch, in the interest of reducing information overhead and duplicative efforts for current teams and prospective developers. 👹

Also included is general tradeoffs and considerations for Snarks / Ring Signatures. Developers looking to create their own mixer will hopefully be able to use this as a reference.

This survey will give most attention to simple mixers, ie, swapping ether for ether, and then exiting. There are more ambitious projects much larger in scope that allow transfers within the contract, effectively creating deep pools of private value transfer. Future versions of mixers may attempt to graduate to this stage as well.

I received significant input on this from the Semaphore Telegram channel, specifically Haarorld, Roman (tornado.cash) Barrywhitehat. Thank you as well to the mixer teams who answered my many questions. 🙏 If there is any incorrect or missing information please make a PR or message me and I will do my best to maintain.

## Strategic importance of mixers 
In the early days of Ethereum, before mining was inundated with highly specialised ASICs, it was relatively easy to get new Ether directly from the network. With a modest hardware setup, users could participate in network consensus and be rewarded with enough Ether to use on the platform. In this state, users were given several important advantages: 

1. The Ether minted from Block Rewards was a "clean" source, without any possibility of a tainted past. Transfers are trivially tracked, opening users up to law enforcement targeting of past activity.
2. No potentially malicious intermediaries / onramps could interfere, as exists today in varying degrees through exchanges and local services.
3. Mining is permissionless - meaning there are addresses from that time which will never have legacy identities associated with them. By not having to go through an exchange (and following opsec best practices), there's a low likelihood of ever getting targeted for theft and extortion.

Given the relatively high levels of mining difficulty and the uncertainty of local transfers, getting uncompromised Ether is nearly impossible today. Properly implemented mixers promise to restore at least a degree of this past, making them incredibly useful for any community that emphasises norms like sovereignty and strong privacy. 

However, it's important to state that individual mixers at the application layer will never provide absolute privacy to users, only probabilistic guarantees. As with many mechanisms, it depends which tradeoffs were made in pursuit of which goals. This survey will explore these  further.

For additional resources on user privacy outside of mixers at different levels:

* general intro from Vitalik Buterin, 2016: [Privacy on the Blockchain](https://blog.ethereum.org/2016/01/15/privacy-on-the-blockchain)
* 'Sandbox' closed-loop ecosystems like [Aztec Protocol](https://medium.com/aztec-protocol/an-introduction-to-aztec-47c70e875dc7)
* a very helpful curated resource list from Mikerah: [Awesome Privacy on Blockchains](https://github.com/Mikerah/awesome-privacy-on-blockchains)

## BTC coinjoin context / prior art

* https://en.bitcoin.it/wiki/CoinJoin
* https://en.bitcoin.it/wiki/Privacy
* https://coinlab.com/blog/post/coinjoin/

> CoinJoin, and similar schemes without a growing anonymity set, are vulnerable to correlation analysis, as demonstrated by: https://www.coinjoinsudoku.com/ - systems which use this approach, be it aggregate signature schemes, ring signatures etc. have many security caveats which can result in all transactions essentially being deanonymised.

## Abstract Tradeoffs

The final output of a mixer heavily depends on the foundational cryptographic component providing privacy. There are two mechanisms generally used: SNARKs, aka "Succinct Non-Interactive Argument of Knowledge," or Ring-Signatures. Both have specific peculiarities which must be considered in the mixer architecture.


### SNARKs

#### High-level

 * Algorithm used for merkle tree **(makes proving on mobile devices possible)**
     * Depth of merkle-tree
     * On-chain Gas cost for computing merkle tree inserts
     * Number of circuit constraints, per-level
     * Maximum number of items in merkle-tree
     * Trade-off: depth vs max-items vs on-chain cost vs circuit constraints vs acceptable cost
         * Example:
             * SHA256 = cheap on-chain, *very expensive* to prove
             * Pedersen hash = *very expensive* on-chain, cheap to prove
             * Poseidon = $1\over2$ depth merkle trees (very cheap to prove), expensive on-chain (~90k gas per-level)
 * Trusted setup **(the thing which stops us deploying)**
     * How can this be handled?
     * Groth16 phase1 being handled by Gnosis
         * Require per-circuit setup
     * SONIC, SHARK, others etc. are slower, but don't require per-circuit trusted setup
     * Bulletproofs = no trusted setup, but $\Theta(n)$ verification time versus number of constraints `n`.
     * SNARKs = trusted setup, but $\Theta(1)$ verification time
 * Platform support **(running the same code, at near-native speeds, on as many platforms as possible = better)**
     * WASM / JS support for in-browser no-download-necessary web-wallets.
         * WASM slower than natively compiled optimised code, but write-once-run-anywhere
     * Native iOS and Android support?
     * Natively compiled code usually faster & lower memory than WASM / JS
         * Rust on iOS / Android?
         * C++ on iOS / Android = easier
     * Do Mixers need parallel / multi-cpu / GPU support?
 * Proving time **(lower is better)**
 * On-chain cost **(lower is better)**

#### More detail

* The depth of the merkle tree, which determines the total number of deposits it supports
    * The algorithm used for the Merkle-tree, and the depth of the tree, usually dominates the number of constraints and subsequently the proving time
    * The choice of hashing algorithm and tree depth also significantly impacts the on-chain deposit GAS requirements
        * There is a batching technique, where inserts into the tree are processed by a zkSNARK to 'append N items to tree', which reduces on-chain GAS cost
    * Hashing algorithms:
        * MiMC + Miyaguchi-Preneel compression function (2nd fastest on-chain)
        * MiMC feistel sponge (approx 2x MiMC+MP GAS cost)
        * Poseidon, allows quarternary trees (4 items per node), reducing tree-depth by half, low number of constraints per bit
        * Pedersen hash (as used by Zcash Sapling, slowest on-chain)
        * SHA-256 (fastest on-chain)
    * Hashing algorithm measurements:
        * Constraints per-bit
        * Tree-depth versus node width (e.g. binary tree, vs n-ary tree)
        * On-chain GAS cost, per-bit
* The number of constraints in the circuit. this highly impacts proving time. (do constraints affect level of privacy in the end?)
* Which snark library used
    * Is it web-browser compatible? (JS/WASM etc.)
        * Good for end-user web-based wallets
    * Does it support faster parallel proving
        * Good for large batches of transactions, aka 'rollup'
    * Does it support mobile devices
        * Native iOS, Android support
    * Does it support GPU acceleration
        * Good for reducing overall proving-time with larger circuits
* proof generation time? generally higher or lower than ring sigs? or does it depend on the mixer

### Ring Signatures

1. Proofs verification costs scale linearly relative to the pool of inputs (users)
2. Proofs can be verified entirely on-chain
3. No trusted setup (a.k.a no 'toxic waste')
4. Relatively cheap to compute proof/signature
5. Anonymity set can be limited depending on the interval between first deposit and withdrawal (first withdrawal ends ring deposits and starts new rings)


# MIXER LIST

The majority of mixers on Ethereum under active development are SNARK based. The following chart compiles a variety of relevant benchmarks to compare across implementations.

|  |Deposit Cost|Withdraw Cost|MT Depth|MT Max items|AVG Proving time (ms)|Constraints per-level|Constraints overhead|User Keys (zipped / expanded) (mb)|
|---|---:|---:|---:|---:|---:|---:|---:|---:|
|**Miximus** |1,886,209|605,181|29|536,870,912|3,600 (native)|812|-|7 / 11|
| **Semaphore**|1,146,008|816,249|20|1,048,576|63,000 (browser)|1,320|-|40 / 176|
| **Hopper**|<1mm|~660k|15|32,768|10,000 (iPhone 7)|~700|~65k|-| 
| **Tornado**|888,054|692,133|16|65,536|6,116 (browser)|-|-|10 / 32|
| **Heiswap**|<200,000|~1,250,000|n/a|n/a|-|-|-|-|

|  |**Miximus**|**Semaphore**|**Hopper**|**Tornado**|**Heiswap**|
|---|---:|---:|---:|---:|---:|
| Deposit Cost | 1,886,209 |1,146,008|<1mm|888,054|<200,000|
| Withdraw Cost | 605,181 |816,249|~660k|692,133|~1,250,000|
| MT Depth | 29 |20|15|16|n/a|
| MT Max items | 536,870,912 |1,048,576|32,768|65,536|n/a|
| AVG Proving time (ms) | 3,600 (native) |63,000 (browser)|10,000 (iPhone 7)|6,116 (browser)|-|
| Constraints per-level |812|1,320|~700|-|-|
| Constraints overhead | - |-|~65k|-|-|
| User Keys (zipped / expanded) (mb) | 7 / 11 | 40 / 176 |-|10 / 32|-|


## Snark based

 * TODO: https://github.com/apguerrera/MinimalMixer

### Miximus 

The following are based on the initial Miximus code by BarryWhiteHat

* https://github.com/HarryR/ethsnarks-miximus
* https://github.com/barryWhiteHat/miximus
* https://github.com/AntoineRondelet/snark-mixer

However, currently, only the ethsnarks-miximus project is being actively maintained and developed. It (ethsnarks-miximus) serves as the basis (with some modification) for the Hopper project.

### Ethsnarks-Miximus

Development Status: Active
Deployed to: - 

 * Library: ethsnarks
 * Merkle-Tree algorithm: MiMC
 * Browser-compatible: Yes, via [Emscripten build](https://github.com/Ethsnarks/miximus-emscripten) - with 10x proving time :thumbsdown: 
 * Relayer support: [not yet](https://github.com/HarryR/ethsnarks-miximus/issues/13)
 * Vitalik-spec-compatible: [not yet](https://github.com/HarryR/ethsnarks-miximus/issues/14)
 * serves as inspiration for 

### Semaphore Mixer

Development Status: Active
Deployed to: Kovan 

* [Front end](https://github.com/weijiekoh/mixer)
* [General Spec](https://hackmd.io/qlKORn5MSOes1WtsEznu_g)
* based on [Semaphore](https://github.com/kobigurk/semaphore)
* State of the project: Should be releasing a POC in the next few weeks
* Loose Team: Barry Whitehat, Kobi, Wei Jie, Lakshman Sankar
* Funded by an EF grant (?)
* library: snarkjs/circom
* Relayer implementation: working on a [burn relay registry](https://ethresear.ch/t/burn-relay-registry-decentralized-transaction-abstraction-on-layer-2/5820)
----
### Hopper 

Development Status: Active
Deployed to: Mainnet

* https://github.com/argentlabs/hopper
* [Intro Medium Post](https://medium.com/argenthq/introducing-hopper-mobile-web-friendly-privacy-for-ethereum-d02a8c400dad): for now it is a mobile-only mixer. 
* Made by argent team: Itamar, Olivier 
* Library: ethsnarks (uses [MiMC](https://eprint.iacr.org/2016/492))
* Amount limited to 1 ETH in, 1 ETH out
* funded as an internal project (?)
* Relayer implementation: https://github.com/argentlabs/hopper/pull/3
* NOTE: Relayer can potentially be [front run](https://github.com/argentlabs/hopper/blob/master/solidity/contracts/Mixer.sol#L122)
---
### K0

Development Status: Active
Deployed to: --

* https://github.com/appliedblockchain/k0
* [Technical Description](https://appliedblockchain.com/k0.pdf)
* Based on [Zerocash](zerocash-project.org/media/pdf/zerocash-extended-20140518.pdf)


---
### Zeth

 Development Status: prototype
 Deployed to: --
 
* http://github.com/clearmatics/zeth
* https://arxiv.org/abs/1904.00905

---
### Tornado Mixer 

Development Status: Active
Deployed to: Live on Kovan, Mainnet  (limit .1 ETH)

* Mainnet address: [0x94A1B5CdB22c43faab4AbEb5c74999895464Ddaf](https://etherscan.io/address/0x94A1B5CdB22c43faab4AbEb5c74999895464Ddaf)
* Kovan Address: [0x1Cea940cA15a303A0E01B7F8589F39fF34308DB2](https://kovan.etherscan.io/address/0x1Cea940cA15a303A0E01B7F8589F39fF34308DB2)
* https://github.com/peppersec/tornado-mixer
* Team: [Peppersec](https://peppersec.com/) (Roman 1, Roman 2, Alex)
* Received Moloch grants recently for [future updates](https://paper.dropbox.com/doc/MGP-21-Tornado.Cash-UspFxL4pTJ7ioxDtrvWvt) to the mixer and past work
* Serverless, executed entirely in the browser (except relays)
* Circom, Websnark prover
* Fixed deposit amount
* MiMC hash
* Pedersen (nullifier, secret) commitments as leaves
* Relayer support with adjustable fees
* Constraints = 1869 + 1325 * tree_depth
Deposit gas = 43381 + 50859 * tree_depth
Proof time = 1071 + 347 * tree_depth ms
* 28,369 Constraints
1,060,561 Gas deposit
8,011 ms proof time

---

## Ring Signature based

### Heiswap
Development Status: Active / Prototype 
Deployed to: Ropsten

* https://github.com/kendricktan/heiswap-dapp
* [Introducing Heiswap post](https://kndrck.co/posts/introducing_heiswap/)
* Heiswap (黑 swap) is an Ethereum transaction mixer that ultilizes parts of CryptoNote to enable zero-knowledge transactions.
* Uses Ring Signatures and pseudo-stealth addresses to achieve its zero-knowledge properties
* The deployed smart contract handles the signature verification, while the client is responsible for generating the pseudo-stealth address.
* [Relayer for gasless withdrawals](https://github.com/kendricktan/heiswap-relayer)

### Ethereum-RingCT

Status: unmaintained

* https://github.com/vwvw/Ethereum-RingCT

> This repository contain the work done during the spring semester of 2017 as part of an Introduction to Research in Computer Science at ETH Zürich. I was helped and advised by Dr Arthur Gervais.
---
### RingToken

Status: unmaintained

 * https://github.com/sontol/RingToken
---
### SolidBlu1992

Status: ??? 

A collection of ring-signature based confidential transaction kernels created by 'SolidBlu1992'

 * https://github.com/solidblu1992/ethereum/tree/master/SimpleRingMixer
 * https://github.com/solidblu1992/ethereum/tree/master/CToEToken
 * https://github.com/solidblu1992/ethereum/tree/master/CToETokenV3
 * https://github.com/solidblu1992/PlasmaCT
 * https://github.com/solidblu1992/RingCTToken
---
### Mobius

Status: unmaintained

 * https://github.com/clearmatics/mobius
 * "Trustless Tumbling for Transaction Privacy"
---

### Laundromat

Status: Unmaintained

* https://github.com/blackyblack/laundromat

---

## Others

### ShareLock

   * [Mixing for Cryptocurrencies from Multiparty ECDSA](https://eprint.iacr.org/2019/563)
   * [EthResearch Post](https://ethresear.ch/t/sharelock-mixing-for-cryptocurrencies-from-multiparty-ecdsa/5525) 
   * [GitHub Post](https://github.com/KZen-networks/ShareLock)

> Instead of ring signatures being computed on-chain, this scheme uses an interactive aggregate signature mechanism to sever the link between depositor and withdrawee. This means it costs the same as a regular ethereum transaction without any additional on-chain overhead.


---
# Recommendations for continued efforts

1. **USER AWARENESS / ADOPTION** of mixers will be crucial for sustained success. Without a large and consistently fresh sets of deposits / withdrawals, the anonymity set will become stale and "capturable". Proposals may include: 

    * Signaling from high profile organisations (ie, EF or MakerDAO) or individuals encouraging use. 
    * Mixer pen-testing sessions with bounties for the most successful deanonymisation techniques. 
    * Organising mixing schemes at large events (Ethglobal hackathons, DevCon).

2. **USER EDUCATION** on the general tradeoffs between various mixer implementations. Users should be made aware in general that transactions can still leak privacy via IP address or Dapp cookies. Tor can help with this and should be recommended. Mixers should emphasise on front-ends or documentation that accounts should not be reused after interacting. Teams should be honestly present the levels of possible privacy and the tradeoffs made between implementations.

3. Points 1 and 2 can be significantly aided by an **integrating mixing functions into popular wallets**. User initiated privacy can leak identifying information over time by accidentally sending between addresses used before and after mixing. To avoid this, mixing should ideally occur pseudo-randomly, in the background, without the user's direct involvement. That's not say users shouldn't be able to initiate, but ideally would bias toward minimal effort on their part. It is a baseline assumption that addresses should not be reused. This can be accomplished through a properly considered UI that automatically retires (eg. hides) addresses once it is swept of funds. Finally, automatic modals  warning users of possible account linkage before allowing transaction sends. 
4. Reducing the gas cost of onchain precompiles like [EIP-1108](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-1108.md) is a significant step forward for all privacy initiatives. Developers should continue collaborating to make the public Ethereum chain a welcoming place for effective privacy. The EF and other interested parties should sponsor EIP champions if needed to shepherd upgrades through to mainnet.

5. **Relayers**: Relay networks like [Gas Station Network](https://blog.openzeppelin.com/build-your-app-with-the-gas-station-network/) but more decentralised. Will need additional research on what the difference in relayer types are
    * https://ethresear.ch/t/burn-relay-registry-decentralized-transaction-abstraction-on-layer-2/
    * https://github.com/lsankar4033/micromix_relayer

7. **Trusted setup** of each SNARK circuit is a significant overhead. There will need to be some coordination on how to best accomplish this until mechanisms like [SONIC](https://eprint.iacr.org/2019/099) and [PLONK](https://eprint.iacr.org/2019/953.pdf) are better developed.
8. Make use of existing financial support: [POA: Zero Knowledge Fund](https://forum.poa.network/t/introducing-the-poa-zero-knowledge-fund/2698), [MolochDAO](https://molochdao.com/), and other Grant giving organisations.
9. Explore incentivised mixer mechanisms. For example, incentivising anonymity set participation. (more research needed to determine viability, some [arguments against here](https://ethresear.ch/t/against-anonymity-pool-incentives/6011).
10. A strong focus on **security auditing and standardizing cryptographic components** would be beneficial for current and future efforts. (CIRCOM lib audit, websnark - prover written in WAss., solidity verifier audit)
11. UX studies of which factors are important (which numbers  displayed) (# since last withdrawal vs deposit)
12. Experiment with [xDAI chain](https://poa.network/xdai) for more complex experiments (fewer gas limitations)
