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
Contract 가 Solidity 의 주요 데이터 타입임  
객체지향 언어의 객체 정의와 유사하게 contract 는 data 와 method 을 포함하는 컨테이너임  
Solidity 는 2가지 객체 타입임 있음  

* interface
  인터페이스 정의는 contract 와 정확히 같은 구조체를 갖는다  
  하지만, 함수는 정의되지 않고, 선언만 됨  
  이런 형식의 정의를 stub 이라고 함  
  함수 인자와 리턴 타입만 있고, 구현하지 않음  
  인터페이스는 상속할때 contract 형태를 구체화하고  
  인터페이스에 선언된 각 함수는 자식쪽에서 정의되어야 함  
  
* library
  라이브러리 컨트랙트는 오직 한번 배포되도록 만들어짐  
  다른 contract 에 의해서 사용되고, 그때 delegatecall 메소드를 사용함  
  

## Functions
contract 내에 EOA 트랜잭션이나 다른 컨트랙트에 의해 호출될 수 있는 함수를 정의함  
Faucet 예제엔 2가지 함수를 가지고 있음 : withdraw, 이름없는 fallback
문법은 아래와 같음  
```
function FunctionName([parameters]) {public|private|internal|external}
[pure|view|payable] [modifiers] [returns (return types)]
```

#### FunctionName
  함숨의 이름은 다음과 같이 3가지 경우로 호출되기위해 사용함  
  트랜잭션에서 호출할때, 다른 컨트랙트에서 호출할때, 같은 contract 에서 호출할때  
  함수에 "fallback" 키워드를 사용해서 폴백 함수로 사용하거나,  
  "receive" 키워드를 사용해서 리시브 이더 함수롤 사용할 수 있음  
  리시브 이더 함수는 call 데이터가 비어 있을때마다 호될되고  
  폴백 함수는 contract 이내에 함수 이름이 없는 함수가 있을때 호출됨  
  폴백 함수는 인자와 리턴 형이 없음  
  
####  parameters
  함수에 전달되어야 할 인자를 구체화한다  
  이름과 형식을 표기함  
  
#### public
  Public 이 기본형이고 다른 contract 나 트랜잭션에 의해서 호출될 수 있음.  
  
#### external
  External 함수는 public 함수와 같다.  대신 this 키워드가 명확히 선언되어 있지 않으면 자신의 contract 내에서 호출될 수 없음  
  
#### internal
  Internal 함수는 자신의 contract 내에서만 접근할 수 있음. 본 contract 를 상속한 contract 내에서는 호출함 수 있음  

#### private
  Private 함수는 internal 함수와 같다. 대신 상속 contract 에서는 호출할 수 없음  
  
여기서 사용하는 internal 과 private 용어는 함수간에 언제, 어떻게 호출하는지에만 영향을 미침   
퍼블릭 블록체인에서는 모든 함수와 데이터가 외부에 노출됨을 주의하라  

(pure, constant, view, payable) 키워드는 함수의 행동에 영향을 미침  

#### constant or view
  본 키워드가 있으면 상태를 변경할 수 없음
  
#### pure
  본 키워드가 있으면 변수를 읽거나 쓰기가 불가함. 오직 인자와 리턴 값만 취하고 어떤 저장된 데이터도 참조하지 않음. Pure 함수는 선언형 형태의 프로그래밍을 유도할 수 있음  
  
#### payable
  payable 함수는 들어오는 지불을 받아드리는 기능을 함. 본 키워드가 없는 함수는 들어오는 지불을 거부함. 2가지 예외가 있다. coinbase 지불과 SELFDESTRUCT 인스턴스는 payable 이라고 선언되지 않아도 폴백 기능을 함.  
  

## Contract Constructor and selfdestruct
데이터 초기화 용도로 사용하는 constructor 함수가 있음  
오직 한번만 호출되고 사용은 optional 임  
생성자는 컨트랙트 생성할때 같은 트랜잭션에서 호출됨  

생성자는 Contract 이름가 같은 이름을 사용함  
```
contract MEContract {
    function MEContract() {
        // This is the constructor
    }
}
```

Solidity v0.4.22 부터는 constrctor 키워드를 도임함  
```
pragma ^0.4.22
contract MEContract {
    constructor () {
        // This is the constructor
    }
}
```
constract  라이프 사이클은 EOA 나 컨트랙트 계정으로부터 트랜잭션 생성과 함께 시작  
생성자가 있다면, 컨트랙트 초기화 기능을 수행함  

