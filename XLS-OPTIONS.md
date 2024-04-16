# XLS-62d Options — Options for XRPL Protocol Chains
```
Title: Options
Type: Draft
Author:
	Denis Angell, XRPL-Labs <a href="https://github.com/dangell7/">(dangell7)</a>
Affiliation: XRPL-Labs
```

# Problem Statement

The current (XRPL) protocol provides a robust and efficient platform for transferring digital assets and currencies. However, there is a growing demand for more advanced financial instruments, such as options, which are not natively supported by the XRPL in its current form. Options are financial derivatives that give buyers the right or obligation, to buy or sell an underlying asset at a predetermined price within a specified time period. The lack of a standardized protocol for creating and managing options on the XRPL protocols limit the functionality and potential use cases for the ledger. To address this gap and enhance the capabilities of XRPL protocols, we propose a new amendment that introduces a lightweight and flexible framework for options trading. This will enable developers and users to create, trade, and exercise options, thereby expanding the financial utility of XRPL protocols and opening up new opportunities for decentralized finance (DeFi) applications.

# Disclaimer

This decentralized investment vehicle involves the creation and use of options, which are complex financial derivatives. Users should be aware that engaging with options carries significant risk and requires a deep understanding of the mechanisms involved.

**Licensing Requirements for Listing Options:**

- Entities or platforms that list options for trading may be required to obtain the appropriate licenses and authorizations from regulatory authorities.
- Users who intend to list options must ensure that they are in compliance with all relevant financial regulations and have obtained any necessary licenses or approvals.

**Accreditation and Sophistication Requirements:**

- Participation in options trading may be limited to accredited, sophisticated, or otherwise qualified investors, depending on the jurisdiction and the nature of the investment.
- It is the user's responsibility to confirm their eligibility as an accredited or sophisticated investor and to possess the requisite knowledge to understand the risks associated with options trading.

**Legal and Regulatory Compliance:**

- All users must adhere to the legal and regulatory requirements of their respective jurisdictions when interacting with options.
- Failure to comply with these regulations can lead to legal penalties, including fines and other sanctions.

**Due Diligence and Professional Advice:**

- Users are urged to conduct thorough due diligence and seek advice from a certified professional or financial advisor before engaging in options trading.
- The creators, developers, or facilitators of this decentralized investment vehicle do not provide financial advice and are not responsible for ensuring user compliance with licensing or qualification requirements.

**No Financial Advice:**

- The information provided regarding options within this decentralized investment vehicle is for informational purposes only and should not be construed as financial advice or a recommendation to trade.
- Users must make their own informed decisions and assess the risks and benefits of participating in options trading.

# Amendment

The proposed amendment introduces a new framework for options trading on the XRPL protocols, providing a lightweight and flexible way to create, trade, and exercise options, thus expanding the financial capabilities of the XRPL for decentralized finance applications.

The amendment adds:

* A new type of ledger object: `ltOPTION`
* A new type of ledger directory: `OPTION_DIR`
* A new type of ledger object: `ltOPTION_OFFER`
* A new serialized Hash256 field: `OptionID`
* A new serialized UInt64 field: `Quantity`
* A new serialized Hash256 field: `SealedID`
* A new serialized Hash256 field: `SwapID`

Three new transaction types:
* `OptionList`
* `OptionCreate`
* `OptionExecute`

## New Ledger Object Type: `Option`

The `Option` ledger object is a new on-ledger object that is created with the `OptionList` transaction. This object represents an option contract on the XRPL, detailing the terms under which the option can be exercised.

The object has the following fields:

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfAmount | Amount | ✔️ | The underlying asset and the strike price. The value of the amount is the strike price. |
| sfExpiration | UInt32 | ✔️ | The time at which the option expires. |

Example Option object:

```json
{
   "LedgerEntryType": "Option",
   "Amount" : {
      "currency" : "USD",
      "issuer" : "rHb9CJAWyB4rj91VRWn96DkukG4bwdtyTh",
      "value" : "10"
   },
   "Expiration": "745329202",
}
```

