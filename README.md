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
**Definition:**  

**Why it exists:** 

**Key mechanics:** 

**Major risks:**

---

### Bridges & Interop
**Definition:**  

**Why it exists:** 

**Key mechanics:** 

**Major risks:**

---

### Yield Aggregation / Vaults
**Definition:**  

**Why it exists:** 

**Key mechanics:** 

**Major risks:**

---

### Restaking & AVS 
**Definition:**  

**Why it exists:** 

**Key mechanics:** 

**Major risks:**

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