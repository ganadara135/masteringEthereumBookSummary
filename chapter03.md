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
Geth를 빌드하려면 소스 코드가 다운로드된 디렉토리로 변경하고 make 명령을 사용합니다:
```
$ cd go-ethereum
$ make geth
```
모든 것이 잘 되면 get 실행 파일을 생성할 때까지 Go 컴파일러가 각 구성 요소를 빌드하는 것을 볼 수 있다:  
```
build/env.sh go run build/ci.go install ./cmd/geth
>>> /usr/local/go/bin/go install -ldflags -X main.gitCommit=58a1e13e6dd7f52a1d...
github.com/ethereum/go-ethereum/common/hexutil
github.com/ethereum/go-ethereum/common/math
github.com/ethereum/go-ethereum/crypto/sha3
github.com/ethereum/go-ethereum/rlp
github.com/ethereum/go-ethereum/crypto/secp256k1
github.com/ethereum/go-ethereum/common
[...]
github.com/ethereum/go-ethereum/cmd/utils
github.com/ethereum/go-ethereum/cmd/geth
Done building.
Run "build/bin/geth" to launch geth.
$
```
실행을 실제로 시작하지 않고 geth가 작동하는지 확인합니다.  
```
$ ./build/bin/geth version

Geth
Version: 1.9.11-unstable
Git Commit: 0b284f6c6cfc6df452ca23f9454ee16a6330cb8e
Git Commit Date: 20200123
Architecture: amd64
Protocol Versions: [64 63]
Go Version: go1.13.4
Operating System: linux
[...]
```
당신의 geth version 명령은 약간 다른 정보를 보여줄 수 있지만, 당신은 여기서 보이는 것과 매우 유사한 버전 보고서를 볼 것이다.  

다음 섹션에서는 이더리움 블록체인의 초기 동기화에 대한 과제를 설명한다.  

## The First Synchronization of Ethereum-Based Blockchains
전통적으로 이더리움 블록체인을 동기화할 때, 고객은 처음부터, 즉 Genesis 블록에서 모든 블록과 모든 트랜잭션을 다운로드하고 검증합니다.

이러한 방식으로 블록체인을 완전히 동기화하는 것은 가능하지만, 이러한 유형의 동기화는 매우 오랜 시간이 걸리고 리소스 요구 사항도 높습니다(램이 훨씬 더 많이 필요하고, 고속 스토리지가 없다면 실제로 매우 오랜 시간이 걸립니다).

많은 이더리움 기반 블록체인은 2016년 말 서비스 거부 공격의 희생자였다. 영향을 받는 블록체인은 전체 동기화를 수행할 때 느리게 동기화되는 경향이 있습니다.

예를 들어 이더리움에서는 새로운 클라이언트가 블록 2,283,397에 도달할 때까지 빠르게 진행됩니다. 이 블록은 2016년 9월 18일에 채굴되었으며 DoS 공격의 시작을 알린다. 이 블록에서 2,700,031 블록(2016년 11월 26일)까지 트랜잭션 검증이 매우 느려지고 메모리 집약적이며 입출력 집약적입니다. 이로 인해 유효화 시간이 블록당 1분을 초과하게 됩니다. 이더리움은 DoS 공격에서 악용된 기본 취약점을 해결하기 위해 하드포크를 이용한 일련의 업그레이드를 구현했다. 이번 업그레이드로 스팸거래로 생성된 빈 계정 2000여만개가 제거되는 등 블록체인도 싹쓸이했다.

전체 유효성 검사와 동기화하는 경우, 클라이언트의 속도가 느려지고 DoS 공격의 영향을 받는 블록을 검증하는 데 며칠 또는 더 오래 걸릴 수 있습니다.

다행히 현재 대부분의 이더리움 클라이언트는 기본적으로 블록체인 팁과 동기화될 때까지 트랜잭션의 전체 유효성 검사를 건너뛰었다가 전체 유효성 검사를 재개하는 '빠른' 동기화를 수행한다.

Geth는 이더리움에 대해 기본적으로 빠른 동기화를 수행합니다. 선택한 다른 이더리움 체인에 대한 구체적인 지시사항을 참조해야 할 수도 있습니다.  

패리티는 기본적으로 빠른 동기화도 수행합니다.  
|  |  |  
|--|--|  
|노트|Geth는 빈 블록 데이터베이스로 시작할 때만 빠른 동기화를 수행할 수 있습니다. 이미 빠른 모드 없이 동기화를 시작한 경우 Geth는 전환할 수 없습니다. 블록체인 데이터 디렉토리를 삭제하고 처음부터 빠른 동기화를 시작하는 것이 완전한 검증으로 동기화를 계속하는 것보다 빠르다. 블록체인 데이터 삭제 시 지갑이 삭제되지 않도록 주의하세요!|  

