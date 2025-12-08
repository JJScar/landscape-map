# The DeFi Landscape Map
A high-level map of the DeFi ecosystem organized by category, purpose, and major protocols.

| Lending & Borrowing | DEX / AMMs | Derivatives & Perpetuals | Stablecoins | Bridges & Interop | Yield Aggregation / Vaults | Restaking & AVS | Oracles | Liquid Staking & LSDfi | RWA | Insurance / Risk Markets | MEV & Execution Layer Products | Infrastructure Middleware | Account Abstraction / Smart Wallets | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| [Money Markets](./Lending&Borrowing.md), [Isolated Lending Markets](./Lending_Borrowing/IsolatedLendingMarkets.md) | [CLMM](./DEX_AMM/CLMM.md), [CPMM](./DEX_AMM/CPMM.md), [Curve-StableSwap](./DEX_AMM/CurveStyleStableswap.md), [Intent Based](./DEX_AMM/Intent_based_RFQ.md) | [Options](./Derivatives_Perps/Options.md), [Perps](./Derivatives_Perps/Perps.md), [Structured Products](./Derivatives_Perps/StructuredProducts.md) | [Algorithmic/Hybrid](./Stablecoins/AlgorithmicHybrid.md), [Fiat Backed](./Stablecoins/FiatBacked.md), [Overcollateralized](./Stablecoins/Overcollateralized.md) | [Light Client / ZK](./Bridges_Interoperability/Light_client_zk.md), [Messaging](./Bridges_Interoperability/Messaging.md) | [All](./Yield%20Aggregators_Vaults/All.md) | [All](./Restaking_AVS/All.md) | [All](./Oracles/All.md) | [LSDfi](./Liquid%20Staking_LSDfi/LSDfi.md), [LST Providers](./Liquid%20Staking_LSDfi/) | N/A | [All](./Insurance_Risk%20Markets/All.md) | [All](./MEV_Execution%20Layer/All.md) | [All](./Infrastructure_Middleware/All.md) | [All](./Account%20Abstraction%20_Smart%20Wallets/All.md) |

## High Level Breakdown of DeFi Categories
### Lending & Borrowing
**Definition:** Protocols that allow users to deposit assets to earn yield (lending) and borrow assets by providing collateral. They create decentralized credit markets where interest rates adjust algorithmically based on supply and demand.

**Why it exists:** To enable capital efficiency in DeFi: users can access liquidity without selling assets, leverage positions, or earn passive yield. Borrowers get liquidity; lenders get interest; the protocol acts as a trustless intermediary.

**Key mechanics:** 
- *Collateralized borrowing*: Users deposit tokens as collateral and can borrow up to a protocol-defined limit (LTV / collateral factor).
- *Interest rate models*: Dynamic rates (often kink-based) adjust based on utilization of each asset pool. 
- *Liquidation engine*: If a borrower’s health factor drops below a threshold, positions are liquidated by external liquidators or internal mechanisms. 
- *Oracle pricing*: Asset prices come from price feeds (Chainlink, Pyth, etc.) to determine collateral value.
- *Pool architectures*:
    - *Pooled lending*: All lenders share liquidity (Aave, Compound).
    - *Peer-to-pool optimization*: Routing deposits to improve yields (Morpho).
    - *Isolated pools*: Risk-limited markets for volatile assets (Fraxlend, Silo)

**Major risks:**
- *Oracle manipulation*: Fake price feeds can enable undercollateralized borrowing and bad debt.  
- *Liquidation failures*: Congestion or MEV effects can prevent timely liquidations.
- *Interest rate model bugs*: Incorrect math can cause runaway rates or frozen markets.
- *Bad collateral onboarding*: Listing risky assets can introduce systemic insolvency.
- *Reentrancy & accounting bugs*: Incorrect balance updates or rounding errors can allow fund theft or break invariants.
- *Cross-asset contagion*: Shared collateral pools can propagate insolvency across assets.

---

### DEX / AMMs
**Definition:** Decentralized exchanges (DEX) that allow trustless trading of assets by using algorithmic pricing rather than order books. Liquidity is provided by users (LPs) and trades are executed directly on-chain or via intent/RFQ systems.

