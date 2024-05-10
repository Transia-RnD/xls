# XLS-??d Bonds — Bonds for XRPL Protocol Chains
```
Title: Bonds
Type: Draft
Author:
	Denis Angell, XRPL-Labs <a href="https://github.com/dangell7/">(dangell7)</a>
Affiliation: XRPL-Labs
```

# Problem Statement

The XRPL protocol, while robust and efficient for transferring digital assets and currencies, does not natively support more advanced financial instruments such as bonds. Bonds are debt securities that are similar to IOUs. Borrowers issue bonds to raise money from investors willing to lend them money for a certain amount of time. The lack of a standardized protocol for creating and managing bonds on the XRPL protocols limit the functionality and potential use cases for the ledger. To address this gap and enhance the capabilities of XRPL protocols, we propose a new amendment that introduces a lightweight and flexible framework for bond issuance and management. This will enable developers and users to create, trade, and manage bonds, thereby expanding the financial utility of XRPL protocols and opening up new opportunities for decentralized finance (DeFi) applications.

# Amendment

The proposed amendment introduces a new framework for bond issuance and management on the XRPL protocols, providing a lightweight and flexible way to create, trade, and manage bonds, thus expanding the financial capabilities of the XRPL for decentralized finance applications.

The amendment adds:

* A new type of ledger object: `ltBond`

Three new transaction types:
* `BondOffer`
* `BondMint`
* `BondAction`

## New Ledger Object Type: `Bond`

The `Bond` ledger object is a new on-ledger object that represents a bond on the XRPL. This object is used to store the details of a bond, including the issuer, the underlying asset, the term, the coupon rate, the balloon rate, the coupon delay, the maturity date, the principle, the dated date, the DID, the coupon frequency, the bond ID, and the offering ID.

The object has the following fields:

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfOwner | AccountID | ✔️ | The account that created the bond. |
| sfOwnerNode | UInt64 | ✔️ | Reference to the owner directory node that the `Bond` object is part of. |
| sfIssuer | AccountID | ✔️ | The account that issued the bond. |
| sfFlags | UInt32 | ✔️ | The flags of the bond offering. |
| sfOfferingStart | UInt32 | ✔️ | The start time of the offering, specified as a UNIX timestamp. |
| sfOfferingEnd | UInt32 | ✔️ | The end time of the offering, specified as a UNIX timestamp. |
| sfAsset | Currency | ✔️ | The underlying asset of the bond. |
| sfFees | Object | ✔️ | An object containing various fees associated with the bond. |
| sfTerm | UInt32 | ✔️ | The term of the bond in months. |
| sfCouponRate | UInt32 | ✔️ | The coupon rate of the bond as basis points. |
| sfBalloonRate | UInt32 | ✔️ | The balloon rate of the bond as basis points. |
| sfMaturityDate | UInt32 | ✔️ | The maturity date of the bond. |
| sfPrinciple | Amount | ✔️ | The principle of the bond. |
| sfDatedDate | UInt32 | ✔️ | The dated date of the bond. |
| sfDID | String | ✔️ | The DID of the bond. |
| sfCouponFrequency | UInt32 | ✔️ | The coupon frequency of the bond as weeks. |
| sfBondID | Hash256 | ✔️ | The ID of the bond. |
| sfOfferingID | Hash256 | ✔️ | The ID of the bond offering. |

Example `Bond` object:

```json
{
   "LedgerEntryType": "Bond",
   "Owner": "rU9XRmcZiJXp5J1LDJq8iZFujU6Wwn9cV9",
   "OwnerNode": "0000000000000000",
   "Issuer": "rHb9CJAWyB4rj91VRWn96DkukG4bwdtyTh",
   "Flags": 1,
   "OfferingStart": 743171558,
   "OfferingEnd": 743171568,
   "Asset": {
      "currency": "USD",
      "issuer": "rHb9CJAWyB4rj91VRWn96DkukG4bwdtyTh",
   },
   "Fees": {
      "IssuanceFee": 125,
      "RedemptionFee": 500,
   },
   "Term": 24,
   "CouponRate": 125,
   "BalloonRate": 125,
   "MaturityDate": 743171568,
   "Principle": "1000",
   "DatedDate": 743171568,
   "DID": "C24DAF43927556F379F7B8616176E57ACEFF1B5D016DC896222603A6DD11CE05",
   "CouponFrequency": 52,
   "BondID": "C24DAF43927556F379F7B8616176E57ACEFF1B5D016DC896222603A6DD11CE05",
   "OfferingID": "C24DAF43927556F379F7B8616176E57ACEFF1B5D016DC896222603A6DD11CE05"
}
```

## New Transaction Type: `BondOffer`

