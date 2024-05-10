Title: Decentralized Governance
Type: Draft
Author:
	Denis Angell, XRPL-Labs <a href="https://github.com/dangell7/">(dangell7)</a>
Affiliation: XRPL-Labs

# Problem Statement

The XRPL currently lacks a native mechanism to facilitate decentralized governance and budgeting directly on the ledger. This limitation restricts community engagement and the ability to manage budgets and make decisions transparently and efficiently. Additionally, there is no standardized way to create and vote on board member proposals, meeting minutes, and budgets, which can enhance organizational operations on the XRPL. To address these gaps, we propose a new amendment that introduces a structured framework for creating, voting, and managing governance and budgeting activities directly on the XRPL.

# Amendment

The proposed amendment introduces a new framework for managing governance and budgeting on the XRPL, providing a structured and transparent way to engage with community governance and organizational operations.

The amendment adds:

* Four new ledger entries:
   * `BoardOfDirectors`
   * `MeetingMinutes`
   * `Budget`
   * `Proposal`

* Eight new transaction types:
  * `BoardCreate`
  * `BoardMemberProposal`
  * `BoardVote`
  * `MeetingMinutesCreate`
  * `MeetingMinutesVote`
  * `BudgetCreate`
  * `BudgetVote`
  * `ProposalCreate`
  * `ProposalVote`


## New Ledger Objects

### New Ledger Object Type: `BoardOfDirectors`

The `BoardOfDirectors` ledger object will store information about the board members and their roles within the organization.

**Fields for `BoardOfDirectors`:**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfAccount | AccountID | ✔️ | The account that manages the board (typically an admin or a multisig account). |
| sfMembers | Array | ✔️ | A list of objects representing board members, each containing an AccountID and weight. |
| sfFlags | UInt8 | ✔️ | Flag indicating the status of the board: 0 for "active", 1 for "inactive". |

**Example `BoardOfDirectors` object:**

```json
{
   "LedgerEntryType": "BoardOfDirectors",
   "Account": "rAdminAccount",
   "Members": [
       {"MemberAccount": "rMember1", "Weight": 1},
       {"MemberAccount": "rMember2", "Weight": 1}
   ],
   "Flags": 0
}
```

### New Ledger Object Type: `MeetingMinutes`

The `MeetingMinutes` ledger object will store references to meeting documents and record the outcomes of decisions made by the board of directors.

**Fields for `MeetingMinutes`:**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfAccount | AccountID | ✔️ | The account that created the meeting record. |
| sfURI | VariableLength | ✔️ | A URI linking to detailed meeting minutes. |
| sfDecision | UInt8 | ✔️ | Decision outcome: 1 for "approved", 0 for "rejected". |
| sfMeetingDate | UInt32 | ✔️ | The date of the meeting. |
| sfFlags | UInt8 | ✔️ | Flag indicating the status of the meeting record: 0 for "open", 1 for "closed". |

**Example `MeetingMinutes` object:**

```json
{
   "LedgerEntryType": "MeetingMinutes",
   "Account": "rExampleBoardAccount",
   "URI": "https://example.com/meeting123",
   "Decision": 1,
   "MeetingDate": "745329202",
   "Flags": 0
}
```

### New Ledger Object Type: `Budget`

The `Budget` ledger object represents a specific budget allocation for a defined period or project. This object details the total amount allocated, the purpose, and the status of the budget.

**Fields for `Budget`:**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfAccount | AccountID | ✔️ | The account that created the budget. |
| sfURI | VariableLength | ✔️ | A URI linking to detailed budget information. |
| sfTotalAmount | Amount | ✔️ | The total amount allocated for the budget. |
| sfExpiration | UInt32 | ✔️ | The time at which the budget expires. |
| sfFlags | UInt8 | ✔️ | Flag indicating the status of the budget: 0 for "open", 1 for "closed". |

**Example `Budget` object:**

