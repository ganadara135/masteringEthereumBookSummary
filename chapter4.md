# Cryptography
암호기술은 이더리움의 기반 기술임으  
암호기술은 비밀번호를 노출하지 않고 비밀작성문으로 증명할 수 있고(디지털서명)  
데이터 인증을 증명할 수 있음(디지털 핑커프린트-해시)  
현재까지는 이더리움 프로토콜은 노드간 통신에 암호화를 사용하지 않음  
따라서 누구나 데이터를 볼 수 있으므로 컨센서스나 상태 변화를 증명할 수 있음  
향후에 영지식증명과 동형암호 기술을 적용할 예정임  
본 챕터에서는 공개키 암호기술을 알아본다.  

## Keys and Addresses
이더리움은 두가지 형태의 계정이 있음(EOA and Contract)  
EOA 에 의한 이더 소유권은 비밀키, 이더리움 주소, 디지털 서명으로 만들어 짐  
비밀키는 이더리움과 사용자간의 모든 상호작용에 핵심임  
계정 주소는 비밀키로 부터 직접적으로 추출됨  
비밀키가 고유한 이더리움 주소를 결정함  
오직 계정 주소와 디지털 서명만 전송되고 이더리움 시스템에 저장됨  
이더리움 트랜잭션은 블록체인에 담기위해서는 유효한 디지털 서명을 요구함  
공개키는 은행 계정 넘버와 같고, 비밀키는 핀번호와 같음  
비밀키는 자체는 암호화된 파일로 관리되거나 이더리움 지갑 프로그램으로 관리함  

## Pulbic Key Cryptography and Cryptocurrency
(공개키 암호기술과 암호화폐)

공개키 암호기술은 현대 정보 보안에 핵심(비대칭 암호기술)  
1970년에 키 교환 프로토콜을 목적으로 등장함  
공개키 암호기술은 정보를 보호하기 위해서 유일한 키를 사용  
이런 키들은 특별한 수학 함수를 사용함  
고유키를 얻는계산은 쉬우나, 역으로 계산하기는 힘듦  
이런 함수를 기반으로 예측할 수 없는 디지털 서명이나 디지털 시크릿을 만듦  
두 개의 아주 큰 소수를 곱하기는 쉬우나, 역으로 두 소수를 찾아 내기는 힘듦  

Trapdoor 함수는 위에 특별한 경우로서 두 개의 소수 중에 하나를 알면 나머지를 금방 구할 수 있음 

Elliptic curve 함수는 Trapdoor(옆길, 약점) 가 없는 함수 이다  
소수의 나머지 곱셈은 쉽우나,  나누기는 현실적으로 불가함  
이런 경우를 이산 대수라고 함  

디지털서명은 어떤 평문이 든지 사인을 통해서 만들 수 있음  
이더리움 트랜잭션 자체가 메시지로서 사용된다  
비밀키가 타원 곡선 암호기술을 사용해서 트랜잭션에 디지털 서명을 담는다  
스마트컨트랙트나 잔고에서 이더가 움직이려면 
이더리움 네트워크에 트랜잭션을 보낸다  
이때 이더리움 주소와 연관된 비밀키로 만들어진 디지털 서명도 함께 보내짐  
트랜젹션 정보와 이더리움 주소를 디지털 서명과 매칭하여서  
누구나 트랜잭션을 검증할 수 있음  
검증 과정엔 비밀키가 전혀 필요 없음  

# Private Keys(비밀키, 개인키)
비밀키는 랜덤으로 만들어진 숫자이다  
비밀키는 디지털 서명을 만들기 위해서 사용됨  

## Generating a Private Key from a Random Number
(랜덤수로 비밀키 만들기)  

키를 만드는 것의 핵심은 엔트로피(랜덤성)의 안전한 원천을 찾는 것임  

비밀키는 2^256 (78-digit number) 1.158 x 10^77  범위의 숫자를 사용    
위 범위에서 비밀키는 정확히 첫 38 digits 을 사용함  
비밀키 생성 순서
- 256-bit number 랜덤하게 선택  
- 선택한 수가 유효범위인지 체크  
- Keccak-256 or SHA-256 해시 함수 사용  
- 위 함수는 문자열을 넣으면 256-bit 해시 숫자값 나옴  
- 위 결과가 유효범위만 비밀키로 사용, 아니면 위 결과 반복  

비밀키 생성 과정은 오프라인 상태에서 진행됨  
아래 문자가 비밀키 예제임(64 Hexadecimal digits, 각 4비트씩 풀면 256 비트임)
```
f8f8a2f43c8376ccb0871305060d7b27b0554d2cc72bccf41b2705608452f315
```

