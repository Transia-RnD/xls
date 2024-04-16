# XAS-3d Funds — Funds for XRPL Protocol Chains
```
Title: Funds
Type: Draft
Author:
	Denis Angell, XRPL-Labs <a href="https://github.com/dangell7/">(dangell7)</a>
Affiliation: XRPL-Labs
```

# Problem Statement

The current XRPL protocol provides a robust and efficient platform for transferring digital assets and currencies. However, there is a growing demand for more advanced financial instruments, such as funds, which are not natively supported by the XRPL in its current form. Funds are financial vehicles that pool resources from multiple investors to invest in a diversified portfolio of assets. The lack of a standardized protocol for creating and managing funds on the XRPL limits the functionality and potential use cases for the ledger. To address this gap and enhance the capabilities of XRPL protocols, we propose a new amendment that introduces a lightweight and flexible framework for fund management. This will enable developers and users to create, manage, and participate in funds, thereby expanding the financial utility of XRPL protocols and opening up new opportunities for decentralized finance (DeFi) applications.

# Amendment

The proposed amendment introduces a new framework for fund management on the XRPL protocols, providing a structured and flexible way to create, manage, and participate in funds. This enhances the financial capabilities of the XRPL, particularly for decentralized finance (DeFi) applications.

The amendment adds:

* A new type of ledger object: `ltFUND`

Five new transaction types:
* `FundCreate`
* `FundDeposit`
* `FundWithdrawal`
* `FundUpdate`
* `FundClose`

## New Ledger Object Type: `Fund`

The `Fund` ledger object is a new on-ledger object that is created with the `FundCreate` transaction. This object represents a fund on the XRPL, detailing the terms under which the fund operates, including management details, fees, and operational rules.

**Fields:**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfLedgerEntryType | String | ✔️ | The type of ledger object, which is "Fund" for fund objects. |
| sfFundManager | AccountID | ✔️ | The account ID of the fund manager responsible for managing the fund. |
| sfFundFees | Object | ✔️ | An object containing various fees associated with the fund such as management, administrative, frontend, backend, 12B1, exchange fees, account fee, account minimum, redemption fee, and redemption period. |
| sfDID | UInt256 | ✔️ | A decentralized identifier for the fund. |
| sfInitialPrice | String | ✔️ | The initial price of the fund. |
| sfAsset | Object | ✔️ | The underlying asset of the fund, including currency and issuer. |
| sfFlags | UInt32 | ✔️ | Flags to set fund properties (e.g., open or closed). |
| sfMinInvestors | UInt32 | ✔️ | Minimum number of investors required for the fund. |
| sfMaxInvestment | UInt16 | ✔️ | Maximum investment allowed per investor as a percentage of the fund. |
| sfOfferingStart | UInt32 | ❌ | Ledger index when the offering starts. |
| sfOfferingEnd | UInt32 | ❌ | Ledger index when the offering ends. |

**Example `Fund` ledger object:**

```json
{
   "LedgerEntryType": "Fund",
   "index": "D79DE793C6934943D5389CB4E5392A05A8E00881202F05FE41ADC2AE83B24E91",
   "Owner": "rHb9CJAWyB4rj91VRWn96DkukG4bwdtyTh",
   "FundManager": "rHb9CJAWyB4rj91VRWn96DkukG4bwdtyTh",
   "FundFees": {
      "ManagementFee": 250,       // 250 basis points (2.5%)
      "AdminFee": 50,             // 50 basis points (0.5%)
      "FrontendFee": 50,          // 50 basis points (0.5%)
      "BackendFee": 50,           // 50 basis points (0.5%)
      "12B1Fee": 750,             // 750 basis points (7.5%)
      "ExchangeFee": 750,         // 750 basis points (7.5%)
      "AccountFee": 1000,         // $1000 account fee (fixed amount, not in basis points)
      "AccountMin": 10000,        // $10000 minimum account balance (fixed amount, not in basis points)
      "RedemptionFee": 500,       // 500 basis points (5%)
      "RedemptionPeriod": 30      // 30 days redemption period (duration, not in basis points)
   },
   "DID": "D79DE793C6934943D5389CB4E5392A05A8E00881202F05FE41ADC2AE83B24E91",
   "InitialPrice": "100",
   "Asset": {
      "currency": "USD",
      "issuer": "rHb9CJAWyB4rj91VRWn96DkukG4bwdtyTh"
   },
   "Flags": 1, // 0 for open, 1 for closed
   "MinInvestors": 20,
   "MaxInvestment": 0.25,
   "OfferingStart": 0, // Ledger index when offering starts
   "OfferingEnd": 1 // Ledger index when offering ends
}
```

