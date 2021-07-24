# Smart Contracts and Vyper
Vyper 는 EVM을 위한 실험적이고 컨트랙트 중심 프로그래밍 언어이고,   
높은 수준의 추적가능성과 코딩 편의성을 제공함  
Vyper 의 주 목적은 개발자의 코딩 오류를 줄이느 것임  
본 챕터에서는 스마트컨트랙트의 공통적인 문제를 살펴보고   
Vyper 컨트랙트 프로그래밍 언어를 소개하고  
Solidity 와 비교해 본다  

# Vulnerabilities and Vyper 
1백만건의 스마트컨트랙트를 분석한 보고서에 따르면  
대부분의 컨트랙트가 심각한 취약점이 존재함  
보고서에 따르면 그 취약점은 3가지 카테고리로 나눠짐  

### Suicidal contracts
임의의 주소에 의해서 죽는 스마트컨트랙트
### Greedy contracts
이더를 사용할 수 없게되는 스마트컨트랙트
### Prodigal contracts
임의의 주소에서 의해서 이더가 빠져나가는 스마트컨트랙트

취약점이 잘못된 코드에 의해서 스마트컨트랙트에 나타난다  
이런 취약점이 의도적인지 아닌지는 논란거리지만  
원하지 않는 스마트컨트랙트 코드가 이더리움 사용자의 펀드에  
예기치 않은 손실을 야기할 수 있음  
Vyper는 안전 코딩을 쉽게하고 실수로 잘못된 코딩을 하는 것을 방지하게 설계됨  

# Comparison to Solidity
Vyper는 Solidity 주요 특징중 일부를 제거함으로써 코드의 안전성을 높였음  
따라서 Vyper 가 가지고 있는 특징을 이해하는 게 중요함  
본 섹션에서는 무슨 특징을 제거했는지 보여줄 것임  

## Modifiers
Solidity 에서 한정자를 사용해 봤을 것이다  
아래 예제는 한정자를 사용한 코드임  
onlyBy 가 한정자가 사용된 부분임  
```
function changeOwner(address _newOwner)
    public
    onlyBy(owner)
{
    owner = _newOwner;
}
```
위에 사용된 한정자의 의도는 오너쉽과 관련된 규칙을 강제하기 위해서 도입됨  
이 한정자는 changeOwner 함수를 대신해서 사전 체크를 수행하는 메커니즘을 수햄함  
```
modifier onlyBy(address _account)
{
    require(msg.sender == _account);
    _;
}
```
한정자는 여기에 보이는 예제처럼 단순히 체크를 수행하기 사용되지만은 않음  
한정자는 호출하는 문맥에서 스마트컨트랙트에 심각한 변화를 야기함  
아래의 예제처럼 한정자가 빈번히 사용된 예제를 보자  
```
enum Stages {
    SafeStage,
    DangerStage,
    FinalStage
}

uint public creationTime = now;
Stages public stage = Stages.SafeStage;

function nextStage() internal {
    stage = Stages(uint(stage) + 1);
}

modifier stageTimeConfirmation() {
    if (stage == Stages.SafeStage &&
                now >= creationTime + 10 days)
        nextStage();
    _;
}

function a()
    public
    stageTimeConfirmation
    // More code goes here
{
}
```
개발자는 그들 자신의 코드가 호출되는 모든 부분에서 체크를 항상 해야함  
하지만 어떤 상황에서는 개발자가 까먹을 수 있음  
(위 예제에서는 집중력 부족으로 시간 제한 또는 고갈)  
개발자가 큰 파일로 지나치거나  
계층적 함수 호출을 머리로만 추적하다가 또는  
스마트컨트랙트 변수를 상하태를 메모리로 전달하면서  

