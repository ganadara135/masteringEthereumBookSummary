# Smart Contracts and Solidity
이더리움은 2가지 계정이 있음: EOA, 컨트랙트 계정  
EOA 계정은 사용자, 특히 지갑 어플같은 이더리움 외부 플랫폼에 의해서 컨트롤됨  
컨트랙트 계정은 이더리움 가상 머신에 의해서 실행되는 프로그램 코드에 의해서 컨트롤 됨  
EOA 는 연동되는 코드나 데이터 저장 매체없는 단순 계정이고,  
컨트랙트 계정은 연동 코드나 데이터 저장매체를 가진다  

EOA 는 트랜잭션에 의해서 컨트롤되고, 당연히 비밀키가 필요함    
컨트랙트 계정은 비밀키가 필요없고, 스마트 컨트랙트 코드의 의해서 컨트롤됨  
위 두개의 계정 타입은 이더리움 주소의 의해서 구분된다  
본 챕터에서는 컨트랙트 계정에 대해서 다룬다  

# What is a Smart Contract?
1990년대에 암호학자 Nick Szabo 가 "약속의 집합" 이라고 컨트랙트를 정의  
분산 블록체인 플래폼 등장후에 계념의 확장됨  
이더리움 스마트컨트랙트는 스마트하지도 법적인 컨트랙트도 아니다  
이더리움에서는 스마트컨트랙트를 변경할수없는 컴퓨터 프로그램이라고 봄  
해당 프로그램은 이더리움 가상 머신의 컨텍스트안에서 결정론적으로 작동함  

* Computer programs
  스마트 컨트랙트는 컴퓨터 프로그램이다. "컨트랙트" 단어는 본 문장에서 법률적 의미가 없음  
  
* Immutable 
  일단 배포되면 스마트 컨트랙트 코드는 변경할 수 없다. 스마트 컨트랙트를 변경하기 위해서 유일한 방법은 새로운 인스턴스를 배포하는 것임  
  
* Deterministic
  스마트 컨트랙트 실행 결과는 누가 실행하든 같아야 함. 
  
* EVM context
  스마트 컨트랙트 매우 제한된 실행 컨텍스트를 사용한다. 자신의 상태와 트랜잭션의 컨텍스트, 그리고 가장 최신의 블록 정보에 접근할 수 있음  
  
* Decentralized world computer
  EVM 은 모든 이더리움 노드 위에서 로컬 인스턴스로서 작동한다. 한 시스템으로서 하나의 싱글 월드 컴퓨터로서 작동함  
  
# Life Cycle of a Smart Contract
스마트컨트랙트는 솔리디티 같은 하이 레벨 언어로 작성됨  
EVM 에서 작동시키기 위해서는 로우 레벨 바이트코드로 컴파일 돼야함  
컴파일 이후에 특별 컨트랙트 생성 트랜잭션을 사용해서 이더리움 플랫폼에 배포됨  
특별 컨트랙트 생성 주소 즉, 0x0 로서 구분 됨.  
각 컨트랙트는 이더리움 주소에 의해 구분됨  
위 주소는 원천 계정 함수와 nonce 를 통해서 컨트랙트 생성 트랜잭션으로 부터 유도됨  
컨트랙트의 이더리움 주소는 트랜잭션의 수령자 필드에서 사용됨  
위 수령자는 컨트랙트의 펀드를 보내거나 컨트랙트 함수를 호출하는 용도임  
EOA 와는 다르게 새로운 스마트 컨트랙트 만드는 계정은 키와 전혀 연관되지 않음  
컨트랙트 생성자도 프로토콜 레벨에서도 어떤 특별한 권한이 없다  
당연히 스마트 컨트랙트 안에 명확히 코딩을 한다해도 권한을 줄 수 없음  
또한 컨트랙트 계정은 비밀키도 존재하지 않음  

컨트랙트는 오직 트랜잭션의 호출에 의해서 작동함  
모든 스마트 컨트랙트는 EOA 의해 촉박한 트랜잭션에 의해서 실행됨  
컨트랙트는 또다른 컨트랙트를 호출할 수 있고, 연속으로 다른 컨트랙트 호출함  
그러나 당연히 첫 컨트랙트는 항상 EOA 의해 만들어진 트랜잭션의 의해서 호출됨  
컨트랙트는 결코 자기 자신을 호출하거나, 백그라운드에서 작동할 수 없음  
컨트랙트는 어떤 상황에서도 병렬적으로 실행되지 않음  
이더리움 월드 컴퓨터는 싱글 스레드 머신임  

