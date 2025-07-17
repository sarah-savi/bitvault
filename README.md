# BitVault Pro - Advanced Collateralized Stablecoin Engine

[![Clarity](https://img.shields.io/badge/Clarity-3.0-orange.svg)](https://docs.stacks.co/clarity)
[![Stacks](https://img.shields.io/badge/Stacks-Blockchain-purple.svg)](https://stacks.co)

## Overview

BitVault Pro is a next-generation DeFi protocol that transforms Bitcoin into a stable digital asset through sophisticated collateral management and automated market making on the Stacks blockchain. The protocol enables users to unlock Bitcoin's value while maintaining exposure to the world's premier cryptocurrency through advanced algorithmic stability mechanisms and intelligent liquidation protocols.

## Key Features

- **Multi-tiered Collateral Ratios**: Automatic adjustment based on market conditions
- **Sophisticated AMM**: Minimal slippage protection with optimized liquidity pools
- **Real-time Price Oracle**: Integration with fail-safe mechanisms for reliable price feeds
- **Yield Optimization**: Strategic liquidity allocation for maximum returns
- **Enterprise Security**: Advanced risk assessment and automated rebalancing
- **Cross-chain Ready**: Framework designed for future multi-chain expansion

## System Overview

BitVault Pro operates as a collateralized debt position (CDP) system where users can:

1. **Deposit Bitcoin** as collateral into secure vaults
2. **Mint stablecoins** against their BTC collateral (minimum 150% collateralization)
3. **Participate in liquidity pools** to earn yield on their assets
4. **Trade efficiently** through the integrated automated market maker (AMM)

### Core Components

```text
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│  Collateral     │    │   Stablecoin    │    │   Liquidity     │
│     Vaults      │◄──►│     Minting     │◄──►│     Pools       │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         └───────────────────────┼───────────────────────┘
                                 │
                   ┌─────────────────┐
                   │  Price Oracle   │
                   │   & Security    │
                   └─────────────────┘
```

## Contract Architecture

### Data Structures

#### State Variables

- `contract-initialized`: Protocol initialization status
- `oracle-price`: Current BTC/USD price with 6 decimal precision
- `total-supply`: Total stablecoin supply in circulation
- `pool-btc-balance`: BTC reserves in the AMM pool
- `pool-stable-balance`: Stablecoin reserves in the AMM pool

#### Storage Maps

- **`balances`**: User BTC balance tracking
- **`stablecoin-balances`**: User stablecoin balance tracking
- **`collateral-vaults`**: Individual user vault data (collateral, minted amounts, timestamps)
- **`liquidity-providers`**: LP position tracking (pool tokens, provided amounts)

### Core Functions

#### Collateral Management

- `deposit-collateral`: Secure BTC deposit into user vaults
- `mint-stablecoin`: Generate stablecoins against deposited collateral
- `burn-stablecoin`: Reduce debt and free up collateral

#### Liquidity Operations

- `add-liquidity`: Contribute to AMM pools for yield generation
- `remove-liquidity`: Withdraw liquidity and earned fees

#### Administrative

- `initialize`: One-time protocol setup with initial BTC price
- `update-price`: Oracle price feed updates (owner only)

#### Read-Only Queries

- `get-vault-details`: Retrieve user vault information
- `get-collateral-ratio`: Calculate current collateralization ratio
- `get-pool-details`: AMM pool statistics and reserves
- `get-lp-details`: Liquidity provider position data

## Data Flow

### Minting Process

```text
User BTC Deposit → Vault Creation → Collateral Verification → Stablecoin Minting
```

1. User deposits BTC (minimum 0.01 BTC)
2. System creates or updates collateral vault
3. Validates minimum 150% collateralization ratio
4. Mints stablecoin tokens to user balance
5. Updates total supply and vault records

### Liquidity Provision Flow

```text
Asset Deposit → LP Token Calculation → Pool Update → Position Recording
```

1. User provides BTC and stablecoin pairs
2. System calculates proportional LP tokens
3. Updates pool reserves and total liquidity
4. Records provider position for future withdrawals

### Risk Management

```text
Price Update → Collateral Ratio Check → Liquidation Trigger (if < 130%)
```

## Security Features

### Collateralization Requirements

- **Minimum Ratio**: 150% (safe threshold)
- **Liquidation Ratio**: 130% (automatic liquidation trigger)
- **Minimum Deposit**: 0.01 BTC (1,000,000 satoshis)

### Safety Mechanisms

- Price validation against maximum bounds
- Atomic operations for balance updates
- Comprehensive error handling
- Integer overflow protection
- Access control for administrative functions

### Error Codes

- `1000`: Unauthorized access
- `1001`: Insufficient balance
- `1002`: Invalid amount
- `1003`: Insufficient collateral
- `1004`: Pool empty
- `1005`: Slippage too high
- `1006`: Below minimum threshold
- `1007`: Above maximum threshold
- `1008`: Already initialized
- `1009`: Not initialized
- `1010`: Invalid price

## Getting Started

### Prerequisites

- [Clarinet](https://github.com/hirosystems/clarinet) for Stacks development
- Node.js 16+ for testing environment
- Git for version control

### Installation

1. **Clone the repository**

   ```bash
   git clone https://github.com/sarah-savi/bitvault.git
   cd bitvault
   ```

2. **Install dependencies**

   ```bash
   npm install
   ```

3. **Check contract syntax**

   ```bash
   clarinet check
   ```

4. **Run tests**

   ```bash
   npm test
   ```

### Testing

The project includes comprehensive test suites using Vitest and Clarinet SDK:

```bash
# Run all tests
npm test

# Run tests with coverage report
npm run test:report

# Watch mode for development
npm run test:watch
```

### Contract Deployment

1. **Configure deployment settings** in `settings/` directory for your target network
2. **Deploy using Clarinet**:

   ```bash
   clarinet deploy --network testnet
   ```

## Usage Examples

### Initialize the Protocol

```clarity
(contract-call? .bitvault initialize u50000000000) ;; $50,000 BTC price
```

### Deposit Collateral and Mint Stablecoin

```clarity
;; Deposit 0.1 BTC
(contract-call? .bitvault deposit-collateral u10000000)

;; Mint $2000 worth of stablecoin (safe 2.5x collateralization)
(contract-call? .bitvault mint-stablecoin u2000000000)
```

### Add Liquidity

```clarity
;; Provide liquidity to earn fees
(contract-call? .bitvault add-liquidity u5000000 u1500000000)
```

## API Reference

### Public Functions

| Function | Parameters | Description |
|----------|------------|-------------|
| `initialize` | `initial-price: uint` | Initialize protocol with BTC price |
| `update-price` | `new-price: uint` | Update oracle price (owner only) |
| `deposit-collateral` | `btc-amount: uint` | Deposit BTC collateral |
| `mint-stablecoin` | `amount: uint` | Mint stablecoin against collateral |
| `burn-stablecoin` | `amount: uint` | Burn stablecoin to reduce debt |
| `add-liquidity` | `btc-amount: uint, stable-amount: uint` | Add liquidity to AMM |
| `remove-liquidity` | `lp-tokens: uint` | Remove liquidity from AMM |

### Read-Only Functions

| Function | Parameters | Returns |
|----------|------------|---------|
| `get-vault-details` | `owner: principal` | Vault information |
| `get-collateral-ratio` | `owner: principal` | Current collateralization ratio |
| `get-pool-details` | - | Pool statistics |
| `get-lp-details` | `provider: principal` | LP position details |

## Contributing

We welcome contributions to BitVault Pro! Please read our contributing guidelines and submit pull requests for any improvements.

### Development Workflow

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests for new functionality
5. Ensure all tests pass
6. Submit a pull request

## License

This project is licensed under the ISC License - see the [LICENSE](LICENSE) file for details.

## Security Considerations

BitVault Pro is designed with security as a primary concern. However, DeFi protocols carry inherent risks:

- **Smart Contract Risk**: Potential bugs or vulnerabilities in contract code
- **Price Oracle Risk**: Dependency on external price feeds
- **Liquidation Risk**: Collateral may be liquidated if ratios fall below thresholds
- **Market Risk**: Volatility in underlying assets

**Disclaimer**: This software is provided "as is" without warranty. Users should understand the risks before interacting with the protocol.

## Support

For questions, issues, or support:

- Open an issue on GitHub
- Review the documentation
- Check existing discussions