# Public Keys
이더리움 공개키는 Elliptic curve 의 점이다.  
Elliptic curve 방정식을 만족하는 x, y 좌표의 집합임  
이더리움 공개키는 두 숫자 집단이다  
이 숫자들은 오직 한 방향으로만 움직이는 계산식을 통해서 비밀키로 부터 만들어짐  
비밀키로부터 공개키를 연산하기는 쉽지만, 그 반대는 어려움  
공개키는 elliptic curve 곱셈에 비밀키를 사용해서 만들어 짐  
```
K = k * G
```
k 는 비밀키이고, G 는 상수 포인트(생성 포인트), K 는 공개키  
 *  는 elliptic curve 곰셈 연산자 임  
elliptic curve 곰셈은 표준 곰셈과 다르다  
단순히 표준 곱셈의 기능적 속성만 공유함  
elliptic curve 역 연산은 이산대수(discrete logarithm) 찾는 것과 같음  

elliptic curve 산수는 정규 정수 산술과 다르다
K 포인트를 얻기위해서 k 정수를 포인트 G에 곱할 수 있으나, 나눌 수 없음  
따라서 비밀키 k 를 얻기 위해서 포인트 G 에 의해 공개키 K 를 나눌 수 없음  
이것이 단방향 함수임  
곱셈은 한 방향으로 쉽계 할 수 있고, 나눗셈 역 방향은 불가 함  


