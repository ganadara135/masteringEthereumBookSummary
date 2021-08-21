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

## History of Ethereum Name Services

## The ENS Specification
## Bottom Layer: Name Owners and Resolvers
## The Namehash algorithm
## How to choose a valid name
### Root node ownership
### Resolvers

## Middle Layer: The .eth Nodes
### Vickrey auctions
## Top Layer: The Deeds
### Registering a Name
![Timeline](https://github.com/ethereumbook/ethereumbook/raw/develop/images/ens-flow.png)  

![mycrypto.com](https://github.com/ethereumbook/ethereumbook/raw/develop/images/ens-checkname.png)  

![StartAuction](https://github.com/ethereumbook/ethereumbook/raw/develop/images/ens-auction.png)  

![ENSname](https://github.com/ethereumbook/ethereumbook/raw/develop/images/ens-bid.png)  

![metamask](https://github.com/ethereumbook/ethereumbook/raw/develop/images/ens-metamask-bid.png)  

# Managing Your ENS Name
![eth](https://github.com/ethereumbook/ethereumbook/raw/develop/images/ens-manager.png)  

## Creating an ENS subdomain
![manager](https://github.com/ethereumbook/ethereumbook/raw/develop/images/ens_manager_add_subdomain.png)  

## ENS Resolvers

![resolver](https://github.com/ethereumbook/ethereumbook/raw/develop/images/ens-manager-set-default-resolver.png)  

## Resolving a Name to a Swarm Hash (Content)

![set](https://github.com/ethereumbook/ethereumbook/raw/develop/images/ens-manager-set-content.png)  

# From App to DApp

![architecture](https://github.com/ethereumbook/ethereumbook/raw/develop/images/auction_dapp_final_architecture.png)  

# Conclusions