트랜잭션은 원자적임. 성공적인 트랜잭션 처리 종료는 다음과 같이 3가지임  
1) 트랜잭션이 EOA 에서 또다른 EOA로 보내짐
2) 트랜잭션이 EOA 에서 컨트랙트로 보내짐
3) 트랜잭션이 EOA 에서 컨트랙트로 보내짐. 본 컨트랙트는 에러메시지를 전파하는 방식으로 다른 컨트랙트를 호출함  
4) 트랜재션이 EOA 에서 컨트랙트로 보내짐. 본 컨트랙트는 에러메시지를 전파하지 않는 방식으로 다른 컨트랙트를 호출함  

위 과정중에 계좌 잔액, 상태 변수 같은 글로벌 상태 변화는 모두 기록되고, 트랜잭션이 중기되면 모든 효과가 롤백됨.  
실패한 트랜잭션은 기록되고, 사용된 가스 만큼의 이더는 요청자의 계정에서 공제됨  
컨트랙트 코드는 변경될 수 없지만, 지워질 순 있다.   
지워진 컨트랙트 주소에 보내진 트랜잭션은 어떤 코드도 실행하지 않는다  
컨트랙트를 지우기 위해서는 EVM opcode 인 SELFDESTRUCT 를 실행하면 됨.  
SELFDESTRUCT 는 해당 기능이 컨트랙트 코드내에 포함돼 있어야 가능함  
컨트랙트를 지운다는 것이 트랜잭션 히스토리를 지운다는 건 아님  

# Introduction to Ethereum High-Level Languages
EVM 은 EVM 바이트코드를 작동시키는 가상 머신임   
x86_64 와 같은 머신 코드를 운영하는 CPU 와 유사함  

EVM 바이트코드는 직접 프로그래밍하기 어려우므로 상위 레벨 언어로 프로그램을 작성해서 바이트코드로 컴파일함.  

프로그래밍 언어는 크게 2가지 패러다임으로 분류됨: 선언적, 명령적 또는 기능적, 절차적.  
선언적 프로그래밍은 한 프로그램의 로직을 나타내는 기능을 작성하지 흐름을 작성하지 않음  
선언적 프로그래밍은 사이드 효과가 없어야 하는 곳에 주로 사용됨  
선언적 프로그래밍 언어는 Haskell, SQL 등이 있음  
명령적 프로그래밍은 개발자가 로직과 흐름을 결합하는 절차적 집합을 작성함  
명령형 프로그래밍 언어는 C++ 과 Java 가 있음  
위 특징을 모두 사용하는 하이브리드 언어가 Lisp, JavaScrpt, Python 가 있음  
명령형 언어에서 선언형 패리다임을 사용할 수 는 있는 코드가 지저분함  
반면에 순수 선언형 언어는 명렁형 패러다임을 적용할 수 없음  
순수한 선언형 언어는 변수를 사용하지 않는다  

명령형 언어는 개발자들이 주로 사용하고 있으나, 정확히 기대한 방식으로 프로그램을 작동시키기가 어렵다.  
어떤 상태를 변경 시키기 위해서 어떤 부분을 어떤 방식으로도 변경할 수 있는 능력은 프로그램 실행을 추론하기 어렵게 만들어 원하지 않는 부작용을 일으킨다  
반면에 선언형 프로그래밍은 프로그램이 어떻게 작동할 것인지 이해하기 쉽게 만들고, 부작용이 없으며, 프로그램 실행 영역들을 분리하여 이해할 수 있음  

따라서 선언적 언어가 일반 목적의 언어보다 스마트컨트랙트에 중요하나,  
Solidity 는 명령형 언어이다.  

현재 스마트컨트랙트를 지원하는 상위 레벨 프로그래밍 언어는 다음과 같음  
* LLL
  함수형 프로그래밍 언어이고, 문법은 Lisp 같음. 널리 사용되지 않음.  
  
* Serpent
  절차적 프로그래밍 언어이고 Python 과 같은 문법임.
  