## New Ledger Object Type: `OptionOffer`

The `OptionOffer` ledger object is a new on-ledger object that represents an offer to buy or sell an option contract on the XRPL. This object is used to match buyers and sellers in the options market, facilitating the trading of options contracts.

The object has the following fields:

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfOwner | AccountID | ✔️ | The account that created the offer. |
| sfOwnerNode | UInt64 | ✔️ | Reference to the owner directory node that the `OptionOffer` object is part of. |
| sfOptionID | Hash256 | ✔️ | A unique identifier for the option, which is also the OptionID. |
| sfAmount | Amount | ✔️ |  The locked shares in the option contract. The quantity as an Amount. |
| sfPremium | Amount | ✔️ | The premium of the option contract. This is the price that the buyer pays to the seller for the option. |
| sfQuantity | UInt64 | ✔️ | The quantity of shares in the option contract. Must be in multiples of 100. |
| sfSealedID | UInt256 | ❌ | An optional identifier for matching the offer with a specific counterparty or transaction. |
| sfSwapID | UInt256 | ❌ | An optional identifier for a swap transaction associated with the option. |

Example `OptionOffer` object:

```json
{
   "LedgerEntryType": "OptionOffer",
   "Owner": "rU9XRmcZiJXp5J1LDJq8iZFujU6Wwn9cV9",
   "OwnerNode": "0000000000000000",
   "OptionID": "C24DAF43927556F379F7B8616176E57ACEFF1B5D016DC896222603A6DD11CE05",
   "Premium": {
      "currency" : "USD",
      "issuer" : "rHb9CJAWyB4rj91VRWn96DkukG4bwdtyTh",
      "value" : "5"
   },
   "Quantity": 100,
   "SealedID": "D79DE793C6934943D5389CB4E5392A05A8E00881202F05FE41ADC2AE83B24E91",
   "SwapID": "E5F6AEBCC10B8C2A9C3B2A1C4A6EEBDBF5D48D6749F3A5B07B9FCDABB3F70279"
}
```

## New Transaction Type: `OptionList`

The `OptionList` transaction is used to create a new option contract on the XRPL. This transaction specifies the terms of the option, including the underlying asset, strike price, expiration time, and the broker responsible for the contract.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfTransactionType | String | ✔️ | The type of transaction, which is "OptionList" for listing a new option. |
| sfAccount | AccountID | ✔️ | The account creating the option contract. |
| sfIssuer | Issuer | ✔️ | The underlying asset. |
| sfExchangeRate | UInt64 | ✔️ | The strike price. |
| sfExpiration | UInt32 | ✔️ | The time at which the option expires, specified as a UNIX timestamp. |

The `Amount` field represents both the underlying asset that the option contract pertains to and the strike price at which the option can be exercised. The `Expiration` field is a UNIX timestamp indicating when the option contract will expire and can no longer be exercised. The `Broker` field identifies the broker who will manage the option contract.

Example `OptionList` transaction:

```json
{
   "Account": "raKG2uCwu71ohFGo1BJr7xqeGfWfYWZeh3",
   "TransactionType": "OptionList",
   "Issuer": {
      "currency" : "USD",
      "issuer" : "rHb9CJAWyB4rj91VRWn96DkukG4bwdtyTh",
   },
   "ExchangeRate": "1.25",
   "Expiration": 743171558
}
```

### `OptionList` Summary

The `OptionList` transaction is a proposed transaction for creating option contracts on the XRP Ledger. When executed, it creates a new ledger entry representing the option, which includes details like the underlying asset, strike price and expiration date. If the transaction is successful, the option contract is officially listed on the ledger.

## New Transaction Type: `OptionCreate`

