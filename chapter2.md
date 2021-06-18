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





