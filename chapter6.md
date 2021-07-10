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
이런 데이터들은 트랜잭션에서 유도되지,  트랜잭션 메시지 자체가 아님  


# The Transaction Nonce
Nonce 의 정의, in Yellow Paper
___
+nonce+: 스칼라 값은 해당 주소에서 보내진 트랜잭션 숫자와 같다. 또는 해당 계정에서 만들어진 컨트랙트 생성 숫자.
___

간단히 말해서 nonce 는 기원 주소의 속성임  
즉, 보내는 주소의 컨텍스트에서 의미를 갖음  
nonce 는 블록체인의 계정 상태의 일부로서 명확히 저장되지 않음  
주소에서 기원한 확정된 트랜잭션 수를 계산해서 유동적으로 계산함  

트랜잭션 nonce 카운팅 존재가 중요한 2가지 시나리오가 있음  
생성 순서에 포함되는 트랜잭션의 사용 특성과 트랜잭션 복제 방지하는 활성 특성임  
각각의 시나리오 예는 아래와 같음  
1. 2개의 트랜잭션을 만들기 원한다고 가정하자.  
   6 이더를 사용하는 지불 절차가 있음  
   또한 8 이더의 지불 절차가 있음  
   6 이더 트랜잭션을 먼저 사인하고 발송함  
   그리고 두 번째 8 이더 트랜잭션을 사인하고 발송함  
   슬프게 당신 계좌는 오직 10 이더만 있다는 것 알아차리지 못했음  
   따라서 네트워크는 위 2개의 트랜잭션을 처리하지 못함  
   당신은 더 중요하다고 여긴 6 이더를 먼저 보냈기 때문에,  
   당신은 6 이더 전송은 성공하고 8 이더는 거부될 것이라고 기대함  
   하지만, 이더리움 같은 분산 시스템에서 노드들은 위 2개의 순서로 트랜잭션을 받음.  
   다른 것에 앞서 특정 노드가 하나의 트랜잭션을 가질 것이라는 보장은 없음  
   일부 노드는 6 이더 트랜잭션을 먼저 받을 것이고,  
   다른 노드는 8 이더 트랜잭션을 먼저 받을 것이다  
   nonce 가 없다면, 일부는 처리되고, 일부는 거부되는게 랜덤으로 발생할 것임  
   하디만 nonce 가 있다면 6 이더를 전송하는 첫번째 트랜잭션과  
   8 이더를 전송하는 두번째 트랜잭션의 nonce 값에 의해서 순서적으로 처리될 것임  
   
2. 당신이 100 이더를 가진 계정이 있다고 상상하자  
   온라인에서 누군가 맥프로 노트북을 이더로 판매한다  
   당신은 2 이더를 보내고, 판매자가 노트북을 보낸다  
   2 이더 지불을 위해서 당신의 계정에서 판매자 계정으로 2 이더를 보내는  
   트랜잭션에 사인함  
   그 이후에 해당 트랜잭션을 이더리움 네트워크와 전송하여서  
   블록체인에 검증되고 포함되게 함  
   트랜잭션에 nonce 값이 없이 같은 주소로 2번 연속으로 2 이더를 보내는 경우  
   첫번째 트랜잭션와 정확히 같은 트랜잭션이 만들어 질 것임  
   이것은 누구든지 이더리움 네트워크에서 당신의 트랜잭션을 본다면  
   해당 트랜잭션을 여러번 재전송할 수 있음을 의미함  
   다신의 전체 이더가 제로가 될때까지 재전송할 수 있음  
   하지만 트랜잭션 데이터에 nonce 값이 포함되어 있다면  
   모든 트랜잭션은 유니크함을 보장함  
   그리하여서 트랜잭션의 데이터 중에 증가하는 값인 nonce 값이 있으면  
   지불이 이중으로 발생하는 것이 불가함  
   
결론으로 계정 중심 프로토콜에서 nonce 의 사용은 정말 중요하다  
비트코인의 UTXO 메커니즘과는 대비된다  

## Keeping Track of Nonces


## Gaps in Nonces, Duplicate Nonces, and Confirmation

## Concurrency, Transaction Origination, and Nonces


# Transaction Gas

# Transaction Recipient

# Transaction Value and Data
그
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