**Why it exists:** To enable permissionless, always-on trading without centralized intermediaries. AMMs solve the order book liquidity problem on blockchains by replacing market makers with mathematical pricing curves and incentivized liquidity providers.

**Key mechanics:** 
- *Pricing curves*:
    - Constant Product (x*y=k) — Uniswap V2, many forks
    - Concentrated Liquidity (CLMM)— Uniswap V3, Algebra
    - Stableswap (low slippage around a peg) — Curve
    - Custom / Hybrid curves — Maverick, Balancer

- *Liquidity pools*: Users deposit token pairs or baskets and earn trading fees (and sometimes emissions). The pool’s invariant defines how prices move as reserves change.

- *Swapping*:
    - Traders swap one asset for another by interacting with the pool, changing reserves and implicitly adjusting price.
    - Multihop routing allows trades across multiple pools.

- *Fees & incentives*:
    - Swap fees distributed to LPs.
    - Optional liquidity mining or boosted curves.

- *Advanced DEX architectures*:
    - Intent-based / RFQ - Traders express “what they want”, solvers fill the order (CowSwap, UniswapX, 1inch Fusion).
    - Hybrid AMMs - Combine concentrated liquidity, dynamic curves, or external price data for improved efficiency.

**Major risks:**
- *Price manipulation*: AMMs are highly manipulable within single blocks — used to exploit lending protocols, oracles, or pool accounting.
- *Impermanent loss*: LPs can lose value relative to holding assets, especially during high volatility.
- *Invariant violations / math bugs*: Rounding errors, unsafe math, or broken invariant logic can cause value leakage or pool insolvency.
- *Liquidity migration or fragmentation*: Liquidity spread across chains/pools reduces efficiency and increases oracle attack surface.
- *Concentrated liquidity risks*: 
    - Mispositioned liquidity, toxic flow, or tick math bugs.
    - Incorrect handling of range orders or tick crossing.
- *Flash loan interactions*: AMM state can be fully manipulated inside a single transaction, interacting dangerously with protocols using AMM prices.

---

### Derivatives & Perpetuals
**Definition:** Protocols that allow users to trade synthetic exposure to assets—such as perpetual futures, options, or structured products—without holding the underlying tokens. They provide leveraged long/short positions, volatility trading, or yield strategies through smart contracts.

**Why it exists:** To enable leveraged speculation, hedging, volatility trading, and sophisticated financial strategies that traditional AMMs or lending markets cannot provide. Derivatives deepen market efficiency and enable price discovery across crypto assets.

**Key mechanics:** 
- *Perpetual Futures (Perps)*:
    - Traders open long/short positions using collateral.
    - Funding rates keep perp prices aligned with the spot price.
    - Two main models:
        - vAMM / Virtual Liquidity (dYdX v3, Perpetual Protocol v1)
        - Oracle-driven, vault-backed perps (GMX, Hyperliquid, Drift)

- *Options Protocols*:
    - Call/put options represented on-chain (European/American).
    - Liquidity provided via AMMs or order books (Lyra, Premia).
    - Greeks and implied volatility drive pricing.

- *Structured Products*: 
    - Pre-packaged yield or hedging strategies using options or perps (Ribbon, Opyn).
    - Often implement vaults that auto-roll strategies.

- *Collateral & Liquidations*: 
    - Positions are backed by margin; if value falls below a threshold, they are liquidated.
    - Oracle accuracy and latency are critical to fair liquidation.

- *Oracles*:
    - Price feeds (Chainlink, Pyth, internal TWAPs) determine collateral value, PnL, and liquidation levels.

