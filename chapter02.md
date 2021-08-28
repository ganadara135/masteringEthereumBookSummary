# Ethereum Basics
본 챕터에서 배울 항목  
+ 지값 사용법  
+ 트랜잭션 만드는법  
+ 기초적인 스마트컨트랙트 사용법  

## Ether Currency Units
이더 화폐 단위  

화폐 단위: ether    
표기 :  ETH  
심볼 :  &#926;  (Unicode U+039E)  
심볼 :  &#9830;  (Unicode U+2666)  

최소단위 : wei  
1 ether  =  1 quintillion wei ( 1 * 10^18^  or 1,000,000,000,000,000,000)  

[ether_denominations] 
Ether denominations and unit names  
| Value (in wei) | Exponent | Common name | SI name |  
|---|---|---|---|  
| 1 | 1 | wei | Wei |  
| 1,000 |  10^3^ | Babbage | Kilowei or femtoether |  
| 1,000,000 | 10^6^ | Lovelace | Megawei or picoether |  
| 1,000,000,000 | 10^9^ | Shannon | Gigawei or nanoether |  
| 1,000,000,000,000 | 10^12^ | Szabo | Microether or micro |  
| 1,000,000,000,000,000 | 10^15^ | Finney | Milliether or milli |  
| _1,000,000,000,000,000,000_ | _10^18^_ | _Ether_ | _Ether_ |  
| 1,000,000,000,000,000,000,000 | 10^21^ | Grand | Kiloether |  
| 1,000,000,000,000,000,000,000,000 | {10^24} | | Megaether |  


## Choosing an Ethereum Wallet
지갑(Wallet)은 소프트웨어 애플리케이션을 의미, 이더리움 계좌를 관리함  
지갑은 키를 보관하고 트랜잭션을 만들고, 보냄  

지갑의 종류가 많으나 최고의 지갑은 이더리움 플랫폼 업그레이드를 반영한 것이 좋음  
지갑을 바꾸는 경우 새 지갑으로 트랜잭션을 보내면 됨.
또는 개인키를 내보내고, 새 지갑에서 임포트하면됨  

본 책에서 모바일, 데스트톱, 웹페이스 지갑 등을 소개하나 공식 지지하는 것은 아님
지갑 어플을 사용하고자 하면 비밀키가 필요하므로 믿을 수 있는 지갑 어플을 사용  

### - MetaMask
  메타마스크는 브라우저 확장 지갑임
  모바일 웹용 지갑도 있음
  사용성이 편하고 이더리움 노드와 연결하는 다양한 벙법이 있어 개발용으로 많이 사용됨
  
### - Jaxx
  Jaxx 는 멀티플랫폼 및 멀티커런시 지원하는 지갑임
  편해서 초보자 사용하기 좋음
  
### - MyEtherWallet(MEW)
  브라우저에서 작동하는 웹기반 지갑임  
  Android와 iOS 도 지원
  다양한 기능이 있음
  
### - Emerald Wallet
  이더리움 클래식 플랫폼에서 작동하게 만들어졌으나, 이더리움에서도 작동함  
  데스크톰 애플리케이션있고, 윈도우, 맥, 리눅스 지원함
  풀 노드로 운영할 수도 있고, 외부 노드에 연결만할 수도 있음  
  
  
## Control and Responsibility(조종과 책임)
오픈 블록체인은 사용자가 개인키를 직접 관리해야함  
개인키는 펀드와 스마트컨트랙트에 다루므로 주의해야 관리해야함  
하나의 개인키는 계좌와 동일함  
일부 사용자는 제3자에게 개인키를 맡기는 커스터디 서비스를 사용함, 즉 거래소 같은.  

개인키를 분실하면 영원히 펀드와 스마트컨트랙트에 접근할 수 없음  
보안 팁
+ 보안은 표준 규칙을 따르라  
+ 계좌가 중요할수록 더 높은 보안 조치를 취하라  
+ 온라인에서 멀어질수록 보안 레벨이 높다  
+ 비밀키를 평문으로 저장하지 마라  
+ 개인키는 암호화하여서 관리하라. 즉 keystore file  
+ 비밀번호를 디지털문서로 관리하지마라.  
+ 비밀키를 mnemonic 단어순으로 백업할 시에 펜과 페이퍼을 사용해서 물리적으로 보관하라.  
+ 많은 돈을 보낼때는 나눠서 보내라.  
+ 새로운 계좌를 생성하면 우선 적은 돈을 보내서 정상 계좌인지 주고 받아라.
+ 생성된 계좌에 다양한 문제가 있을 수 있다.  
+ 퍼블릭 블록 탐색기가 보낸 트랜잭션이 이더리움 네트워크에서 승인되었는지 확인 기능 제공함  