```json
{
   "LedgerEntryType": "Budget",
   "Account": "rBudgetCreatorAccount",
   "URI": "https://example.com/budget123",
   "TotalAmount": {
      "currency": "USD",
      "issuer": "rIssuerAddress",
      "value": "10000"
   },
   "Expiration": "745329202",
   "Flags": 0
}
```

### New Ledger Object Type: `Proposal`

The `Proposal` ledger object is a new on-ledger object that is created with the `ProposalCreate` transaction. This object represents a proposal for community funding or decision-making on the XRPL, detailing the terms and status of the proposal.

The object has the following fields:

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfAccount | AccountID | ✔️ | The account that created the proposal. |
| sfURI | VariableLength | ✔️ | A URI linking to detailed proposal information. |
| sfAmount | Amount | ✔️ | The funding amount being requested. |
| sfExpiration | UInt32 | ✔️ | The time at which the proposal expires. |
| sfFlags | UInt8 | ✔️ | Flag indicating the status of the proposal: 0 for "open", 1 for "closed". |
| sfBudgetID | Hash256 | ✔️ | The budget ID |

Example `Proposal` object:

```json
{
   "LedgerEntryType": "Proposal",
   "Account": "rExampleAccount",
   "URI": "https://example.com/proposal123",
   "BudgetID": "ABC123...",
   "Amount": {
      "currency": "USD",
      "issuer": "fIssuerAddress",
      "value": "1000"
   },
   "Expiration": "745329202",
   "Flags": 0
}
```

This ledger object type allows for the structured and transparent handling of quotations on the XRPL, enabling businesses and individuals to engage in commercial activities with greater efficiency and accountability.

## New Transaction Types

### `BoardCreate`

This transaction type is used to initially create the board of directors when no board exists. It sets up the foundational structure and appoints initial members.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfAccount | AccountID | ✔️ | The account submitting the transaction (typically an admin or a founding member). |
| sfMembers | Array | ✔️ | An array of objects representing initial board members, each containing an AccountID and weight. |

**Example `BoardCreate`:**

```json
{
   "TransactionType": "BoardCreate",
   "Account": "rFounderAccount",
   "Members": [
       {"MemberAccount": "rMember1", "Weight": 1},
       {"MemberAccount": "rMember2", "Weight": 1},
       {"MemberAccount": "rMember2", "Weight": 1}
   ]
}
```

### `BoardMemberProposal`

This transaction type is used to propose the addition or removal of a board member. It initiates a voting process among existing board members.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfAccount | AccountID | ✔️ | The account submitting the proposal. |
| sfProposalType | UInt8 | ✔️ | Type of proposal: 1 for "Add", 0 for "Remove". |
| sfMemberAccount | AccountID | (Conditional) | The account of the member to be added or removed. |
| sfRole | VariableLength | (Conditional) | The role assigned to the new board member (required if adding). |

**Example `BoardMemberProposal` for Adding a Member:**

```json
{
   "TransactionType": "BoardMemberProposal",
   "Account": "rBoardSecretary",
   "ProposalType": 1,
   "MemberAccount": "rNewMember",
   "Weight": 1
}
```

**Example `BoardMemberProposal` for Removing a Member:**

```json
{
   "TransactionType": "BoardMemberProposal",
   "Account": "rBoardSecretary",
   "ProposalType": 0,
   "MemberAccount": "rExistingMember"
}
```

### `BoardVote`

This transaction type is used by board members to vote on proposals regarding the addition or removal of members.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfAccount | AccountID | ✔️ | The account of the board member voting. |
| sfProposalID | Hash256 | ✔️ | The ID of the proposal being voted on. |
| sfVote | UInt8 | ✔️ | Vote on the proposal: 1 for "approve", 0 for "reject". |

**Example `BoardVote`:**

```json
{
   "TransactionType": "BoardVote",
   "Account": "rBoardMember",
   "ProposalID": "DEF456...",
   "Vote": 1
}
```