* Solidity
  절차적 프로그래밍 언어이고 문법은 JavaScript, C++ or Java 같음.
  
* Vyper
  가장 최신의 개발된 언어이고 문법은 Python 과 유사함. Python 의 순수 함수형 규칙을 사용함.  
  
* Bamboo
  Erlang 에 영향을 받은 선언형 언어임. 명확한 상태 전황과 반복문을 사용 안함. 따라서 부작용을 줄이고 추적가능성을 높였음. 최신 언어라 확산되지 않음  
  
본 과정에서는 Solidity 를 다룰 것임  

# Building a Smart Contract with Solidity
Dr. Gavin Wood 가 Solidity 를 만들었다. 현재는 GitHub 에서 독립적으로 유지 관리되고 있음  
Solc 가 Solidity 의 메인 상품인 컴파일러임.  
이더리움 스마트 컨트랙트를 위한 ABI 가 본 챕터의 메인 주제임  
Solidity 컴파일러 각 버전이 일치해야 Solidity 언어에 각 버전에 맞게 컴파일함  
# Selecting a Version of Solidity
Solidity 도 Semantic versioning 규칙을 따른다.  
따라서 특정 버전 넘버가 3가지 숫자로 구분된다: MAJOR.MINOR.PATCH.  
MAJOR 숫자는 과거버전 비친화적이고 MINOR 는 과거버전 친화적이고 주요 기능 추가할 때 사용한다. PATCH 숫자는 과거버전 친화적이고 버그 수정에 사용함.  

Solidity에서는 버전이 0.6.4. 이라고 하면, MAJOR VERSION 6 이고 MINOR VERSION 은 4 이다.  

Solidity 는 pragma 선언문을 가질 수 있음  
pragma 선언문은 최소에서 최대 지원하는 Solidity 버전을 표기할 수 있음  

## Download and Install
Ubuntu/Debian 운영 체제에서는 최신 릴리즈 버전을 apk 패키지 관리자로 설치할 수 있음  
```
$ sudo add-apt-repository ppa:ethereum/ethereum
$ sudo apt update
$ sudo apt install solc
```

solc 가 설치되었다면 아래과 같이 버전을 체크할 수 있음  
```
$ solc --version
solc, the solidity compiler commandline interface
Version: 0.6.4+commit.1dca32f3.Linux.g++
```

## Development Environment
어떤 텍스트 에디터가 사용 가능하고 solc 를 커맨트 라인으로 실행할 수 있음  
또한 Emacs, Vim, Atom 같은 전용 에디터도 사용 가능  
웹 기반의 개발 환경도 있음: Remix IDE, EthFiddle  

## Writing a Simple Solidity Program
아래 링크주소에서 Faucet.sol  을 사용하자  

## Compiling with the Solidity Compiler (solc)
Solidity 컴파일러를 커맨드 라인을 사용해서 아래와 같이 직접적으로 컴파일할 수 있음  
```
$ solc --optimize --bin Faucet.sol
======= Faucet.sol:Faucet =======
Binary:
608060405234801561001057600080fd5b5060cc8061001f6000396000f3fe6080604052600436106
01f5760003560e01c80632e1a7d4d14602a576025565b36602557005b600080fd5b34801560355760
0080fd5b50605060048036036020811015604a57600080fd5b50356052565b005b67016345785d8a0
000811115606657600080fd5b604051339082156108fc029083906000818181858888f19350505050
1580156092573d6000803e3d6000fd5b505056fea26469706673582212205cf23994b22f7ba19eee5
6c77b5fb127bceec1276b6f76ca71b5f95330ce598564736f6c63430006040033
```
위 결과는 hex 형 직렬화된 이진수이고 이더리움 블록체인에 제출할 수 있음  

# The Ethereum Contract ABI
Application binary interface 는 두 프로그램 모듈간의 인터페이스임.  
주로 운영체제와 프로그램사이에 사용됨  
ABI 는 어떻게 데이터 구조와 함수가 머신 코드의 의해서 접근할 수 있는지 정의함  
API 는 더 높은 레벨에서 인간이 읽을수 있는 형태인 소스 코드에서 사용함  
ABI 는 머신 코드에서 데이터를 인코딩하고 디코딩하는 방식임  