## Running Geth or Parity
이제 '첫 번째 동기화'의 과제를 이해했으므로 이더리움 클라이언트를 시작하고 블록체인을 동기화할 준비가 완료됐다. Geth 및 Parity 모두에 대해 --help 옵션을 사용하여 모든 구성 매개 변수를 볼 수 있습니다. 기본 설정은 일반적으로 적절하며 대부분의 용도에 적합합니다. 필요에 맞게 선택적 파라미터를 구성하는 방법을 선택한 다음 Geth 또는 Parity를 시작하여 체인을 동기화합니다. 그럼 기다려봐…
|  |  |  
|--|--|  
|팁|이더리움 블록체인을 동기화하는 데는 RAM이 많은 매우 빠른 시스템에서 반나절부터 속도가 느린 시스템에서 며칠이 걸릴 것이다.|  

## The JSON-RPC Interface
이더리움 클라이언트는 자바스크립트 객체 표기법(JSON)으로 인코딩된 응용 프로그램 프로그래밍 인터페이스와 원격 프로시저 호출(RPC) 명령 집합을 제공한다. 이를 JSON-RPC API라고 합니다. 본질적으로 JSON-RPC API는 이더리움 클라이언트를 이더리움 네트워크와 블록체인의 게이트웨이로 사용하는 프로그램을 작성할 수 있는 인터페이스다.  

일반적으로 RPC 인터페이스는 포트 8545에서 HTTP 서비스로 제공됩니다. 보안상의 이유로 기본적으로 localhost(자신의 컴퓨터의 IP 주소인 127.0.0.1)의 연결만 허용하도록 제한됩니다.  

JSON-RPC API에 액세스하려면 각 사용 가능한 RPC 명령에 해당하는 "stub" 함수 호출을 제공하는 특수 라이브러리를 사용하거나 수동으로 HTTP 요청을 구성하고 JSON 인코딩 요청을 보내거나 받을 수 있습니다. 컬과 같은 일반 명령줄 HTTP 클라이언트를 사용하여 RPC 인터페이스를 호출할 수도 있습니다. 한번 해보자. 먼저 HTTP가 RPC 인터페이스에 액세스할 수 있도록 --rpc를 사용하여 Get를 설정하고 실행 중인지 확인한 다음 다음과 같이 새 터미널 창(예: 기존 터미널 창에 Ctrl-Shift-N 또는 Ctrl-Shift-T 포함)으로 전환합니다.  
```
$ curl -X POST -H "Content-Type: application/json" --data \
  '{"jsonrpc":"2.0","method":"web3_clientVersion","params":[],"id":1}' \
  http://localhost:8545

{"jsonrpc":"2.0","id":1,
"result":"Geth/v1.9.11-unstable-0b284f6c-20200123/linux-amd64/go1.13.4"}
```
이 예에서는 컬을 사용하여 HTTP를 http://localhost:8545 주소에 연결합니다. 우리는 이미 포트 8545에서 HTTP 서비스로 JSON-RPC API를 제공하는 geth를 실행하고 있습니다. HTTP POST 명령을 사용하고 내용을 application/json 형식으로 식별하도록 컬을 지시합니다. 마지막으로 HTTP 요청의 데이터 구성요소로 JSON 인코딩 요청을 전달합니다. 우리의 대부분의 명령줄은 HTTP 연결을 올바르게 하기 위해 컬을 설정하는 것입니다. 흥미로운 부분은 실제 JSON-RPC 명령입니다.  
```
{"jsonrpc":"2.0","method":"web3_clientVersion","params":[],"id":1}
```
JSON-RPC 요청은 JSON-RPC 2.0 사양에 따라 포맷됩니다. 각 요청에는 네 가지 요소가 포함됩니다.  

jsonrpc
JSON-RPC 프로토콜 버전. 정확히 "2.0"이어야 합니다.

method  
호출할 메서드의 이름입니다.

params  
메서드를 호출하는 동안 사용할 매개 변수 값을 보유하는 구조화된 값입니다. 이 구성원은 제외될 수 있습니다.

id  
클라이언트에서 설정한 식별자로, 문자열, 숫자 또는 NULL 값이 포함되어 있어야 합니다. 서버는 응답 오브젝트가 포함된 경우 동일한 값으로 응답해야 합니다. 이 멤버는 두 개체 간의 컨텍스트를 연관시키는 데 사용됩니다.
|  |  |  
|--|--|  
|팁|id 매개 변수는 주로 단일 JSON-RPC 호출에서 여러 요청을 할 때 사용됩니다. 일괄 처리(batching)는 모든 요청에 대해 새로운 HTTP 및 TCP 연결의 오버헤드를 피하기 위해 사용됩니다. 예를 들어 이더리움 컨텍스트에서 하나의 HTTP 연결을 통해 수천 개의 트랜잭션을 검색하려면 일괄 처리를 사용합니다. 일괄 처리할 때 각 요청에 대해 다른 ID를 설정한 후 JSON-RPC 서버의 각 응답에 있는 ID와 일치시킵니다. 이를 구현하는 가장 쉬운 방법은 카운터를 유지하고 각 요청의 값을 증가시키는 것입니다.|  