**Major risks:**
- *Oracle attacks*: Manipulated prices can misprice funding, liquidations, or PnL accounting, leading to insolvency.
- *Liquidity shortfalls / bad debt*: Vault-backed perps (e.g., GMX) can accrue losses if traders consistently win or if oracle updates lag.
- *Funding rate exploits*: Incorrect funding calculations can incentivize toxic flow or create opportunities for cyclical manipulation.
- *Complex accounting bugs*: Derivatives require accurate margin, PnL, and position accounting. Errors can create or destroy value unintentionally.
- *Greeks & volatility mispricing (options)*: Mathematical bugs in Black-Scholes models, implied volatility updates, or AMM curves can cause structural losses.
- *Liquidation engine failures*: Latency, MEV interference, or incorrect thresholds can cause cascading insolvency during volatility spikes.
- *Cross-contract interactions*: Derivatives often compose with lending, AMMs, LSTs, and oracles, dramatically increasing surface area.

---

### Stablecoins
**Definition:** Tokens designed to maintain a stable value (usually pegged to USD) through collateralization, algorithmic mechanisms, or backing by real-world assets. They serve as the primary unit of account and liquidity foundation in DeFi.

**Why it exists:** Stablecoins provide price stability in an otherwise volatile crypto environment, enabling predictable trading, borrowing, lending, and payments. They act as the settlement layer for most DeFi transactions and are essential for risk management, on-chain liquidity, and capital efficiency.

**Key mechanics:** 
- *Overcollateralized (Crypto-Backed)*:
    - Users deposit volatile assets (ETH, BTC, LSDs) and mint stablecoins against them.
    - Stability enforced via liquidation systems and interest/stability fees.
    - Examples: MakerDAO (DAI), Liquity (LUSD).

- *Algorithmic / Hybrid Models*: 
    - Use on-chain algorithms, bonding curves, or multi-token systems to maintain peg.
    - Frax uses fractional collateral + algorithmic supply adjustment.
    - Historically the riskiest category.

- *Fiat-Backed / Custodial*:
    - Backed 1:1 by off-chain assets (USD reserves, treasuries).
    - Requires trust in issuers and custodians; highly scalable.
    - Examples: USDC, USDT, PYUSD.

- *Stability Mechanisms*:
    - Collateral ratios / LTVs determine minting capacity.
    - Liquidations ensure solvency when collateral value drops.
    - Oracle feeds provide price data for collateral and redemption.
    - Redemption mechanisms allow arbitrage when price diverges from $1.
    - Stability fees / interest influence supply/demand dynamics.

- *Peg Maintenance Tools*:
    - Arbitrage incentives (mint/burn, redeem).
    - Market operations (buyback/burn, yield adjustments).
    - Risk modules for collateral onboarding.

**Major risks:**
- *Collateral risk*:
    - Crypto-backed stables depend on volatile collateral.
    - Bad collateral onboarding can lead to protocol insolvency.

- *Oracle manipulation*: Mispriced collateral → undercollateralized minting → bad debt.

- *Depegging events*:
    - Liquidity holes or bank-run dynamics can break the $1 peg.
    - Historical failures: UST, FEI (partial), USDC depeg during banking crisis.

- *Redemption mechanism failures*: If redemption is paused, slow, or too costly, peg can’t be restored.

- *Custodial/Regulatory risk (Fiat-backed)*: Bank failures, frozen reserves, regulatory actions.

- *Algorithmic instability:* 
    - Reflexive feedback loops can cause death spirals.  
    - Difficult to verify mathematically; dynamic systems are fragile.

- *Liquidity fragmentation*: Peg maintenance becomes harder when liquidity is spread across chains or pools.

- *Systemic risk propagation*: Stablecoin failures often cascade across DeFi (AMMs, lending, perps, collateralized systems).

---

### Bridges & Interoperability

**Definition:**  
Protocols that enable the transfer of data, messages, or assets across different blockchains. They act as communication layers that allow cross-chain tokens, state synchronization, and multi-chain applications.

**Why it exists:**  
Because blockchains are isolated environments with no native way to trust information from other chains. Bridges allow liquidity, applications, and users to move freely across ecosystems, enabling multi-chain DeFi, shared liquidity, modular architectures, and unified user experiences.

**Key mechanics:**  
- *Message Passing*: 
  - Bridges relay arbitrary messages (not just tokens) between chains.  
  - Used for cross-chain governance, swaps, and multi-chain app logic.  
  - Examples: LayerZero, Wormhole, Axelar.

