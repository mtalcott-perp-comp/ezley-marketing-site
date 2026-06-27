---
layout: legal
permalink: /privacy/
title: "Privacy Policy"
description: "What information Ezley collects, how we use it, who we share it with, and the choices you have. We do not sell personal information."
effective_date: 2026-06-27
contact_email: privacy@ezley.com
---

## 1. Who we are

Ezley is an online marketplace operated by **Tarheel Solutions, LLC** ("Ezley," "we," "us," or "our") at **ezley.com** (and related domains), where individuals and small businesses ("Sellers") offer high-value used goods to other individuals and small businesses ("Buyers"). Ezley provides the platform, identity verification, access to escrow services (provided by licensed third parties — Stripe for card and ACH, and a licensed escrow provider for wire transfers), dispute mediation, and an optional concierge support program. This Privacy Policy explains what personal information we collect from visitors and registered users, how we use it, who we share it with, and the choices you have.

## 2. What we collect

### 2.1 Account information (registered users)

- **Email address** and **display name** — required to create an account.
- **Auth0-issued identifier** — a stable user ID we use to map across our systems.
- **Phone number** — optional, used for transaction notifications and concierge contact.
- **Profile photo and biographical details** — optional.
- **Verification information** — if you choose to upgrade to a Verified or Trusted Seller tier, we collect identity-verification information through our identity-verification provider. Sensitive identity documents are processed by that provider and we retain only the verification status, not the underlying documents.

### 2.2 Listing information (Sellers)

- **Item details** — make, model, year, hours, serial number, horsepower, fuel type, photos, price, and any free-text description you provide.
- **Safe-meeting location** — for Local Meetup transactions, the meetup location address or place ID.
- **Equipment-specific structured fields** for the category you list in.

### 2.3 Transaction information (Buyers and Sellers)

- **Counterparty identifiers** — the Buyer and Seller participating in each Transaction see each other's display name and (where applicable) phone number.
- **Payment information** — handled by Stripe. Ezley never sees your full credit-card number, bank-routing details, or wire-transfer bank account. We see the payment method type (Card / ACH / Wire), a Stripe payment-intent ID, and the amount.
- **Escrow state** — the lifecycle of your Transaction's escrow (Captured, Released, Disputed, Refunded).
- **Handover record** — for Local Meetup transactions, a confirmation that the physical handover occurred (and optionally a single photo provided by the Concierge).
- **Messages** — the content of in-app messages exchanged between Buyer, Seller, and (where applicable) Concierge.

### 2.4 Automatically collected information

- **Device and browser information** — user agent, IP address, screen size, timezone — used for analytics and to prevent fraud.
- **Usage events** — page views, listing impressions, search queries, time-on-page. We do not sell this data; we use it to improve the product.
- **Cookies and similar technology** — see § 7 for the cookie list.

### 2.5 What we do NOT collect

- **Full payment-card numbers.** Stripe is our PCI-DSS-compliant payment processor; cards are tokenized at the browser before any value reaches Ezley.
- **Full bank-account numbers** for ACH or wire transfers. Same — Stripe handles the bank data.
- **Social Security numbers, driver's license numbers, or other government IDs.** Except where required by our identity-verification provider for Verified/Trusted Seller tier upgrades, and even then Ezley does not retain the document.
- **Location data beyond the safe-meeting place ID you choose.** We do not track your real-time location.

## 3. How we use what we collect

We use the information described above to:

- **Operate the marketplace.** Match Buyers to Sellers, host Listings, process Transactions, run Escrow, dispatch Concierge support, and resolve Disputes.
- **Protect users.** Detect fraud, prevent abuse, enforce our Terms of Service, and verify that Sellers offering high-value Listings meet our verification standards.
- **Communicate with you.** Send Transaction-related notifications (state changes, handover reminders, wire-instructions, dispute notices) and operational announcements. You can choose which notification channels (in-app, email, SMS) you receive via your account settings.
- **Improve the product.** Aggregate usage analytics to understand what works and what doesn't.
- **Comply with law.** Respond to subpoenas, court orders, and tax-reporting obligations.

We do **NOT** use your information to:

- Sell or rent personal information to third-party marketers.
- Train external AI models without your consent.
- Deliver behavioral advertising on third-party sites.

## 4. Who we share with

