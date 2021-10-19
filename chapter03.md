# Ethereum Clients
Ethereum 클라이언트는 Ethereum 규격을 구현하고 p2p 네트워크를 통해 다른 Ethereum 클라이언트와 통신하는 소프트웨어 어플리케이션이다.   
서로 다른 Ethereum 클라이언트가 참조 규격과 표준화된 통신 프로토콜을 준수하는 경우 상호 운용된다. 서로 다른 Ethereum 클라이언트는 각기 다른 프로그램 언어로 구현되지만 모두 같은 프로토콜을 말하고 같은 규칙을 따른다. 따라서 모두 같은 Ethereum network를 운영하고 상호 작용하는데 사용할 수 있다.  
Ethereum 은 오픈소스 프로젝트로 모든 주요 소스코드는 오픈소스 라이선스로 이용할 수 있으며, 어떤 목적으로든 무료 다운로드가 가능하다. 이는 Ethereum이 자원봉사자들의 열린 커뮤니티에 의해 개발되어 누구나 수정할 수 있음을 의미하고 이것은 더욱 신회할 수 있는 코드임을 의미한다.  
Ethereum은 yellow paper 라는 공식 사양에 정의 된다.  
예를 들면 공식적인 방식으로 정의 되지 않는 비트코인과는 대조적이다. 비트 코인의 사양이 참조 구현인 비트코인 코어 인 경우, Ethereum 사양은 영어와 수학(공식) 사양을 결합한 논문에 기록된다.  이 공식적인 규격은 다양한 Ethereum의 개선 제안 외에더 Ethereum 클라이언트의 표준 동작을 정의한다. Yellow paper는 Ethereum의 주요 변경사항에 따라 업데이트 된다.  
Ethereum의 명확한 공식 규격의 결과, Ethereum 클라이언트의 독립적으로 개발되었지만 상호 운용 가능한 소프트웨어 구현이 다수 존재한다. Ethereum은 다른 블록체인에 비해 네트워크에서 실행되는 구현의 다양성이 크다. 예를 들면  특정 클라이언트의 구현 전략을 악용하면 개발자들이 이 취약점을 해결하는 동안 개발자들을 괴롭힐 뿐이고, 다른 클라이언트는 네트워크를 거의 영향을 받지 않고 계속 실행하도록 하기 때문에 네트워크에 대한 공격을 방어하는 훌륭한 방법임이 입증되었다.  

# Ethereum Networks
Ethereum yellow paper 에 정의된 다양한 Ethereum 기반 네트워크가 존재하지만 이들은 서로 상호 운영할 수도 있고 그렇지 않을 수도 있다.  
이러한 Ethereum 기반 네트워크 에는 Ethereum, Ethereum Classic,Ellas,Expanse,ubiq,Musicoin 등이 있다. 대부분 프로토콜 수준에서 호환되지만 , 각 네트워크를 지원하기 위해 Ethereum 클라이언트 소프트웨어의 유지 관리자가 작은 변경을 해야 하는 특징이나 속성이 있다. 이 때문에 모든 버전의 Ethereum 클라이언트 소프트웨어가 Ethereum 기반 블록체인을 모두 실행 하는 것은 아니다.  
현재 6개의 다른 언어로 작성된 Ethereum 프로토콜의 주요 구현  
•	Parity, written in Rust  
•	Geth, written in Go  
•	cpp-ethereum, written in C++  
•	pyethereum, written in Python  
•	Mantis, written in Scala  
•	Harmony, written in Java  

이 섹션에서는 가장 일반적인 두 클라이언트 인 Parity와 Geth를 살펴 보겠습니다. 각 클라이언트를 사용하여 노드를 설정하는 방법을 보여주고 일부 명령 줄 옵션과 API를 살펴 봅니다.  

