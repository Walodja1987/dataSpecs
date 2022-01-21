# Diva Protocol Specific Feeds

## Query Name

- `divaProtocolPolygon`

## Query Description

This query returns the result for a given pool ID (a specific prediction market) on the DIVA Protocol on Polygon

## Query Parameters

The `divaProtocolPolygon` query has one parameter, which specifies the requested data.  

1. **poolId** (uint256): ID of the prediction market

The `poolId` should be a valid prediction market on the divaProtocol on the Polygon network, ready to be settled. 

## Response Type

The query response will consist of a single 256-bit value in the following format:

- `abi_type`: ufixed256x18 (18 decimals of precision)
- `packed`: false

## Examples

### Prediction Market 

*Query Descriptor:*

```json
    {
        "type": "divaProtocolPolygon",
        "inputs": [{
            "type": "uint256",
            "name": "poolId"
        }],
        "outputs": [
            {
              "name": "divaProtocolValue",
              "decimals": 18,
              "type": "uint256",
              "packed":false
            }
        ]
    }
```

```s
    abi.encode("divaProtocolPolygon",1)
```

*queryID:*

    keccak256(abi.encode("divaProtocolPolygon",1))

    `0x4c6bfa4040303750318007ec849efdc93255109ff3c4e656171a6dfe1b35f2aa`

### Encoding/Decoding

A value of 99.9 would be submitted on-chain using the following bytes:

    0x0000000000000000000000000000000000000000000000056ba3d73af34eec04


## Dispute Considerations

Reporters should use care in selecting data sources and choosing the algorithm to combine them.

- The prediction market nature of DIVA Protocol means that the query could have a result that is a price, a temperature, a boolean, a sports score, or any other metric. The expected input type of the final value is `uint256`. 
- The result should be the correct one AT THE TIME of expiration. This means that if the prediction market bets on the price of BTC/USD on Feb 1st at midnight, the query should always return the price of BTC/USD at midnight on Feb 1st, even if it get called/requested again. 
- If no price is available at the exact time of expiration, use the latest available prior to expiration.   
- Multiple sources should be used whenever possible.
- When retrieving data directly from exchanges, feed users might expect that exchanges with lower volumes have their prices weighted accordingly to avoid erratic results.
- Care should also be used when retrieving data from aggregators.  
- It is the reporters responsibility to ensure that the feed result is *reasonable* enough for a community consensus, otherwise it may be subject to dispute.
- If a *reasonable* value cannot be determined, a value should not be submitted

## DIVA Queries
Pool parameters including expiration time and reference asset are stored at the time of pool creation and can be queried in two ways:

### DIVA Smart contract
Function: `getPoolParameters(poolId)`

ABI:
```
{
    "inputs": [
      { "internalType": "uint256", "name": "_poolId", "type": "uint256" }
    ],
    "name": "getPoolParameters",
    "outputs": [
      {
        "components": [
          {
            "internalType": "string",
            "name": "referenceAsset",
            "type": "string"
          },
          {
            "internalType": "uint256",
            "name": "inflection",
            "type": "uint256"
          },
          { "internalType": "uint256", "name": "cap", "type": "uint256" },
          { "internalType": "uint256", "name": "floor", "type": "uint256" },
          {
            "internalType": "uint256",
            "name": "supplyShortInitial",
            "type": "uint256"
          },
          {
            "internalType": "uint256",
            "name": "supplyLongInitial",
            "type": "uint256"
          },
          {
            "internalType": "uint256",
            "name": "supplyShort",
            "type": "uint256"
          },
          {
            "internalType": "uint256",
            "name": "supplyLong",
            "type": "uint256"
          },
          {
            "internalType": "uint256",
            "name": "expiryDate",
            "type": "uint256"
          },
          {
            "internalType": "address",
            "name": "collateralToken",
            "type": "address"
          },
          {
            "internalType": "uint256",
            "name": "collateralBalanceShortInitial",
            "type": "uint256"
          },
          {
            "internalType": "uint256",
            "name": "collateralBalanceLongInitial",
            "type": "uint256"
          },
          {
            "internalType": "uint256",
            "name": "collateralBalanceShort",
            "type": "uint256"
          },
          {
            "internalType": "uint256",
            "name": "collateralBalanceLong",
            "type": "uint256"
          },
          {
            "internalType": "address",
            "name": "shortToken",
            "type": "address"
          },
          { "internalType": "address", "name": "longToken", "type": "address" },
          {
            "internalType": "uint256",
            "name": "finalReferenceValue",
            "type": "uint256"
          },
          {
            "internalType": "enum LibDiamond.Status",
            "name": "statusFinalReferenceValue",
            "type": "uint8"
          },
          {
            "internalType": "uint256",
            "name": "redemptionAmountLongToken",
            "type": "uint256"
          },
          {
            "internalType": "uint256",
            "name": "redemptionAmountShortToken",
            "type": "uint256"
          },
          {
            "internalType": "uint256",
            "name": "statusTimestamp",
            "type": "uint256"
          },
          {
            "internalType": "address",
            "name": "dataFeedProvider",
            "type": "address"
          },
          {
            "internalType": "uint256",
            "name": "redemptionFee",
            "type": "uint256"
          },
          {
            "internalType": "uint256",
            "name": "settlementFee",
            "type": "uint256"
          },
          { "internalType": "uint256", "name": "capacity", "type": "uint256" }
        ],
        "internalType": "struct LibDiamond.Pool",
        "name": "",
        "type": "tuple"
      }
    ],
    "stateMutability": "view",
    "type": "function"
  }
```