앞선 예제를 더 깊이 들여다 보자  
개발자가 public 함수 a 를 호출하는 코드를 쓴다고 가정하자  
개발자는 본 컨트랙트가 처음이고 누군가가 쓴 한정자를 사용하고 있음  
눈대중으로 stageTimeConfirmation 한정자는 컨트랙트 수명에 관해서   
단순 체크를 수행한다. 이것은 호출하는 함수에 관련해서 수행함  
개발자가 깨닫지 못하는 것은 한정자가 또다른 함수 nextStage 를 호출한다  
이 단순한 시나리오에서 퍼블릭 함수 a 를 호출하는 것이 SafeStage 에서  
DangerStage 로 스마트컨트랙트 상태 변수를 바꾸는 결과를 만든다  

Vyper 는 한정자를 모두 제거함  
Vyper 는 아래와 같은 방법을 도입함  
한정자를 assertions 과만 수행한다면, 인라인 체크를 수행하고  
함수의 일부로서 assert 하라  
스마트컨트랙트 상태를 변경하려면 해당 변화를 함수의 명확한 부분으로 해서 수행함  
이렇게하면 추적성과 가독성을 높인다  

## Class Inheritance
상속은 기존에 소프트웨어 라이브러리에 존재하던 행동, 특성, 기능을 재사용할 수 있도록 함  
Solidity 도 다형성 뿐만 아니라 복수 상속도 지원함  
이런 기능이 객체지향 프로그래밍에 주요 특징이기는 하나, Vyper는 지원하지 않음  
Vyper는 상속을 구현하기 위해서는 코더와 감시자가 여러 파일 사이를 점프하도록 함  
즉, 프로그램이 무엇을 하는 이해하도록 유도하기위해서 임  
Vyper는 또한 복수 상속은 코드를 너무 이해하기 어럽게 만들도록 디자인했음  

## Inline Assembly
Inline Assembly 는 개발자에게 EVM 로우 레벨에 접근할 수 있도록 했음  
즉, Solidity 단에서 EVM 명령어에 직접 접근해 프로그래밍할 수 있음  
아래 예제는 inline Assembly 샘플로써 메모리 위치 0x80 에 3을 더함  
```
 0x80 mload add 0x80 mstore
```
Vyper 는 가독성의 손실이 추가적인 능력보다 위선시 하여서 inline Assembly 을 지원 안함  

## Function Overloading
함수 재정의는 같음 이름의 함수에 여러 기능을 할 수 있도록 함  
어느 함수가 실행될지는 주어진 인자의 타입에 의존함  
아래의 예제를 보자  
```
function f(uint _in) public pure returns (uint out) {
    out = 1;
}

function f(uint _in, bytes32 _key) public pure returns (uint out) {
    out = 2;
}
```
다른 인자를 취하는 같음 이름의 복수 함수 정의를 갖는 것은 혼란을 야기할 수 있음  
따라서 Vyper 에서는 지원하지 않음  

## Variable Typecasting
두 가지 종류의 타입캐스팅이 있음 : 묵시적, 명시적  

묵시적 타입캐스팅은 컴파일 시에 작동함  
즉, 타입 전환이 의미적으로 건전하고 정보 손실이 없을때,  
컴파일러는 묵시적 전환을 수행함 : 타입 uint8 에서 unit16 으로 변수 전환시  
초기 버전의 Vyper는 묵시적 타입캐스팅을 지원했으나 지금음 중단함  

