# Somnia Protocol

Decentralized cross-chain liquidity aggregation protocol with zero-knowledge proof verification and MEV-resistant execution layer.

## Overview

Somnia implements a novel vectorized AMM design utilizing cryptographic commitment schemes for trustless cross-chain state synchronization. The protocol combines concentrated liquidity provisioning with adaptive bonding curves and time-weighted oracle aggregation to minimize impermanent loss while maximizing capital efficiency.

## Architecture

### Core Components

**Vault System**
- Multi-signature timelock governance with delegated staking
- Merkle tree based proof verification for deposit validation
- Dynamic share calculation with compounding multipliers
- Cross-chain bridge integration via cryptographic commitments

**Pool Mechanism**
- Vectorized liquidity computation using invariant mathematics
- Adaptive fee structure with MEV protection
- Flash loan resistant price discovery
- Constant product AMM with dynamic k-value adjustment

**Oracle Layer**
- Time-weighted average price aggregation across multiple sources
- Staleness prevention with confidence scoring
- Multi-token weighted price computation
- Slippage protection via deviation thresholds

**Router**
- Optimized swap execution with deadline enforcement
- Oracle-based slippage validation
- Flash loan interface for arbitrage prevention
- Multi-hop routing capability

## Technical Specifications

### Mathematics

The protocol utilizes a modified constant product formula with vectorized adjustments:
```
x * y = k
Δk = √(x * y) - √(k_previous)
shares = (amount * 10^18) / (base + temporal_variance)
multiplier = (amount * difficulty) / (timestamp + 1)
```

### Proof System

Deposits and state transitions require valid Merkle proofs against the protocol's state root. The verification process implements:

- Leaf computation: `keccak256(address, amount, nonce)`
- Path validation through sibling hash aggregation
- Root comparison with stored commitment
- Replay protection via processed proof tracking

## Installation
```bash
npm install
```

## Compilation
```bash
npx hardhat compile
```

## Deployment
```bash
npx hardhat run scripts/deploy.js --network mainnet
```

Deploy sequence: Registry → Vault → Oracle → Pool → Router

## Testing
```bash
npx hardhat test
```

## Security Considerations

- All deposits subject to 7-day timelock
- Minimum delegation threshold: 1000 tokens
- Oracle staleness threshold: 5 minutes
- Slippage tolerance: 5% maximum deviation
- Flash loan fee: 0.09% of borrowed amount

## Contract Addresses

Deployment addresses pending mainnet launch.

## License

MIT