- *Light Client / Verification-Based Bridges*:
  - One chain verifies the consensus of another chain (e.g., zk/light-client bridges).  
  - Highest security because no external trust is required.  
  - Examples: zkSync zkBridge, Succinct-built bridges.

- *Lock-and-Mint / Burn-and-Mint Models*:
  - Assets are locked on the source chain and minted on the destination chain.  
  - On return, wrapped tokens are burned and underlying assets released.  
  - Common for older or simpler bridges.

- *Validator / Relayer Sets*:
  - Off-chain entities observe chain A and submit proofs or attestations to chain B.  
  - Security depends on the honesty threshold of the committee.

- *Unified Liquidity Networks*:
  - Liquidity providers facilitate cross-chain swaps without locking assets.  
  - Examples: Stargate (powered by LayerZero), Connext (before pivot).

- *Security Models*:
  - Verification-based: chain verifies the state transition proofs (safest).  
  - External validator sets: rely on multisigs or committees.  
  - Optimistic verification: assume correctness unless challenged.  
  - Liquidity networks: rely on LP honesty + slippage controls.

**Major risks:**  
- *Bridge hacks (most common failure in crypto)*:
  - Over $2B+ lost historically due to compromised validator sets or multisig keys.  
  - Example: Ronin, Wormhole (later reimbursed), Nomad.

- *Message forgery / replay attacks:*
  - Incorrect or missing signature verification.  
  - Poor nonce or state tracking leads to duplicated or forged messages.

- *Light client implementation bugs:* 
  - Incorrect consensus verification logic can break trust assumptions.

- *Liquidity exhaustion / trapped funds:*
  - Lock-and-mint designs rely heavily on proper accounting of minted vs. locked tokens.

- *Cross-chain reentrancy:*
  - Bridged contracts can cause asynchronous execution attacks.

- *Incorrect assumptions about finality:*  
  - Fast chains may reorganize, breaking message integrity.

- *Wrapped asset risk:* 
  - If the original chain or bridge fails, wrapped tokens can become worthless.

- *Inter-protocol contagion:*  
  - A bridge compromise can cascade across all protocols using the bridged asset.

---

### Yield Aggregation / Vaults
**Definition:** Protocols that automatically allocate user funds across multiple DeFi strategies to maximize yield, manage risk, or optimize capital efficiency. Users deposit assets into vaults and receive shares representing proportional ownership of the underlying strategies.

**Why it exists:** Because actively managing yield in DeFi is complex, time-consuming, and gas-intensive. Yield aggregators abstract strategy selection, compounding, and rebalancing, allowing users to earn optimized returns without manual intervention.

**Key mechanics:** 
- *Vault shares*:
  - Depositors receive shares representing a claim on the vault’s assets.
  - Share price increases as yield is generated.

- *Strategies*:
  - Vaults deploy capital into one or more strategies (lending, AMMs, staking, farming).
  - Strategies can be upgraded, rotated, or paused based on performance or risk.

- *Auto-compounding*: Rewards are periodically harvested and reinvested to increase effective APY.

- *Allocation logic*: Capital may be split dynamically across strategies based on utilization, risk limits, or governance decisions.

- *Role separation*:
  - Vault handles deposits, withdrawals, accounting.
  - Strategy contracts interact with external protocols.

- *Governance & permissions*:
  - Strategy changes often gated by multisigs or DAOs.
  - Emergency shutdown mechanisms are common.

- *Composable primitives*: Strategies may stack multiple protocols (e.g., deposit → lend → LP → stake LP → borrow → loop).

**Major risks:**
- *Composability risk*:
  - Vaults inherit all risks of underlying protocols (lending, AMMs, bridges, oracles).
  - A bug in any dependency can break the system.

- *Share accounting bugs*:
  - Incorrect mint/burn logic can allow value extraction or dilution of other users.
  - Rounding issues during deposits/withdrawals are especially dangerous.

- *Strategy misbehavior*:
  - Strategies returning incorrect values can corrupt vault accounting.
  - Lack of invariants around totalAssets vs shares is a common failure mode.