## New Transaction Type: `FundCreate`

The `FundCreate` transaction allows an account to establish a new fund on the XRPL. This transaction sets up the initial parameters and rules for the fund, including management fees, administrative fees, and other relevant details.

**Fields:**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfAccount | AccountID | ✔️ | The account creating the fund. |
| sfTransactionType | String | ✔️ | The type of transaction, which is "FundCreate" for creating a new fund. |
| sfFundManager | AccountID | ✔️ | The account ID of the fund manager responsible for managing the fund. |
| sfFundFees | Object | ✔️ | An object containing various fees associated with the fund such as management, administrative, frontend, backend, 12B1, exchange fees, account fee, account minimum, redemption fee, and redemption period. |
| sfDID | UInt256 | ✔️ | A decentralized identifier for the fund. |
| sfInitialPrice | String | ✔️ | The initial price of the fund. |
| sfAsset | Object | ✔️ | The underlying asset of the fund, including currency and issuer. |
| sfFlags | UInt32 | ✔️ | Flags to set fund properties (e.g., open or closed). |
| sfMinInvestors | UInt32 | ✔️ | Minimum number of investors required for the fund. |
| sfMaxInvestment | UInt16 | ✔️ | Maximum investment allowed per investor as a percentage of the fund. |
| sfOfferingStart | UInt32 | ❌ | Ledger index when the offering starts. |
| sfOfferingEnd | UInt32 | ❌ | Ledger index when the offering ends. |

**Example `FundCreate` transaction:**

```json
{
   "Account": "raKG2uCwu71ohFGo1BJr7xqeGfWfYWZeh3",
   "TransactionType": "FundCreate",
   "FundManager": "rHb9CJAWyB4rj91VRWn96DkukG4bwdtyTh",
   "FundFees": {
      "ManagementFee": 250,       // 250 basis points (2.5%)
      "AdminFee": 50,             // 50 basis points (0.5%)
      "FrontendFee": 50,          // 50 basis points (0.5%)
      "BackendFee": 50,           // 50 basis points (0.5%)
      "12B1Fee": 750,             // 750 basis points (7.5%)
      "ExchangeFee": 750,         // 750 basis points (7.5%)
      "AccountFee": 1000,         // $1000 account fee (fixed amount, not in basis points)
      "AccountMin": 10000,        // $10000 minimum account balance (fixed amount, not in basis points)
      "RedemptionFee": 500,       // 500 basis points (5%)
      "RedemptionPeriod": 30      // 30 days redemption period (duration, not in basis points)
   },
   "DID": "D79DE793C6934943D5389CB4E5392A05A8E00881202F05FE41ADC2AE83B24E91",
   "InitialPrice": "100",
   "Asset": {
      "currency": "USD",
      "issuer": "rHb9CJAWyB4rj91VRWn96DkukG4bwdtyTh"
   },
   "Flags": 1, // 0 for open, 1 for closed
   "MinInvestors": 20,
   "MaxInvestment": 0.25,
   "OfferingStart": 0, // Ledger index when offering starts
   "OfferingEnd": 1 // Ledger index when offering ends
}
```

## Fees Object Details for `FundCreate` Transaction

The `sfFundFees` object within the `FundCreate` transaction contains various fees associated with the management and operation of the fund. Here is a detailed breakdown of each fee component within this object:

**Fields:**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfManagementFee | UInt16 | ❌ | The percentage fee charged for the management of the fund. |
| sfAdminFee | UInt16 | ❌ | The percentage fee charged for administrative expenses of the fund. |
| sfFrontendFee | UInt16 | ❌ | The percentage fee charged upfront when an investment is made. |
| sfBackendFee | UInt16 | ❌ | The percentage fee charged when profits are realized or upon withdrawal. |
| sf12B1Fee | UInt16 | ❌ | The percentage fee charged for marketing and distribution expenses. |
| sfExchangeFee | UInt16 | ❌ | The percentage fee charged for any exchange services provided by the fund. |
| sfAccountFee | UInt64 | ❌ | A fixed fee charged per account within the fund. |
| sfAccountMin | UInt64 | ❌ | The minimum account balance required to maintain an account in the fund. |
| sfRedemptionFee | UInt16 | ❌ | The percentage fee charged when withdrawing funds before a specified period. |
| sfRedemptionPeriod | UInt32 | ❌ | The period (in days) during which the redemption fee applies. |

**Example of `sfFundFees` Object:**

```json
{
   "FundFees": {
      "ManagementFee": 250,       // 250 basis points (2.5%)
      "AdminFee": 50,             // 50 basis points (0.5%)
      "FrontendFee": 50,          // 50 basis points (0.5%)
      "BackendFee": 50,           // 50 basis points (0.5%)
      "12B1Fee": 750,             // 750 basis points (7.5%)
      "ExchangeFee": 750,         // 750 basis points (7.5%)
      "AccountFee": 1000,         // $1000 account fee (fixed amount, not in basis points)
      "AccountMin": 10000,        // $10000 minimum account balance (fixed amount, not in basis points)
      "RedemptionFee": 500,       // 500 basis points (5%)
      "RedemptionPeriod": 30      // 30 days redemption period (duration, not in basis points)
   }
}
```

### Information Stored in the DID

In the `FundCreate` transaction, the `sfDID` (Decentralized Identifier) plays a crucial role in encapsulating a comprehensive set of information about the fund that is not explicitly listed as separate fields in the transaction itself. The DID serves as a unique identifier that can store and reference detailed metadata about the fund. Below is an explanation of how various aspects of creating a fund are likely included within the DID, even though they are not directly listed as fields in the `FundCreate` transaction:

1. **Fund Name**: The official name of the fund is embedded within the DID, ensuring unique identification across the platform.
2. **Fund Type**: The type of fund (e.g., equity, bond, balanced, index) is specified in the DID, categorizing the fund accordingly.
3. **Investment Objective**: The primary goal of the fund, such as capital appreciation or income, is detailed in the DID.
4. **Investment Strategy**: The strategy outlining how the investment objectives will be achieved is included in the DID, providing a blueprint for fund management.
5. **Fund Manager(s)**: Information about the fund managers, including their names and details, is stored in the DID, linking their identity to the fund's operations.
6. **Target Audience**: The intended investors, whether retail or institutional, are specified in the DID to ensure appropriate marketing and compliance.
7. **Minimum Investment Amount**: The minimum amount required to invest in the fund is recorded in the DID, setting the entry threshold for investors.
8. **Fees and Expenses**: All associated fees, including management and administrative fees, load charges, etc., are detailed in the DID for transparency and compliance.
9. **Offering Document/Prospectus**: The DID links to or contains the offering document or prospectus, which outlines all critical aspects of the fund.
10. **Subscription Period**: The timeframe during which investors can initially buy into the fund is noted in the DID, defining the investment window.
11. **Regulatory Compliance**: Compliance with legal and regulatory requirements is affirmed in the DID, ensuring the fund meets all necessary standards.
12. **Marketing and Distribution Plans**: Strategies for marketing the fund and the distribution channels used are included in the DID, outlining how the fund reaches its target audience.
13. **Custodian and Auditor Information**: Details about the custodian of the fund’s assets and the auditor are stored in the DID, ensuring safekeeping and financial transparency.
14. **Risk Disclosure**: Information on potential risks involved with investing in the fund is disclosed in the DID, providing necessary warnings to investors.
15. **Benchmark**: The performance comparison benchmark is included in the DID, allowing investors to measure the fund's performance against standard indices.

