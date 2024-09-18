# XLS-XXe Limited Token Issuance
```
Title: Limited Token Issuance
Type: Draft
Author:
    Denis Angell, XRPL-Labs <a href="https://github.com/dangell7/">(dangell7)</a>
```

## Introduction

The ability to issue tokens on the XRPL provides users with a powerful tool for creating and managing digital assets. However, without proper controls in place, token issuance can lead to excessive supply, which may dilute the value of existing tokens and create instability in the market. To address these concerns, we propose a new amendment that introduces a mechanism for limiting token issuance on the XRPL.

The objective of the Limited Token Issuance amendment is to provide users with the ability to set a maximum limit on the number of tokens that can be issued for a specific asset. This will help maintain the integrity and value of tokens while allowing for controlled growth in the token ecosystem.

## Problem Statement

Currently, the XRPL does not have a native mechanism to limit the issuance of tokens. This lack of control can lead to situations where token creators issue an excessive number of tokens, potentially harming the value of the asset and the interests of token holders. To provide users with more control over token issuance and to promote responsible asset management, we propose the introduction of a new transactor and ledger object that will facilitate limited token issuance.

## Amendment

The proposed amendment introduces a new feature that allows users to set limits on the total number of tokens that can be issued for a specific asset. This feature enables users to define a maximum token count, and any attempts to exceed this limit will result in the rejection of the transaction.

The amendment adds the following:

- A new transaction type: `CreateTrust`
- A new ledger entry: `TokenTrust`

## New Transaction Type: `CreateTrust`

The `CreateTrust` transaction is used to create a ledger object that defines the maximum number of tokens that can be issued for a specific asset. This transaction allows users to set the maximum token limit and initialize the token count.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfTransactionType | String | ✔️ | The type of transaction, which is "CreateTrust" for setting the maximum token limit. |
| sfAccount | AccountID | ✔️ | The account for which the token issuance limit is being set. |
| sfMaxTokens | Amount | ✔️ | The maximum number of tokens that can be issued for the asset. |
| sfIssue     | STIssue    | ✔️  | The asset details, including the issuer and currency of the token.        |

Example `CreateTrust` transaction:

```json
{
   "Account": "rU9XRmcZiJXp5J1LDJq8iZFujU6Wwn9cV9",
   "TransactionType": "CreateTrust",
   "Issue": {
        "issuer": "rU9XRmcZiJXp5J1LDJq8iZFujU6Wwn9cV9",
        "currency": "XYZ"
   },
   "MaxTokens": "1000000"
}
```

## New Ledger Entry: `TokenTrust`

The `TokenTrust` ledger entry is a new on-ledger object that represents the token issuance limits and related settings for an asset on the XRPL. This object is used to store the details of the token trust, including the maximum token limit and the current token count.

The object has the following fields:

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfAccount | AccountID | ✔️ | The account for which the token trust is set. |
| sfMaxTokens | Amount | ✔️ | The maximum number of tokens that can be issued for the asset. |
| sfTokenCount | Amount | ✔️ | The current count of tokens that have been issued. |
| sfIssue     | STIssue    | ✔️  | The asset details, including the issuer and currency of the token.        |

Example `TokenTrust` object:

```json
{
   "LedgerEntryType": "TokenTrust",
   "Account": "rU9XRmcZiJXp5J1LDJq8iZFujU6Wwn9cV9",
   "Issue": {
        "issuer": "rU9XRmcZiJXp5J1LDJq8iZFujU6Wwn9cV9",
        "currency": "XYZ"
   },
   "MaxTokens": "1000000",
   "TokenCount": "0"
}
```

## Handling Payment Transactions

When a payment transaction is submitted, the following rules apply:

1. **Token Count Update**: The `TokenCount` in the `TokenTrust` object will be updated based on the amount specified in any transaction that includes a `STAmount` field from the issuer for the specific currency.
2. **Maximum Token Limit Check**: If the updated `TokenCount` exceeds the `MaxTokens` limit, the transaction will be rejected with a new Transaction Error Code (`tecTOKEN_LIMIT_EXCEEDED`).
3. **Successful Transaction**: If the updated `TokenCount` is within the limit, the transaction will be successful (`tesSUCCESS`).

### Implementation Details

The counting logic for the token issuance will be implemented in the `Transactor.cpp` file. This will ensure that all `STAmount` transactions from the issuer are accurately counted towards the `TokenCount` in the `TokenTrust` ledger entry.

### Transactions to be Monitored

The following transactions will be monitored for their `Amount` or `STAmount` fields, and the `TokenTrust` object will be updated accordingly:

- **Payment**
- **NFTokenMint** (Can Include Offer Amount)
- **NFTokenOfferCreate**
- **NFTokenOfferCancele**
- **OfferCreate**
- **OfferCancele**
- **AMMCreate**
- **AMMDeposit**
- **AMMWithdraw**

### Example Payment Transaction

```json
{
   "Account": "rU9XRmcZiJXp5J1LDJq8iZFujU6Wwn9cV9",
   "TransactionType": "Payment",
   "Destination": "rHb9CJAWyB4rj91VRWn96DkukG4bwdtyTh",
   "Amount": {
        "value": "100",
        "currency": "XYZ",
        "issuer": "rU9XRmcZiJXp5J1LDJq8iZFujU6Wwn9cV9"
   }
}
```

### Updated `TokenTrust` Ledger Entry

```json
{
   "LedgerEntryType": "TokenTrust",
   "Account": "rU9XRmcZiJXp5J1LDJq8iZFujU6Wwn9cV9",
   "Issue": {
        "issuer": "rU9XRmcZiJXp5J1LDJq8iZFujU6Wwn9cV9",
        "currency": "XYZ"
   },
   "MaxTokens": "1000000",
   "TokenCount": "100"
}
```
