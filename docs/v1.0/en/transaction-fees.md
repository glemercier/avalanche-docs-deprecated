# Transaction Fees

This document describes the Avalanche Network's fees.

## Basics

In order to prevent spam, transactions on the Avalanche network require the payment of a transaction fee.
The fee is paid in AVAX, Avalanche's native token.

**The transaction fee is burned.**
That is, the AVAX tokens paid in a transaction fee do not go to anywhere. Instead, they are destroyed forever.

When you issue a transaction through Avalanche's API, the transaction fee is automatically deducted from
one of the addresses you control.

## Fee Schedule

Different types of transactions require payment of a different transaction fee.
This table shows the transaction fee schedule:


```boo
+----------+-------------------+------------------------+
| Chain    : Transaction Type  | Transaction Fee (AVAX) |
+----------+-------------------+------------------------+
| P        : Create Blockchain |                   0.01 |
+----------+-------------------+------------------------+
| P        : Add Validator     |                      0 |
+----------+-------------------+------------------------+
| P        : Add Delegator     |                      0 |
+----------+-------------------+------------------------+
| P        : Create Subnet     |                   0.01 |
+----------+-------------------+------------------------+
| P        : Import AVAX       |                  0.001 |
+----------+-------------------+------------------------+
| P        : Export AVAX       |                  0.001 |
+----------+-------------------+------------------------+
| X        : Send              |                  0.001 |
+----------+-------------------+------------------------+
| X        : Create Asset      |                   0.01 |
+----------+-------------------+------------------------+
| X        : Mint Asset        |                  0.001 |
+----------+-------------------+------------------------+
| X        : Import AVAX       |                  0.001 |
+----------+-------------------+------------------------+
| X        : Export AVAX       |                  0.001 |
+----------+-------------------+------------------------+
```

The C-Chain gas price is 4.7e-7 AVAX/gas.
The C-Chain gas limit is 10e8.