## Getting Started with MetaMask
다운로드 메타마스크 : [https://metamask.io](https://metamask.io)  

### - Creating a Wallet
1. 아래그림처럼 비밀번호를 생성한다  
![alt text](https://github.com/ethereumbook/ethereumbook/raw/develop/images/metamask_password.png
 "메타마스크")  
 
2. Mnemonic 키 생성, 12개 영단어로 구성됨  
   본 단어를 종이에 적어서 안전한 곳에 보관  
 ![mnemonic](https://github.com/ethereumbook/ethereumbook/raw/develop/images/metamask_mnemonic.png "니모닉키")

3. 최종 생성된 지갑 화면  
![생성된지갑](https://github.com/ethereumbook/ethereumbook/raw/develop/images/metamask_account.png)  


## Switching Networks
메타마스크는 디폴트로 메인넷으로 세팅됨  
추가로 테스트넷과 로컬넷을 설정할 수 있음  
### - Main Ethereum Network
리얼 네크워크  
### - Ropsten Test Network  
퍼블릭 테스트 네트워크이므로 이더는 실제 가치 없음  
### - Kovan Test Network
퍼블릭 테스트 네트워크이고 채굴 알고리즘을 Proof of Authority(PoA) 사용  
### - Rinkeby Tes Network
Kovan Test Network 같은 알고리즘 사용  
### - Localhost 8545 
로컬호스트에서 운영되는 노드에 연결  
노드는 메인, 테스트넷, 프라이빗 테스트넷일 수 있음  
### - Custom RPC
Geth 친화적인 Remote Procedure Call 인터페이스를 사용해서 메타마스크를 노드와 연결해줌  


## Getting Some Test Ether
지갑 사용 경험을 위해서 이더가 필요  
따라서 테스트 이더를 얻는다  
메타마스크를 Ropsten Test Network 로 변경  
클릭 Deposit -> 클릭 Ropsten Test Fauchet.  
메타마스크가 아래 처럼 새로운 웹페이지 오픈함  
![faucet](https://github.com/ethereumbook/ethereumbook/raw/develop/images/metamask_ropsten_faucet.png )  
위 화면에서 처럼 메타마스크 이더리움 지갑 주소가 이미 웹 페이지에 나타남  

- 그린색의 "request 1 ether from faucet" 클릭 함  
- 잠시후에 사용자의 메타마스크 지갑에 1 ETH 가 나타남  
- 트랜잭션 ID 를 클릭하면 block explorer 페이지가 나타남  
- 해당 웹 페이지에선 트랜잭션 정보들을 볼 수 있음  
![Etherscan](https://github.com/ethereumbook/ethereumbook/raw/develop/images/ropsten_block_explorer.png)  
- 트랜잭션 이력은 누가 볼 수 있음  


## Sending Ether from MetaMask
Ropsten Test Faucet 페이지에서 "Donte" 통해서 1 Eth을 보낸다  
메타마스크를 통해서 1 ether 를 보내면 아래와 같은 에러 발생  
![가스부족](https://github.com/ethereumbook/ethereumbook/raw/develop/images/send_to_faucet.png )  
1 Eth 를 그대로 보내니 가스가 부족하다 나타남  
모든 이더리움 트랙잭션은 비용을 요구, 즉 가스  
채굴자가 트랙젹션을 검증하는데 가스비 사용  
가스를 이더로 지급함  
기스비가 DoS 공격 방어함  
트랜젹션 전송시 메타마스크가 최근에 성공한 가스비 평균 계산함, 예 3 gwei.    
가스 제한은 최대 21,000 gas units 임  
따라서 최대 ETH 는 3 x 21,000 gwei = 0.000063 ETH  
전체 1 트랜잭션 비용은 1.000063 ETH  
따라서 거부된 것임  


## Exploring the Transaction History of an Address

![주소탐색](https://github.com/ethereumbook/ethereumbook/raw/develop/images/metamask_account_context_menu.png )  

![이더스캔](https://github.com/ethereumbook/ethereumbook/raw/develop/images/block_explorer_account_history.png )  


## Introducing the World Computer
스마트컨트랙트 사용법과 EVM 소개  
EVM은 글로벌 싱글톤이다  
글로벌 싱글톤은 글로벌 전역변수임  


## Externally Owned Accounts(EOAs) and Contracts
EOA란 메타마스크 지갑에서 만든 계좌임
즉, 비밀키를 통해 계정에 대한 전권을 가짐  
컨트랙트 계좌는 스마트컨트택트 코드로 운영되는 계좌임  
컨트랙트 계좌는 비밀키가 없고, 컨트랙트 코드에 의해 운영됨  
컨트랙트는 EOA 와 같이 주소를 갖고 있음  
컨트랙트도 EOA 처럼 이더를 주고 받을 수 있으나,  
트랜잭션 도착지가 컨트랙트 주소임  
컨트랙트 트랜잭션은 이더 이외에 특정 함수와 파라미터 데이터도 갖고 있음  
컨트랙트 계정은 비밀키가 없으므로 트랜잭션 초기화 할 수 없음  
EOA가 트랜잭션을 초기화하고 컨트랙트는 다른 컨트랙트 호출에 의한 트랜잭션에 반응할 수 있음  
예로서 EOA가 멀티시그니처 스마트컨트랙트 지갑에 이더를 다른 주소로 보내도록 할 수 있음  


## A Simple Contract: A Test Ether Faucet
Ethereum 은 여러 상위 레발 언어가 있음  
Solidity 는 그 중에 하나이고, Gavin Wood 가 만듦  

```
// SPDX-License-Identifier: CC-BY-SA-4.0

// Version of Solidity compiler this program was written for
pragma solidity 0.6.4;

// Our first contract is a faucet!
contract Faucet {
    // Accept any incoming amount  // 컨트랙트를 트리거 시키는 함수는 이름 없음
    // external 은 외부에 공개된 함수 의미, payable 은 이더만 데이터만 통과가능 
    receive() external payable {}

    // Give out ether to anyone who asks
    function withdraw(uint withdraw_amount) public {
        // Limit withdrawal amount
        require(withdraw_amount <= 100000000000000000);

        // Send the amount to the address that requested it
        // transfer 는 내장 함수임
        // 읽기는 backward 롤 이해하라
        msg.sender.transfer(withdraw_amount);
    }
}
```

## Compiling the Faucet Contract
위 코드를 컴파일해서 EVM bytecode 생성됨  
Remix IDE 에서 컴파일 [https://remix.ethereum.org](https://remix.ethereum.org)  

첫 리믹스화면에서 보이는 샘플 컨트랙트 ballot.sol 삭제  
![삭제](https://github.com/ethereumbook/ethereumbook/raw/develop/images/remix_close_tab.png )  

새로운 탭생성하기 위해서 십자 모양 클릭  
![십자](https://github.com/ethereumbook/ethereumbook/raw/develop/images/remix_toolbar.png )  

아래 처럼 우리의 코드르 복사 붙여넣기 함  
![코드](https://github.com/ethereumbook/ethereumbook/raw/develop/images/remix_faucet_load.png )  
컴파일 탭에서 완료여부 확인  
![컴파일](https://github.com/ethereumbook/ethereumbook/raw/develop/images/remix_compile.png )  
위 화면은 성공적으로 Faucet.sol 컨트랙트를 컴파일한 결과를 보여줌  
문제가 발생한다면 대부분은 솔리디티 컴파일러 버전 미스매치 문제임  

솔리디티 컴파일러가 Faucet.sol 를 EVM 바이트코드로 컴파일한 결과는 아래와 같이 생김  
```
PUSH1 0x80 PUSH1 0x40 MSTORE CALLVALUE DUP1 ISZERO PUSH2 0x10 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST POP PUSH1 0xF4 DUP1 PUSH2 0x1F PUSH1 0x0 CODECOPY PUSH1 0x0 RETURN INVALID PUSH1 0x80 PUSH1 0x40 MSTORE PUSH1 0x4 CALLDATASIZE LT PUSH1 0x1F JUMPI PUSH1 0x0 CALLDATALOAD PUSH1 0xE0 SHR DUP1 PUSH4 0x2E1A7D4D EQ PUSH1 0x2A JUMPI PUSH1 0x25 JUMP JUMPDEST CALLDATASIZE PUSH1 0x25 JUMPI STOP JUMPDEST PUSH1 0x0 DUP1 REVERT JUMPDEST CALLVALUE DUP1 ISZERO PUSH1 0x35 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST POP PUSH1 0x5F PUSH1 0x4 DUP1 CALLDATASIZE SUB PUSH1 0x20 DUP2 LT ISZERO PUSH1 0x4A JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP2 ADD SWAP1 DUP1 DUP1 CALLDATALOAD SWAP1 PUSH1 0x20 ADD SWAP1 SWAP3 SWAP2 SWAP1 POP POP POP PUSH1 0x61 JUMP JUMPDEST STOP JUMPDEST PUSH8 0x16345785D8A0000 DUP2 GT ISZERO PUSH1 0x75 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST CALLER PUSH20 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF AND PUSH2 0x8FC DUP3 SWAP1 DUP2 ISZERO MUL SWAP1 PUSH1 0x40 MLOAD PUSH1 0x0 PUSH1 0x40 MLOAD DUP1 DUP4 SUB DUP2 DUP6 DUP9 DUP9 CALL SWAP4 POP POP POP POP ISZERO DUP1 ISZERO PUSH1 0xBA JUMPI RETURNDATASIZE PUSH1 0x0 DUP1 RETURNDATACOPY RETURNDATASIZE PUSH1 0x0 REVERT JUMPDEST POP POP JUMP INVALID LOG2 PUSH5 0x6970667358 0x22 SLT KECCAK256 STOP CODECOPY 0xDC DUP16 0xD SGT PUSH6 0xD2245039EDD7 RETURN CALLDATALOAD 0xC2 0xE4 SWAP9 0xF6 0x2C 0xF8 0xB3 OR JUMPDEST 0xAC 0xD8 CREATE2 SSTORE 0x4E SIGNEXTEND PUSH4 0x3164736F PUSH13 0x634300060C0033000000000000
```

## Creating the Contract on the Blockchain
이제 컴파일된 컨트랙트를 이더리움 블록체인에 등록하자   
Ropsten 테스트넷을 사용할 것임  
블록체인에 컨트랙트를 등록하는 것은 목적지 주소가 아래 처럼 특이함  
0x0000000000000000000000000000000000000000  
위 주소를 제로 어드레스라고 함  
제로 어드레스는 특별 어드레스로서 블록체인에게 컨트랙트를 등록한다는 것을 알려주는 행위임   
Remix IDE 가 위 과정을 다 핸들링하고, 트랜잭션을 메타마스크에게 보냄  

첫째로 Run 탭으로 전환하고 "Injected Web3" 을 Enviroment 드롭다운 메뉴에서 선택함  
이것이 Remix IDE 와 메타마스크 지갑과 연결해줌  
또한 메타마스크를 통해 Ropsten 테스트넷에 연결함  
Account 선택 박스에서 자신의 지갑 주소가 나타냄  
![figure14](https://github.com/ethereumbook/ethereumbook/raw/develop/images/remix_run.png)  
Run 설정 바로 아래에서 Faucet 컨트랙트가 준비되어 있음  
Deploy 버튼을 클릭하면 배포됨  

Remix 가 내부적으로 컨트랙트 생생 트랜잭션 만들어서 메타마스크에게 승인을 요청함  
아래에 보이는 것처럼 컨트랙트 생성 트랜잭션은 이더가 제로임  
컴파일된 컨트랙트의 바이트 크기는 275이며 3 gwei 가스를 소비함  
![figure15](https://github.com/ethereumbook/ethereumbook/raw/develop/images/remix_metamask_create.png)  
승인 버튼을 누르면 15 ~ 30 초 걸림  

컨트랙트가 생성되면 아래처럼 Run 탭 버튼 아래에 주소가 나타남  
![figure16](https://github.com/ethereumbook/ethereumbook/raw/develop/images/remix_contract_interact.png)  
이제 Fuacet 컨트랙트는 자신의 주소를 갖음  

## Interacting with the Contract
다시 요약해 보자  
이더리움 컨트랙트은 돈을 컨트롤하는 프로그램이고 내부적으로 EVM 가상 머신이 작동하고 있음  
컨트랙트는 특별 트랜잭션으로 생성되고 블록체인에 기록되기 위해 바이트코드로 전달됨  
컨트랙트가 블록체인에 생성되면 지갑처럼 이더리움 주소를 갖음   
언제든지 누군가 컨트랙트 주소로 트랜잭션을 전송하면 트랜잭션에 입력한 값과 함께 컨트랙트가 EVM 에서 작동함  
컨트랙트 주소에 보내진 트랜잭션은 이더나 데이터를 갖고 있음  
데이터가 보내지면 해당 데이터에서는 호명된 함수를 구체적으로 적어야하고  호출할 수 있고, 함수 인자도 전달해야함  

### Viewing the Contract Address in a Block Explorer
블록체인에 배포된 컨트랙트를 Block Explorer 에서 확인해 보자  
Remix IDE 에서 컨트랙트 주소를 복사하자  
![figure17](https://github.com/ethereumbook/ethereumbook/raw/develop/images/remix_contract_address.png)  

브라우저 주소창에 ropsten.etherscan.io 를 호출하고 검색 창에 복사한 주소를 넣자  그러면 아래와 같은 결과화면을 볼 수 있음  
![Figure18](https://github.com/ethereumbook/ethereumbook/raw/develop/images/etherscan_contract_address.png)  

## Funding the Contract
현재 만들어진 컨트랙트는 이더가 없으므로 보내보자  
아래처럼 메타마스크를 켜서 스마트 컨트랙트 주소에 1 이더를 전송해 보자  
![Figure19](https://github.com/ethereumbook/ethereumbook/raw/develop/images/metamask_send_to_contract.png)  
1분 뒤에 Etherscan block explorer 를 릴로드하면 컨트랙트 주소에 또하나의 트랜잭션 기록이 보일것이고 1 이더가 잔액을 볼 수 있다  

이름없는 디폴트 external payable 함수가 아래 처럼 생겼음  
```
receive () external payable {}
```
컨트랙트 주소에 트랜잭션을 전송할때 호출할 함수를 구체화하지 않으면 위의 디폴트 함수를 호출함  
왜냐하면 payable 로 선언했기 때문에 컨트랙트 계정 잔고에 1 이더를 예치함  
트랜잭션이 EVM에 의해 컨트랙트를 작동하게해서 잔고를 업데이트함  

## Withdrawing from Our Contract
이제 Faucet 에서 일부 금액을 인출해보자  
인출 함수와 인출할 양을 전달한 인자가 정의된 withdraw 함수가 필요함   

Remix 탭에서 Run 탭에서 아래 그림과 같이 보일 것임  
![Figure 20](https://github.com/ethereumbook/ethereumbook/raw/develop/images/remix_contract_interact.png)  
이게 컨트랙트의 Remix 인터페이스이고 컨트랙트의 정의된 함수를 호출하는 트랜잭션을 만듦  
인출할 금액을 넣고 트랜잭션을 생성하고자 인출 버튼을 클릭하자  

인출 양은 wei 로 표기되므로 0.1 ether 는 100,000,000,000,000,000  wei 임(17 제로)  
|  |  |  
|--|--|  
|팁|자바스크립트는 숫자 표기에 제한이 있으므로 10^17 은 Remix 에서 처리할 수 없음. 대신 쌍 따옴표로 문자화해서 표기하고 계산할 시에는 내부적으로 BigNumber 형을 사용함|  
![Figure 21](https://github.com/ethereumbook/ethereumbook/raw/develop/images/remix_withdraw.png)  
인출 버튼을 누르면 메타마스크가 파업창으로 나타내서 승인을 요청함  
![Figure 22](https://github.com/ethereumbook/ethereumbook/raw/develop/images/metamask_withdraw.png)  
승인 요청하고 1분후에 Etherscan 에서 해당 트랜재션을 조회해보자  
![Figure 23](https://github.com/ethereumbook/ethereumbook/raw/develop/images/etherscan_withdrawal_tx.png)  
컨트랙트 잔고를 보면 0.9 로 바뀐것을 볼 수 있음  
Internal Transactions 에서 컨트랙트 주소의 히스토리 페이지를 볼 수 있음  
컨트랙트 코드에 의해서 발생한 0.1 이더 전송을 internal 트랜잭션(또는 메시지)라고 함   
"internal transaction" 은 아래와 같은 코드를 통해서 발생했음  
```
msg.sender.transfer(withdraw_amount);
```
아래는 internal transaction 결과화면음  
![figure 24](https://github.com/ethereumbook/ethereumbook/raw/develop/images/etherscan_withdrawal_internal.png)  

# Conclusions
본 장에서는 메타마스크를 사용해서 지갑을 설정하고 faucet 를 Ropsten 에 배포함  
그리고 이더를 컨트랙트 주소를 통해서 주고 받음  
다음 챕터에서 스마트컨트랙트 프로그래밍에서 대해서 더 배워보자  
