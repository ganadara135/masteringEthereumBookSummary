# Transactions
트랜잭션은 EOA 로 디지털 서명된 메시지이고,  
이더리움 네트워크로 전송되고,  
이더리움 블록체인에 기록됨.  
트랜잭션은 상태의 변화를 일으키고, EVM 에서 컨트랙트를 실행함  

이더리움은 글로벌 싱글톤 상태 머신이다  
트랜잭션이 상태 머신의 변화를 만듦  
컨트랙트 같이 모든 것이 트랜잭션에서 실행됨  
본 챕터에서 트랜잭션을 분석해봄  
어떻게 작동하고, 그 디테일을 볼 것임  
트랜잭션을 로우 레벨에서 분석해 보길 원하는 개발자라면 흥미 있을 것임  

# The Structure of a Transaction
트랜잭션은 이더리움 네트워크에서 직렬화되어서 전송됨  
직렬화된 트랜잭션을 받은 클라이언트 어플은 자체 데이터 구조체를 사용해서 내부 메모리에 저장할 것임  
자체 데이터 구조체는 직렬화된 트랜잭션 자체가 네트워크엔 존재하지 않은 메타데이터로 꾸며져 있음  
직렬화된 네트워크가 트랜잭션의 유일한 표준 형태임

트랜잭션은 아래와 같은 데이터를 직렬화된 이진 메시지임
+ Nonce  
  EOA 가 발행하는 시퀀스 숫자, 메시지 재전송을 막기 위해 사용됨  
+ Gas price  
  이더의 양, 트랜잭션 전송을 위한 수수료
+ Gas limit  
  트랜잭션을 전송하기 위해서 지불할 수 있는 최대 값  
+ Recipient    
  수령자 이더리움 주소  
+ Value  
  도착지에 보낼 이더의 양  
+ Data  
  변수의 길이, 이진 데이터 페이로드
+ v,r,s  
  EOA 용 ECDSA 디지털 서명을 위한 세 가지 요소    

트랜잭션 메시지 구조는 RLP 인코드 체계를 사용하여서 직렬화 됨  
이더리움에서 모든 숫자는 big-endian 정수로 인코드 됨  
길이는 다수의 8비트 임.  
수령자, 이더 양과 같은 필드 라벨은 여기서 명확성을 위해서 사용하나 
RLP 인코드된 필드값을 포함하는 직렬화된 트랜잭션 데이터는 아님  
일반적으로 RLP는 필드 구분자나 라벨을 포함하지 않음  
RLP 길이 저사는 각 필드의 길이를 구분하기 위해 사용함  
정의된 길이를 넘어서는 어떤 값도 다음 필드에 속하게 됨  

이것이 전송되는 실제 트랜잭션이고,  
대부분의 내부 표현과 사용자 인터페이스 표기는 트랜잭션에서 유도하는 추가적인 정보를 사용해서 이것을 꾸밈  

예로서, EOA를 구분하는 주소 안에 from 데이터가 비여 있음  
왜냐하면 EOA의 공개키는 ECDSA 시그니처의 v,r,s 요소로 부터 유추할 수 있음  
주소는 공개키를 통해서 추출할 수 있음  
from 필드를 나타내는 트랜잭션이 있다면,  
트랜잭션을 표현하기 위해서 사용되는 소프트웨어에 의해서 넣어 짐  
클라이언트 소프트웨어에 의해서 트랜잭션에 넣어진 다른 메타데이터들은 블록 넘버와 트랜잭션 아이디가 있음  
이런 데이터들은 트랜잭션에서 유도되지,  트랜잭션 메시지 자체에서 일부가 아님  


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
|Morden (obsolete), Expanse|2|
|Ropsten|3|
|Rinkeby|4|
|Rootstock mainnet|30|
|Rootstock testnet|31|
|Kovan|42|
|Ethereum Classic mainnet|61|
|Ethereum Classic testnet|62|
|Geth private testnets|1337|

# The Signature Prefix Value (v) and Public Key Recovery
# Separating Signing and Transmission (Offline Signing)
![offline signing](https://github.com/ethereumbook/ethereumbook/raw/develop/images/offline_signing.png)  

# Transaction Propagation
# Recording on the Blockchain
# Multiple-Signature (Multisig) Transactions
# Conclusions


