# Transactions

# The Structure of a Transaction

# The Transaction Nonce

## Keeping Track of Nonces


## Gaps in Nonces, Duplicate Nonces, and Confirmation

## Concurrency, Transaction Origination, and Nonces


# Transaction Gas

# Transaction Recipient

# Transaction Value and Data

## Transmitting Value to EOAs and Contracts
## Transmitting a Data Payload to an EOA or Contract

# Special Transaction: Contract Creation

# Digital Signatures

## The Elliptic Curve Digital Signature Algorithm
## How Digital Signatures Work
### Creating a digital signature
### Verifying the Signature

## ECDSA Math

# Transaction Signing in Practice
# Raw Transaction Creation and Signing
## Raw Transaction Creation with EIP-155
[Table 1. Chain identifiers]
|Chain	|Chain ID|
|--|--|
|Ethereum mainnet|1|

Morden (obsolete), Expanse

2

Ropsten

3

Rinkeby

4

Rootstock mainnet

30

Rootstock testnet

31

Kovan

42

Ethereum Classic mainnet

61

Ethereum Classic testnet

62

Geth private testnets

1337
# The Signature Prefix Value (v) and Public Key Recovery
# Separating Signing and Transmission (Offline Signing)
![offline signing](https://github.com/ethereumbook/ethereumbook/raw/develop/images/offline_signing.png)  

# Transaction Propagation
# Recording on the Blockchain
# Multiple-Signature (Multisig) Transactions
# Conclusions