### `MeetingMinutesCreate`

This transaction type is used to create a new record for meeting minutes.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfAccount | AccountID | ✔️ | The account creating the meeting record. |
| sfURI | VariableLength | ✔️ | A URI linking to detailed meeting minutes. |

**Example `MeetingMinutesCreate`:**

```json
{
   "TransactionType": "MeetingMinutesCreate",
   "Account": "rExampleBoardAccount",
   "URI": "https://example.com/meeting123"
}
```

### `MeetingMinutesVote`

This transaction type is used by board members to vote on the decision documented in the meeting minutes.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfAccount | AccountID | ✔️ | The account of the board member voting. |
| sfMeetingMinutesID | Hash256 | ✔️ | The ID of the meeting minutes being voted on. |
| sfVote | UInt8 | ✔️ | Vote on the decision: 1 for "approve", 0 for "reject". |

**Example `MeetingMinutesVote`:**

```json
{
   "TransactionType": "MeetingMinutesVote",
   "Account": "rBoardMemberAccount",
   "MeetingMinutesID": "ABC123...",
   "Vote": 1
}
```

### `BudgetCreate`

This transaction type is used to create a new budget. It specifies the total amount, purpose, and duration of the budget.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfAccount | AccountID | ✔️ | The account creating the budget. |
| sfURI | VariableLength | ✔️ | A URI linking to detailed budget information. |
| sfTotalAmount | Amount | ✔️ | The total amount allocated for the budget. |
| sfExpiration | UInt32 | ✔️ | The time at which the budget expires. |

**Example `BudgetCreate`:**

```json
{
   "TransactionType": "BudgetCreate",
   "Account": "rBudgetCreatorAccount",
   "URI": "https://example.com/budget123",
   "TotalAmount": {
      "currency": "USD",
      "issuer": "rIssuerAddress",
      "value": "10000"
   },
   "Expiration": "745329202"
}
```

### `BudgetVote`

This transaction type is used to vote on the approval of a new budget. Community members or designated voters can approve or reject the proposed budget.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfAccount | AccountID | ✔️ | The account voting on the budget. |
| sfBudgetID | Hash256 | ✔️ | The ID of the budget being voted on. |
| sfVote | UInt8 | ✔️ | Vote on the budget: 1 for "approve", 0 for "reject". |

**Example `BudgetVote`:**

```json
{
   "TransactionType": "BudgetVote",
   "Account": "rVoterAccount",
   "BudgetID": "ABC123...",
   "Vote": 1
}
```

### `ProposalCreate`

This transaction type is used to create a new proposal for community funding or decision-making.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfAccount | AccountID | ✔️ | The account creating the proposal. |
| sfURI | VariableLength | ✔️ | A URI linking to detailed proposal information. |
| sfAmount | Amount | ✔️ | The funding amount being requested. |
| sfExpiration | UInt32 | ✔️ | The time at which the proposal expires. |
| sfBudgetID | Hash256 | ✔️ | The ID of the budget. |

Example `ProposalCreate`:

```json
{
   "TransactionType": "ProposalCreate",
   "Account": "rExampleAccount",
   "URI": "https://example.com/proposal123",
   "BudgetID": "ABC123...",
   "Amount": {
      "currency": "USD",
      "issuer": "fIssuerAddress",
      "value": "1000"
   },
   "Expiration": "745329202",
}
```

### `ProposalVote`

This transaction type is used to vote on existing proposals.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| sfAccount | AccountID | ✔️ | The account voting on the proposal. |
| sfProposalID | Hash256 | ✔️ | The ID of the proposal being voted on. |
| sfFlags | UInt8 | ✔️ | Flag indicating voting preference: 1 for "for", 0 for "against". |

Example `ProposalVote`:

```json
{
   "TransactionType": "ProposalVote",
   "Account": "rAnotherExampleAccount",
   "ProposalID": "9FAB321...",
   "Flags": 1
}
```
