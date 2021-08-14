# Wallets
지갑의 의미가 다양함  
상위 개념에선 이더리움과 주 사용자 인터페이스로서 소프트웨어 애플리케이션이다  
범위를 좁혀서 개발자 관점에선 사용자 키를 관리하는 프로그램임  
본 챕터에서는 개발자 관점에서만 다룬다  

# Wallet Technology Overview


## Nondeterministic (Random) Wallets


## Deterministic (Seeded) Wallets


## Hierarchical Deterministic Wallets (BIP-32/BIP-44)



## Seeds and Mnemonic Codes (BIP-39)


# Wallet Best Practices


## Mnemonic Code Words (BIP-39)



## Generating mnemonic words
[Mnemonic codes: entropy and word length]
| Entropy  (bits) | Checksum  (bits) | Entropy + checksum  (bits) | Mnemonic length  (word) |    
|---|---|---|--|  
| 128 | 4 | 132 | 12 |    
| 160 | 5 | 165 | 15 |  
| 192 | 6 | 198 | 18 |  
| 224 | 7 | 231 | 21 |  
| 256 | 8 | 264 | 24 |  

![이미지1](https://github.com/ethereumbook/ethereumbook/raw/develop/images/bip39-part1.png)

![이미지2](https://github.com/ethereumbook/ethereumbook/raw/develop/images/bip39-part2.png)
## From mnemonic to seed


## Optional passphrase in BIP-39



## Working with mnemonic codes


## Creating an HD Wallet from the Seed


## HD Wallets (BIP-32) and Paths (BIP-43/44)


## Extended public and private keys



## Hardened child key derivation


## Index numbers for normal and hardened derivation


## HD wallet key identifier (path)


## Navigating the HD wallet tree structure


# Conclusion