ABI 는 EVM를 위해서 컨트랙트 콜을 인코드하기위해 사용하거나, 트랜잭션 밖에서 데이터를 읽을 때 사용됨  
ABI 는 목적은 컨트랙트안에서 호출되어야할 함수를 정의하고,  
어떻게 각 함수가 인자를 받아드리고, 결과를 리턴하지는 기술함  

컨트랙트 ABI 는 JSON 배열 형식으로 함수 기술과 이벤트를 구체화함  
함수 기술은 JSON 객체형태로 필드는 type, name, inputs, outputs, constant, payable 이다.  
이벤트 기술 객체는 type, name, inputs, anonymous 필드를 가진다.  

아래와 같이 Faucet.sol 컨트랙트를 컴파일하면 아래와 같은 ABI를 만들다  
```
$ solc --abi Faucet.sol
======= Faucet.sol:Faucet =======
Contract JSON ABI
[{"inputs":[{"internalType":"uint256","name":"withdraw_amount","type":"uint256"}], \
"name":"withdraw","outputs":[],"stateMutability":"nonpayable","type":"function"}, \
{"stateMutability":"payable","type":"receive"}]
```
위 컴파일러는 JSON 배열형태로 두 개의 함수를 만들었음  
위 JSON 이 배포되면 Faucet 컨트랙트에 접근하길 원하는 어떤 어플에서도 사용할 수 있음  
ABI를 사용하여서 지갑이나 댑 브라우저 같은 어플이 정확한 인자와 인자 타입으로 Faucet 의 함수를 호출하는 트랜잭션을 만들 수 있음  

컨트랙트와 상호작용하는 어플이 필요한 것은 ABI 와 배포후 만들어진 컨트랙트 주소임  

## Selecting a Solidity Compiler and Language Version
Solidity는 컴파일러 선언자로 버전 pragma 를 제공함  
이것은 컴파일러에게 정확한 컴파일러 버전을 알려줌  
```
pragma solidity ^0.6.0;
```
Solidity 컴파일러가 버전 pragma를 읽고 컴파일러 버전이 버전 pragma 와 맞지 않으면 에러를 발생시킨다.  
Pragma 선언자는 EVM 바이트코드로 컴파일 되지않고 오직 컴파일러가 친화도 체크용도로 사용함  

# Programming with Solidity
본 섹션에서는 Solidity 언어의 일부 유용성을 보여줄 것임.  

##### Data Types
기본 데이터 타입  
*Boolean(bool)  
  부울린형  
*Integer(int,uint)
  Signed 와 Unsigned 정수  
*Fixed point(fixed, ufixed)
  고정 포인트 숫자 : fixedMxN,  ufixed32x2.  
*Address
  20 바이트 이더리움 주소. 이 주소 객체는 많은 유용한 멤버 함수가 있음   
*Byte array(fixed)
  바이트형의 고정 크기의 배열  
*Byte array(dynamic)
  바이트형의 변수 크기의 배열  
*Enum
  열거형 이산 값을 위한 사용자 정의 타입  
*Arrays
  어떤 타입의 배열, 고정되거나 유동적인:  
  uint32[][5] 고정 크기 배열이고, 5개의 유동 배열을 가짐  
*Struct
  변수를 그룹핑하는 사용자 정의 데이터 컨테이너  
  NAME {TYPE1 VARIABLES1; TYPE2 VARIABLES2; ...}
*Mapping
  해시 lookup 테이블 key => value 짝:  
  mapping(KEY_TYPE => VALUE_TYPE) NAME.  
*Time units
  단위 초, 분, 시간, 날짜는 접사로 사용되고, 기본 단위인 초로 곱해서 컨버팅됨 
*Ether units
  단위 wei, finney, szabo, ether 들이 접사로 사용되고, 기본 단위 wei 로 곱해서 컨버팅 됨  

Faucet 컨트랙트 예제에서 uint256 형의 withdraw_amount 변수 사용.  
또한 간접적으로 address 변수 msg.sender 를 사용  

단위 곱셈의 예를 아래와 같음  
```
require(withdraw_amount <= 100000000000000000);
```
위에 단위를 읽기 불편하다. 단위 곱셈(unit multiplier)을 아래와 같이 사용 가능  
```
require(withdraw_amount <= 0.1 ether);
```