## Elliptic Curve Cryptography 설명
elliptic curve 암호기술은 이산대수 문제임(discrete logarithm)  
elliptic curve 포인트 위에서 덧셈과 곱셈으로 표현됨  
![elliptic curve](https://github.com/ethereumbook/ethereumbook/raw/develop/images/simple_elliptic_curve.png)  

이더리움은 특별한 elliptic curve 와 수학 상수 집합을 사용함  
NIST 에서 표준화한 secp256k1 을 사용  
secp256k1 curve 는 아래 방정식으로 정의됨  

y 2 = ( x 3 + 7 ) over ( 𝔽 p )  
or:

y 2 mod p = ( x 3 + 7 ) mod p

The mod p (modulo prime number p) indicates that this curve is over a finite field of prime order p, also written as \(\( \mathbb{F}_p \)\), where p = 2256 – 232 – 29 – 28 – 27 – 26 – 24 – 1, which is a very large prime number.  

실수 위에서가 아니라 소수 위수의 유한 필드 위에서 해당 곡선이 정의 됨  
이것은 2차원 위에 뿌려진 도트 패턴 같아서 시각화하기가 어렵다.  
하지만 수학적으로는 실수 위에 elliptic curve 와 같다  

아래 그림은 소수 위수 17 에 대한 도면임  
![Elliptic curve cryptography: visualizing an elliptic curve over F(p), with p=17
](https://github.com/ethereumbook/ethereumbook/raw/develop/images/ec_over_small_prime_field.png)  

아래는 포인트 Q 임, secp256k1 curve 위에서 포인트 좌표(x, y) 의해 만들어짐  
```
Q =
(49790390825249384486033144355916864607616083520101638681403973749255924539515,
59574132161899900045862086493921015780032175291755807399284007721050341297360)
```

## Elliptic Curve Arithmetic Operations
Elliptic curve 위에 P1, P2  포인트가 있으면, P3 도 P3 = P1 + P2 정의 가능  
기하학적으로 P3 는 P1 과 P2를 사이에 라인을 그려서 계산가능  
이 라인은 정확하게 한쪽의 덥셈 장소를 곡선을 교차함  

[타원곡선 연산 설명자료 링크](https://m.blog.naver.com/aepkoreanet/221178375642)

## Generating a Public Key
Private-Key k :   P 보다 적은 소수(Prime)로, 난수생성기로 생성합니다.

Public-Key K :  “Q(x , y) = k x G(x0 , y0)” 즉 타원곡선의 “더하기 연산”으로 만들어 집니다.

“k x G” 란 “G를 k번 더한 값”을 의미 합니다( K = G + G + …….G + G +G )

G는 이미 알려져 있고, K는 키 생성후 공개되어 집니다. 하지만, G 와 K를 안다고 해서, k 값을 유추해 내기가 굉장히 어렵습니다. 이것을 ECDLP(Elliptic Curve Discrete Logarithm Problem)라고 부르며, 이러한 속성으로 인해 공개키 암호기술로 사용되어지게 되었습니다.

##### G 부분을 예측할 수 없는 난수를 넣어 줌으로서 역연산을 방지

공개키 계산에 비밀키 적용 예
```
K = f8f8a2f43c8376ccb0871305060d7b27b0554d2cc72bccf41b2705608452f315 * G
```

암호기술 라이브러리에서는 아래와 같은 함수형식으로 값을 삽입  
x = k;   y = G
```
K = (x, y)
```

```
x = 6e145ccef1033dea239875dd00dfb4fee6e3348b84985c92f103444683bae07b
y = 83b5c38e5e2b0c8529d7fa3f64d46daa1ece2d9ac14cab9477d042c84c32ccd0
```

이더리움에서 공개키는 130 hexadecimal 문자열임(65 바이트)  
elliptic curve 위에서 포인트를 구분할 수 있는 4가지 가능한 접미사 있음  

[Serialized EC public key prefixed]
| Prefix | Meaming | Length(bytes) |  
|---|---|---|
| 0x00 | Point at infinity | 1 |  
| 0x04 | Uncompressed point | 65 |
| 0x02 | Compressed point with even y | 33 |
| 0x03 | Compressed point with odd y | 33 |

이더리움은 uncompressed public keys 만 사용 04(hex)  
공개키는 x , y 좌표를 연결함 아래 처럼  
```
04 + x-coordinate (32 bytes/64 hex) + y-coordinate (32 bytes/64 hex)
```
따라서 최종 모습은 아래와 같음  
```
046e145ccef1033dea239875dd00dfb4fee6e3348b84985c92f103444683bae07b83b5c38e5e2b0c8529d7fa3f64d46daa1ece2d9ac14cab9477d042c84c32ccd0
```

## Elliptic Curve Libraries
- OpenSSL
- libsecp256k1


## Cryptographic Hash Functions
이더리움 공개키를 주소로 바꾸는데 사용  
데이터 증명을 위한 디지털 핑거프린트 만드는데 사용  
임의 크기의 데이터를 고정된 크기의 데이터로 만들어줌  
입력하는 데이터를 pre-image, 또는 메시지라고 하고  
출력 데이터를 해시 라고 함  

암호 해시 함수는 단방향 함수이다  
무한정 대입만으로 역추적할 수 있는 방법 밖에 없으나,  
그 가능성이 무한정에 가깝다.   
맞는 경우에는 해시 충돌이 발생했다고  
many to one 함수임  
충돌이 적을 수록 좋음 해시 함수 임  

#### 암호 해시 함수의 특징  
+ 결정론적 : 같은 입력 데이터는 같은 해시 결과로 나와야 함
+ 검증가능성 : 해시 메시지 게산이 효율적임(선형 복잡동)  
+ 비상호관련 : 추측가능하지 않게 
+ 비가역적 : 해시 값으로 원본 메시지 추적 불가  
+ 출돌 회피 : 한 해시값이 복수의 입력 값중에 나타나지 않아야 함  


사용영역
- 데이터 핑거프린팅
- 메시지 무결성(에러 복구)
- 작업증명
- 인증(패스워드 해싱 과 키 확장)
- 유사 랜덤 넘버 생성
- 메시지 전송
- 고유한 번호

#### 이더리움 암호 해시 함수 : Keccak-256
NIST 에서 만든 SHA-3 대신 이더리움 재단에서 Keccak 알고리즘을 구현함  
SHA-3 에는 난수 생성 알고리즘에 백도엉가 심어져 있음  

## Which Hash Function Am I Using?
공백 값을 해시 함수에 넣어 보면 알 수 있음  
```
Keccak256("") =
  c5d2460186f7233c927e7db2dcc703c0e500b653ca82273b7bfad8045d85a470

SHA3("") =
  a7ffc6f8bf1ed76651c14756a061d662f580ff4de43b49fa82d80a4b80f8434a  
```
향하우 EIP-59 규칙에 따라서 SHA-3 로 구현돼 있는 부분을 Keccak-256 으로 바꿀것임  


## Ethereum Addresses
이더리움 주소는 고유한 식별자임  
Keccak-256 단반향 해시 함수를 사용해서 공개키나 컨트랙트 주소로부터 만들어짐  

비밀키 k:   소문자  
```
  k  =  f8f8a2f43c8376ccb0871305060d7b27b0554d2cc72bccf41b2705608452f315  
 ```
 공개키 K ( x 와 y 좌표가 연결됨)  
 ```
   K = 6e145ccef1033dea239875dd00dfb4fee6e3348b84985c92f103444683bae07b83b5c38e5e     
```
[ 주의 팁 ]
| 팁 | 팁 |
|---|---|
| Note | 공개키가 주소 연산 시에 접미사 0x 로 시작하지 않음 |  

공개키 해시 연산을 위해서 Keccak-256 사용
```
Keccak256(K) = 2a5bc342ed616b5ba5732269001d3f1ef827552ae1114027bd3ecf1f086ba0f9
```

위 결과에서 마지막 20 bytes 를 이더리움 주소로 사용함  
```
001d3f1ef827552ae1114027bd3ecf1f086ba0f9
```
하지만 위 결과값을 평소에 사용한 이더리움 주소와 는 다를 것이다.  
아래가 이더리움의 일반적 형태임  
```
0x001d3f1ef827552ae1114027bd3ecf1f086ba0f9
```

## 이더리움 주소 포맷
이더리움 주소는 공개키를 Keccak-256 해시 결과값의 마지막 20 바이트로부터  
추출하는 헤사 숫자와 식별자임  
비트코인 주소는 사용자 미스 입력을 방지코자  
체크섬 내부 함수가 사용자 인터페이스에 포함돼 있음  
이더리움 주소는 체크섬 없는 원형의 해사값임  

이더리움 네임 서비스를 통해서 체크섬 기능을 제공하려고 했으나 계획보다 느림  

## Inter Exchange Client Address Protocol
ICAP 국제 은행 계좌 넘버(IBAN) 인코딩 프로토콜과 일부 친화적인 이더리움 주소 인코딩임  
ICAP 주소는 이더리움 주소를 인코드 할 수 있고  
이더리움 이름 레지스트리에 등록된 공통 이름도 인코드 가능함  

IBAN 은 은행 계좌번호를 구별하는 국제 표준임  
IBAN 은 중앙회돼 있고 강한 규제를 받음  
ICAP 은 비중앙회돼있고, 이더리움 주소 구현에 적합함  

IBAN 은 34 알파벳숫자형 문자열로 구성되고  
국가코드, 체크섬, 뱅크 계좌 번호 식별자로 구성됨  

ICAP 은 IBAN의 구조를 착용함  
"XE" 는 이더리움을 상징  
그리고 2 문자 체크섬과 계좌 식별자로 3가지가 가능함  
#### Direct 
- IBAN 과 친화적임
- Big-endian base 36 integer
- 30 알파벳숫자형 문자열
- 이더리움 주소의 155 least significant bits
- XE60HAMICDXSV5QXVJA7TJW47Q9CHWKJD (33 characters long)
#### Basic
- Direct 인코딩과 대부분 같고
- 31 문자열 길이만 다름
- IBAN 필드 변화와 비 친화적임
- XE18CHDJBPLTBCJ03FE9O2NS0BPOJVQCU2P (35 characters long).
#### Indirect
- 이름 레지스트리 제공자를 통해서 이더리움 주소와 연결된 식별자 인코딩함
- 16 알파벳숫자형을 사용
- 자산 식별자 ( ETH), 네임 서비스 (XREG)
- 9 문자, human-readable name (KITTYCATS)
- XE##ETHXREGKITTYCATS (20 characters long)
- 위에 ## 은 체크섬 문자로 바뀜

helpeth command-line 툴 설치
```
$ npm install -g helpth
```

아래처럼 ICAP 주수를 비밀키를 사용해서 생성함  
```
$ helpeth keyDetails \
  -p 0xf8f8a2f43c8376ccb0871305060d7b27b0554d2cc72bccf41b2705608452f315

Address: 0x001d3f1ef827552ae1114027bd3ecf1f086ba0f9
ICAP: XE60 HAMI CDXS V5QX VJA7 TJW4 7Q9C HWKJ D
Public key: 0x6e145ccef1033dea239875dd00dfb4fee6e3348b84985c92f103444683bae07b...
```

### Hex Encoding with Checksum in Capitalization (EIP-55)
EIP-55 은 백워드 친화적인 체크섬임  
이더리움 주소는 case-insensitive  
모든 지갑은 대소문자 구분없이 이더리움 주소를 처리하게 구현됨  

주소상의 알파벳형 문자를 대문자함으로써    
읽기나 타이핑 실수를 대응코자 주소 무결성 보호에 체크섬을 심는다  
대부분 지갑이 EIP-55 체크섬을 지원안함  
따라서 주소가 혼합 대문자를 포함하면 무시함  
하지만 지원하는 지갑은 정확도를 99.986% 유지함  

혼합 문자형 인코딩  
```
0x001d3f1ef827552ae1114027bd3ecf1f086ba0f9
```
EIP-55 혼합 대문자 체크섬 결과
```
0x001d3F1ef827552Ae1114027BD3ECF1f086bA0F9
```

위 두 결과의 차이는  
알파멧 (A-F) 문자만 헤사형 인코딩 알파벳만 대문자이고, 나머진 소문자  

EIP-55 구현은 소문자 헤사숫자형 주소의 Keccak-256 해시를 취함  