응답 내용은 다음과 같습니다.  
```
{"jsonrpc":"2.0","id":1,
"result":"Geth/v1.9.11-unstable-0b284f6c-20200123/linux-amd64/go1.13.4"}
```
이것은 JSON-RPC API가 Geth 클라이언트 버전 1.9.11에 의해 제공되고 있음을 알려준다.

좀 더 재미있는 걸 해보자. 다음 예에서는 JSON-RPC API에 wei의 현재 가스 가격을 요청합니다.  
```
$ curl -X POST -H "Content-Type: application/json" --data \
  '{"jsonrpc":"2.0","method":"eth_gasPrice","params":[],"id":4213}' \
  http://localhost:8545

{"jsonrpc":"2.0","id":4213,"result":"0x430e23400"}
```
응답 0x430e23400은 현재 휘발유 가격이 18gwei(기가웨이 또는 10억wei)라고 알려주고 있습니다. 만약 당신이 16진법을 사용하지 않는다면, 명령행에서 약간의 bash-fu로 그것을 십진수로 변환할 수 있다:  
```
$ echo $((0x430e23400))

18000000000
```
전체 JSON-RPC API는 이더리움 위키에서 조사할 수 있다.

## Parity's Geth compatibility mode
패리티는 특별한 "Geth 호환성 모드"를 가지고 있으며, Geth가 제공하는 것과 동일한 JSON-RPC API를 제공한다. 이 모드에서 패리티를 실행하려면 --geth 스위치를 사용합니다.  
```
$ parity --geth
```

# Remote Ethereum Clients
원격 클라이언트는 전체 클라이언트의 기능 중 일부를 제공합니다. 이들은 전체 이더리움 블록체인을 저장하지 않아 설정 속도가 빠르고 데이터 저장 공간도 훨씬 적게 든다.

이러한 클라이언트는 일반적으로 다음 중 하나 이상을 수행할 수 있는 기능을 제공합니다.

+ 지갑에서 개인 키와 이더리움 주소를 관리합니다.  

+ 트랜잭션을 작성, 서명 및 브로드캐스트합니다.  

+ 데이터 페이로드를 사용하여 스마트 계약과 상호 작용합니다.  

+ DApp을 찾아보고 상호 작용합니다.  

+ 블록 탐색기와 같은 외부 서비스에 대한 링크를 제공합니다.  

+ 이더넷 단위를 변환하고 외부 소스에서 환율을 검색합니다.  

+ 웹3 인스턴스를 JavaScript 개체로 웹 브라우저에 삽입합니다.  

+ 다른 클라이언트가 제공하거나 브라우저에 삽입한 웹3 인스턴스를 사용합니다.  

+ 로컬 또는 원격 이더리움 노드에서 RPC 서비스에 액세스합니다.  

모바일(스마트폰) 지갑과 같은 일부 원격 클라이언트는 기본적인 지갑 기능만 제공합니다. 다른 원격 클라이언트는 완전한 DApp 브라우저입니다. 원격 클라이언트는 일반적으로 다른 곳에서 실행 중인 전체 노드(예: 사용자의 컴퓨터나 웹 서버에서 로컬에 의해 또는 서버의 제3자에 의해)에 연결하여 이더리움 블록체인의 로컬 복사본을 동기화하지 않고 전체 노드 이더리움 클라이언트의 일부 기능을 제공합니다.  

가장 인기 있는 원격 클라이언트와 원격 클라이언트가 제공하는 기능을 살펴보겠습니다.  


# Mobile (Smartphone) Wallets
스마트폰이 완전한 이더리움 클라이언트를 구동하기에 충분한 자원을 가지고 있지 않기 때문에 모든 모바일 지갑은 원격 클라이언트이다. 라이트 클라이언트는 개발 중이며 이더리움용으로 일반적으로 사용되지 않는다. 패리티의 경우 라이트 클라이언트는 "실험적"으로 표시되며 --light 옵션으로 패리티를 실행하여 사용할 수 있습니다.

인기 있는 모바일 지갑은 다음을 포함한다. (우리는 이러한 지갑의 보안이나 기능을 보증하거나 나타내는 것이 아니다.):  

Jaxx    
비트코인, 라이트코인, 이더리움, 이더리움 클래식, ZCash, 다양한 ERC20 토큰 및 기타 통화를 지원하는 BIP-39 니모닉 시드를 기반으로 하는 다중 통화 모바일 지갑입니다. Jaxx는 안드로이드와 iOS에서 브라우저 플러그인 지갑으로, 그리고 다양한 운영 체제를 위한 데스크톱 지갑으로 사용할 수 있다.