## Predefined Global Variables and Functions
컨트랙트가 EVM 에서 실행될때, 전역 객체에 접근할 수 있다  
해당 객체는 접근 가능한 부분은 block, msg, tx 객체이다.  
Solidity 는 사전 정의된 함수로써 많은 EVM opcode를 제공함   
이번 섹션에서는 스마트 컨트랙트에서 접근할 수 있는 변수와 함수를 알아봄  

#### Transaction/message call context
msg 객체는 트랜잭션 콜(EOA 기원자)이거나 메시지 콜(컨트랙트 기원자)  
해당 컨트랙트 실행을 초발함  
이것은 아래와 같은 속성을 가지고 있음  

* msg.sender  
  해당 컨트랙트 콜을 야기한 주소. 하지만 반드시 트랜잭션을 보낸 기원자 EOA는 아님. EOA 트랜잭션의 의해서 컨트랙트가 호출됐다면 이것은 트랜잭션을 사인한 주소임. 그렇지 않다면 컨트랙트 주소임. 
* msg.value
  이번 콜과 함께 보내짐 이더의 값  
* msg.gas
  해당 실행 환경에서 가스 공급하고 남은 양. gasleft 함수로 바뀜.  
* msg.data
  자신의 컨트랙트에 data payload
* msg.sig
  data payload 의 첫 4 바이트 는 함수 선택자임.  
| | |
|--|--|
|Note|컨트랙트가 다른 컨트랙트를 호출할때마다 모든 msg 속성값은 새로운 호출자 정보를 반영하여 변한다. 하지만 delegatecall 함수는 다른 컨트랙트나 라이브러리의 기원자의 msg 컨텍스트의 코드를 작동시킨다|  


#### Transaction context
tx 객체는 트랜잭션과 관련된 정보에 접근하는 방법을 제공함  

* tx.gasprice
  호출하는 트랜잭션의 가스 가격  
* tx.origin
  해당 트랜잭션을 위한 기원자의 EOA 의 주소  
  주의 : 안전하지 않음
  
#### Block context
블록 객체는 현 블록에 관한 정보를 가지고 있음  

* block.blockhash(__blockNumber__)
  특정 블록 숫자의 블록 해시, 과거 256 개의 블록까지 가능. blockhash 함수로 바뀜  
* block.coinbase
  현재 블록의 수수료와 블록 보상의 수령자 주소  
* block.difficulty
  현재 블록의 난이도(Proof of work)  
* block.gaslimit
  현재 블록에 포함된 모든 트랜잭션에서의 최대 가스 양  
* block.number
  현재 블록 숫자(블록체인 높이)  
* block.timestamp
  채굴자에 의한 현재 블록에 있는 타임스탬프
  
#### Address object
입력으로서 전달되는 주소나, 컨트랙트 객체로 부터 던저진 주소도 속성과 메소드가 있음  

* address.balance
  주소의 잔액(wei). 예로서 현재 컨트랙트의 잔액은 address(this).balance 
* address.transfer(__amount__)
  이 주소에 일정 금액을 전송하고, 에러시에 예외를 전달함. Faucet 에서 msg.sender.transfer 와 같이 사용.  
* address.send(__amount__)
  transfer 와 유사하게 예외를 던지는 것에 대신하여 false 를 리턴함. 경고: 항상 리턴값 체크.
* address.call(__payload__)
  로우 레발 CALL 함수. 데이터 페이로드와 함께 임의의 message call을 만듦. 에러시 false 리턴.
* address.delegatecall(__payload__)
  로우 레벨 DELEGATECALL 함수. callcade() 같음. 현재 컨트랙트의 풀 msg context 함께 사용. 에러 발생시 false 을 리턴함.  

#### Built-in functions

* addmod, mulmod
  modulo 덧셈, 곱셈용. 예) addmod(x,y,k) ===  (x + y) % k
  
* keccak256, sha256, sha3, ripemd160
  댜앙햔 표준 해시 알고리즘으로 해시를 계산하는 함수들  
  
* ecrecover
  시그니처로 부터 메시지를 사인하기위해 사용하는 주소를 복구함
  
* selfdestruct(__recipient_address__)
  현재 컨트랙트를 지우고, 남아있는 이더는 수령자 주소 계정으로 보냄  
  
* this
  현재 실행하는 컨트랙트 계정의 주소  

# Contract Definition









