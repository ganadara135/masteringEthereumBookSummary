# Decentralized Applications(DApps)
본 챕터에서는 분권화된 어플리케인션(DApps)에 대해서 알아본다  
이더리움 개발 초기부터 스마트컨트랙트에 그치지 않고 더 큰 비전이 있었음  
웹을 재창조하는 것을 넘어서 DApp 의 새로운 세상을 만듦, 그것이 web3 임.  
스마트컨트랙트 로직과 애플리케이션의 지불 기능과 컨트롤 로직을 분권화하는 것이며,  
Web3 DApp 은 애플리케이션 나머지 부분을 분권화하는 것임: 저장소, 메시징, 네이밍 등.  
![Dapp image](https://github.com/ethereumbook/ethereumbook/blob/develop/images/web3suite.png)  
|  |  |
|--|--|
|Warning|분권화된 앱은 대담한 비전임. DApp 이란 용어는 스마트컨트랙트와 연동하는 웹프런트엔드에 종종 사용되나, 대부분은 중앙화된 앱(CApps)이므로 주의하라.|  

본 챕터에서 DApp 샘플로 옥션 플랫폼을 개발하고 배포할 것임.

# What is a DApp?
DApp 은 완전히 분권화된 애플리케이션임  
분권화되어야하는 애플리케이션의 모든 가능한 측면을 고려해보자  
+ 백엔드 소프트웨어(응용 로직)  
+ 프런트엔드 소프트웨어  
+ 데이터 저장소  
+ 메시지 교환  
+ 이름 해석 서비스(Name Resolution)  

위 각각은 일부는 중앙화돼 있거나 일부는 분권화돼있음  
예로서 프런트엔드가 중앙화된 서버에서 운영되는 웹 앱으로 개발되거나,  
자신의 폰에서 작동하는 모바일 앱으로 개발될 수 있고  
백엔드와 저장소는 개별 서버나 특정 데이터베이스일 수 있음  
그렇지않으면 스마트컨트랙트나 P2P 저장소를 사용할 수 도 있음  

전형적인 중앙화된 아키텍처가 제공할 수 없는 DApp의 장점이 있음  

#### Resiliency(회복력)  
비즈니스 로직이 스마트컨트랙트로 조정되기 때문에 DApp 백엔드는 완전히 분산돼있고  
블록체인 플랫폼 위에서 운영됨  
중앙화된 서버에 배포된 애플리케이션과는 다르게 DApp 은 중단되지않고 플랫폼이 작동중이면 언제나 사용할 수 있음  

#### Transparency
DApp 의 온체인 본질상 누구나 코드를 조사하는걸 허용하고, 기능에 대해서 확인할 수 있음  
DApp 과의 모든 상호작용이 블록체인에 영구히 저장된다  

#### Censorship resistance
사용자가 이더리움 노드에 접근하자마자, 사용자는 어떤 중앙화된 통제없이 DApp과 상호작용할 수 있음  
서비스 제공자나 심지어 스마트컨트랙트 오너도 일반 배포되면 코드를 변경할 수 없음  

이더리움 생태계에서도 완전히 분권화된 앱은 드물다.  
대부분이 아직 중앙화된 서비스나 서버에 그들의 일부 기능을 의존함  
향후에는 완전히 분권화된 방식으로 운영되는 DApp 이 가능할 것임  

## Backend(Smart Contract)
DApp 안에서 스마트컨트랙트는 비즈니스 로직이나 어플과 관련된 상태를 저장하기 위해서 사용됨  
따라서 정규 어플에서 백엔드처럼 서버쪽 요소를 대체하는것으로 스마트컨트랙트를 생각할 수 있음  
당연히 이것은 극단적 단순화임  
스마트컨트랙트에서 실행되는 모든 연산은 매우 비싸므로 가능한 작게 유지해야함  
그러므로 어플의 어느 부분이 신뢰성있고 분권화되어 실행되어야하는 구분하는 중요함  

이더리움 스마트컨트랙트를 배포이후에는 해당 비즈니스 로직은 향후에도 다른 개발자들에 의해서 사용될 수 있음  
스마트컨트랙트 아키텍처 디자인의 주요 고려사항은 배포되면 스마트컨트랙트의 코드를 변경할 수 없다는 것임  
유일한 접근방식은 SELFDESTRUCT 실행코드로 삭제할 수 있으나, 코드는 어떤 방식으로도 변경할 수 없음  

스마트컨트랙트 아키텍처 디자인의 두번째 주요 고려사항은 DApp 크기이다.  
정말 큰 일체구조 스마트컨트랙트는 배포하고 사용하기 위해선 많은 가스비고 비용하다  
그래서 일부 어플은 오프체인 연산과 외부 데이터 소스를 갖도록 디자인함  
하지만 DApp 의 코어 비즈니스 로직이 외부 데이터로 의존한다는 것은 사용자들이 외부 자원 사용을 신뢰할 수 있도록해야함  

## Frontend(Web User Interface)
개발자에게 EVM 과 Solidity를 이해하게 요구하는 DApp 의 비즈니스 로직과는 다르게  
DApp 의 클라이언트 쪽 인터페이스는 표준 웹 기술(HTML, CSS, JavaScript)를 사용할 수 있음  
이것은 전통 웹 개발자들이 친숙한 툴, 라이브러리, 프레임워크 사용을 허함  
메시지 사인, 트랜잭션 보내기, 키 관리하기 같은 이더리움과의 상호작동은 주로 웹브라우저를 통해서 수행됨.  

모바일 DApp 을 만드는 것이 가능할지라도 현재까지는 모바일 DApp 프런트엔드를 만드는데 활용가능한 자원이 충분치 않음  
프런트엔드는 주로 web3.js 자바스크리브 라이브러리를 통해서 이더리움에 연결됨  
web3.js 는 프런트엔드 자원과 번들링 되고, 웹서버를 통해서 브라우저로 서비스됨  

## Data Storage
높은 가스 비용과 현재 낮은 블록 가스 한계 때문에 스마트컨트랙트는 대규모의 데이터를 저장하거나 처리하는데 적합하지 않음  
그래서 대부분의 DApp은 오프체인 데이터 저장 서비스를 이용함  
이것은 벌키 데이터를 이더리움 체인 밖에 저장한다는 것임  
해당 데이터 저장소 플랫폼은 전형적인 클라우드 데이터베이스처럼 중앙화될 수 있고,  또는 IPFS, Swarm 처럼 P2P 방식으로 저장하여서 분권화될 수 있음  

분권화된 P2P 저장소는 이미지, 비디오, 프런트엔드 인터페이스자원(HTML,CSS) 같은 정적 자원을 분산화하여서 저장하는데 이상적임  
아래에서 그 예를 보자  

### IPFS
Inter-Planetary File Systems(IPFS) 는 P2P 네트워크상에서 피어간에 저장된 객체를 분산하는 분권화된 콘텐츠 어드레서블 저장 시스템임 
"컨텐츠 어드레서블"은 각각의 컨텐츠가 해시화되고 해당 해시는 각 파일을 구분하기 위해서 사용된다는 것을 의미한다  
당연히 해당 해시를 IPFS 노드에게 요청하면 매칭된 컨텐츠(파일)을 추출할 수 있음  

IPFS 는 웹 애플리케이션 전달을 위한 프로토콜 옵션으로써 HTTP를 대체하는 것이 목표임  
단독 서버에 웹 어플을 저장하는 것을 대신해서 파일을 IPFS 에 저장할 수 있고  
어떤 IPFS 노드에서든 추출할 수 있음  

### Swarm
Swarm 은 IPFS 유사한 또하나의 컨텐츠 어드레셔블 P2P 저장 시스템임  
Swarm 은 이더리움 재단에서 만들었고 Go-Ethereum 슈트 툴중에 하나임  
Swarm 은 중앙화된 웹 서버 대신해 분권화된 P2P 시스템으로 부터 웹사이트를 접근하게 해준다  

## Decentralized Message Communications Protocols
프로세스간 통신이란 애플리케이션 사이나 다른 애플리케이션 사이 또는 애플리케이션 사용자간에 메시지를 교환할 수 있다는 것을 의미함  
전통적으로 이런 기능은 중앙화된 서버에 의존해서 구현된다  
하지만, P2P 네트워크를 위에서 메시지 전송을 통해서 서버 기반 프로토콜에 비해서 다양한 분권화된 대안이 있음  
Whisper 가 DApp을 위한 가장 유명한 P2P 메시징 프로토콜임  

마지막 단계의 분권화되어야 하는 어플은 네임 해석 서비스임  

# A Basic DApp Example: Auction DApp
본 섹션에서는 DApp 예제로 만들면서 다양한 분권화 지원 툴을 살펴볼 것임   
Auction DApp 은 사용자가 "deed" 토큰을 등록하도록 한다   
deed 토큰은 집, 차, 상표 같은 고유한 자산을 나타냄  
토큰이 등록되면 토큰 소유권이 Auction DApp 에서 전송됨  
이것은 판매를 위해서 목록화된다는 것을 의미함  
Auction DApp 이 등록된 토큰을 목록화화면 여타 사용자들이 경매에 참여할 수 있음  
각각의 경매동안 사용자들은 해당 경매를 위해 만들어진 채팅방에 참여할 수 있음  
경매가 완료되면, deed 토큰 소유권이 경매 낙찰자에게 전송됨  

경매 DApp 의 주요 요소는 다음과 같음  
+ ERC721 교환 불가 deed 토큰 구현한 스마트컨트랙트(DeedRepository)  
+ deed 을 팔기위한 경매를 구현한 스마트컨트랙트(AuctionRepository)  
+ Vue/Vuetify 자바스크립트 프레임워크를 사용하는 웹 프런트엔드  
+ MetaMask 나 다른 클라이언트를 통해서 이더리움 체인에 연결하기 위한 web3.js 라이브러리  
+ 이미지 같은 자원을 저장하기 위한 Swarm 클라이언트  
+ 모든 참여자들이 경매당 만들어진 채팅방을 만들기 위한 Whisper 클라이언트  
![erc721](https://github.com/ethereumbook/ethereumbook/raw/develop/images/auction_diagram.png)  

## Auction DApp: Backend Smart Contracts
본 경매 DApp 은 두 가지 스마트컨트랙트가 지원됨  
애플리케이션을 지원하기 위해 이더리움 블록체인에 배포하는게 필요함: AuctionRepository, DeedRepository  

DeedRepository.sol: ERC721 deed 토큰 소스 위치  
```
link:code/auction_dapp/backend/contracts/DeedRepository.sol[]
```

Auction DApp 은 경매를 위한 토큰을 발행하고 추적하기 위해서 DeedRepository 컨트랙트를 사용함  
경매 자체는 AuctionRepository 컨트랙트에 의해서 조율됨  
아래에 AuctionRepository 컨트랙트의 주요 기능과 데이터 구조를 보여줌  
전체 컨트랙트는 Masterting Ethereum 원본 링크에서 참고하라  
```
contract AuctionRepository {

    // Array with all auctions
    Auction[] public auctions;

    // Mapping from auction index to user bids
    mapping(uint256 => Bid[]) public auctionBids;

    // Mapping from owner to a list of owned auctions
    mapping(address => uint[]) public auctionOwner;

    // Bid struct to hold bidder and amount
    struct Bid {
        address from;
        uint256 amount;
    }

    // Auction struct which holds all the required info
    struct Auction {
        string name;
        uint256 blockDeadline;
        uint256 startPrice;
        string metadata;
        uint256 deedId;
        address deedRepositoryAddress;
        address owner;
        bool active;
        bool finalized;
    }
```
AuctionRepository 컨트랙트는 아래 함수들로 모든 경매 기능 다룸  
```
getCount()
getBidsCount(uint _auctionId)
getAuctionsOf(address _owner)
getCurrentBid(uint _auctionId)
getAuctionsCountOfOwner(address _owner)
getAuctionById(uint _auctionId)
createAuction(address _deedRepositoryAddress, uint256 _deedId,
              string _auctionTitle, string _metadata, uint256 _startPrice,
              uint _blockDeadline)
approveAndTransfer(address _from, address _to, address _deedRepositoryAddress,
                   uint256 _deedId)
cancelAuction(uint _auctionId)
finalizeAuction(uint _auctionId)
bidOnAuction(uint _auctionId)
```
트러블을 사용해서 이더리움 블록체인 테스트넷( Ropsten) 에 위 컨트랙트를 배포함  
```
$ cd code/auction_dapp/backend
$ truffle init
$ truffle compile
$ truffle migrate --network ropsten
```

## DApp governance
Auction DApp 에 2개 스마트컨트랙트를 소스 코드를 보면, 일부 중요한 것을 볼 수 있음: DApp 에 대해서 특별한 권한을 갖고 있는 특별 계정이나 역할이 없음.  
각 옥션은 특별 능력을 가진 오너는 있으나 Auction DApp 자체는 특별 권한 사용자가 없음  

이것은 DApp 통제를 분권화하기 위한 의도적인 선택임  
배포 이후에 어떤 통제도 포기한 것임  
대조적으로 일부 DApp 들은 특별한 능력이 있는 특권 계정을 갖고 있음  
주로 DApp 컨트랙트를 파쇄하거나, 설정자료를 변경하거나, 특정 작동을 거부하는 능력임  
대개 이런 통제 기능은 DApp 상에 버그나 알수없는 문제가 발생했을때 대응하기 위해서 넣음  

통제 이슈는 특별히 해결하기 어려운 문제중에 하나이므로, 양날의 칼임  
한 쪽의 특별한 계정 사용은 위험함: 오염되었을때, DApp 의 보안을 침범할 수 있음  
다른 한 쪽은 어떤 특별한 계정이 없으므로, 버그 발생시에 복구할 수 있는 옵션이 없음  
DAO 가 특별 계정을 사용하지않는 예의 DApp 이고, Bancor 가 특별 계정 사용으로 공격 받았던 예의 DApp 의 예임  

## Auction DApp: Frontend User Interface
Auction DApp 컨트랙트가 배포되면 자바스크립트 콘솔이나 web3.js 같은 것을 통해서 상호작동할 수 있음  
본 Auction DApp 사용자 인터페이스는 구글의 Vue2/Vuetify 자바스크립트 프레임워크를 사용해서 만들어짐  

전체 컨트랙트는 Masterting Ethereum 원본 링크에서 참고하고  
디렉토리 구조는 아래와 같음  
```
frontend/
|-- build
|   |-- build.js
|   |-- check-versions.js
|   |-- logo.png
|   |-- utils.js
|   |-- vue-loader.conf.js
|   |-- webpack.base.conf.js
|   |-- webpack.dev.conf.js
|   `-- webpack.prod.conf.js
|-- config
|   |-- dev.env.js
|   |-- index.js
|   `-- prod.env.js
|-- index.html
|-- package.json
|-- package-lock.json
|-- README.md
|-- src
|   |-- App.vue
|   |-- components
|   |   |-- Auction.vue
|   |   `-- Home.vue
|   |-- config.js
|   |-- contracts
|   |   |-- AuctionRepository.json
|   |   `-- DeedRepository.json
|   |-- main.js
|   |-- models
|   |   |-- AuctionRepository.js
|   |   |-- ChatRoom.js
|   |   `-- DeedRepository.js
|   `-- router
|       `-- index.js
```
컨트랙트를 배포후에 프런트엔드 설정파일(frontend/src/config.js) 에서 DeedRepository 와 AuctionRepository 컨트랙트 주소를 수정하라  
프런트엔드 애플리케이션은 JSON-RPC 와 WebSocket 인터페이스를 통해서 이더리움 노드에 접근할 수 있음  
프런트엔드 설정을 완료후에 로컬 머신에서 웹서버를 아래와 같이 가동함  
```
$ npm install
$ npm run dev
```
Auctin DApp 프런트엔드 가동하고 웹브라우저에서 아래 주소로 접근할 수 있음  
http://localhost:8080  
웹브라우저에서는 아래와 같은 화면이 나타남  
![Auction Dapp](https://github.com/ethereumbook/ethereumbook/raw/develop/images/auction_dapp_home_screen.png)  

# Further Decentralizing the Auction DApp
DApp 을 더 분권화해보자  
AuctionRepository 컨트랙트는 누구에게나 오픈되어있고 어떤 감시로부터도 독립적으로 작동함  
일단 배포되면 가동을 멈추지 않고 어떤 경매도 통제 받지 않음  
각 경매는 검열과 본인인증없이 경매하는 동안에 누구하든 메시지 교환할 수 있는 분리된 채팅방이 있음  
연관된 이미지나 설명문 같은 다양한 경매 자원들이 Swarm 에 저장되므로 검열이 힘듦  
누구둔지 자신의 로컬 머신에 Vue 프런트엔드를 돌리거나 수동으로 직접 트랜잭션을 만들어서 Auction DApp 과 상호작용할 수 있음  

여기서 DApp 을 더 분권화하고 회복력있게 만들수 있음  
+ 모든 애플리케이션 코드를 Swarm 이나 IPFS 에 저장함  
+ 이더리움 이름 해석 서비스를 통해서 DApp 에 접근함  

# Storing the Auction DApp on Swarm
Auction DApp 은 이미 각 경매를 위한 아이콘 이미지를 저장하기 위해서 Swarm 을 사용하고 있음  
이것은 비싼 이더리움에 데이터를 저장하는 것보단 훨씬 효율적인 방식임  
또는 웹서버나 파일서버처럼 중앙화된 서비스에 저장하는 것보다 더 회복력도 좋음  

하지만 여기서 한 발짝 더 나가서, DApp 의 프런트엔드 전체를 Swarm 에 저장해서  
웹서버에서 가동하는 게 아니라 Swarm 노드 직접적으로 운영하게 할 수 있음  

## Preparing Swarm
Swarm 을 설치하고 Swarm 노드를 가동하자  
일단 Swarm 이 설치하면 버전 체크 명령어로 Swarm 이 제대로 작동하는 확인할 수 있음  
```
$ swarm version
Version: 0.3
Git Commit: 37685930d953bcbe023f9bc65b135a8d8b8f1488
Go Version: go1.10.1
OS: linux
```
Swarm 을 가동후에 Geth 에 연결하거나, JSON-RPC API 에 연결하기 위한 방법을 Swarm 에 알려줘야하는데 이것은 아래 링크를 참고하라  
https://swarm-guide.readthedocs.io/en/latest/gettingstarted.html

Swarm 을 가동하면 아래와 같은 결과를 볼 수 있음  
```
Maximum peer count                       ETH=25 LES=0 total=25
Starting peer-to-peer node               instance=swarm/v0.3.1-225171a4/linux...
connecting to ENS API                    url=http://127.0.0.1:8545
swarm[5955]: [189B blob data]
Starting P2P networking
UDP listener up                          self=enode://f50c8e19ff841bcd5ce7d2d...
Updated bzz local addr                   oaddr=9c40be8b83e648d50f40ad3... uaddr=e
Starting Swarm service
9c40be8b hive starting
detected an existing store. trying to load peers
hive 9c40be8b: peers loaded
Swarm network started on bzz address: 9c40be8b83e648d50f40ad3d35f...
Pss started
Streamer started
IPC endpoint opened                      url=/home/ubuntu/.ethereum/bzzd.ipc
RLPx listener up                         self=enode://f50c8e19ff841bcd5ce7d2d...
```
Swarm 노드가 제대로 작동하는지는 로컬 Swarm 게이트웨이 웹 인터페이스로 접근하여 확인할 수 있음  
http://localhsot:8500  
위 주소로 접근하면 아래와 같은 화면을 볼 수 있고, Swarm hash 나 ENS 이름을 넣어서 조회할 수 있음  
![Welcom Swarm](https://github.com/ethereumbook/ethereumbook/raw/develop/images/swarm-gateway.png)  

## Uploading Files to Swarm
로컬 Swarm 노드와 게이트웨이가 작동중이면 파일을 업로드할 수 있고,  
파일 해시 값을 통해서 어떤 Swarm 노드에서도 접근할 수 있음  
아래와 같이 파일을 업로드할 수 있음  
```
$ swarm up code/auction_dapp/README.md
ec13042c83ffc2fb5cb0aa8c53f770d36c9b3b35d0468a0c0a77c97016bb8d7c
```

하나의 파일을 업로드하기는 직관적이라서 쉬우나, 전체 DApp 프런트엔드를 업로드하기는 복잡함  
다양한 DApp 자원(HTML,CSS,JS)들이 서로간의 내부 참조하기 때문임  
일반적으로 웹서버는 URL을 로컬 파일로 변환하고 정확한 자원과 연결함  
우리는 DApp 을 패키징하여서 Swarm 에서 같은 효과를 얻음  

Auction DApp 을 아래와 같이 패키징함  
```
$ cd code/auction_dapp/frontend
$ npm run build

> frontend@1.0.0 build /home/aantonop/Dev/ethereumbook/code/auction_dapp/frontend
> node build/build.js

Hash: 9ee134d8db3c44dd574d
Version: webpack 3.10.0
Time: 25665ms
Asset     Size
static/js/vendor.77913f316aaf102cec11.js  1.25 MB
static/js/app.5396ead17892922422d4.js   502 kB
static/js/manifest.87447dd4f5e60a5f9652.js  1.54 kB
static/css/app.0e50d6a1d2b1ed4daa03d306ced779cc.css  1.13 kB
static/css/app.0e50d6a1d2b1ed4daa03d306ced779cc.css.map  2.54 kB
static/js/vendor.77913f316aaf102cec11.js.map  4.74 MB
static/js/app.5396ead17892922422d4.js.map   893 kB
static/js/manifest.87447dd4f5e60a5f9652.js.map  7.86 kB
index.html  1.15 kB

Build complete.
```
위 명령에 결과로 새로운 폴더가 생성됨: code/auction_dapp/frontend/dist  
```
dist/
|-- index.html
`-- static
    |-- css
    |   |-- app.0e50d6a1d2b1ed4daa03d306ced779cc.css
    |   `-- app.0e50d6a1d2b1ed4daa03d306ced779cc.css.map
    `-- js
        |-- app.5396ead17892922422d4.js
        |-- app.5396ead17892922422d4.js.map
        |-- manifest.87447dd4f5e60a5f9652.js
        |-- manifest.87447dd4f5e60a5f9652.js.map
        |-- vendor.77913f316aaf102cec11.js
        `-- vendor.77913f316aaf102cec11.js.map
```
아래와 같이 DApp 전체를 Swarm 에 업로드 할 수 있음  
index.html 이 기본 패스라고 지시할 수 있음  
```
$ swarm --bzzapi http://localhost:8500 --recursive \
  --defaultpath dist/index.html up dist/

ab164cf37dc10647e43a233486cdeffa8334b026e32a480dd9cbd020c12d4581
```
이제 아래와 같이 Swarm URL 로 접근할 수 있음  
```
bzz://ab164cf37dc10647e43a233486cdeffa8334b026e32a480dd9cbd020c12d4581
```
위 URL 이 사용자 편의적이지 않다.  다음 섹션에서 Ethereum Name Service 를 알아본다  

# The Ethereum Name Service (ENS)
전통적인 인터넷에선 Domain Name System(DNS)가 브라우저를 통해서 사람이 읽을 수 있는 이름을 제공했음  
DNS 는 내부적으로 해당 이름과 IP 주소나 다른 식별자들을 연결해줌  
이더리움 블록체인에서는 Ethereum Naming System(ENS)가 이런 기능을 분권화해서 제공하고 있음  

예로서 이더리움 재단 후원 주소는 아래와 같음  
0xfB6916095ca1df60bB79Ce92cE3Ea74c37c5d359  
위 주소는 ENS 와 연동되어 있으므로 아래와 같음  
ethereum.eth  

ENS 는 단순히 스마트컨트랙트 이상인 기본적인 DApp 자체로서 분권화된 네임 서비스를 제공함  
더욱이 ENS 는 등록, 관리, 등록된 이름 경매를 위해서 수많은 DApp 에서 지원됨  
ENS DApp 이 다른 DApp 에게 서비스하기 위해서 만들어짐  

본 섹션에서 어떻게 ENS가 작동하는지 볼 것임  

## History of Ethereum Name Services
이름 등록이 블록체인에서 첫 화폐기반이 아닌 애플리케이션임  
이름 서비스를 위한 ERC 와 많은 제안이 있었으나, Johnson 이 2016년 참여하면서 본격화 됨  
ENS 가 2017년 5월 4일에 가동됨  

## The ENS Specification
ENS 은 3가지 이더리움 개선 제안으로 규정됨: EIP-317 은 기본 기능을 정의하고  
EIP-162 는 .eth 루트에 위한 경매 시스템을 기술함  
EIP-181 은 주소의 역 연결성을 규정함  

ENS 은 샌드위치 디자인 철학을 따라서 만들어짐  
맨 아래층은 단순하며 위로 올라갈 수록 복잡해지나 코드는 교환될 수 있음  
탑 층이 분린된 계정에 모든 자금을 유지함  

## Bottom Layer: Name Owners and Resolvers
ENS는 사람이 읽을 수 있는 이름 대신 "노드"로 동작한다: 사람이 읽을 수 있는 이름은 "나메하시" 알고리즘을 사용하여 노드로 변환된다.

ENS의 기본 계층은 ERC137에 의해 정의된 영리하게 단순한 계약(50줄 미만의 코드)으로 노드의 소유자만 자신의 이름에 대한 정보를 설정하고 하위 노드(DNS 하위 도메인과 동등한 ENS)를 만들 수 있다.

기본 계층의 유일한 기능은 노드 소유자가 자신의 노드(특히 리졸버, 라이브 또는 소유권 이전)에 대한 정보를 설정하고 새 하위 노드의 소유자를 생성할 수 있도록 하는 기능입니다.  

## The Namehash algorithm
나메하시(Namehash)는 모든 이름을 이름을 식별하는 해시로 변환할 수 있는 재귀 알고리즘입니다.

'반복형'은 같은 유형의 더 작은 문제인 하위 문제를 풀어서 문제를 푼 다음, 하위 문제에 대한 해법을 활용해 원래 문제를 푸는 것을 의미한다.

나메해시는 이름의 구성 요소를 재귀적으로 해시하여 유효한 입력 도메인을 위해 고유한 고정 길이 문자열(또는 "노드")을 생성한다. 예를 들어 subdomain.example.eth의 Namehash 노드는 keccak('<example.eth' 노드) + keccak('<subdomain>')입니다. 우리가 해결해야 할 하위 문제는 예를 들어 keccak('<.eth' 노드) + keccak('<예시>')인 노드를 계산하는 것이다. 

루트 노드는 우리가 재귀의 기본 케이스라고 부르는 것이고, 우리는 분명히 이것을 재귀적으로 정의할 수 없습니다. 그렇지 않으면 알고리즘은 절대 종료되지 않습니다! 루트 노드는 0x000000000000000000000000000000000000(32 0바이트)으로 정의됩니다.

이 모든 것을 종합하면, subdomain.example.eth의 노드는 따라서 keccak(케칵(0x0...0 + keccak('eth') + keccak('example') + keccak('subdomain')이다.

일반화하면 Namehash 함수를 다음과 같이 정의할 수 있습니다(루트 노드의 기본 대소문자 또는 빈 이름의 다음 재귀 단계):  
```
namehash([]) = 0x0000000000000000000000000000000000000000000000000000000000000000
namehash([label, ...]) = keccak256(namehash(...) + keccak256(label))
```
Python 에서는 아래와 같다:  
```
  def namehash(name):
  if name == '':
    return '\0' * 32
  else:
    label, _, remainder = name.partition('.')
    return sha3(namehash(remainder) + sha3(label))
```
따라서 mastering-ethereum.eth 는 아래와 같이 처리함:  
```
    namehash('mastering-ethereum.eth')
⇒ sha3(namehash('eth') + sha3('mastering-ethereum'))
⇒ sha3(sha3(namehash('') + sha3('eth')) + sha3('mastering-ethereum'))
⇒ sha3(sha3(('\0' * 32) + sha3('eth')) + sha3('mastering-ethereum'))

```
하위 도메인은 하위 도메인 자체를 가질 수 있습니다. 하위 도메인 뒤에 하위 도메인.예제.eth가 있을 수 있으며, 하위 도메인.예제.eth 뒤에 하위 도메인.예제.eth가 있을 수 있습니다. 비싼 계산을 피하기 위해, 나메해시는 이름 자체에 의존하기 때문에, 주어진 이름의 노드를 미리 계산하여 계약에 삽입할 수 있으며, 문자열 조작의 필요성을 없애고 원시 이름의 구성 요소 수에 관계 없이 ENS 레코드를 즉시 검색할 수 있다.  
    

## How to choose a valid name
이름은 점으로 구분된 일련의 레이블로 구성됩니다. 비록 대문자와 소문자가 허용되지만, 모든 레이블은 해시하기 전에 레이블을 대소문자로 접는 UTS #46 정규화 과정을 따라야 한다.

레이블과 도메인은 길이 제한 없이 사용할 수 있지만 레거시 DNS와의 호환성을 위해 다음 규칙을 사용하는 것이 좋습니다.

+ 라벨은 각각 64자 이하여야 합니다.

+ 전체 ENS 이름은 255자 이하여야 한다.

+ 레이블은 하이픈으로 시작하거나 끝나거나 숫자로 시작하면 안 됩니다.
    
### Root node ownership
이 계층 시스템의 결과 중 하나는 최상위 도메인(TLD)을 만들 수 있는 루트 노드의 소유자에게 의존한다는 것이다.

최종 목표는 새로운 TLD에 대한 분산형 의사결정 프로세스를 채택하는 것이지만, 작성 시 루트 노드는 다른 국가의 사람들이 보유한 7개 중 4개 멀티시그(DNS 시스템의 7개 키홀더의 반영으로 구축됨)에 의해 제어된다. 따라서 변경 사항을 적용하려면 7개 키홀더 중 적어도 4개 이상의 키홀더가 필요합니다.

현재 이러한 핵심 보유자의 목적과 목표는 지역사회와 협력하여 다음을 수행하는 것입니다.

+ 시스템이 평가되면 .eth TLD의 임시 소유권을 보다 영구적인 계약으로 마이그레이션하고 업그레이드합니다.

+ 커뮤니티가 필요하다고 동의하면 새로운 TLD를 추가하는 것을 허용합니다.

+ 루트 멀티시그의 소유권을 시스템이 합의, 테스트 및 구현될 때 보다 분산된 계약으로 마이그레이션합니다.

+ 최상위 레지스트리의 버그나 취약성을 처리하는 최종 해결 방법 역할을 합니다.    
    
### Resolvers
기본적인 ENS 계약은 이름에 메타데이터를 추가할 수 없다; 그것은 소위 "해결사 계약"의 일이다. 앱과 관련된 Swarm 주소, 앱에 대한 지불을 받는 주소(이더 또는 토큰 단위) 또는 앱의 해시가 무엇인지(무결성 확인을 위해)와 같은 이름에 대한 질문에 답할 수 있는 사용자가 만든 계약이다.  
    
## Middle Layer: The .eth Nodes
작성 당시 스마트 계약서에 고유하게 등록할 수 있는 최상위 도메인은 .eth뿐이다.  
|  |  |  
|--|--|  
|노트|전통적인 DNS 도메인 소유자들이 ENS 소유권을 주장할 수 있도록 하기 위한 작업이 진행 중입니다. 이론적으로는 .com에서 동작할 수 있지만, 지금까지 구현된 도메인은 .xyz뿐이며, Ropsten 테스트넷에서만 동작한다.|  
.eth 도메인은 경매 시스템을 통해 배포됩니다. 예약된 목록이나 우선순위는 없으며 이름을 얻는 유일한 방법은 시스템을 사용하는 것입니다. 경매 시스템은 복잡한 코드 부분(500줄 이상)이며, ENS의 초기 개발 노력(및 버그!)의 대부분은 시스템의 이 부분에 있었다. 그러나 이 제품은 나중에 더 큰 위험 없이 교체 및 업그레이드가 가능합니다.  
    
### Vickrey auctions
이름은 수정된 빅레이 경매를 통해 배포된다. 전통적인 빅레이 경매에서는 모든 입찰자가 봉인된 입찰가를 제출하고, 모든 입찰자가 동시에 공개되는데, 이때 가장 높은 입찰자가 경매에서 이기지만 두 번째로 높은 입찰가만 지불한다. 그러므로 입찰자들은 그들의 진정한 가치를 입찰하는 것은 그들이 당첨될 가능성을 증가시키지만 결국 그들이 지불할 가격에는 영향을 미치지 않기 때문에 그들에게 그 이름의 진정한 가치보다 적게 입찰하지 않도록 장려된다.

블록체인에서는 다음과 같은 변화가 필요하다.

+ 입찰자가 지불할 의사가 없는 입찰서를 제출하지 않도록 하려면 입찰가가 유효한지 확인하기 위해 사전에 입찰가와 같거나 높은 값을 고정해야 한다.  

+ 블록체인에 비밀을 숨길 수 없기 때문에 입찰자가 입찰한 원래의 가치와 이름을 숨기기 위해서는 최소 두 건의 거래(커밋-공개 과정)를 실행해야 한다.  

+ 분산형 시스템에서는 모든 입찰을 동시에 공개할 수 없기 때문에 입찰자가 입찰을 직접 공개해야 하며, 그렇지 않으면 보관된 자금을 몰수해야 합니다. 이 몰수가 없다면 많은 입찰을 할 수 있고 한두 개만 공개할 수 있어 봉인된 입찰 경매를 전통적인 가격 상승 경매로 바꿀 수 있다.  

따라서 경매는 4단계 과정이다.

1. 경매를 시작합니다. 이것은 이름을 등록하려는 의도를 브로드캐스트하기 위해 필요합니다. 이것은 모든 경매 마감일을 만듭니다. 이름은 해시 처리되어 사전에 이름이 있는 사람만이 어떤 경매가 열렸는지 알 수 있습니다. 이렇게 하면 일부 개인 정보가 허용되므로 새 프로젝트를 만들 때 프로젝트에 대한 세부 정보를 공유하지 않으려는 경우에 유용합니다. 당신은 동시에 여러 개의 더미 경매를 열 수 있기 때문에, 만약 누군가가 당신을 따라온다면 그들은 단순히 당신이 여는 모든 경매에 입찰할 수 없다.  

2. 봉인 입찰하다. 입찰 마감일 전에 주어진 양의 에테르를 비밀 메시지의 해시에 묶어서 이 작업을 수행해야 합니다(이름의 해쉬, 입찰의 실제 금액 및 소금을 포함). 당신의 진정한 가치를 감추기 위해 실제 입찰 금액보다 더 많은 에테르를 잠글 수 있습니다.  

3. 입찰 공개. 공개 기간 동안 입찰가를 공개하는 거래를 해야 최고 입찰가와 차순위 입찰가를 계산해 낙찰자에게 에테르를 돌려보낸다. 매번 입찰이 공개될 때마다 현재 입찰자가 다시 계산되므로 공개 마감일 전에 설정된 마지막 입찰자가 전체 입찰자가 됩니다.  

4. 치우다. 낙찰자일 경우 경매에 부쳐진 입찰가와 차액 간의 차액을 돌려받기 위해 경매를 마무리할 수 있습니다. 만약 당신이 공개하는 것을 잊었다면 늦게 공개하고 입찰 금액을 조금 회수할 수 있습니다.   
    
## Top Layer: The Deeds
ENS의 최상위 계층은 자금을 보유하는 단일 목적을 가진 또 다른 초단순 계약이다.

이름을 따내면 실제로 자금이 어디로 보내지는 것이 아니라 이름을 보유하려는 기간(최소 1년) 동안만 잠기게 된다. 이는 보증된 바이백과 같이 동작한다: 소유자가 더 이상 이름을 원하지 않을 경우 시스템에 다시 판매하고 에테르를 복구할 수 있다.

물론, 단일 계약이 수백만 달러를 이더에 보유하는 것은 매우 위험하다는 것이 증명되었고, 대신 ENS는 각각의 새로운 이름에 대한 증서 계약을 만듭니다. 증서계약은 매우 간단하며(약 50줄의 코드), 자금을 단일 계좌(증서소유자)로 다시 이체하고 단일 실체에 의해 호출(등록기관 계약)만 가능하다. 이러한 접근 방식은 버그가 자금을 위험에 빠뜨릴 수 있는 공격 표면을 크게 줄여줍니다.  
    
### Registering a Name
ENS에 이름을 등록하는 것은 빅레이 경매에서 보았듯이 4단계 과정입니다. 먼저 가능한 모든 이름을 입찰하고 48시간 후에 입찰 결과를 발표하겠습니다 등록을 위한 ENS 연대표는 등록 연대표를 보여주는 도표이다.

우리 이름 등록하자!

우리는 이용 가능한 사용자 친화적인 여러 인터페이스 중 하나를 사용하여 이용 가능한 이름을 검색하고, 이더리움북이라는 이름에 입찰을 하고, 입찰을 공개하고, 이름을 확보할 것이다.

ENS에는 ENS 디앱과 상호 작용할 수 있는 많은 웹 기반 인터페이스가 있다. 이 예에서는 MyCrypto 인터페이스를 MetaMask와 함께 지갑으로 사용할 것입니다.  
    
![Timeline](https://github.com/ethereumbook/ethereumbook/raw/develop/images/ens-flow.png)   
우선 우리가 원하는 이름을 사용할 수 있는지 확인해야 합니다. 이 책을 쓰면서, 우리는 이름 마스터링을 꼭 등록하고 싶었는데, 아아, MyCrypto.com에서 ENS 이름을 검색해보니 이미 사용 중이었음이 드러났다! ENS 등록은 1년 밖에 지속되지 않기 때문에, 미래에 그 명칭을 확보하는 것이 가능할 수 있다. 그동안 이더리움북(MyCrypto.com에서 ENS 이름 검색)을 검색해보자.  
![mycrypto.com](https://github.com/ethereumbook/ethereumbook/raw/develop/images/ens-checkname.png)  
좋습니다! 사용 가능한 이름입니다. 등록하기 위해서는 ENS 이름 경매 시작을 진행해야 합니다. 메타마스크를 풀고 ethereumbook.eth 경매를 시작합시다.  
![StartAuction](https://github.com/ethereumbook/ethereumbook/raw/develop/images/ens-auction.png)  
입찰합시다. 그러기 위해서는 ENS 이름을 입찰하는 단계를 따라야 합니다.  
![ENSname](https://github.com/ethereumbook/ethereumbook/raw/develop/images/ens-bid.png)  
|  |  |  
|--|--|  
|경고|빅레이 경매에서 언급했듯이 경매가 끝난 후 48시간 이내에 입찰을 공개해야 하며 그렇지 않으면 입찰 자금을 잃게 된다. 우리가 이것을 깜빡하고 0.01 ETH를 잃었나요? 당연하지.

스크린샷을 찍고 비밀 문구를 (입찰에 대한 백업으로) 저장한 후 일정관리에 공개 날짜와 시간에 대한 주의사항을 추가하여 자금을 잃어버리는 일이 없도록 하십시오.|  
마지막으로, 우리는 당신의 입찰이 포함된 메타마스크 거래에 보이는 큰 녹색 제출 버튼을 클릭하여 거래를 확인합니다.      
![metamask](https://github.com/ethereumbook/ethereumbook/raw/develop/images/ens-metamask-bid.png)  
모든 일이 잘 풀리면 이런 방식으로 거래서를 제출한 뒤 48시간 안에 입찰서를 반납하고 공개하면 요청하신 이름이 이더리움 주소로 등록된다.  
    
# Managing Your ENS Name
ENS 이름을 등록하면 사용자에게 친숙한 다른 인터페이스인 ENS 관리자를 사용하여 관리할 수 있습니다.

검색 상자에 관리하려는 이름을 입력합니다(ENS Manager 웹 인터페이스 참조). ENS 관리자 DApp이 사용자를 대신하여 이름을 관리할 수 있도록 이더리움 지갑(예: MetaMask)을 잠금 해제해야 합니다.      
![eth](https://github.com/ethereumbook/ethereumbook/raw/develop/images/ens-manager.png)  
이 인터페이스를 통해 하위 도메인을 생성하고 해결 프로그램 계약을 설정하고(나중에 자세히 설명), 각 이름을 적절한 리소스에 연결할 수 있습니다(예: DApp 프런트 엔드의 Swarm 주소).  
    
## Creating an ENS subdomain
먼저 Action DApp 예제의 하위 도메인을 생성해 보겠습니다(하위 도메인 경매 추가.ethereumbook.eth 참조). 서브도메인 경매의 이름을 정해서 정식 명칭은 경매입니다.ehereumbook.eth.입니다.      
![manager](https://github.com/ethereumbook/ethereumbook/raw/develop/images/ens_manager_add_subdomain.png)  
서브도메인이 만들어지면 기존에 도메인 ethereumbook.eth 을 관리했던 것처럼 검색창에 auction.ethereumbook.eth 를 입력하고 관리할 수 있다.  
    
## ENS Resolvers
ENS에서 명칭을 결정하는 것은 두 단계 과정이다.

1. ENS 레지스트리는 해시 후 해결할 이름과 함께 호출됩니다. 레코드가 있으면 레지스트리는 확인자의 주소를 반환합니다.  

2. 요청 중인 리소스에 적합한 메서드를 사용하여 해결 프로그램을 호출합니다. 해결자는 원하는 결과를 반환합니다.  
이 2단계 프로세스에는 몇 가지 이점이 있습니다. 리졸버의 기능을 네이밍 시스템 자체에서 분리하면 훨씬 더 많은 유연성을 얻을 수 있습니다. 이름 소유자는 사용자 지정 해결 프로그램을 사용하여 유형이나 자원을 해결하고 ENS의 기능을 확장할 수 있습니다. 예를 들어, 나중에 지리 위치 리소스(경도/경도)를 ENS 이름에 연결하려는 경우 지리 위치 조회에 응답하는 새 해결자를 생성할 수 있습니다. 미래에 어떤 애플리케이션이 유용할 지 누가 알겠는가? 맞춤형 리졸버와 함께라면, 유일한 제한은 상상력입니다.

편의를 위해 주소(월렛 또는 계약용)와 콘텐츠(DApp 또는 계약 소스 코드용 스웜 해시)를 포함한 다양한 리소스를 해결할 수 있는 기본 공개 리졸버가 있습니다.

옥션 디앱을 Swarm 해시에 링크하고 싶기 때문에, oction.eth의 기본 공개 리졸버 설정에 나와 있는 것처럼 콘텐츠 해상도를 지원하는 공개 리졸버를 사용할 수 있으며, 사용자 정의 리졸버를 코딩하거나 배포할 필요가 없습니다.  
    
![resolver](https://github.com/ethereumbook/ethereumbook/raw/develop/images/ens-manager-set-default-resolver.png)  

## Resolving a Name to a Swarm Hash (Content)
경매에 대한 해결자가 공개 해결자로 설정되면 Swarm 해시를 우리 이름의 콘텐츠로 반환하도록 설정할 수 있습니다  
![set](https://github.com/ethereumbook/ethereumbook/raw/develop/images/ens-manager-set-content.png)  
우리의 거래가 확인될 때까지 잠시 기다린 후에, 우리는 이름을 정확하게 해결할 수 있을 것입니다. 이름을 설정하기 전에 Swarm 게이트웨이에서 다음과 같은 해시를 통해 옥션 디앱을 찾을 수 있습니다.  
    +  https://swarm-gateways.net/bzz:/ab164cf37dc10647e43a233486cdeffa8334b026e32a480dd9cbd020c12d4581  
    
또는 DApp 브라우저 또는 Swarm 게이트웨이에서 Swarm URL을 검색하여 다음을 수행합니다:      
    +  bzz://ab164cf37dc10647e43a233486cdeffa8334b026e32a480dd9cbd020c12d4581  
이제 이름에 이 이름을 붙였으므로 훨씬 쉬워졌습니다:  
    + http://swarm-gateways.net/bzz:/auction.ethereumbook.eth/  
또한 ENS 호환 지갑 또는 DApp 브라우저(예: 미스트)에서 "auction.ethereumbook.eth"를 검색하여 찾을 수 있습니다.  
    
# From App to DApp
지난 여러 섹션에 걸쳐 점차 분산형 애플리케이션을 구축해 왔습니다. ERC721 증서에 대한 경매를 진행하기 위해 한 쌍의 스마트 계약으로 시작했습니다. 이 계약들은 지배계정이나 특권계정을 두지 않도록 설계되었으며, 따라서 이들의 운영은 진정으로 분산된다. 우리는 자바스크립트로 구현된 프런트엔드를 추가했는데, 이 프런트엔드는 우리의 디앱에 편리하고 사용자 친화적인 인터페이스를 제공한다. 경매 디앱은 분산형 스토리지 시스템인 Swarm을 사용하여 이미지와 같은 애플리케이션 자원을 저장합니다. 디앱은 또한 중앙 서버 없이 각 경매에 대해 암호화된 채팅방을 제공하기 위해 분산형 통신 프로토콜인 위스퍼를 사용한다.

우리는 전체 프런트엔드를 Swarm에 업로드하여 DApp이 파일을 제공하는 웹 서버에 의존하지 않도록 했습니다. 마지막으로 ENS를 사용하여 DApp의 이름을 할당하고 프런트엔드의 스웜 해시에 연결하여 사용자가 간단하고 기억하기 쉬운 이름으로 액세스할 수 있도록 했다.

이러한 각 단계를 통해 애플리케이션의 분산화가 증가했습니다. 최종 결과는 권한의 중심 지점이나 실패의 중심 지점이 없고 "웹3" 비전을 표현하는 DApp이다.

옥션 디앱 아키텍처는 옥션 디앱의 완전한 아키텍처를 보여줍니다.  
![architecture](https://github.com/ethereumbook/ethereumbook/raw/develop/images/auction_dapp_final_architecture.png)  

# Conclusions
가장 초기 설계부터 설립자들이 표현한 것처럼 분산형 애플리케이션은 이더리움 비전의 정점이다. 오늘날 많은 애플리케이션들이 스스로를 "DApps"라고 부르지만, 대부분은 완전히 분산되어 있지 않습니다. 그러나 거의 완전히 분산된 애플리케이션을 구축하는 것은 이미 가능합니다. 시간이 지남에 따라 기술이 더욱 발전함에 따라 점점 더 많은 애플리케이션을 분산시킬 수 있게 되어 보다 탄력적이고, 검열에 강하며, 자유로운 웹을 구축할 수 있게 되었습니다.  