명시적 타입캐스트는 Solidity 에도 도입됨. 불행히 예측할 수 없는 결과를 만들어 냄  
예로서 uint32 를 더 작은 타입은 uint16 으로 전환시 상위 비트가 날아감.  아래는 그 예제임  
```
uint32 a = 0x12345678;
uint16 b = uint16(a);
// Variable b is 0x5678 now
```
Vyper 는 대신 명시적 캐스트를 위한 전환 함수를 도입함  
전환 함수는 일부분은 보여줌(라인 82 of convert.py)  
```
def convert(expr, context):
    output_type = expr.args[1].s
    if output_type in conversion_table:
        return conversion_table[output_type](expr, context)
    else:
        raise Exception("Conversion to {} is invalid.".format(output_type))
```
위 코드의 conversion_table 은 아래와 같이 생겼음  
```
conversion_table = {
    'int128': to_int128,
    'uint256': to_unint256,
    'decimal': to_decimal,
    'bytes32': to_bytes32,
}
```
개발자가 전환 기능 호출할때, 적합한 전환을 수행을 보장코자 conversion_table 을 참조한다  
즉, 개발자가 int128 을 전환 함수에 전달하면, to_int128 함수가 실행될 것임   to_int128 함수는 아래와 같음  
```
@signature(('int128', 'uint256', 'bytes32', 'bytes'), 'str_literal')
def to_int128(expr, args, kwargs, context):
    in_node = args[0]
    typ, len = get_type(in_node)
    if typ in ('int128', 'uint256', 'bytes32'):
        if in_node.typ.is_literal
            and not SizeLimits.MINNUM <= in_node.value <= SizeLimits.MAXNUM:
            raise InvalidLiteralException(
                "Number out of range: {}".format(in_node.value), expr
            )
        return LLLnode.from_list(
            ['clamp', ['mload', MemoryPositions.MINNUM], in_node,
            ['mload', MemoryPositions.MAXNUM]], typ=BaseType('int128'),
            pos=getpos(expr)
        )
    else:
        return byte_array_to_num(in_node, expr, 'int128')
```
위에 나타난것처럼 전환 과정은 정보 분실이 없도록 보장함   
정보 분실이 발생하면 예외를 던진다  
위 전환 코드는 묵시적 타입캐스팅에 의해 발생할 수 있는 비이상적 상황뿐만 아니라  
비트 잘내님을 방지한다  
묵시적 타입캐스팅에서 명시적 선택은 개발자가 모든 책임을 진다것을 의미한다  
이런 접근 방법이 부차적인 코드가 늘어나는 반면에 스마트컨트랙트에 안전성과 감시성을 높여줌  

## Infinite Loop
Solidity 에선 Gas 한계에 따라 메리트가 없지만 무한 루프를 사용할 수 있음  
무한 루프는 Gas 한계 조건에서 상위 경계를 설정할 수 없으므로 Gas 한계 공격을 받을 수 있음  
그래서 Vyper 는 아래와 같은 절차를 허용하지 않음  

#### while 문
Solidity 에서 while 문 사용 가능하나, Vyper 는 없음  
#### for 문 (끝이있는 반복문)
Vyper는 for 문은 지원함.  하지만 반복문의 상위 한계 값이 존재해야 함  
#### 재귀적 호출
재귀적 호출은 Soldity 에서 가능하나, Vyper는 지원 안함  

## Preconditions and Postconditions(사전-사후조건)
Vyper 는 사전조건, 사후조건, 상태 변환을 명시적으로 관리함  
이것은 코드량을 늘릴 수 있으나, 가독성과 안전성을 높여줌  
Vyper 에서 스마트컨트랙트를 작성할때 아래 3가지 포인트에 집중하라  
+ Conditions
무엇이 이더리움 상태 변수의 현재 상태나 조건인지?  
+ Effects
이 스마트컨트랙트 코드가 실행시에 현 상태 변수의 조건에서 무슨 효과를 가져올지?   
+ Interaction
위 2가지 조건이 완전히 처리된 이후에 이젠 코드를 실행할 차례임  
배포 전에 코드를 논리적으로 머리속에서 돌려보고  
모든 가능한 영구적 결과나 시나리오를 고려해서 시뮬레이션해보아라  
특히 다른 컨트랙트와의 상호작용도 고려해야함  

위의 3가지 포인트를 주의깊게 고려해서 철저히 문서화해야함  
그래야 코드의 설계와 가독성 감시성(추적성)을 높여줌  

# Decorators
아래의 꾸밈자들은 각 함수의 맨 앞에 사용되어야 함  
+ @private
본 꾸밈자는 컨트랙트 밖에서 함수를 접근할 수 없도록 함  

+ @public
본 꾸밈자는 함수를 외부에서 보이게도 실행할 수 있게도 함  
예로서 이더리움 지갑이 컨트랙트를 보려고할때 선언된 함수들을 볼 수 있다  