SELFDESTRUCT EVM opcode 는 다음과 같고, 인자로 남은 이더를 받을 주소를 넣음  
```
selfdestruct(address recipient);
```
위 코드가 컨트랙트에 포함되어야 삭제할 수 있고, 없으면 영원히 컨트랙트가 지워지지 않음  

## Adding a Constructor and selfdestruct to Our Faucet Example
Faucet 예제에 생성자와 selfdestruct 를 넣어보자  
selfdestruct는 컨트랙트 생성자만 호출할 수 있게 변경함  
```
// Version of Solidity compiler this program was written for
pragma solidity ^0.6.0;

// Our first contract is a faucet!
contract Faucet {

    address owner;

    // Initialize Faucet contract: set owner
    constructor() {
        owner = msg.sender;
    }

    [...]
}
```
일반 함수에서는 msg.sender 가 해당 함수 호출자를 가르키지만  
생성자에서는 EOA 또는 컨트랙트 생성하는 컨트랙트 주소임  

아래와 같이 컨트랙트 소멸자를 만들수 있음  
```
// Contract destructor
function destroy() public {
    require(msg.sender == owner);
    selfdestruct(owner);
}
```

## Function Modifiers
함수 선언시에  modifier 를 넣으므로서 함수에 수정자를 넣을 수 있음  
수정자는 컨트랙트내에 많은 함수를 적용하는 조건을 만들기 위해 사용됨  
```
modifier onlyOwner {
    require(msg.sender == owner);
    _;
}
```
위에 문장에서 _; 부분을 볼 수 있다.  이 부분에 실제 코드로 대체됨  
```
function destroy() public onlyOwner {
    selfdestruct(owner);
}
```

위 결과 코드는 onlyOwner 수정자 함수가 destory() 함수 코드를 감싸고 있는 것과 같음  
수정자 안에서 수정된 함수에 선언된 모든 변수에 접근가능함  
따라서, 위에 경우에는 owner 변수에 접근 가능함  
하지만 역으로는 부가함, 수정되는 함수에서는 수정자 변수에 접근할 수 없음  

## Constract Inheritance
상속을 사용하기 위해서는 부모 컨트랙트를 아래와 같이 사용함  
```
contract Child is Parent {
    ...
}
```
따라서 Child 컨트랙트는 Parent 에 메소드, 기능, 변수를 상속함  
또한 복수 상속을 아래와 같이 사용할 수 있음  
```
contract Child is Parent1, Parent2 {
    ...
}
```

아래와 같이 기초 컨트랙트 Owned 를 선언함  
```
contract Owned {
	address owner;

	// Contract constructor: set owner
	constructor() {
		owner = msg.sender;
	}

	// Access control modifier
	modifier onlyOwner {
	    require(msg.sender == owner);
	    _;
	}
}
```
다음엔 Owned 컨트랙트를 상속한 Mortal 컨트랙트 선언함  
```
contract Mortal is Owned {
	// Contract destructor
	function destroy() public onlyOwner {
		selfdestruct(owner);
	}
}
```
Faucet 컨트랙트에 Mortal 을 상속함  
```
contract Faucet is Mortal {
    // Give out ether to anyone who asks
    function withdraw(uint withdraw_amount) public {
        // Limit withdrawal amount
        require(withdraw_amount <= 0.1 ether);
        // Send the amount to the address that requested it
        msg.sender.transfer(withdraw_amount);
    }
    // Accept any incoming amount
    receive () external payable {}
}
```
Faucet 은 Mortal 을 상속함으로써 생성자와 소멸자 함수와 owner 변수도 있음  
이것은 코드 재사용과 모듈화에 좋은 예임  

## Error Handling (assert, require, revert)
Solidity 는 다음과 같이 4가지 함수로 에러를 핸들링함: assert, require, revert, throw(now deprecated)  

컨트랙트에 에러 발생시에 되돌려짐,  이것이 트랜잭션의 원자성을 보장함  
assert 와 require 함수가 에러 발생시에 실행을 중지하고 되돌린다  