We share information only as needed to operate the platform:

| Recipient | What we share | Why |
|---|---|---|
| **Stripe (Stripe Connect)** | Payment method, amount, transaction reference, Seller and Buyer identifiers, your payment information when you initiate a charge. | Process payments, hold funds in escrow, transfer to Sellers, handle chargebacks. |
| **Auth0** | Email, display name, phone number (if provided), Auth0 identifier. | Manage your account login and authentication. |
| **Microsoft Azure** | All operational data — Listings, Transactions, Messages, event logs. | Hosting infrastructure. Data is stored in US-based Azure regions. |
| **SendGrid** | Email address, the content of operational emails we send you. | Deliver transactional and notification email. |
| **Your transaction counterparty** | Display name, phone number (if you provided one), the content of in-app messages. | Let the parties to a Transaction communicate. |
| **Concierge operator(s)** | Listing details, Transaction details, messages, dispute records — for the specific Transactions they shepherd. | Provide the concierge service. |
| **Identity-verification provider** | Identity-document submission (only at your initiation, only at verified-tier upgrade). | Confirm Seller identity. |
| **Law-enforcement / courts** | Whatever is legally compelled by a valid subpoena, search warrant, or court order. | Comply with applicable law. |

We do **NOT** sell personal information to third parties.

## 5. Your rights

Regardless of where you reside, Ezley offers all users:

- **Access.** Request a copy of the personal information we hold about you. Email privacy@ezley.com; we respond within 30 days.
- **Deletion.** Request that we delete your account and associated personal information. Note: financial records (Transactions, escrow ledger entries) are retained for the period required by applicable tax and financial-services law (typically 7 years).
- **Correction.** Update any inaccurate information directly via your account settings, or by email request.
- **Portability.** Request an export of your account data in a machine-readable format (JSON or CSV).
- **Withdraw consent.** Disable notification channels in account settings. Disable optional features (concierge-as-help, agent representation) at any time.

If you are a California resident, you have additional rights under the **California Consumer Privacy Act (CCPA)**. If you are in the EU/UK or EEA, you have rights under the **GDPR**. Contact privacy@ezley.com to exercise them.

## 6. Retention

| Category | Retention period |
|---|---|
| Account information | Until you delete your account, plus 30 days for cleanup. |
| Listing information | While the Listing is Active, plus 90 days after Removed or Sold. |
| Transaction records (including escrow ledger entries) | **7 years** after Transaction completion or cancellation — required by federal tax and financial-services recordkeeping rules. |
| Messages between Buyer and Seller | 2 years after the Transaction ends. |
| Dispute records | 7 years after dispute closure. |
| Verification status | Until account deletion. Underlying identity documents are not retained by Ezley. |
| Aggregated, anonymized analytics | Indefinitely. Contains no personal information. |

## 7. Cookies and similar technology

We use the following cookies:

| Cookie | Purpose | Duration |
|---|---|---|
| `auth0_session` | Keep you logged in. | Session (cleared on logout). |
| `ezley_csrf` | Prevent cross-site request forgery on form submissions. | Session. |
| `ezley_sse_session` | Server-sent-event stream session for real-time notifications. | 5 minutes. |
| `ezley_ui_prefs` | Remember non-sensitive UI preferences (theme, last-viewed category). | 30 days. |

We do not use third-party advertising cookies. We do not use analytics that share data with ad networks.

## 8. Children's privacy

Ezley is not directed to individuals under 18. We do not knowingly collect personal information from individuals under 18. If you are aware that a person under 18 has provided personal information to Ezley, contact privacy@ezley.com and we will delete it.

## 9. Security

We use industry-standard practices to protect your information: encryption in transit (TLS 1.2+), encryption at rest for sensitive data, role-based access controls for operators, and security review processes for code changes. No system is perfectly secure. We will notify affected users within 72 hours of confirming a personal-information breach as required by applicable breach-notification laws.

## 10. Changes to this policy

We may update this Privacy Policy. Material changes will be announced via in-app notification and email at least 30 days before they take effect. The "Last updated" date at the top reflects the most recent revision.

## 11. Contact

Questions, requests, or complaints: **privacy@ezley.com**

For unresolved complaints, residents of the EU may contact their local Data Protection Authority. California residents may contact the California Attorney General.