The `BondOffer` transaction is used to create a new bond offering on the XRPL. This transaction specifies the terms of the bond offering, including the offering start and end, quantity, asset, term, coupon rate, balloon rate, coupon delay, maturity date, principle, dated date, DID, coupon frequency, bond ID, and offering ID.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfTransactionType | String | ✔️ | The type of transaction, which is "BondOffer" for creating a new bond offering. |
| sfAccount | AccountID | ✔️ | The account creating the bond offering. |
| sfFlags | UInt32 | ✔️ | The flags of the bond offering. |
| sfOfferingStart | UInt32 | ✔️ | The start time of the offering, specified as a UNIX timestamp. |
| sfOfferingEnd | UInt32 | ✔️ | The end time of the offering, specified as a UNIX timestamp. |
| sfQuantity | UInt64 | ✔️ | The quantity of bonds in the offering. |
| sfAsset | Object | ✔️ | The underlying asset of the bond. |
| sfFees | Object | ✔️ | An object containing various fees associated with the bond. |
| sfTerm | UInt32 | ✔️ | The term of the bond, in months. |
| sfCouponRate | UInt32 | ✔️ | The coupon rate of the bond, as basis points. |
| sfBalloonRate | UInt32 | ✔️ | The balloon rate of the bond, as basis points. |
| sfMaturityDate | UInt32 | ✔️ | The maturity date of the bond, specified as a UNIX timestamp. |
| sfPrinciple | String | ✔️ | The principle of the bond. |
| sfDatedDate | UInt32 | ✔️ | The dated date of the bond, specified as a UNIX timestamp. |
| sfDID | UInt32 | ✔️ | The DID of the bond. |
| sfCouponFrequency | UInt32 | ✔️ | The frequency of coupon payments, in weeks. |
| sfBondID | Hash256 | ❌ | The ID of the bond, if refunding. |
| sfOfferingID | Hash256 | ❌ | The ID of the offering, if refunding. |

Example `BondOffer` transaction:

```json
{
   "Account": "raKG2uCwu71ohFGo1BJr7xqeGfWfYWZeh3",
   "TransactionType": "BondOffer",
   "Flags": 1,
   "OfferingStart": 743171558,
   "OfferingEnd": 743171568,
   "Quantity": "1000",
   "Asset": {
      "currency": "USD",
      "issuer": "rHb9CJAWyB4rj91VRWn96DkukG4bwdtyTh",
   },
   "Fees": {
      "IssuanceFee": 125,  // 125 basis points (1.25%)
      "RedemptionFee": 500,  // 500 basis points (5%)
   },
   "Term": 24, // In Months
   "CouponRate": 125, // As Basis Points
   "BalloonRate": 125, // As Basis Points
   "MaturityDate": 743171568,
   "Principle": "1000",
   "DatedDate": 743171568,
   "DID": "C24DAF43927556F379F7B8616176E57ACEFF1B5D016DC896222603A6DD11CE05",
   "CouponFrequency": 52, // As weeks (once per year)
   "BondID": "C24DAF43927556F379F7B8616176E57ACEFF1B5D016DC896222603A6DD11CE05", // If Refunding
   "OfferingID": "C24DAF43927556F379F7B8616176E57ACEFF1B5D016DC896222603A6DD11CE05", // If Refunding
}
```

### `BondOffer` Flags

The `BondOffer` transaction uses flags to specify the type of option, the action being taken, and whether the position is being opened or closed.

| Flag Name | Value | Description |
| --- | --- | --- |
| tfTerm | 0x00000001 | Set for long-term bonds. |
| tfSerial | 0x00000002 | Set for serial bonds. |
| tfBalloon | 0x00000004 | Set for balloon bonds. |
| tfCallAll | 0x00020000 | Set for bonds callable in entirety. |
| tfCallPartial | 0x00040000 | Set for bonds callable in part. |
| tfCallLottery | 0x00080000 | Set for bonds callable by lottery. |

## New Transaction Type: `BondMint`

The `BondMint` transaction is used to mint a new bond contract from an existing bond offering on the XRPL. This transaction allows the holder of a bond offering to mint new bonds, increasing the supply of the bond in the market.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfTransactionType | String | ✔️ | The type of transaction, which is "BondMint" for minting a new bond. |
| sfAccount | AccountID | ✔️ | The account minting the bond contract. |
| sfBondID | Hash256 | ✔️ | The ID of the bond ledger entry object. |
| sfOfferingID | Hash256 | ✔️ | The ID of the offering ledger entry object. |
| sfAmount | Amount | ✔️ | The principle of the bond contract. This is the initial investment paid by the buyer. |

Example `BondMint` transaction:

```json
{
   "Account": "raKG2uCwu71ohFGo1BJr7xqeGfWfYWZeh3",
   "TransactionType": "BondMint",
   "BondID": "C24DAF43927556F379F7B8616176E57ACEFF1B5D016DC896222603A6DD11CE05",
   "OfferingID": "C24DAF43927556F379F7B8616176E57ACEFF1B5D016DC896222603A6DD11CE05",
   "Amount": {
      "currency" : "USD",
      "issuer" : "rHb9CJAWyB4rj91VRWn96DkukG4bwdtyTh",
      "value": "1000"
   },
}
```

## New Transaction Type: `BondAction`

The `BondAction` transaction is used to perform actions on a bond contract on the XRPL. This transaction allows the holder of a bond to claim their coupon or reclaim their principle.

|     Field        |   Type   | Required | Description |
| --- | --- | --- | --- |
| sfTransactionType | String | ✔️ | The type of transaction, which is "BondAction" for performing actions on a bond. |
| sfAccount | AccountID | ✔️ | The account performing the action on the bond contract. |
| sfBondID | Hash256 | ✔️ | The ID of the bond ledger entry object. |
| sfFlags | UInt32 | ❌ | A set of flags to distinguish the type of bond action being taken. |

Example `BondAction` transaction:

```json
{
   "Account": "raKG2uCwu71ohFGo1BJr7xqeGfWfYWZeh3",
   "TransactionType": "BondAction",
   "BondID": "C24DAF43927556F379F7B8616176E57ACEFF1B5D016DC896222603A6DD11CE05",
   "Flags": 262144
}
```

### `BondAction` Flags

The `BondAction` transaction uses flags to specify the action being taken on the bond.

| Flag Name | Value | Description |
| --- | --- | --- |
| tfMature | 0x00020000 | To reclaim the principle. |
| tfCoupon | 0x00040000 | To claim the coupon. |