기존에 수정자 onlyOwner 에서 require 를 아래와 같이 사용함  
```
require(msg.sender == owner);
```
require 함수는 게이트 조건으로 사용함  
즉, 조건이 만족되지 않으면 함수의 나머지를 실행하지 않음  

Solidity v0.6.0 부터 도움 메시지를 require 에 넣을 수 있음  
에러 메시지는 트랜잭션 로그에 기록된다  
```
require(msg.sender == owner, "Only the contract owner can call this function");
```
revert 와 throw 는 실행을 중지하고 초기 상태 변화로 되돌림  
revert 도 에러 메시지를 트랜잭션 로그에 기록할 수 있다  

아래와 같은 상황에서는 revert, require, assert 가 없이도 에러가 발생하면 되돌려 진다  
충분한 이더가 없으면 transfer 함수는 에러 발생하고 되돌릴 것임  
```
msg.sender.transfer(withdraw_amount);
```
하지만 아래와 같이 충분한 에러 메시지를 전달하는게 좋을 것임  
```
require(this.balance >= withdraw_amount,
        "Insufficient balance in faucet for withdrawal request");
msg.sender.transfer(withdraw_amount);
```
추가적인 에러 체크 코드가 가스 소모를 약간 늘릴 것이다.  
에러 체크로 충분한 정보를 줄지 아니면, 가스 소모량을 줄일지 당신이 선택하라  

## Events
트랜잭션이 성공적으로 생성되거나 또는 실패하거나 트랜잭션 영수증을 만들어짐  
트랜재션 영수증은 트랜잭션 실행동안 나타난 정보를 가지고 있음  
Events 는 솔리디티 상위 레벨 객체이고, 위와 같은 로그를 만들기 위해 사용됨  

Events 는 클라이언트나 댑 서비스에서 유용하다  
사용자 인터페이스로 특정 이벤트를 보고할 수 있음  
Events 객체는 트랜잭션 로그에 기록될 인자를 취함  
인자로 인덱스된 키워드를 같이 넣어서 어플단에서 검색이나 필터 조건으로 사용 가능함  

Faucet 예제에 event 를 넣어서 로그 기록을 남길 수 있음  
```
contract Faucet is Mortal {
	event Withdrawal(address indexed to, uint amount);
	event Deposit(address indexed from, uint amount);

    [...]
}
```
address 타입에 indexed 키워드를 넣었다.  
따라서 조사하거나 필터링하는데 사용할 수 있음   

emit 키워드로 트랜잭션 로그 안에 있는 event 데이터와 연동할 수 있음  
```
// Give out ether to anyone who asks
function withdraw(uint withdraw_amount) public {
    [...]
    msg.sender.transfer(withdraw_amount);
    emit Withdrawal(msg.sender, withdraw_amount);
}
// Accept any incoming amount
receive () external payable {
    emit Deposit(msg.sender, msg.value);
}
```
event 키워드는 선언용 
emit 키워드는 사용용

## Catching events
web3.js 라이브러리가 트랜잭션 로그를 가지고 있는 데이터 구조체를 가지고 있음  
위 구조체에서 트랜잭션에 의해 만들어진 이벤트를 볼 수 있음  

