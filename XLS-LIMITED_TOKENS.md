# XLS-XXd Limited Token Issuance
```
Title: Limited Token Issuance
Type: Draft
Author:
    Denis Angell, XRPL-Labs <a href="https://github.com/dangell7/">(dangell7)</a>
```

## Introduction

The ability to issue tokens on the XRPL provides users with a powerful tool for creating and managing digital assets. However, without proper controls in place, token issuance can lead to excessive supply, which may dilute the value of existing tokens and create instability in the market. Currently, one method to limit token issuance involves "blackholing" the issuer account, which effectively disables the account by removing its master key. While this approach can prevent further token issuance, it has significant drawbacks, including ledger bloat, as the account cannot be deleted once the master key is disabled. This results in an accumulation of unused accounts in the ledger, leading to inefficiencies and increased storage requirements.

To address these concerns, we propose a new amendment that introduces a mechanism for limiting token issuance on the XRPL without resorting to blackholing the issuer account. This will help maintain the integrity and value of tokens while allowing for controlled growth in the token ecosystem.

## Amendment

The proposed amendment introduces a new feature that allows users to set limits on the total number of tokens that can be issued for a specific asset. This feature enables users to define a maximum token count, and any attempts to exceed this limit will result in the rejection of the transaction.

The amendment adds the following:

- A new transaction type: `CreateFToken`
- A new ledger entry: `FToken`

## New Transaction Type: `CreateFToken`

The `CreateFToken` transaction is used to create a ledger object that defines the maximum number of tokens that can be issued for a specific asset. This transaction allows users to set the maximum token limit and initialize the token count.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfTransactionType | String | ✔️ | The type of transaction, which is "CreateFToken" for setting the maximum token limit. |
| sfAccount | AccountID | ✔️ | The account for which the token issuance limit is being set. The issuer. |
| sfCurrency     | STCurrency    | ✔️  | The currency of the token.        |
| sfMaximumAmount | UInt64 | ✔️ | The maximum number of tokens that can be issued for the asset. |
| sfURI     | Blob    |   | An optional URI that can hold additional data for the token        |

Example `CreateFToken` transaction:

```json
{
   "TransactionType": "CreateFToken",
   "Account": "rU9XRmcZiJXp5J1LDJq8iZFujU6Wwn9cV9",
   "Currency": "XYZ",
   "MaximumAmount": "1000000",
   "URI": "https://example.com"
}
```

## New Ledger Entry: `FToken`

The `FToken` ledger entry is a new on-ledger object that represents the token issuance limits and related settings for an asset on the XRPL. This object is used to store the details of the token trust, including the maximum token limit and the current token count.

The object has the following fields:

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfAccount | AccountID | ✔️ | The account for which the token trust is set. The issuer. |
| sfCurrency     | STCurrency    | ✔️  | The asset currency of the token.        |
| sfMaximumAmount | UInt64 | ✔️ | The maximum number of tokens that can be issued for the asset. |
| sfOutstandingAmount | UInt64 | ✔️ | The current count of tokens that have been issued. |
| sfURI     | Blob    |   | An optional URI that can hold additional data for the token        |

Example `FToken` object:

```json
{
   "LedgerEntryType": "FToken",
   "Account": "rU9XRmcZiJXp5J1LDJq8iZFujU6Wwn9cV9",
   "Currency": "XYZ",
   "MaximumAmount": "1000000",
   "OutstandingAmount": "0",
   "URI": "https://example.com",
}
```

## Handling Payment Transactions

When a payment transaction is submitted, the following rules apply:

1. **Token Count Update**: The `OutstandingAmount` in the `FToken` object will be updated based on the amount specified in any transaction that includes a `STAmount` field from the issuer for the specific currency.
2. **Maximum Token Limit Check**: If the updated `OutstandingAmount` exceeds the `MaximumAmount` limit, the transaction will be rejected with a new Transaction Error Code (`tecTOKEN_LIMIT_EXCEEDED`).
3. **Successful Transaction**: If the updated `OutstandingAmount` is within the limit, the transaction will be successful (`tesSUCCESS`).

### Implementation Details

The counting logic for the token issuance will be implemented in the `Transactor.cpp` file. This will ensure that all transactions containing an `STAmount` from the issuer are accurately counted towards the `OutstandingAmount` in the `FToken` ledger entry.

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

### Updated `FToken` Ledger Entry

```json
{
   "LedgerEntryType": "FToken",
   "Account": "rU9XRmcZiJXp5J1LDJq8iZFujU6Wwn9cV9",
   "Currency": "XYZ",
   "MaximumAmount": "1000000",
   "OutstandingAmount": "100"
}
```

## Frequently Asked Questions (FAQ)

### Q1: What happens if a transaction exceeds the maximum token limit?
**A1:** If a transaction attempts to exceed the maximum token limit set in the `FToken` object, the transaction will be rejected with a new Transaction Error Code (`tecTOKEN_LIMIT_EXCEEDED`).

### Q2: Which transactions will affect the `OutstandingAmount` in the `FToken` object?
**A2:** The following transactions will be monitored for their `Amount` or `STAmount` fields, and will affect the `OutstandingAmount`:
- Payment
- EscrowCreate
- EscrowCancel
- EscrowFinish
- PayChanCreate
- PayChanFund
- PayChanClaim
- NFTokenMint
- NFTokenOfferCreate
- NFTokenOfferCancel
- OfferCreate
- OfferCancel
- AMMCreate
- AMMDeposit
- AMMWithdraw
- Clawback

### Q3: Can I set a token limit after tokens have already been issued?
**A3:** The amendment allows users to set a maximum token limit at the time of creating the `FToken` object. Once the limit is set, any attempts to issue more tokens than the specified limit, or update the limit will be rejected

### Q4: Is the `URI` field mandatory in the `CreateFToken` transaction?
**A4:** No, the `URI` field is optional in the `CreateFToken` transaction. It can be used to hold additional data for the token if desired, but it is not required.

### Q5: How will the implementation of this amendment affect existing tokens?
**A5:** The implementation of this amendment will not retroactively affect existing tokens. It will apply to new token issuances going forward, allowing users to set limits on tokens created after the amendment is enacted. Existing tokens will continue to operate under the rules that were in place at the time of their issuance.

### Q6: How does the `OutstandingAmount` get reduced?
**A6:** When funds are sent back to the issuer, the `OutstandingAmount` in the `FToken` object will be reduced accordingly. This occurs during payment transactions where the issuer receives tokens back. There are also other transactions that can reduce the `OutstandingAmount`.

