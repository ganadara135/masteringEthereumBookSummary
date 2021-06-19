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


## Creating the Contract on the Blockchain



## Interacting with the Contract

### Viewing the Contract Address in a Block Explorer

## Funding the Contract

## Withdrawing from Our Contract

## Conclusions


