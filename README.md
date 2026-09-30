# Citi_Drunix_Hackathon

## 1. The idea in one line

A permissioned marketplace where real-world assets are **verified before
tokenization**, represented as digital units on DRUNIX, traded between
verified participants, and settled only after payment confirmation.

## 2. Why are we building this?

Buying or selling a valuable real-world asset usually involves
documents, manual verification, intermediaries, payments, ownership
records, and a lot of trust.

Even if we put a token on a blockchain, one important question remains:

> **Does this token actually represent a real, verified asset?**

Our solution focuses on that missing trust layer.

Instead of simply creating tokens, we create a flow where an authorized
verifier first validates the underlying asset. Only then can the asset
be tokenized and listed in the marketplace.

The blockchain becomes the shared source of truth for the important
events: verification, token issuance, ownership, signatures, payment
confirmation, settlement, and audit history.

## 3. What makes the solution different?

The core principle is:

**Verify → Tokenize → Trade → Pay → Settle → Audit**

The marketplace is not just a crypto-style exchange. It is designed
around permissioned participants and real-world asset verification.

For example:

-   A seller submits a gold asset with its supporting certificate.
-   A verifier checks the certificate and asset details.
-   The verifier attests the asset on DRUNIX.
-   The system mints tokenized units only after verification.
-   A buyer purchases some of those units.
-   Both parties sign the digital contract.
-   Payment is confirmed through the payment adapter.
-   Ownership is settled on DRUNIX.
-   The regulator can view the complete audit trail.

## 4. Example

Imagine a verified 100 g gold asset.

We can represent it as:

-   Asset: 100 g verified gold
-   Token unit: 0.1 g
-   Total supply: 1,000 units

A seller could list 250 units.

A buyer purchases 100 units.

The system does not immediately transfer the tokens just because the
buyer clicked "Buy".

Instead:

1.  Trade is created.
2.  Contract terms are generated.
3.  Seller and buyer sign the contract.
4.  Payment is confirmed.
5.  DRUNIX settles the token transfer.
6.  The ledger records the complete transaction history.

The same framework can support other asset classes if the appropriate
verifier and legal structure exist.

## 5. High-level architecture


                         ┌───────────────────────────────┐
                         │        USERS / PORTALS        │
                         │ Seller | Buyer | Regulator    │
                         └───────────────┬───────────────┘
                                         │
                                         ▼
                         ┌───────────────────────────────┐
                         │      MARKETPLACE BACKEND      │
                         │ API • Auth • Listings • Trade  │
                         │ Matching • Notifications      │
                         └───────┬───────────────┬───────┘
                                 │               │
                    ┌────────────┘               └─────────────┐
                    ▼                                          ▼
          ┌──────────────────┐                       ┌──────────────────┐
          │ AI / ML SERVICES │                       │ PAYMENT ADAPTER  │
          │ Document checks  │                       │ NPCI/UPI Sandbox │
          │ Risk / anomaly   │                       │ Payment status   │
          │ Price insights   │                       └────────┬─────────┘
          └────────┬─────────┘                                │
                   │                                          │
                   └──────────────────┬───────────────────────┘
                                      ▼
                         ┌───────────────────────────────┐
                         │       DRUNIX NETWORK          │
                         │                               │
                         │ Seller Org   Buyer Org        │
                         │ Verifier Org Regulator Org    │
                         │                               │
                         │ Chaincode + Ledger + PDCs     │
                         └───────────────────────────────┘


## 6. What stays on-chain?

Only information that benefits from a shared, tamper-evident ledger
should be stored on DRUNIX.

-   Asset ID
-   Asset type
-   Token supply
-   Verification status
-   Verifier attestation
-   Token balances / ownership state
-   Listing and trade state
-   Contract hash
-   Digital-signature records
-   Payment confirmation
-   Settlement event
-   Freeze/unfreeze state
-   Audit history

## 7. What stays off-chain?

Large files and application data do not need to live directly on the
ledger.

-   User interface
-   Authentication/session data
-   Asset images
-   PDFs and certificates
-   Full contract documents
-   Search indexes
-   Marketplace analytics
-   Notifications
-   AI/ML processing
-   Payment API integration

The hash of important documents can be recorded on DRUNIX so that a
later copy can be checked for integrity.

## 8. DRUNIX organizations

### Seller / Originator

Registers assets and creates listings.

### Buyer / Investor

Browses listings, proposes purchases, signs contracts, and pays.

### Verifier

Checks the real-world evidence and attests that the submitted asset
passed verification.

### Regulator / Auditor

Gets visibility into the ledger and audit history and can apply controls
such as freezing an asset in the prototype.

The regulator does not need to endorse every normal trade.

## 9. Core smart-contract / chaincode flow

``` text
RegisterAsset
      ↓
AttestAsset
      ↓
MintUnits
      ↓
CreateListing
      ↓
ProposeTrade
      ↓
SellerSigns / BuyerSigns
      ↓
FULLY_SIGNED
      ↓
ConfirmPayment
      ↓
SettleTrade
      ↓
Ownership Updated
```

Possible exceptional states:

``` text
CANCELLED
FROZEN
```

## 10. Digital contract design

Before signatures are collected, the backend creates a canonical
representation of the trade:

``` json
{
  "assetId": "GOLD-001",
  "units": 100,
  "price": 85000,
  "buyer": "BUYER-01",
  "seller": "SELLER-01",
  "nonce": "unique-value",
  "expiry": "timestamp"
}
```

The canonical data is hashed using SHA-256.

The buyer and seller sign the hash.

DRUNIX records:

-   contract hash
-   signature information
-   signing identities
-   timestamps
-   trade state