+ @constant
본 꾸밈자는 상태 변수를 변경하는 것을 허용하지 않음  
컴파일러가 전체 프로그램을 거부할 것이다  

+ @payable
본 꾸밈자가 있는 함수만 전송 기능이 가능하다  

Vyper는 명시적으로 꾸밈자 로직을 구현함  
Vyper 컴파일 과정에서 함수가 @payable 과 @constant 꾸밈자를 둘다 가지고 있다면 거부한다  
이것은 전송하는 함수가 상태 업데이트를 하므로 @constant 할 수 없다는 것은 자명하다  
또한, 모든 Vyper 함수는 @public 하거나 @private 해야함  

# Function and Variable Ordering
모든 개발젹인 Vyper 스마트 컨트랙트는 오직 한 개의 Vyper 파일로 구성됨  
따라서 주어진 Vyper 스마트 컨트랙트 코드는 한 곳에 존재한다, 즉 함수, 변수, 기타 등등.  
Vyper 는 각 스마트컨트랙트 함수와 변수 선언이 특정한 순으로 작성되기를 요구함  
하지만, Solidity 는 이런 요구사항이 없음. 아래는 Solidity 예제임   
```
pragma solidity ^0.4.0;

contract ordering {

    function topFunction()
    external
    returns (bool) {
        initiatizedBelowTopFunction = this.lowerFunction();
        return initiatizedBelowTopFunction;
    }

    bool initiatizedBelowTopFunction;
    bool lowerFunctionVar;

    function lowerFunction()
    external
    returns (bool) {
        lowerFunctionVar = true;
        return lowerFunctionVar;
    }

}
```
위 예에서 topFunction 을 호출하는 함수가 또다른 함수 lowerFunction 을 호출한다  
topFunction 또한 initiatizedBelowTopFunction 호출하는 한 변수에 값을 할당하고 있음  
위와 같이 Solidity 는 실행하는 코드에 의해서 호출되기 전에 함수와 변수가 선언되기를 요구하지 않음  

Vyper 순서 요구는 새로운 것이 아님: 사실 순서 요구는 Python 프로그래밍에서도 존재함  
Vyper 에 의해서 요구되는 순서는 직관적이로 논리적임. 아래 예를 보자  
```
# Declare a variable called theBool
theBool: public(bool)

# Declare a function called topFunction
@public
def topFunction() -> bool:
    # Assign a value to the already declared function called theBool
    self.theBool = True
    return self.theBool

# Declare a function called lowerFunction
@public
def lowerFunction():
    # Call the already declared function called topFunction
    assert self.topFunction()
```
어떻게 theBool 변수와 topFunction 함수가 호출되고 값이 할당되기전에 각각 호출되기전에 선언되는지 주의해서 보자  
theBool 이 topFunction 아래와 선언되거나, topFunction 이 lowerFunction 아래에 선언되면,  
위 컨트랙트는 컴파일되지 않는다  

# Compilation
Vyper 는 온라인 코드 에디터와 컴파일러가 있음  
스마트컨트랙트를 바이트코드, ABI, LLL 로 웹브라우저에서 변환해줌  
Vyper 온라인 컴파일러는 샘플 코드 같이 먼저 작성된 스마트컨트랙트가 많음   
또한 오직 한가지 버전의 컴파일러 소프트웨어를 제공하고 정기적으로 업데이트 됨  
Etherscan 은 온라인 Vyper 컴파일러가 컴파일러 버전을 선택할 수 있게 지원함  
또한 Remix 도 Vyper 플러그인을 환경설정 탭에서 지원함  
| | |  
|--|--|
|Note| Vyper 의 컨트랙트는 전역 변수(Global variables) 로 선언되어야 함. 아래 예야 같이 ERC20 변수를 선언시에 아래와 같다   token: address(ERC20)  |

command line 방식으로 컨트랙트를 컴파일할 수 있음  
Vyper 컨트랙트는 .vy 확장자명으로 단일 파일로 저정됨  
Vyper 가 설치되면 아래와 같이 컴파일 할 수 있음  
```
vyper ~/hello_world.vy
```
사람이 읽을 수 있는 ABI 기술문서로(JSON format) 아래와 같은 명령어 얻을 수 있음  
```
vyper -f json ~/hello_world.v.py
```