상태  
다양한 토큰과 인기 있는 디앱을 지원하는 모바일 지갑과 디앱 브라우저입니다. iOS 및 Android에서 사용할 수 있습니다.

신뢰 지갑  
ERC20 및 ERC223 토큰은 물론 이더리움 및 이더리움 클래식을 지원하는 모바일 멀티 화폐 지갑입니다. Trust Wallet은 iOS 및 Android에서 사용할 수 있습니다.

암호 브라우저  
이더리움 앱 및 토큰과의 통합이 가능한 모든 기능의 이더리움 지원 모바일 디앱 브라우저 및 지갑입니다. iOS 및 Android에서 사용할 수 있습니다.  

# Browser Wallets
다양한 지갑과 디앱 브라우저가 크롬과 파이어폭스 같은 웹 브라우저의 플러그인이나 확장 기능으로 이용 가능하다. 브라우저 내에서 실행되는 원격 클라이언트입니다.

가장 인기 있는 것들 중 일부는 메타마스크, 잭스, 마이에더월렛, 마이크립토이다.  

## MetaMask
[intro_chapter]에 소개된 MetaMask는 다용도 브라우저 기반 지갑, RPC 클라이언트 및 기본 계약 탐색기입니다. 크롬, 파이어폭스, 오페라, 브레이브 브라우저에서 사용할 수 있다.

메타마스크는 다른 브라우저 지갑과 달리 웹3 인스턴스를 브라우저 자바스크립트 컨텍스트에 주입하여 다양한 이더리움 블록체인(메인넷, 로프스텐 테스트넷, 코반 테스트넷, 로컬 RPC 노드 등)에 연결하는 RPC 클라이언트 역할을 한다. 웹3 인스턴스를 주입하고 외부 RPC 서비스의 게이트웨이 역할을 하는 기능은 메타마스크를 개발자와 사용자 모두에게 매우 강력한 도구로 만든다. 예를 들어 웹3 공급자 역할을 하는 MyEtherWallet 또는 MyCrypto와 이러한 도구의 RPC 게이트웨이와 결합할 수 있습니다.  

## Jaxx
이전 섹션에서 모바일 지갑으로 소개된 Jaxx는 크롬과 파이어폭스 확장 기능과 데스크톱 지갑으로도 사용이 가능하다.  

## MyEtherWallet (MEW)
MyEtherWallet은 다음을 제공하는 브라우저 기반 자바스크립트 원격 클라이언트입니다.

+ Trezor 및 Ledger와 같은 인기 있는 하드웨어 지갑으로 연결되는 다리  

+ 다른 클라이언트가 주입한 웹3 인스턴스(예: MetaMask)에 연결할 수 있는 웹3 인터페이스  

+ 이더리움 전체 클라이언트에 연결할 수 있는 RPC 클라이언트  

+ 계약의 주소 및 애플리케이션 이진 인터페이스(ABI)가 주어지면 스마트 계약과 상호 작용할 수 있는 기본 인터페이스  

+ MEWConnect라는 모바일 앱은 하드웨어 지갑처럼 호환 안드로이드나 iOS 기기를 사용하여 자금을 저장할 수 있습니다.  

+ JavaScript에서 실행되는 소프트웨어 지갑  

## MyCrypto
2018년 초, 마이에더월렛 프로젝트는 두 개의 독립적인 개발 팀인 "포크"에 의해 두 개의 경쟁 구현체로 분리되었다. 이 두 프로젝트는 MyEtherWallet(원래 브랜드)과 MyCrypto라고 불린다. MyCrypto는 MyEtherWallet과 거의 동일한 기능을 제공하지만 MEWConect를 사용하는 대신 패리티 사인어 모바일 앱과의 연결을 제공합니다. MEWConnect와 마찬가지로 패리티 서명자는 전화기에 키를 저장하고 하드웨어 지갑과 유사한 방식으로 MyCrypto와 인터페이스합니다.  

## Mist (Deprecated)
미스트는 이더리움 재단이 만든 최초의 이더리움 지원 브라우저였다. ERC20 토큰 표준의 첫 구현체인 브라우저 기반 지갑을 포함하고 있다. 미스트는 또한 카멜케이스 체크섬(EIP-55)을 도입한 최초의 지갑이기도 하다. 2019년 3월부로 미스트는 더 이상 사용되지 않는다.  

# Conclusions
이 장에서는 이더리움 클라이언트를 살펴보았다. 당신은 클라이언트를 다운로드, 설치, 동기화하여 이더리움 네트워크의 참여자가 되었고, 자신의 컴퓨터에 블록체인을 복제함으로써 시스템의 건강과 안정성에 기여했습니다.  
