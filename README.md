# Somnia Protocol
```
 ____                        _       
/ ___|  ___  _ __ ___  _ __ (_) __ _ 
\___ \ / _ \| '_ ` _ \| '_ \| |/ _` |
 ___) | (_) | | | | | | | | | | (_| |
|____/ \___/|_| |_| |_|_| |_|_|\__,_|
                                      
Cross-Chain Liquidity Aggregation Protocol
```

Advanced decentralized liquidity provisioning infrastructure implementing zero-knowledge proof verification for trustless cross-chain asset bridging with dynamic fee optimization and MEV-resistant transaction ordering mechanisms.
```
┌─────────────────────────────────────────────────────────────────────────┐
│                          PROTOCOL ARCHITECTURE                          │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐            │
│  │   Vault      │◄───┤   Oracle     │◄───┤   Router     │            │
│  │   System     │    │  Aggregator  │    │   Layer      │            │
│  └──────┬───────┘    └──────┬───────┘    └──────┬───────┘            │
│         │                   │                   │                      │
│         └───────────────────┴───────────────────┘                      │
│                             │                                          │
│                      ┌──────▼───────┐                                  │
│                      │   Pool       │                                  │
│                      │ Mechanism    │                                  │
│                      └──────────────┘                                  │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

## Technical Overview

Somnia utilizes a hybrid AMM architecture combining concentrated liquidity ranges with vectorized pool mathematics and cryptographic commitment schemes for cross-chain state validation. The protocol employs EIP-2535 Diamond pattern for upgradeability while maintaining immutable core logic through Byzantine fault tolerant state transitions.

### Core Mechanisms

**Vault Architecture**
- Multi-signature timelock governance with delegated staking
- Merkle tree based proof verification for deposit validation  
- Dynamic share calculation implementing compounding multipliers
- Cross-chain bridge integration via cryptographic commitments

**Pool Mathematics**
- Vectorized liquidity computation using invariant mathematics
- Adaptive fee structure with MEV protection layers
- Flash loan resistant oracle-based price discovery
- Constant product AMM with dynamic k-value adjustment

**Oracle Aggregation**
- Time-weighted average price computation across multiple sources
- Staleness prevention with confidence scoring mechanisms
- Multi-token weighted price aggregation
- Slippage protection via deviation threshold validation

**Router Optimization**
- Optimized swap execution with deadline enforcement
- Oracle-based slippage validation pre-execution
- Flash loan interface for arbitrage prevention
- Multi-hop routing with gas optimization

## Mathematical Specifications

### Invariant Formula

The protocol implements a modified constant product formula with vectorized adjustments:
```
x * y = k

Δk = √(x * y) - √(k_previous)

shares = (amount × 10^18) / (base + temporal_variance)

multiplier = (amount × difficulty) / (timestamp + 1)
```

### Proof Verification

State transitions require valid Merkle proofs against the protocol state root:
```
leaf = keccak256(address, amount, nonce)

path_validation = ⊕(sibling_hashes)

root_comparison = computed_root == stored_root

replay_protection = processed_proofs[leaf] == false
```

### Fee Structure
```
base_fee = (amount × fee_numerator) / FEE_DENOMINATOR

adjusted_fee = base_fee × (1 - time_factor)

slippage = (expected_out - actual_out) / expected_out

valid_swap = slippage ≤ MAX_SLIPPAGE
```

## Repository Structure
```
somnia-protocol/
│
├── contracts/
│   ├── core/
│   │   ├── SomniaVault.sol          # Vault implementation
│   │   ├── SomniaPool.sol           # Pool mechanics
│   │   └── SomniaRouter.sol         # Routing logic
│   │
│   ├── oracle/
│   │   ├── OracleAggregator.sol     # Price aggregation
│   │   └── ChainlinkAdapter.sol     # External oracle adapter
│   │
│   ├── libraries/
│   │   ├── MerkleProofLib.sol       # Proof verification
│   │   ├── VectorMath.sol           # Mathematical operations
│   │   └── SafeCast.sol             # Type conversions
│   │
│   └── interfaces/
│       ├── IVectorizedPool.sol      # Pool interface
│       ├── IOracle.sol              # Oracle interface
│       └── IVault.sol               # Vault interface
│
├── scripts/
│   ├── deploy.js                    # Deployment script
│   └── verify.js                    # Contract verification
│
├── test/
│   ├── Vault.test.js                # Vault tests
│   └── Pool.test.js                 # Pool tests
│
├── hardhat.config.js
├── package.json
└── README.md
```

## Deployment Sequence
```
Registry → Vault → Oracle → Pool → Router
```

Each contract must be deployed in sequence with address dependencies resolved at deployment time.

## Security Parameters
```
TIMELOCK_DURATION        = 7 days
MIN_DELEGATION           = 1000 ether
STALENESS_THRESHOLD      = 5 minutes
FEE_DENOMINATOR          = 10000
FEE_NUMERATOR            = 30
MAX_SLIPPAGE             = 500 basis points
FLASH_LOAN_FEE           = 9 basis points
```

## Account Structures

### GlobalState
```solidity
struct GlobalState {
    Pubkey admin;
    u8 bump;
    u8 escrow_bump;
    u8 pool_bump;
    u32 next_index;
    bytes32 current_root;
    u8 root_history_idx;
    Vec<u8> zeroes;
    Vec<u8> filled_subtrees;
    Vec<u8> root_history;
    u64 total_deposited;
    u64 total_withdrawn;
}
```

### LiquidityPosition
```solidity
struct LiquidityPosition {
    u128 amount;
    u128 shares;
    u64 last_update;
    u64 multiplier;
}
```

### PriceData
```solidity
struct PriceData {
    u128 price;
    u64 timestamp;
    u64 confidence;
}
```

## Transaction Flow

### Deposit Mechanism
```
1. invoke deposit_with_proof(amount, commitment, proof)
2. verify merkle_proof(commitment, proof, root)
3. require processed_proofs[commitment] == false
4. transfer lamports: user → pool
5. insert commitment into merkle_tree
6. update pool accounting
7. emit deposit_event
```

### Withdrawal Mechanism
```
1. invoke prepare_withdraw(proof, nullifiers, outputs)
2. verify groth16_proof(public_inputs)
3. validate merkle_root in root_history
4. create nullifier_flags for inputs
5. calculate fees and validate thresholds
6. create prepared_tx PDA
7. execute_withdraw()
8. transfer lamports: pool → recipient
9. update accounting and validate invariants
```

### Swap Execution
```
1. validate deadline > current_timestamp
2. query oracle_price(token_in)
3. calculate expected_output
4. execute swap(amount_in, is_x)
5. validate slippage within threshold
6. validate oracle_deviation < MAX_DEVIATION
7. update reserves and k_value
8. emit swap_event
```

## Installation
```bash
npm install
```

## Compilation
```bash
npx hardhat compile
```

## Testing
```bash
npx hardhat test
```

## Deployment
```bash
npx hardhat run scripts/deploy.js --network mainnet
```

## Verification
```bash
npx hardhat run scripts/verify.js --network mainnet
```

## Configuration
```javascript
module.exports = {
  solidity: {
    version: "0.8.19",
    settings: {
      optimizer: {
        enabled: true,
        runs: 200
      }
    }
  },
  networks: {
    mainnet: {
      url: process.env.MAINNET_RPC_URL,
      accounts: [process.env.PRIVATE_KEY]
    }
  }
}
```

## Security Invariants
```
CRITICAL INVARIANTS:

1. total_deposited >= total_withdrawn
2. pool.balance >= sum(outstanding_positions)
3. nullifier_used[n] implies note_spent[n]
4. merkle_root in root_history
5. proof_valid implies state_transition_valid
```

## Attack Mitigations

| Vector | Mitigation |
|--------|------------|
| Double-spending | Nullifier flags with permanent on-chain markers |
| Merkle forgery | Groth16 verification with root history validation |
| Amount overflow | Checked arithmetic with circuit range validation |
| Pool drainage | Strict accounting invariants with pre-transfer checks |
| Replay attacks | Nonce-based PDA with executed flag tracking |
| MEV extraction | PreparedTx PDA locks recipient and amounts |
| Oracle manipulation | Multi-source aggregation with confidence scoring |
| Front-running | Two-phase withdrawal with commitment locking |

## Constants

| Constant | Value | Description |
|----------|-------|-------------|
| TREE_DEPTH | 26 | Merkle tree depth |
| ROOT_HISTORY | 1000 | Historical root buffer size |
| MIN_DELEGATION | 1000 | Minimum delegation amount |
| TIMELOCK_DURATION | 604800 | 7 days in seconds |
| STALENESS_THRESHOLD | 300 | 5 minutes in seconds |
| FEE_DENOMINATOR | 10000 | Fee calculation denominator |
| FEE_NUMERATOR | 30 | 0.3% fee |
| MAX_SLIPPAGE | 500 | 5% maximum slippage |

## Error Codes

| Code | Name | Description |
|------|------|-------------|
| 6000 | InvalidProof | Proof verification failed |
| 6001 | Overflow | Arithmetic overflow detected |
| 6002 | TreeFull | Merkle tree at capacity |
| 6003 | UnknownRoot | Root not in history |
| 6004 | NullifierAlreadyUsed | Double-spend attempt |
| 6010 | InsufficientAmount | Amount below threshold |
| 6012 | InsufficientFunds | Pool cannot cover withdrawal |
| 6013 | Unauthorized | Caller not authorized |
| 6014 | InvariantViolation | Accounting invariant broken |


