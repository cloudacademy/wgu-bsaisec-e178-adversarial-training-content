# Datasets

## ieee_fraud_sample.csv.gz

A sample of the **IEEE-CIS Fraud Detection** dataset: online card transactions labelled as fraudulent or legitimate, published for the IEEE Computational Intelligence Society competition hosted on Kaggle.

| Property | Value |
| --- | --- |
| Source | IEEE-CIS Fraud Detection, <https://www.kaggle.com/competitions/ieee-fraud-detection/data> |
| Rows | 40,000 (3,185 fraud, 7.96%) |
| Columns | 31 (25 numeric, 5 categorical, plus the `isFraud` label) |

The sample joins `train_transaction.csv` with `train_identity.csv` on `TransactionID`, keeps only rows that carry device identity information, and takes a stratified sample that preserves the natural class imbalance.

The sample is committed to this repository, so the lab environment needs no Kaggle account and no network access to the competition data.

### Columns

| Group | Columns | Meaning |
| --- | --- | --- |
| Amount | `TransactionAmt` | Transaction amount |
| Card | `card1`, `card2`, `card3`, `card5` | Anonymised payment card attributes |
| Counts | `C1`–`C14` | Counting features, such as how many addresses are associated with the card |
| Timedelta | `D1` | Days since a previous related transaction |
| Identity | `id_01`, `id_02`, `id_05`, `id_06`, `id_11` | Anonymised network and device signals |
| Categorical | `ProductCD`, `card4`, `card6`, `P_emaildomain`, `DeviceType` | Product code, card network, card type, purchaser email domain, device type |
| Label | `isFraud` | 1 = fraud |

`P_emaildomain` keeps the ten most common domains and buckets the remainder as `other`; missing values are labelled `missing`. `DeviceType` is `desktop` or `mobile`.

### Class imbalance

About 8% of the transactions are fraudulent. A classifier that labelled every transaction legitimate would score roughly 92% accuracy while catching no fraud at all, so overall accuracy has to be read alongside the fraud-class metrics in every measurement this lab takes.

### Which columns an attacker can move

Only one column in this dataset is set outright by whoever makes the purchase: `TransactionAmt`.

The `C` counting features, `D1`, and the `id_*` signals are not fields in a payment request. They are values the bank derives from account history or observes about the session, such as how many addresses are associated with the card, how long it has been since a related transaction, and what the network and device look like. An attacker influences them indirectly and over time, by choosing how many cards and addresses to cycle through, how to space transactions out, and what devices to operate from.

The remaining columns are fixed for a given transaction. The card identifiers are properties of the account in use, and the categorical columns are genuine choices rather than continuous quantities: a card network is Visa or Mastercard, never a fraction of each.

The lab's attack mask covers the first two groups, which models a patient, adaptive fraudster shaping a behavioural profile rather than someone editing a single request. That is a deliberately generous threat model, and the lab asks students to measure how much of the attack's success depends on it.