truffle 를 사용해서 Faucat 컨트랙트에 테스트 트랜잭션을 확인해 보자  
```
$ truffle develop
truffle(develop)> compile
truffle(develop)> migrate
Using network 'develop'.

Running migration: 1_initial_migration.js
  Deploying Migrations...
  ... 0xb77ceae7c3f5afb7fbe3a6c5974d352aa844f53f955ee7d707ef6f3f8e6b4e61
  Migrations: 0x8cdaf0cd259887258bc13a92c0a6da92698644c0
Saving successful migration to network...
  ... 0xd7bc86d31bee32fa3988f1c1eabce403a1b5d570340a3a9cdba53a472ee8c956
Saving artifacts...
Running migration: 2_deploy_contracts.js
  Deploying Faucet...
  ... 0xfa850d754314c3fb83f43ca1fa6ee20bc9652d891c00a2f63fd43ab5bfb0d781
  Faucet: 0x345ca3e014aaf5dca488057592ee47305d9b3e10
Saving successful migration to network...
  ... 0xf36163615f41ef7ed8f4a8f192149a0bf633fe1a2398ce001bf44c43dc7bdda0
Saving artifacts...

truffle(develop)> Faucet.deployed().then(i => {FaucetDeployed = i})
truffle(develop)> FaucetDeployed.send(web3.utils.toWei(1, "ether")).then(res => \
                  { console.log(res.logs[0].event, res.logs[0].args) })
Deposit { from: '0x627306090abab3a6e1400e9345bc60c78a8bef57',
  amount: BigNumber { s: 1, e: 18, c: [ 10000 ] } }
truffle(develop)> FaucetDeployed.withdraw(web3.utils.toWei(0.1, "ether")).then(res => \
                  { console.log(res.logs[0].event, res.logs[0].args) })
Withdrawal { to: '0x627306090abab3a6e1400e9345bc60c78a8bef57',
  amount: BigNumber { s: 1, e: 17, c: [ 1000 ] } }
```
컨트랙트를 배포후에 2가지 트랜잭션을 실행했다  
첫 트랜잭션은 deposit 이고 트랜잭션 로그의 Deposit event를 emit 함  
```
Deposit { from: '0x627306090abab3a6e1400e9345bc60c78a8bef57',
  amount: BigNumber { s: 1, e: 18, c: [ 10000 ] } }
```
다음으로 withdraw 함수를 사용한다. 이것은 Withdrawal event 를 발생시킴(emit)  
```
Withdrawal { to: '0x627306090abab3a6e1400e9345bc60c78a8bef57',
  amount: BigNumber { s: 1, e: 17, c: [ 1000 ] } }
```
첫 로그는 logs[0]는 logs[0].event 에 이벤트 이름을 가지고 있고, logs[0].args 에 이벤트 인자를 가지고 있음   
Event 는 내부 데이터 흐름 뿐만 아니라, 디버그 용도로 사용 가능함  

## Calling Other Contracts (send, call, callcode, delegatecall)
당신의 컨트랙트에서 다른 컨트랙트를 호출하는 건 매우 유용하나, 위험성도 가지고 있음  

### Creating a new instance
가장 안전한 외부 컨트랙트 호출은 자신이 만들 컨트랙트를 호출하는 것임  
Faucet 예제를 보자  
```
import "Faucet.sol";

contract Token is Mortal {
	Faucet _faucet;

    constructor() {
        _faucet = new Faucet();
    }
}
```

생성자 부분에 아래와 같이 전송 이더를  구체화할 수 있다  
```
import "Faucet.sol";

contract Token is Mortal {
	Faucet _faucet;

    constructor() {
        _faucet = (new Faucet).value(0.5 ether)();
    }
}
```
아래와 같이 destroy 함수에서 Faucet 함수를 호출할 수 있다  
```
import "Faucet.sol";

contract Token is Mortal {
	Faucet _faucet;

    constructor() {
        _faucet = (new Faucet).value(0.5 ether)();
    }

    function destroy() ownerOnly {
        _faucet.destroy();
    }
}
```
Token 컨트랙트의 소유자이면 Token 컨트랙트 내에서 Foacet 컨트랙트를 파괴할 수 있다.  

### Addressing an existing instance
컨트랙트를 호출할 수 있는 또다른 방법은 컨트랙트의 인스턴스의 주소를 전달하는 것임  
```
import "Faucet.sol";

contract Token is Mortal {

    Faucet _faucet;

    constructor(address _f) {
        _faucet = Faucet(_f);
        _faucet.withdraw(0.1 ether);
    }
}
```
위에서 생성자의 인자로 주소를 넘겨준다.  이것은 이전 방법보다 더 위험하다  
왜나하면 해당 주소가 Faucet 객체인지 확신할 수 없다  
입력 값으로 주소를 전달하는 것은 컨트랙트 자체를 생성하는 것보다 더 위험함  

### Raw call, delegatecall
Solidity 는 다른 컨트랙트를 호출하는 로우 레벨 함수를 제공함  
이것은 EVM opcode를 직접 호출하는 것과 같다  
이것은 유연하지만 다른 호출 방식보다 더 위험할 수 있다  
```
contract Token is Mortal {
	constructor(address _faucet) {
		_faucet.call("withdraw", 0.1 ether);
	}
}
```
이런 방식의 호출을 눈먼 호출 방식이라고 함  
상당한 보안 이슈가 있음, 재진입 호출 같은.  
아래와 같이 에러 핸들링할 수 있음  
```
contract Token is Mortal {
	constructor(address _faucet) {
		if !(_faucet.call("withdraw", 0.1 ether)) {
			revert("Withdrawal from faucet failed");
		}
	}
}
```
또다른 방식인 delegatecall 이 있으나 deprecated 되었다  