The use of a DID in the `FundCreate` transaction allows for a robust and flexible method of storing and accessing detailed information about the fund, ensuring that all necessary data is securely managed and easily retrievable within the XRPL ecosystem. This approach enhances transparency, efficiency, and trust in the management and operation of mutual funds on the platform.

## New Transaction Type: `FundDeposit`

The `FundDeposit` transaction allows an account to deposit funds into a fund managed by a broker. This transaction is subject to strict security requirements to ensure the integrity of the fund's assets. Depending on whether the fund is open-ended or closed-ended, the depositor receives NAV (Net Asset Value) tokens in proportion to the current NAV or the Initial Price, respectively.

**Fields:**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfAccount | AccountID | ✔️ | The account ID of the depositor. |
| sfTransactionType | String | ✔️ | The type of transaction, which is "FundDeposit". |
| sfFundID | UInt256 | ✔️ | The unique identifier of the fund into which the deposit is being made. |
| sfAmount | Amount | ✔️ | The amount and currency being deposited into the fund. |

Example Fund Deposit:

```json
{
   "Account": "raKG2uCwu71ohFGo1BJr7xqeGfWfYWZeh3",
   "TransactionType": "FundDeposit",
   "FundID": "D79DE793C6934943D5389CB4E5392A05A8E00881202F05FE41ADC2AE83B24E91",
   "Amount": {
      "currency" : "USD",
      "issuer" : "rHb9CJAWyB4rj91VRWn96DkukG4bwdtyTh",
      "value" : "10"
   }
}
```

## New Transaction Type: `FundWithdraw`

The `FundWithdraw` transaction allows an account to withdraw funds from a fund by redeeming NAV tokens. This transaction is subject to strict security requirements to ensure the integrity of the fund's assets. The amount of the underlying asset received by the withdrawer is calculated based on the current NAV of the fund.

**Fields:**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfAccount | AccountID | ✔️ | The account ID of the withdrawer. |
| sfTransactionType | String | ✔️ | The type of transaction, which is "FundWithdraw". |
| sfFundID | UInt256 | ✔️ | The unique identifier of the fund from which the withdrawal is being made. |
| sfAmount | Amount | ✔️ | The amount and currency being withdrawn from the fund. |

**Example `FundWithdraw` transaction:**

```json
{
   "Account": "raKG2uCwu71ohFGo1BJr7xqeGfWfYWZeh3",
   "TransactionType": "FundWithdraw",
   "FundID": "D79DE793C6934943D5389CB4E5392A05A8E00881202F05FE41ADC2AE83B24E91",
   "Amount": {
      "currency" : "NAV",
      "issuer" : "rHb9CJAWyB4rj91VRWn96DkukG4bwdtyTh",
      "value" : "10"
   },
}
```

## New Transaction Type: `FundUpdate`

The `FundUpdate` transaction is used to modify the parameters of an existing fund on the XRPL. This transaction allows for updates to various aspects of the fund, including management details, fees, and operational rules.

**Fields:**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfTransactionType | String | ✔️ | The type of transaction, which is "FundUpdate" for updating an existing fund. |
| sfAccount | AccountID | ✔️ | The account ID of the fund manager or authorized party making the update. |
| sfFlags | UInt32 | ✔️ | Flags to set specific properties for the fund update (e.g., extension of offering period). |
| sfFundManager | AccountID | ❌ | The account ID of the new fund manager if changing the fund manager. |
| sfDID | UInt256 | ✔️ | The decentralized identifier of the fund being updated. |
| sfFundFees | Object | ❌ | An object containing updated fees associated with the fund such as management, administrative, frontend, backend, 12B1, exchange fees, account fee, account minimum, redemption fee, and redemption period. |
| sfMinInvestors | UInt32 | ❌ | Updated minimum number of investors required for the fund. |
| sfOfferingEnd | UInt32 | ❌ | Updated ledger index when the offering ends. |