The `OptionCreate` transaction is used to create a new option contract or to manage an existing one. This transaction can be used to buy or sell (write) a call or put option, and to open or close a position.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfTransactionType | String | ✔️ | The type of transaction, which is "OptionCreate" for creating or managing an option. |
| sfAccount | AccountID | ✔️ | The account creating or managing the option contract. |
| sfOptionID | Hash256 | ✔️ | The ID of the option ledger entry object. |
| sfFlags | UInt32 | ❌ | A set of flags to distinguish the type of option contract and the action being taken. |
| sfAmount | Amount | ✔️ | The premium of the option contract. This is the price paid by the buyer or received by the seller. |
| sfQuantity | UInt64 | ✔️ | The quantity of shares in the option contract. Must be in multiples of 100. |
| sfSwapID | UInt256 | ❌ | An identifier for a swap transaction associated with the option, used when closing an option. |

### `OptionCreate` Flags

The `OptionCreate` transaction uses flags to specify the type of option, the action being taken, and whether the position is being opened or closed.

| Flag Name | Value | Description |
| --- | --- | --- |
| tfType | 0x00010000 | Leave unset for Call (default) and set for Put. |
| tfAction | 0x00020000 | Leave unset for Buy (default) and set for Sell (write). |
| tfPosition | 0x00040000 | Leave unset for Open (default) and set for Close. |

The `Amount` field represents the premium of the option contract, which is the price paid by the buyer or received by the seller for the option. The `Quantity` field specifies the number of shares or units covered by the option contract.

The `SwapID` field is used when closing an option position and must specify the swap ID associated with the option being closed. In a decentralized exchange (DEX) environment, the matching of options would occur automatically and programmatically, and the `SwapID` would not be necessary.

Example `OptionCreate` transaction (Open Position):

```json
{
   "Account": "raKG2uCwu71ohFGo1BJr7xqeGfWfYWZeh3",
   "TransactionType": "OptionCreate",
   "OptionID": "C24DAF43927556F379F7B8616176E57ACEFF1B5D016DC896222603A6DD11CE05",
   "Flags": 0, // Call, Buy, Open
   "Amount": {
      "currency" : "USD",
      "issuer" : "rHb9CJAWyB4rj91VRWn96DkukG4bwdtyTh",
      "value": "100"
   },
   "Quantity": 100
}
```

### `OptionCreate` Summary

The `OptionCreate` transaction is used to create or manage options contracts on the XRPL ledger. It involves specifying the option details, adjusting account balances, and updating ledger entries. The transaction can seal options by matching buyers and sellers, and it handles the transfer of premiums and collateral. It supports buying or selling options and opening or closing positions.

## New Transaction Type: `OptionExercise`

The `OptionExercise` transaction is used to exercise an option contract on the XRPL. This transaction allows the holder of a call or put option to exercise their right to buy or sell the underlying asset at the specified strike price.

|     Field        |   Type   | Required | Description |
| --- | --- | --- | --- |
| sfFlags       |  UInt32 |     ❌    | If the `tfExpire` flag is set the option will be removed from the ledger. |
| sfOptionID       |  Hash256 |     ✔️    | The ID of the option ledger entry object. |
| sfOrderID        |  Hash256 |     ✔️    | The ID of the option order ledger entry object. |

Example Option Exercise:

```json
{
   "Account": "raKG2uCwu71ohFGo1BJr7xqeGfWfYWZeh3",
   "TransactionType": "OptionExercise",
   "FLags": 0,
   "OptionID": "C24DAF43927556F379F7B8616176E57ACEFF1B5D016DC896222603A6DD11CE05",
   "OrderID": "D5EB856370B233B4384D02613E6C71305C57BCA5FF4A5D857D4628DB9D72BC55",
}
```

### `OptionExercise` Summary

The `OptionExercise` transaction on the XRPL allows a holder of an option contract to exercise their right to buy or sell the underlying asset at a set price. When this transaction is executed, it checks for the necessary permissions and account balances, transfers the asset and funds between the option holder and writer, updates the ledger, and then finalizes the transaction. If successful, the option is exercised, and the involved accounts are adjusted accordingly. If the `tfExpire` flag is set, the option is also removed from the ledger.

> This proposal is still in draft. Please subscribe for updates and ask any questions below.