대리 호출은 주의해서 사용해야함. 예상할 수 없는 결과를 발생시길 수 있다  
특히 라이브러리로 설계되어 있지 않은 걸 호출하면 특히 그렇다  

```
truffle(develop)> migrate
Using network 'develop'.
[...]
Saving artifacts...
truffle(develop)> (await web3.eth.getAccounts())[0]
'0x627306090abab3a6e1400e9345bc60c78a8bef57'
truffle(develop)> caller.address
'0x8f0483125fcb9aaaefa9209d8e9d7b9c8b9fb90f'
truffle(develop)> calledContract.address
'0x345ca3e014aaf5dca488057592ee47305d9b3e10'
truffle(develop)> calledLibrary.address
'0xf25186b5081ff5ce73482ad761db0eb0d25abfbf'
truffle(develop)> caller.deployed().then( i => { callerDeployed = i })

truffle(develop)> callerDeployed.make_calls(calledContract.address).then(res => \
                  { res.logs.forEach( log => { console.log(log.args) })})
{ sender: '0x8f0483125fcb9aaaefa9209d8e9d7b9c8b9fb90f',
  origin: '0x627306090abab3a6e1400e9345bc60c78a8bef57',
  from: '0x345ca3e014aaf5dca488057592ee47305d9b3e10' }
{ sender: '0x627306090abab3a6e1400e9345bc60c78a8bef57',
  origin: '0x627306090abab3a6e1400e9345bc60c78a8bef57',
  from: '0x8f0483125fcb9aaaefa9209d8e9d7b9c8b9fb90f' }
{ sender: '0x8f0483125fcb9aaaefa9209d8e9d7b9c8b9fb90f',
  origin: '0x627306090abab3a6e1400e9345bc60c78a8bef57',
  from: '0x345ca3e014aaf5dca488057592ee47305d9b3e10' }
{ sender: '0x627306090abab3a6e1400e9345bc60c78a8bef57',
  origin: '0x627306090abab3a6e1400e9345bc60c78a8bef57',
  from: '0x8f0483125fcb9aaaefa9209d8e9d7b9c8b9fb90f' }
```
make_calls 함수를 호출하고, calledContract 주소를 전달함  
그리고 다른 호출 각각의 이벤트를 캐치한 결과임  

```
_calledContract.calledFunction();
```
상위 레벨 ABI 를 사용해서 직접적으로 호출했다 
위 결과는 아래와 같은 결과를 만듦
```
sender: '0x8f0483125fcb9aaaefa9209d8e9d7b9c8b9fb90f',
origin: '0x627306090abab3a6e1400e9345bc60c78a8bef57',
from: '0x345ca3e014aaf5dca488057592ee47305d9b3e10'
```
msg.sender 가 호출자 컨트랙트의 주소임  
tx.origin 은 자신의 계정의 주소임, web3.eth.accounts[0], 호출자에게 트랜잭션을 전송함  
본 이벤트는 calledContract 의해서 만들어짐  

다음 호출은 라이브러리에 make_calls 임  
```
calledLibrary.calledFunction();
```
위 호출 결과를 아래와 같음  
```
sender: '0x627306090abab3a6e1400e9345bc60c78a8bef57',
origin: '0x627306090abab3a6e1400e9345bc60c78a8bef57',
from: '0x8f0483125fcb9aaaefa9209d8e9d7b9c8b9fb90f'
```

이번엔 msg.sender 가 호출자의 주소가 아님  
이번엔 우리 계정의 주소임, 트랜잭션 오리진과 같다  
라이브러리에서 호출할때는 항상 delegatecall 을 사용하고,  
호출자의 컨텍스트내에서 작동한다  
그래서 calledLibrary 코드에서 운영시에는 호출자의 실행 컨텍스트를 상속함  
따라서, calledLibrary 내에서 접근할 지라도, 변수 this 는 호출자의 주소이다.  