Example response:
```
  referenceAsset: 'ETH/USDT',
  inflection: BigNumber { value: "22000000000000000000" },
  cap: BigNumber { value: "27000000000000000000" },
  floor: BigNumber { value: "17000000000000000000" },
  supplyShortInitial: BigNumber { value: "105000000000000000000" },
  supplyLongInitial: BigNumber { value: "105000000000000000000" },
  supplyShort: BigNumber { value: "105000000000000000000" },
  supplyLong: BigNumber { value: "105000000000000000000" },
  expiryDate: BigNumber { value: "1642021490" },
  collateralToken: '0xaD6D458402F60fD3Bd25163575031ACDce07538D',
  collateralBalanceShortInitial: BigNumber { value: "20000000000000000" },
  collateralBalanceLongInitial: BigNumber { value: "10000000000000000" },
  collateralBalanceShort: BigNumber { value: "20000000000000000" },
  collateralBalanceLong: BigNumber { value: "10000000000000000" },
  shortToken: '0x43a9f0adaa48F4D42BdFd0A4761611a468733A3d',
  longToken: '0x0881c26507867d5020531b744D285778432c7DAc',
  finalReferenceValue: BigNumber { value: "85000000000000000000" },
  statusFinalReferenceValue: 3,
  redemptionAmountLongToken: BigNumber { value: "284857142857142" },
  redemptionAmountShortToken: BigNumber { value: "0" },
  statusTimestamp: BigNumber { value: "1642075118" },
  dataFeedProvider: '0x47566C6c8f70E4F16Aa3E7D8eED4a2bDb3f4925b',
  redemptionFee: BigNumber { value: "2500000000000000" },
  settlementFee: BigNumber { value: "500000000000000" },
  capacity: BigNumber { value: "0" }
```

|Parameter|Description|
|:---|:---|
| `referenceAsset` | Stores the reference asset string (e.g., "ETH/USD", "BTC/USD", "ETH Gas Price (Wei)", "TVL locked in DeFi").|
| `dataFeedProvider` | Stores the address of the Ethereum account that is supposed to report the final value of the reference asset. See [address section](#tellor-oracle-address) for the  | 

Note that data feeds will be added to a whitelist maintained by DIVA and made available to their users via the app.

The following two parameters specify the range that the pool is tracking and can be helpful when implementing sanity checks on the reporter side:
|Parameter|Description|
|:---|:---|
| `floor` | The lower bound of the range that the reference asset is tracking. A final reference asset value that is equal to or smaller than the floor will result in a zero payoff for the long side and maximum payoff for the short side|
| `cap` | The upper bound of the range that the reference asset is tracking. A final reference asset value that is equal to or larger than the cap will result in a zero payoff for the short side and maximum payoff for the long side|

### DIVA pool subgraph 
Contains the same parameters as received from `getPoolParameters` and more.
Ropsten: https://thegraph.com/hosted-service/subgraph/divaprotocol/diva-ropsten
Polygon: n/a

### DIVA whitelist subgraph
Contains the list of whitelisted data providers and their data feeds. Tellor oracle will be a single data provider with multiple data feeds. Tellor data feeds will be added by DIVA governance after thorough due diligence.  
Ropsten: https://thegraph.com/hosted-service/subgraph/divaprotocol/diva-whitelist-ropsten
Polygon: n/a

## Tellor oracle address
DIVA users specify the oracle address at pool creation. The Tellor oracle is a whitelisted data provider in DIVA which is made available to users on DIVA's platform. 
Ropsten: n/a
Polygon: n/a

## DIVA addresses
* DIVA protocol:
   * Ropsten: 0x6455A2Ae3c828c4B505b9217b51161f6976bE7cf
   * Polygon (test version!): 0xe3343218CAa73AE523D40936D64E7f335AfDe8f9 
* DIVA whitelist:
   * Ropsten: 0x50D327C638B09d0A434185d63E7193060E6271B2
   * Polygon: n/a
