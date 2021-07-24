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





## Class Inheritance
## Inline Assembly
## Function Overloading
## Variable Typecasting
## Infinite Loop

# Decorators

# Function and Variable Ordering
# Compilation
## Protecting Against Overflow Errors at the Compiler Level
# Reading and Writing Data

# Conclusions
