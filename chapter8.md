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

## Modifiers
## Class Inheritance
## Inline Assembly
## Function Overloading
## Variable Typecasting
## Infinite Loop

# Decorators

# Function and Variable Ordering
# Compilation
# Protecting Against Overflow Errors at the Compiler Level
# Reading and Writing Data

# Conclusions