## Should I Run a Full Node?
블록체인의 건강, 복원력 및 검열 저항은 블록체인이 독립적으로 운영되고 지리적으로 분산된 전체 노드를 많이 갖느냐에 달려 있다. 각 전체 노드는 다른 새로운 노드가 자신의 운영을 부트스트랩하기 위한 블록 데이터를 얻을 수 있도록 도울 수 있을 뿐만 아니라 운영자에게 모든 트랜잭션과 계약에 대한 권위 있고 독립적인 검증을 제공할 수 있다.  
live mainnet network 실행되는 전체 노드는 이더리움 개발에 필요하지 않다.   testnet node 로, Ganache같은 로컬 프라이빗 블록체인으로, Infura 같은 서비스 제공업체가 제공하는 클라우드 기반 이더리움 클라이언트로 필요한 거의 모든 작업을 할 수 있다.  
블록체인의 로컬 사본을 저장하지 않거나 블록과 트랜잭션을 검증하지 않는 원격 클라이언트를 실행할 수도 있다. 클라이언트는 지갑의 기능을 제공하고 트랜잭션을 생성하고 브로드 캐스트 할 수 있다. 원격 클라이언트는 자체 풀 노드, 퍼블릭 블록체인, 퍼블릭 또는 허가된 (권한 증명) 테스트넷 또는 프라이빗 로컬 블록체인과 같은 기존 네트워크에 연결하는 데 사용할 수 있다. 실제로 MetaMask, Emerald Wallet, MyEtherWallet 또는 MyCrypto 와 같은 원격 클라이언트를 사용하여 모든 다른 노드 옵션을 편리하게 전환 할 수 있다.  
"원격 클라이언트"와 "지갑"이라는 용어는 약간의 차이가 있지만 같은 의미로 사용됩니다. 일반적으로 원격 클라이언트는 지갑의 트랜잭션 기능 외에도 API (예 : web3.js API)를 제공합니다.  
이더리움의 원격지갑 개념과 라이트 클라이언트의 개념  
라이트 클라이언트의 개념 : 비트코인의 간편결제 검증 클라이언트와 유사, 블록 헤더를 검증하고 Merkle 증명을 사용하여 블록체인에 트랜잭션이 포함된 것을 검증하고 그 효과를 결정함으로써 전체 노드와 유사한 수준의 보안을 제공한다  
이더리움의 원격지갑 개념 : 블록 헤더나 트랜잭션의 유효성을 검사하지 않는다. 그들은 블록체인에 대한 접근 권한을 부여하기 위해 완전한 클라이언트를 전적으로 신뢰하며, 따라서 상당한 보안과 익명성 보장을 잃게 된다. 직접 실행하는 전체 클라이언트를 사용하여 이러한 문제를 완화할 수 있다.  

## Full Node Advantages and Disadvantages
장점  
 + 이더리움 기반 네트워크 검열에 대응 가능
 + 모든 트랜잭션을 검증할 수 있음
 + 중개자 없이 퍼블릭 블록체인의 어떤 컨트랙트와도 상호 작용 가능
 + 중개자 없이 퍼블릭 블록체인의 직접적으로 컨트랙트 배포 가능
 + 블록체인 상태를 조회할 수 있음  
 
단점  
 + 하드웨어와 밴드위드스 자원 필요
 + 처음 설치하면 풀 노드와 싱크를 맞추기 위해서 수일이 걸림
 + 싱크를 유지하기 위해서 유지 관리를 해야함
 
## Public Testnet Advantages and Disadvantages
장점  
•	테스트 넷 노드는 메인 넷에 비해 훨씬 적은 양의 데이터를 동기화하고 저장 (네트워크에 따라 약 75GB).  
•	테스트 넷 노드는 훨씬 짧은 시간에 완전히 동기화 가능.  
•	계약을 전개하거나 거래를하려면 가치가없고, 무료로 얻을 수있는 테스트 에테르가 필요.  
•	테스트 넷은 "실시간"으로 실행되는 다른 많은 사용자 및 계약이있는 공용 블록 체인  
단점  


## Local Blockchain Simulation Advantages and Disadvantages
여러 테스트 목적으로 단일 인스턴스 프라이빗 블록체인을 출시하는 것이 최선의 선택이다. Ganache는 다른 클라이언트가 없어도 교류할 수 있는 가장 인기 있는 현지 블록체인 시뮬레이션 중 하나이다.   

