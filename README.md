# hundred-dev

## **Overview**

To deploy a new hToken (the Hundred Finance interest-bearing token), we need to:

1. Deploy a hToken contract.
2. Configure Chainlink oracle proxy.
3. Add the hToken to the money market.
4. Configure collateral factor.
5. Configure reserve factor.

### 1. Deploy a hToken contract

The contract to deploy is called a CErc20Delegator.
Flatten source code: https://arbiscan.io/address/0xfcd8570ad81e6c77b8d252bebeba62ed980bd64d#code

- Compiler Version: v0.5.16+commit.9c3226ce
- Optimization Enabled: Yes with 200 runs

As example, we examin the process for `hUSDC` (Hundred USDC token) or Arbitrum.

### **{Constructor}**

#### Parameters:

`underlying_`: The address of the underlying asset
`0xff970a61a04b1ca14834a43f5de4533ebddb5cc8 `

`comptroller_`: The address of the Comptroller.
`0x0f390559f258eb8591c8e31cf0905e97cf36ace2`

`interestRateModel_`: The address of the interest rate model contract address.
`0xe4e43864ea18d5e5211352a4b810383460ab7fcc`

`initialExchangeRateMantissa_`: The initial exchange rate, scaled by 1e18.
It is caculated as follows: ` 2 * 10^8 * 10^i`
where i is the number of decimals of the underlying asset of the hToken. (example: i = 6 for USDC, USDT, i = 6 for WBTC and i = 18 for main ERC20 tokens).

`name_`: ERC-20 name of this token.
`Hundred USDC`

`symbol_`: ERC-20 symbol of this token.
`hUSDC`

`decimals_`: ERC-20 decimal precision of this token. It is always 8.
`8`

`admin_`: Address of the administrator of this token. This address is set to the multi-signatures address.
`0xb982841d8Caf7cCc1b5c8ec414347316F54A06c9`

`implementation_`: The address of the implementation the contract delegates to.
`0xbb93c7f378b9b531216f9ad7b5748be189a55807`

`becomeImplementationData`: The encoded args for becomeImplementation. This value is unused.
`0x0`

### 2. Configure Chainlink oracle proxy

In order to consume the Price feeds from Chainlink, a `ChainlinkPriceOracleProxy` is deployed.

Next, the configuration settings are done, by calling the `setTokenConfigs` function on the `ChainlinkPriceOracleProxy` address.

#### Parameters:

`chainlinkAggregatorAddress`: The adress of the aggregator address as publised by Chainlink
`0x50834F3163758fcC1Df9973b6e91f0F0F0434aD3`

`chainlinkPriceBase`: This value is used to retrieve the asset price compared to either USD, ETH, BNB or WBTC
`1`

`underlyingTokenDecimals`: The number of the underlyingtoken.
`6` in case of USDC

### 3. Add the hToken to the money market.

To add a market, its contract would need to be added to the `Unitroller` using the `_supportMarket` function.
This function is available on the `Comptroller`, the `Unitroller` implementation.

To add the market,

Call the `Unitroller` by using its address.
`0x0f390559f258eb8591c8e31cf0905e97cf36ace2`

Use the `comptroller` ABI. To be copied from the network explorer like Arbiscan, Ftmscan...

Call the `_supportMarket` function.

#### Parameters:

Add the `hToken` address.

### 4. Config collateral factor

When the `hToken` has been added, the `collaterFactor` is to be set.

Call the `Unitroller` address and use the `Comptroller` ABI

Call function `_setCollateralFactor`

#### Parameters:

_ `hToken`: The market to set the factor on.
_ `newCollateralFactorMantissa`: The new collateral factor, scaled by 1e18.
`750000000000000000` as 75% for example.

### 5. Config reserve factor

The `reserveFactor` is set in the `hToken` contract

Call the `hToken` contract

Call the function `_setReserveFactor`

#### Parameters:

`newReserveFactorMantissa`: The new reserve factor, scaled by 1e18.
`100000000000000000` as 10% for example.