# Gas Considerations
Gas 개념은 스마트컨트랙트에서 정말 중요한 역할 수행  
Gas 는 컴퓨팅 최대치를 제한하는 자원임  
즉 이더리움이 트랜잭션에 사용할 수 있는 양을 결정  
Gas 한계가 컴퓨팅 동안 넘어셔면, 아래와 같은 이벤트 발생  
+ Out of Gas 예외 발생
+ 예외 전으로 컨트랙트 상태가 복구 됨  
+ 가스 사용을 위해 지불된 모든 이더는 트랜잭션 수수료로 사용됨, 즉 안 돌려줌  

Gas 가 트랜잭션을 발생시킨자에게 청구되므로  
높은 가스 비용을 요구하는 함수 호출을 주저하게 됨  
따라서 Gas 비용을 최소화하는 것이 프로그래머의 최대 관심사임  

## Avoid Dynamically Sized Arrays(유동형 크기의 배열을 회피)
유동형 크기의 배열을 반복문으로 호출하거나 특정 요소를 찾는 행위는 고비용의 가스 사용을 야기할 수 있음  
따라서 원하는 결과를 얻기전에 가스를 소진할 수 있음  

## Avoid Calls to Other Contracts(다른 컨트랙트 호출 회피)
Gas 비용을 알수없는 상태에서 다른 컨트랙트를 호출하는 것은  Gas 소진 리스크를 높임  
따라서 테스트가 충분히 되었거나 널리 사용되는 라이브러리가 아니면 사용을 피하라  

## Estimating Gas Cost(가스 비용 측정)
아래의 코드 샘플은 다른 컨트랙트의 함수를 호출할 때 필요한 Gas 비용을 측정해 줌  
```
var contract = web3.eth.contract(abi).at(address);
var gasEstimate = contract.myAweSomeMethod.estimateGas(arg1, arg2,
    {from: account});
```
gasEstimate 은 해당 함수 시행을 위해서 필요한 Gas 단위의 숫자를 전달 받음  
이 결과는 EVM 튜링 완결성에 따른 추산치임.  
즉 상황에서 측정할 때는 다른 결과를 볼 수 있음  
하지만, 대부분의 상황에서는 잘 맞을 것임  

Gas 가격을 얻기 위해서는 아래와 같이 수행함  
```
var gasPrice = web3.eth.getGasPrice();
```
위 결과와 함께 최종 Gas 비용을 얻을 수 있음  
```
var gasCostInEther = web3.utils.fromWei((gasEstimate * gasPrice), 'ether');
```

위 Gas 비용을 계산하는 기능을 Faucet.sol 예제에 넣어보자  
```
var FaucetContract = artifacts.require("./Faucet.sol");

FaucetContract.web3.eth.getGasPrice(function(error, result) {
    var gasPrice = Number(result);
    console.log("Gas Price is " + gasPrice + " wei"); // "10000000000000"

    // Get the contract instance
    FaucetContract.deployed().then(function(FaucetContractInstance) {

		// Use the keyword 'estimateGas' after the function name to get the gas
		// estimation for this particular function (aprove)
		FaucetContractInstance.send(web3.utils.toWei(1, "ether"));
        return FaucetContractInstance.withdraw.estimateGas(web3.utils.toWei(0.1, "ether"));

    }).then(function(result) {
        var gas = Number(result);

        console.log("gas estimation = " + gas + " units");
        console.log("gas cost estimation = " + (gas * gasPrice) + " wei");
        console.log("gas cost estimation = " +
                FaucetContract.web3.utils.fromWei((gas * gasPrice), 'ether') + " ether");
    });
});
```
위 결과는 Truffle 개발 콘솔에 아래와 같이 나타난다  
```
$ truffle develop

truffle(develop)> exec gas_estimates.js
Using network 'develop'.

Gas Price is 20000000000 wei
gas estimation = 31397 units
gas cost estimation = 627940000000000 wei
gas cost estimation = 0.00062794 ether
```

Gas 비용 함수를 자신의 컨트랙트에 넣는 것을 추천함  
그래야 자신의 컨트랙트를 메인넷에 배포할 때 갑작스러운 놀람을 방지 가능 ^^  

# Conclusions
본 챕터에서는 스마트 컨트랙트에 대해서 자세히 알아 봤다  
또한 Solidity 컨트랙트 프로그래밍 언어를 공부했다  
또다른 언어인 Vyper 을 공부할 것이다.
  
  
  
  
  
  
  
  
  
  
  
  
  




