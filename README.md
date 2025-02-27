# Decentralized Stablecoin System

A decentralized, exogenous, crypto-collateralized stablecoin system built on Ethereum. This project implements a stablecoin (DSC) that maintains a 1:1 peg with USD through algorithmic mechanisms and overcollateralization.

## Core Features

- **Exogenous Collateral**: Uses WETH and WBTC as collateral
- **Dollar Pegged**: Maintains 1:1 peg with USD
- **Algorithmic Stability**: Purely algorithmic stability mechanism
- **Overcollateralized**: System maintains >100% collateralization ratio
- **Chainlink Price Feeds**: Real-time price data for collateral assets
- **Liquidation System**: Protects system health through liquidations

## System Architecture

### Core Components

1. **DecentralizedStableCoin (DSC)**: ERC20 token implementing the stablecoin
2. **DSCEngine**: Core logic for minting, burning, depositing, and liquidations
3. **Price Feeds**: Chainlink oracle integration for collateral pricing

### Key Parameters

- Minimum Collateral Ratio: 200% (LIQUIDATION_THRESHOLD = 50)
- Liquidation Bonus: 10%
- Supported Collateral: WETH, WBTC

## Getting Started

### Prerequisites

- [What are Stablecoins?](https://www.youtube.com/watch?v=pGzfexGmuVw&t=172s&pp=ygUVc3RhYmxlY29pbnMgZXhwbGFpbmVk)
- [Foundry](https://book.getfoundry.sh/getting-started/installation)

### Installation

```bash
# Clone the repository
git clone https://github.com/adityaxxz/stablecoin-defi-project
cd stablecoin-defi-project

# Install dependencies
forge install

# Build the project
forge build
```

## Core Functions

### DSCEngine

1. **Collateral Management**
   - `depositCollateral(address token, uint256 amount)`: Deposit collateral
   - `redeemCollateral(address token, uint256 amount)`: Withdraw collateral
   - `depositCollateralAndMintDsc(address token, uint256 amount, uint256 mintAmount)`: One-step deposit and mint

2. **DSC Operations**
   - `mintDsc(uint256 amount)`: Mint new DSC tokens
   - `burnDsc(uint256 amount)`: Burn DSC tokens
   - `redeemCollateralForDsc(address token, uint256 amount, uint256 burnAmount)`: Redeem collateral by burning DSC

3. **Liquidation**
   - `liquidate(address collateral, address user, uint256 debtToCover)`: Liquidate undercollateralized positions

### DecentralizedStableCoin

- `mint(address to, uint256 amount)`: Mint new tokens (restricted to DSCEngine)
- `burn(uint256 amount)`: Burn tokens (restricted to DSCEngine)

## Running Tests

```bash
# Run all tests
forge test

# Run specific test 
forge test --mt testCanDepositCollateralAndGetAccountInfo 

# Run tests with verbosity
forge test --mt testCanDepositCollateralAndGetAccountInfo -vv

# Run fuzz tests
forge test --match-path test/fuzz/Handler.t.sol
```

## Invariants Testing

Invariants tests are crucial for ensuring the stability and reliability of the stablecoin system. These tests verify that certain conditions hold true throughout the operation of the system, preventing unexpected behavior and vulnerabilities.

### Key Invariants

1. **Total Supply vs. Collateral Value**
   - The total supply of DSC should always be less than the total value of collateral in the system. This ensures that the system is overcollateralized and can cover all issued DSC.

2. **Non-Reverting Getter Functions**
   - All getter functions should be non-reverting, ensuring that they can be called safely without causing errors. This is important for maintaining the integrity of the system's state and providing reliable data to users.

### Running Invariants Tests

To run the invariants tests, use the following command:

```bash
forge test --match-path test/fuzz/OpenInvariantsTest.t.sol

forge test --mt invariant_protocolMustHaveMoreValueThanTotalSupply -vv
```

This command will execute the invariants tests, checking the core conditions that must always be true for the system to function correctly.

## Deployment

### Local Deployment

```bash
# Start local Anvil chain
anvil

# Deploy to local network
forge script script/DeployDSC.s.sol --rpc-url http://localhost:8545 --broadcast
```

### Testnet Deployment (Sepolia)

```bash
# Set your environment variables
export PRIVATE_KEY=your_private_key
export SEPOLIA_RPC_URL=your_rpc_url

# Deploy to Sepolia
forge script script/DeployDSC.s.sol --rpc-url $SEPOLIA_RPC_URL --broadcast
```

## Security Considerations

- System maintains overcollateralization at all times
- Liquidation mechanism protects against undercollateralization
- Price feed manipulation protection through Chainlink oracles
- Reentrancy protection on critical functions
- Owner-only access control on sensitive operations