장점  
•	동기화가 필요 없고 디스크에 데이터가 거의 없음. 첫 번째 블록을 직접 채굴.  
•	테스트 에테르를 얻을 필요 없음; Ganache는 테스트를 위해 ether를 이미 보유하고 있는 계정으로 초기화됨  
•	다른 사용자가 없다.  
•	기존 이더리움 노드를 분기하는 옵션을 사용하지 않는 한 다른 계약은 없으며 실행 후 배포한 계약만 해당  

단점    
•	다른 사용자가 없다는 것은 퍼블릭 블록체인과 동일하게 행동하지 않는다는 것을 의미한다.  트랜잭션 공간이나 트랜잭션 순서에 대한 경쟁 없다.  
•	당신 외에  채굴자는 채굴이 더 예측 가능하다는 것을 의미하기 때문에, 퍼블릭 블록체인을 통해 일어나는 몇몇 시나리오를 테스트할 수 없다.  
•	기존 이더리움 노드를 분기하는 경우, 그렇지 않은 경우 정리 되었을 수 있는 블록의 상태와 상호 작용하기 위한 아카이브 노드여야 함  

# Running an Ethereum Client
시간과 리소스가 있다면 프로세스에 대해 자세히 알아보더라도 전체 노드를 실행
 Ethereum 클라이언트 Parity 및 Geth를 다운로드, 컴파일 및 실행하는 방법을 다룹니다.
전체 노드에 대한 하드웨어 요구 사항
블록체인의 전체 사본을 저장하려면 최소 300GB의 디스크 공간이 필요
이더리움 테스트넷에서 전체 노드를 실행하려면 최소 75GB가 추가로 필요하다. 375GB의 블록체인 데이터를 다운로드하는 데는 오랜 시간이 걸릴 수 있으므로 빠른 인터넷 연결 
최소 요건:
•	2 개 이상의 코어가있는 CPU
•	최소 300GB의 여유 저장 공간
•	최소 4GB RAM (SSD 포함), 8GB + (HDD 포함)
•	8MBit / sec 다운로드 인터넷 서비스
권장 사양 :
•	코어가 4 개 이상인 고속 CPU  
•	16GB 이상의 RAM  
•	최소 500GB의 여유 공간이있는 빠른 SSD  
•	25MBit / sec 이상의 다운로드 인터넷 서비스  

## Hardware Requirements for a Full Node

### Minimum requirements:
- CPU with 2+ cores  
- At least 300 GB free storage space
- 4 GB RAM minimum with an SSD, 8 GB+ if you have an HDD
- 8 MBit/sec download internet service

### Recommended specifications:
- Fast CPU with 4+ cores
- 16 GB+ RAM
- Fast SSD with at least 500 GB free space
- 25+ MBit/sec download internet service

## Software Requirements for Building and Running a Client (Node)
클라이언트 구축 및 실행을 위한 소프트웨어 요구 사항(노드)  
섹션에서는 Parity 및 Geth 클라이언트 소프트웨어를 다룹니다  
일반적으로 모든 블록 체인에는 자체 버전의 Geth가 있으며 Parity는 동일한 클라이언트 다운로드 로 여러 이더 리움 기반 블록 체인 (Ethereum, Ethereum Classic, Ellaism , Expanse, Musicoin)에 대한 지원을 제공  
 Git,go,rust 설치  
https://git-scm.com.  https://www.rustup.rs/. https://golang.org/   

# Parity
패리티는 전체 노드 이더리움 클라이언트와 DApp 브라우저를 구현한 것이다. 시스템 프로그래밍 언어인 러스트(Rust)로 작성되었으며, 모듈식, 안전하고 확장 가능한 이더리움 클라이언트를 구축하기 위한 목적으로 작성되었다. 패리티는 영국 회사인 패리티 테크(Parity Tech)에 의해 개발되었으며 GPLv3 자유 소프트웨어 라이센스로 출시되었습니다.

Parity를 설치하려면 Rust 패키지 관리자 카고를 사용하거나 GitHub에서 소스 코드를 다운로드하면 됩니다. 패키지 매니저도 소스 코드를 다운로드하기 때문에 두 옵션 간에 큰 차이가 없습니다. 다음 섹션에서는 직접 패리티를 다운로드하고 컴파일하는 방법을 보여드리겠습니다.