**Example `FundUpdate` transaction:**

```json
{
   "Account": "raKG2uCwu71ohFGo1BJr7xqeGfWfYWZeh3",
   "TransactionType": "FundUpdate",
   "Flags": 0, // 0 for no specific flags, 1 for extension of offering period
   "FundManager": "rHb9CJAWyB4rj91VRWn96DkukG4bwdtyTh",
   "DID": "D79DE793C6934943D5389CB4E5392A05A8E00881202F05FE41ADC2AE83B24E91",
   "FundFees": {
      "Management": 0.025,
      "Administrative": 0.005,
      "Frontend": 0.005,
      "Backend": 0.005,
      "12B1": 0.075,
      "Exchange": 0.075,
      "AccountFee": 1000,
      "AccountMin": 10000,
      "RedemptionFee": 0.05,
      "RedemptionPeriod": 30
   },
   "MinInvestors": 20,
   "OfferingEnd": 1 // Updated ledger index when offering ends
}
```

## New Transaction Type: `FundClose`

The `FundClose` transaction is used to close an existing fund on the XRPL. This transaction can be initiated under circumstances such as the end of the fund's operation, liquidation, or other administrative reasons. It ensures the orderly closure of the fund, including the distribution or transfer of assets as specified.

**Fields:**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfTransactionType | String | ✔️ | The type of transaction, which is "FundClose" for closing an existing fund. |
| sfAccount | AccountID | ✔️ | The account ID of the fund manager or authorized party initiating the fund closure. |
| sfFundID | UInt256 | ✔️ | The unique identifier of the fund being closed. |
| sfFlags | UInt32 | ✔️ | Flags to specify the type of closure (e.g., liquidation, merger). |
| sfMergeTarget | UInt256 | ❌ | The fund ID of another fund with which the closing fund may be merged (if applicable). |

**Example `FundClose` transaction:**

```json
{
   "Account": "raKG2uCwu71ohFGo1BJr7xqeGfWfYWZeh3",
   "TransactionType": "FundClose",
   "FundID": "D79DE793C6934943D5389CB4E5392A05A8E00881202F05FE41ADC2AE83B24E91",
   "Flags": 0, // 0 for liquidation, 1 for merger
   "MergeTarget": "D79DE793C6934943D5389CB4E5392A05A8E00881202F05FE41ADC2AE83B24E91"
}
```

## Calculation of NAV

The Net Asset Value (NAV) for funds managed on the XRPL is calculated by determining the value of the fund's assets, adjusting for liabilities and any locked balances such as those in Escrows or PaymentChannels, and dividing by the number of outstanding shares. Here’s a detailed approach:

1. **Asset Valuation**: Sum the current market value of all investment assets held by the fund, including stocks, bonds, commodities, and any other financial instruments. For assets that are publicly traded, use the latest trading prices. For assets that are less liquid, use the most recent valuations available.

2. **Liability and Locked Balance Adjustment**: Aggregate the total value of liabilities and locked balances. This includes:
   - **PaymentChannels**: Sum the value of all active PaymentChannels, which represent the fund's financial obligations that need to be settled.
   - **Escrows**: Include the total value of funds held in Escrow, which are reserved for future payments and thus not available for immediate use.
   - **Locked Balances on Trustlines**: Consider any balances that are locked or reserved on trustlines of the underlying asset (e.g., USD). This might include funds that are pledged as collateral or reserved for specific purposes.

3. **NAV Formula**: Calculate the NAV using the following formula:

   NAV = (Total Asset Value - Total Liabilities) / Number of Outstanding Shares

   Here, "Total Liabilities" includes the sum of all liabilities, funds in Escrow, and any locked balances on trustlines related to the fund's underlying asset.

This method ensures a comprehensive assessment of the fund's net assets, providing a clear and accurate measure of its value per share.