The complete PDF can remain off-chain.

This creates a simple integrity check: if the document changes later,
its hash will no longer match the recorded value.

## 11. AI / ML layer

AI is used where it provides practical value rather than being added
just for presentation.

### Document intelligence

Extract fields from certificates, invoices, deeds, valuation reports, or
other evidence.

### Consistency checking

Compare extracted document information with the asset metadata submitted
by the seller.

### Risk/anomaly detection

Flag unusual combinations such as:

-   suspicious valuation
-   repeated document identifiers
-   inconsistent asset details
-   abnormal trading activity
-   unusual price movements

### Explainable buyer view

Instead of showing only a risk score, the system can explain:

> "The submitted certificate and marketplace metadata match on asset ID
> and weight, but the valuation is outside the historical range used by
> the model."

AI flags are advisory; the verifier remains responsible for the
attestation in the prototype.

## 12. Payment and settlement

The system separates **money movement** from **asset state**.

The payment adapter communicates with the payment environment.

After a valid payment confirmation:

``` text
Payment Confirmed
       ↓
confirmPayment()
       ↓
Settlement validation
       ↓
Token ownership transfer
       ↓
Trade = SETTLED
```

This avoids treating a marketplace click as proof that money has
actually moved.

For the hackathon, a sandbox or mocked payment adapter can be used if
the required payment integration is not available.

## 13. Security and privacy

The network is permissioned.

Access is based on organization identity and roles.

Sensitive trade information can be kept in private data structures while
a verifiable hash or state remains on the shared ledger.

Important controls include:

-   Role-based authorization
-   Organization-based endorsement
-   Signature verification
-   Asset freeze capability
-   Document hash verification
-   Immutable audit trail
-   No minting before verifier attestation

## 14. MVP scope

To keep the hackathon implementation realistic, the first demo should
support one asset class.

Recommended demo:

**Verified Gold Marketplace**

MVP features:

1.  Seller registration
2.  Asset document upload
3.  AI document extraction
4.  Verifier approval
5.  Token minting
6.  Marketplace listing
7.  Buyer purchase request
8.  Digital contract generation
9.  Buyer + seller signatures
10. Payment confirmation
11. DRUNIX settlement
12. Regulator audit view
13. Asset freeze demonstration

After this works, additional asset types can be added through
verifier-specific modules.

## 15. Suggested technology stack

### Frontend

-   React.js

### Backend

-   Node.js
-   Express.js

### Database

-   PostgreSQL

### Distributed ledger

-   DRUNIX

### Smart contract

-   Chaincode compatible with the DRUNIX/Fabric-style programming model

### AI/ML

-   Python service
-   Document extraction
-   Anomaly/risk model

### Storage

-   Off-chain object/file storage for PDFs and images

### Payments

-   NPCI/UPI sandbox or a simulated payment adapter for the prototype

## 16. API examples

``` text
POST   /assets
POST   /assets/:id/verify
POST   /assets/:id/mint
GET    /assets
POST   /listings
POST   /trades
POST   /trades/:id/sign
POST   /trades/:id/payment
POST   /trades/:id/settle
POST   /assets/:id/freeze
GET    /trades/:id/history
```

The backend should never bypass the ledger for events that affect
ownership or settlement.

## 17. Demo story

The complete presentation can be demonstrated in about 3--5 minutes:

**1. Seller uploads asset**

The seller submits a gold certificate.

**2. AI reads the document**

The system extracts weight, purity, certificate ID and other fields.

**3. Verifier checks it**

The verifier approves the asset.

**4. Tokenization**

The system creates 1,000 units representing the verified asset.

**5. Seller lists units**

250 units are offered in the marketplace.

**6. Buyer purchases**

The buyer selects 100 units.

**7. Contract**

The system generates the trade contract and both parties sign it.

**8. Payment**

Payment is confirmed through the payment adapter.

**9. Settlement**

DRUNIX transfers ownership units and records the settlement.

**10. Regulator view**

The regulator opens the asset history and can see the complete chain of
events.

## 18. Why blockchain is actually needed here

A normal database can store listings and transactions.

The distributed ledger becomes useful when multiple independent
organizations need to agree on important events without giving one
application owner complete control over the shared record.

In this solution:

-   Seller provides the asset.
-   Verifier provides the attestation.
-   Buyer receives the asset representation.
-   Regulator needs audit visibility.
-   DRUNIX provides a shared permissioned transaction history.

That is the part where a permissioned DLT has a meaningful role.

## 19. Important limitation

Tokenization does not automatically create legal ownership of a
real-world asset.

The legal relationship between the token, the underlying asset, custody,
redemption rights, and ownership must be defined by the applicable legal
and regulatory structure.

Therefore, this prototype should describe tokens as a **digital
representation of an economic or ownership interest**, rather than
claiming that every token is automatically legal title to the underlying
asset.

## 20. Future scope

-   Multiple asset classes
-   More verifier organizations
-   Automated compliance checks
-   Secondary marketplace
-   Cross-organization settlement
-   Real-time fraud monitoring
-   More sophisticated valuation models
-   Asset redemption workflows
-   Regulatory reporting
-   Interoperability with other tokenization networks

## 21. The pitch

> **We are not just putting assets on a blockchain. We are building the
> trust layer around tokenization.**
>
> An asset is verified before it is tokenized, important trade events
> are recorded on a permissioned shared ledger, contracts are
> cryptographically linked to the transaction, payment is confirmed
> before settlement, and regulators get an auditable view of the entire
> lifecycle.
>
> The result is a marketplace where the question is not only "Who owns
> this token?" but also "Why should we trust what this token
> represents?"