## Installing Parity
패리티 위키에서는 다양한 환경 및 컨테이너에서 패리티를 구축하는 방법을 안내합니다. 원본에서 패리티를 작성하는 방법을 보여드리겠습니다. 여기서는 이미 러스트업을 사용하여 러스트를 설치했다고 가정합니다(클라이언트(노드) 구축 및 실행을 위한 소프트웨어 요구사항 참조).

먼저 GitHub에서 소스 코드를 가져옴:
```
$ git clone https://github.com/paritytech/parity
```
그런 다음 패리티 디렉터리로 변경하고 카고를 사용하여 실행 파일을 작성합니다.
```
$ cd parity
$ cargo install --path .
```
모든 것이 잘 되면 다음과 같은 것을 볼 수 있습니다:
```
$ cargo install --path .
Installing parity-ethereum v2.7.0 (/root/parity)
Updating crates.io index
Updating git repository `https://github.com/paritytech/rust-ctrlc.git`
Updating git repository `https://github.com/paritytech/app-dirs-rs`   Updating git repository

 [...]

Compiling parity-ethereum v2.7.0 (/root/parity)
Finished release [optimized] target(s) in 10m 16s
Installing /root/.cargo/bin/parity
Installed package `parity-ethereum v2.7.0 (/root/parity)` (executable `parity`)
$
```
--version 옵션을 호출하여 패리티를 실행하여 설치되어 있는지 확인합니다:
```
$ parity --version
Parity Ethereum Client.
  version Parity-Ethereum/v2.7.0-unstable-b69a33b3a-20200124/x86_64-unknown-linux-gnu/rustc1.40.0
Copyright 2015-2020 Parity Technologies (UK) Ltd.
License GPLv3+: GNU GPL version 3 or later http://gnu.org/licenses/gpl.html.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

By Wood/Paronyan/Kotewicz/Drwięga/Volf/Greeff
   Habermeier/Czaban/Gotchac/Redman/Nikolsky
   Schoedon/Tang/Adolfsson/Silva/Palm/Hirsz et al.
$
```
좋습니다! 이제 패리티가 설치되었으므로 블록체인을 동기화하고 몇 가지 기본 명령줄 옵션을 시작할 수 있습니다.  

# Go-Ethereum (Geth)
겟은 이더리움 재단이 적극적으로 개발하는 바둑 언어 구현체여서 이더리움 클라이언트의 '공식' 구현체로 꼽힌다. 일반적으로 모든 이더리움 기반 블록체인은 자체 겟 구현체가 있을 것이다. Geth를 실행 중인 경우 다음 저장소 링크 중 하나를 사용하여 블록체인에 대한 올바른 버전을 가져올 수 있습니다:

+ Ethereum (or https://geth.ethereum.org/)  

+ Ethereum Classic  

+ Ellaism  

+ Expanse  

+ Musicoin  

+ Ubiq  


### Cloning the repository
첫 번째 단계는 Git 저장소를 복제하여 소스 코드의 복사본을 얻는 것입니다.  

선택한 리포지토리의 로컬 복제본을 만들려면 홈 디렉토리 또는 개발에 사용하는 디렉토리에서 다음과 같이 git 명령을 사용하십시오.  
```
$ git clone <Repository Link>
```
리포지토리를 로컬 시스템에 복사할 때 다음과 같은 진행률 보고서가 표시됩니다.
```
Cloning into 'go-ethereum'...
remote: Enumerating objects: 86915, done.
remote: Total 86915 (delta 0), reused 0 (delta 0), pack-reused 86915
Receiving objects: 100% (86915/86915), 134.73 MiB | 29.30 MiB/s, done.
Resolving deltas: 100% (57590/57590), done.
```
좋습니다! 이제 Geth의 로컬 복사본을 가지고 있으므로 플랫폼에 대한 실행 파일을 컴파일할 수 있습니다.  


### Building Geth from source code

## The First Synchronization of Ethereum-Based Blockchains
## Running Geth or Parity
## The JSON-RPC Interface
# Remote Ethereum Clients
# Mobile (Smartphone) Wallets
# Browser Wallets
## MetaMask
## Jaxx
## MyEtherWallet (MEW)
## MyCrypto
## Mist (Deprecated)
# Conclusions