## Protecting Against Overflow Errors at the Compiler Level
오버플로우 에러는 실수 값을 처리할 때 치명적인 결과를 초래함  
"transaction from mid-April 2018" 사건이 이런 문제가 얼마나 심각한 결과를 초래햐난 보여주는 예임  
오버플로우 에러로 57,896,044,618,658,100,000,000,000,000,000,000,000,000, 000,000,000,000,000,000 BEC tokens.  
우와 같은 토큰이 BeautyChain ERC20 토큰 컨트랙트가 만들어짐(BecToken.sol)  
Solidity 개발자는 SafeMath 와 Mythril OSS 같은 라이브러리를 적극 사용해야함  
하지만 강제 사항이 아니므로 개발자들이 안 따를 여지가 있음  

Vyper 는 내장된 오버플로우 방지 기능이 있음  
위 기능은 2가지 갈래로 접근해서 구현되었음  
첫째 Vyper 는 정수형 산술을 위한 반드시 필요한 예외를 가지고 있는 SafeMath 유사한 기능을 제공한다  
둘째 Vyper 는 죔쇠(Clamps)를 사용해서 언제든 문자형 상수가 로딩되거나, 값이 함수에 전달되거나 변수가 할당될대 오버플로우 방지 기능을 적용함  
Clamps 는 로우 레벨의 Lisp 같은 언어 컴파일러로 구현되었음   
Vyper 컴파일러는 EVM 바이트코드가 아닌 LLL 을 만들어줌  

# Reading and Writing Data
데이터를 저장하고, 읽고, 수정하는 작업은 상당히 비용적인 작업임  
하지만 대부분의 스마트 컨트랙트에서는 필수적인 기능임  
스마트 컨트랙트는 2 장소에 데이터를 쓸 수 있음
+ Global state
스마트 컨트랙트에서 상태 변수는 이더리움 전역 상태 트라이(trie)에 저장됨   
스마트컨트랙트가 특정 컨트랙트의 주소와 관련된 데이터를 오직 저장하고 읽고, 수정할 수 있다.  
스마트 컨트랙트는 다른 스마트 컨트랙트에 읽고 쓸수 없음  

+ Logs
스마트 컨트랙트는 또한 로그 이벤트를 통해서 이더리움 체인 데이터에 쓸 수 있음  
Vyper 가 초기에 이런 이벤트를 선언하기 위해서 __log__ 문법을 적용했으나,  
업데이트가 Solidity 오리지널 문법과 함께 이벤트 선언이 더욱 나타냄  
(Solidity 의 이벤트 선언 방식과 신택스가 같아졌다는 뜻임)

스마트 컨트랙트가 이더리움 체인 데이터게 로그 이벤트를 통해서 쓸 수 있는 반면에  
스마트 컨트랙트는 그들이 만든 온체인 로그 이벤트를 읽을 수 없음  
그럼에도 불구하고 이더리움 체인 데이터에 로그 이벤트를 통해서 쓰는 잇점은  
로그를 퍼블릭 체인에서 라이트 클라이언트들이 찾거나 읽을 수 있다는 것임  
예로서 채굴된 블록에서 logsBoom 값은 로그 이벤트가 존재하는지 아닌지를 나타낸다  
일단 로그 이벤트가 존재가 확정되면, 로그 데이터는 주어진 트랜잭션 영수증으로 부터 얻을 수 있음  

# Conclusions
Vyper 는 강력하고 흥미있는 새로운 컨트랙트 중심의 프로그래밍 언어임  
설계의 초점은 유연성을 포기하면서 정확성에 치중하였음  
이것이 더 낫은 스마트 컨트랙을 작성하고 심각한 취약점을 방지할 수 있게 한다  
다음 절에서 스마트 컨트랙트의 보안 이슈을 더 자세히 알아볼 것임  