- *Reentrancy via external protocols*: External calls during harvests, deposits, or withdrawals can be exploited.

- *Liquidity mismatch*: Vault assets may be locked, staked, or illiquid, causing withdrawal failures under stress.

- *Governance attack risk*: Malicious or compromised governance can swap strategies and drain funds.

- *Upgrade & emergency logic bugs*: Emergency shutdowns are rarely tested but used during crisis situations.

- *Economic griefing*: MEV front-running deposits/withdrawals to extract value from vault share math.

---

### Restaking & AVS 
**Definition:** Protocols that allow users to stake assets (primarily ETH or ETH-derived tokens) while retaining liquidity or reusing the same economic security across multiple systems. Liquid Staking Derivatives (LSDs) tokenize staked positions, while restaking protocols extend staked security to additional services (AVSs).

**Why it exists:** Staking traditionally locks capital and limits composability. LSDs unlock liquidity by issuing transferable tokens that represent staked assets. Restaking further increases capital efficiency by allowing the same staked ETH to secure multiple protocols, creating shared security layers and new economic primitives.

**Key mechanics:** 
- *Liquid Staking (LSDs)*:
  - Users stake ETH via a protocol and receive a liquid token (e.g., stETH, rETH, eETH).
  - The token accrues staking rewards and can be used as collateral across DeFi.
  - Unstaking may involve delays or exit queues.

- *Restaking*:
  - LSDs or native ETH are restaked to secure additional services (AVSs — Actively Validated Services).
  - Validators opt into extra slashing conditions beyond Ethereum consensus.

- *Reward Distribution*:
  - Rewards come from Ethereum staking + AVS fees or emissions.
  - Protocols must accurately track yield across layers and distribute it to holders.

- *Slashing & Risk Attribution*:
  - Misbehavior at any layer (Ethereum or AVS) can result in slashing. 
  - Risks compound across stacked services.

- *Delegation & Operator Sets*:
  - Validators or operators manage restaked capital.
  - Users delegate stake and rely on operator performance and honesty.

- *LSDfi*: Derivative protocols built on LSDs (e.g., Pendle, Lybra, Prisma) provide leverage, yield splitting, or stablecoins.

**Major risks:**
- *Risk stacking / correlated slashing*:
  - Multiple AVSs can slash the same underlying stake, amplifying losses.
  - Hard to reason about tail risk and correlated failures.

- *Slash condition complexity*:
  - Slashing logic is protocol-specific and difficult to formally reason about.
  - Errors in slash attribution can unjustly penalize users.

- *Accounting & reward bugs*: Incorrect reward calculation or distribution across layers can cause silent value leakage.

- *Liquidity illusions*: 
  - LSDs appear liquid but often rely on exit queues or secondary market liquidity. 
  - During stress, liquidity can vanish.

- *Oracle dependency*:
  - LSD prices rely on oracles or assumptions about redemption value.
  - Depegs can cascade across lending and derivatives protocols.

- *Validator and operator risk*: Centralization or misbehavior of operators can jeopardize funds.

- *Composable contagion*: LSDs are widely used as collateral, making failures systemic.

- *Governance & upgrade risk*: Rapid evolution of restaking designs increases upgrade frequency and attack surface.

---

### Oracles
**Definition:**  

**Why it exists:** 

**Key mechanics:** 

**Major risks:**

---

### Liquid Staking & LSDfi
**Definition:**  

**Why it exists:** 

**Key mechanics:** 

**Major risks:**

---

### RWA
**Definition:**  

**Why it exists:** 

**Key mechanics:** 

**Major risks:**

---

### Insurance / Risk Markets
**Definition:**  

**Why it exists:** 

**Key mechanics:** 

**Major risks:**

---

### MEV & Execution Layer Products
**Definition:**  

**Why it exists:** 

**Key mechanics:** 

**Major risks:**

---

### Infrastructure Middleware
**Definition:**  

**Why it exists:** 

**Key mechanics:** 

**Major risks:**

---

### Account Abstraction / Smart Wallets
**Definition:**  

**Why it exists:** 

**Key mechanics:** 

**Major risks:**

---