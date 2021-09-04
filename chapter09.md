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
CALL 및 DELEGECALL 오코드는 Ethereum 개발자가 코드를 모듈화할 수 있도록 하는 데 유용합니다.   
계약에 대한 표준 외부 메시지 호출은 CALL opcode에 의해 처리되며, 여기서 코드는 외부 계약/함수의 컨텍스트에서 실행됩니다.   
대상 주소에서 실행되는 코드가 호출 계약의 컨텍스트에서 실행되고 msg.sender 및 msg.value가 변경되지 않는다는 점을 제외하면 DELETECALL opcode는 거의 동일합니다.   
이 기능은 라이브러리 구현을 가능하게 하여 개발자가 재사용 가능한 코드를 한 번 배포한 후 향후 계약에서 호출할 수 있도록 합니다.  

이 두 오피코드 간의 차이는 간단하고 직관적이지만 DELADECALL을 사용하면 예기치 않은 코드가 실행될 수 있습니다.  

자세한 내용은 Loi를 참조하십시오.이 주제와 Solidity 문서에 대한 Luu의 Ethereum Stack Exchange 질문입니다.  

## The Vulnerability
DELEGECALL의 컨텍스트 보존 특성으로 인해 취약성이 없는 사용자 지정 라이브러리를 구축하는 것은 생각보다 쉽지 않습니다.   
라이브러리의 코드 자체는 안전하고 취약성이 없을 수 있지만 다른 응용 프로그램의 컨텍스트에서 실행될 경우 새로운 취약성이 발생할 수 있습니다. 피보나찌 숫자를 사용한 꽤 복잡한 예를 보자.

FibonacchiLib.sol의 라이브러리를 고려하십시오.   
이 라이브러리는 Fibonacchi 시퀀스와 유사한 형식의 시퀀스를 생성할 수 있습니다(참고: 이 코드는 https://bit.ly/2에서 수정되었습니다) 

Example 6. FibonacciLib.sol  
```
// library contract - calculates Fibonacci-like numbers
contract FibonacciLib {
    // initializing the standard Fibonacci sequence
    uint public start;
    uint public calculatedFibNumber;

    // modify the zeroth number in the sequence
    function setStart(uint _start) public {
        start = _start;
    }

    function setFibonacci(uint n) public {
        calculatedFibNumber = fibonacci(n);
    }

    function fibonacci(uint n) internal returns (uint) {
        if (n == 0) return start;
        else if (n == 1) return start + 1;
        else return fibonacci(n - 1) + fibonacci(n - 2);
    }
}
```
이 라이브러리는 시퀀스에서 n번째 피보나치 숫자를 생성할 수 있는 함수를 제공합니다.   
사용자는 이 새 시퀀스에서 시퀀스의 시작 번호(시작)를 변경하고 n번째 Fibonacci 유사 숫자를 계산할 수 있습니다.

이제 FibonacciBalance.sol에 나와 있는 이 라이브러리를 활용하는 계약을 고려해 보겠습니다.  
Example 7. FibonacciBalance.sol  
```
contract FibonacciBalance {

    address public fibonacciLibrary;
    // the current Fibonacci number to withdraw
    uint public calculatedFibNumber;
    // the starting Fibonacci sequence number
    uint public start = 3;
    uint public withdrawalCounter;
    // the Fibonancci function selector
    bytes4 constant fibSig = bytes4(sha3("setFibonacci(uint256)"));

    // constructor - loads the contract with ether
    constructor(address _fibonacciLibrary) external payable {
        fibonacciLibrary = _fibonacciLibrary;
    }

    function withdraw() {
        withdrawalCounter += 1;
        // calculate the Fibonacci number for the current withdrawal user-
        // this sets calculatedFibNumber
        require(fibonacciLibrary.delegatecall(fibSig, withdrawalCounter));
        msg.sender.transfer(calculatedFibNumber * 1 ether);
    }

    // allow users to call Fibonacci library functions
    function() public {
        require(fibonacciLibrary.delegatecall(msg.data));
    }
}
```
이 계약은 참가자가 계약에서 에테르를 인출할 수 있도록 합니다. 즉, 첫 번째 참가자는 1 에테르를 받고, 두 번째 참가자는 1 에테르를 받고, 세 번째 참가자는 2 에테르를 받고, 네 번째 참가자는 3 에테르를 받고, 다섯 번째 5 등입니다. 인출되는 피보나치 번호보다 작습니다.)

이 계약에는 설명이 필요할 수 있는 여러 요소가 있습니다. 첫째, fibSig라는 재미있게 생긴 변수가 있습니다. 여기에는 'setFibonacci(uint256)' 문자열의 Keccak-256(SHA-3) 해시의 처음 4바이트가 저장됩니다. 이를 함수 선택기라고 하며, 호출 데이터에 입력되어 스마트 계약의 호출 기능을 지정합니다.   
이 기능은 라인 21의 대리자 통화 기능에 사용되어 피보나치(uint256) 기능을 실행하도록 지정합니다. delegatecall의 두 번째 인수는 함수에 전달할 매개 변수입니다.   
둘째, 우리는 FibonacciLib 라이브러리의 주소가 생성자에서 올바르게 참조되고 있다고 가정한다(외부 계약 참조는 이러한 종류의 계약 참조 초기화와 관련된 몇 가지 잠재적 취약점을 논의한다).  
이 계약서에서 오류를 발견할 수 있습니까? 이 계약을 배포하고 에테르를 채운 다음 전화를 끊는다면, 그것은 되돌아갈 것입니다.

상태 변수 시작이 라이브러리와 주 호출 계약에서 모두 사용됨을 알 수 있습니다. 라이브러리 계약에서 시작은 Fibonacci 시퀀스의 시작을 지정하는 데 사용되며 호출 계약에서는 3으로 설정된 반면 0으로 설정됩니다. 또한 FibonacciBalance 계약의 폴백 기능을 통해 모든 호출을 라이브러리 계약으로 전달하여 라이브러리 계약의 setStart 기능을 호출할 수 있습니다. 계약 상태를 보존한다는 점을 상기하면, 이 기능을 통해 현지 Fibonnacci Balance 계약에서 시작 변수의 상태를 변경할 수 있습니다. 그러면 계산된 FibNumber는 시작 변수에 따라 달라지기 때문에(라이브러리 계약에서 보듯이) 더 많은 에테르를 인출할 수 있습니다. 실제로 setStart 함수는 FibonacciBalance 계약에서 시작 변수를 수정하지 않으며 수정할 수도 없습니다. 이 계약의 기본 취약성은 단순히 시작 변수를 수정하는 것보다 훨씬 더 심각합니다.  

실제 문제에 대해 논의하기 전에 상태 변수가 실제로 계약에 어떻게 저장되는지 간단히 살펴보겠습니다. 상태 또는 스토리지 변수(개별 트랜잭션에 걸쳐 지속되는 변수)는 계약에 소개된 대로 순차적으로 슬롯에 배치됩니다. (여기에 몇 가지 복잡한 사항이 있습니다. 자세한 내용은 Solidity 문서를 참조하십시오.)

예를 들어, 도서관 계약서를 살펴보자. 시작 및 계산된 FibNumber의 두 가지 상태 변수가 있습니다. 첫 번째 변수, 시작은 [0] 슬롯(즉, 첫 번째 슬롯)의 계약 저장소에 저장됩니다. 두 번째 변수, 계산된 FibNumber는 사용 가능한 다음 저장소 슬롯[1]에 배치됩니다. setStart 함수는 입력을 받아 입력을 무엇이든 start로 설정합니다. 따라서 이 함수는 슬롯[0]을 setStart 함수에 제공하는 모든 입력으로 설정합니다. 마찬가지로 설정된 Fibonacci 함수는 계산된 FibNumber를 fibonacci(n)의 결과로 설정합니다. 이는 저장 슬롯[1]을 fibonacci(n) 값으로 설정하기만 하면 됩니다.  

이제 피보나치 밸런스 계약서를 봅시다. 이제 저장 슬롯[0]은 피보나치 라이브러리 주소에 해당하고 슬롯[1]은 계산된 FibNumber에 해당합니다. 이 잘못된 매핑에서 취약성이 발생합니다. delegatecall은 계약 컨텍스트를 보존합니다. 이는 대리자 호출을 통해 실행되는 코드가 호출 계약의 상태(즉, 저장소)에 따라 작동함을 의미합니다.

21번 라인에서 인출 시 fibonaciLibrary.delegatecall(fibSig, 인출 카운터)을 실행합니다. 이렇게 하면 setFibonacci 함수가 호출됩니다. 이 함수는 앞서 설명한 대로 현재 컨텍스트에서 계산된 FibNumber 스토리지 슬롯[1]을 수정합니다. 이는 예상대로입니다(즉, 실행 후 계산된 FibNumber가 수정됨). 그러나 FibonacciLib 계약의 시작 변수는 현재 계약의 fibonaciLib 주소인 저장소 슬롯[0]에 있습니다. 이는 피보나찌 함수가 예상치 못한 결과를 준다는 것을 의미합니다. 이는 현재 호출 컨텍스트에서 fibonacciLibrary 주소(흔히 unt로 해석될 때 상당히 큰)인 start(슬롯[0])를 참조하기 때문입니다. 따라서 인출 기능은 계산된 FibNumber가 반환되는 unt(fibonacci Library) 양의 에테르를 포함하지 않기 때문에 되돌아갈 가능성이 높습니다.  
더욱이 피보나치 밸런스 계약을 통해 사용자는 줄 26의 폴백 기능을 통해 모든 피보나치 라이브러리 기능을 호출할 수 있습니다. 앞에서 설명한 것처럼 setStart 기능도 포함되어 있습니다. 이 기능을 통해 누구나 스토리지 슬롯을 수정하거나 설정할 수 있습니다[0]. 이 경우 저장소 슬롯[0]은 피보나치 라이브러리 주소입니다. 따라서 공격자는 악의적인 계약을 만들고 주소를 unt로 변환한 다음 setStart(<attack_contract_address>,16)를 호출할 수 있습니다. 이렇게 하면 피보나치 라이브러리가 공격 계약의 주소로 변경됩니다. 그러면 사용자가 탈퇴나 폴백 기능을 호출할 때마다 fibonacchi Library의 실제 주소를 수정했기 때문에 악성 계약이 실행됩니다(계약 잔액 전체를 도용할 수 있음). 그러한 공격 계약의 예는 다음과 같다.  
```
contract Attack {
    uint storageSlot0; // corresponds to fibonacciLibrary
    uint storageSlot1; // corresponds to calculatedFibNumber

    // fallback - this will run if a specified function is not found
    function() public {
        storageSlot1 = 0; // we set calculatedFibNumber to 0, so if withdraw
        // is called we don't send out any ether
        <attacker_address>.transfer(this.balance); // we take all the ether
    }
 }
```
이 공격 계약은 스토리지 슬롯[1]을 변경하여 계산된 Fib 번호를 수정합니다. 원칙적으로 공격자는 선택한 다른 스토리지 슬롯을 수정하여 이 계약에 대한 모든 종류의 공격을 수행할 수 있습니다. 이러한 계약을 리믹스에 넣고 딜러 통화 기능을 통해 다양한 공격 계약 및 상태 변경을 실험하는 것이 좋습니다.

또한 Delegate 호출이 상태를 보존한다고 할 때 계약의 변수 이름이 아니라 해당 이름이 가리키는 실제 저장소 슬롯에 대해 이야기하는 것입니다. 이 예에서 볼 수 있듯이, 단순한 실수로 공격자가 전체 계약과 계약 에테르를 가로챌 수 있습니다.  

## Preventative Techniques
Solidity는 라이브러리 계약을 구현하기 위한 라이브러리 키워드를 제공합니다(자세한 내용은 문서 참조). 이렇게 하면 라이브러리 계약이 상태 비저장 상태이며 자체 파기할 수 없습니다. 라이브러리를 상태 비저장 상태로 강제로 지정하면 이 섹션에서 설명하는 스토리지 컨텍스트의 복잡성을 줄일 수 있습니다. 상태 비저장 라이브러리는 또한 공격자가 라이브러리의 코드에 종속된 계약에 영향을 미치기 위해 라이브러리 상태를 직접 수정하는 공격을 방지합니다. 일반적으로 DELEGECALL을 사용할 때는 라이브러리 계약과 호출 계약의 가능한 호출 컨텍스트에 주의하고 가능하면 상태 비저장 라이브러리를 빌드하십시오.  

## Real-World Example: Parity Multisig Wallet (Second Hack)
Second Parity Multisig Wallet 해킹은 의도된 컨텍스트에서 실행될 경우 올바르게 작성된 라이브러리 코드를 어떻게 이용할 수 있는지를 보여 주는 예입니다. "패리티 멀티시그 해킹" 등 이 해킹에 대한 여러 가지가지 설명이 있다. 다시" 및 "패리티 멀티시그 버그에 대한 심층 검토"를 참조하십시오.

이러한 참조에 추가하기 위해 악용된 계약에 대해 살펴보겠습니다. 도서관과 지갑 계약서는 GitHub에서 찾을 수 있습니다.

라이브러리 계약은 다음과 같습니다.  
```
contract WalletLibrary is WalletEvents {

  ...

  // throw unless the contract is not yet initialized.
  modifier only_uninitialized { if (m_numOwners > 0) throw; _; }

  // constructor - just pass on the owner array to multiowned and
  // the limit to daylimit
  function initWallet(address[] _owners, uint _required, uint _daylimit)
      only_uninitialized {
    initDaylimit(_daylimit);
    initMultiowned(_owners, _required);
  }

  // kills the contract sending everything to `_to`.
  function kill(address _to) onlymanyowners(sha3(msg.data)) external {
    suicide(_to);
  }

  ...

}
```
다음은 wallet 컨트랙트
```
contract Wallet is WalletEvents {

  ...

  // METHODS

  // gets called when no other function matches
  function() payable {
    // just being sent some cash?
    if (msg.value > 0)
      Deposit(msg.sender, msg.value);
    else if (msg.data.length > 0)
      _walletLibrary.delegatecall(msg.data);
  }

  ...

  // FIELDS
  address constant _walletLibrary =
    0xcafecafecafecafecafecafecafecafecafecafe;
}
```
Wallet 계약은 기본적으로 모든 통화를 Wallet Library 계약으로 위임 통화를 통해 전달합니다. 이 코드 조각의 상수 _walletLibrary 주소는 실제로 배포된 WalletLibrary 계약(0x863)의 자리 표시자 역할을 합니다.DF6BFA4469f3ead0bE8f9F2AAE51c91A907b4).

이러한 계약의 의도된 운영은 WalletLibrary 계약에 코드베이스와 주요 기능이 포함된 간단한 저비용 배포형 Wallet 계약을 갖는 것이었습니다. 유감스럽게도 Wallet Library 계약은 그 자체가 계약이며 자체 상태를 유지합니다. 이게 왜 문제인지 아시겠어요?

Wallet Library 계약서 자체에 전화를 걸 수 있습니다. 구체적으로 Wallet Library 계약이 초기화되어 소유가 될 수 있습니다. 실제로 한 사용자가 Wallet Library 계약의 initWallet 기능을 호출하여 라이브러리 계약의 소유자가 되는 등 이 작업을 수행했습니다. 같은 사용자가 이어서 kill 함수를 호출했습니다. 사용자가 도서관 계약서 소유자였기 때문에 수식어가 통과되고 도서관 계약이 자체 파기되었습니다. 존재하는 모든 Wallet 계약은 이 라이브러리 계약을 참조하고 이 참조를 변경하는 방법을 포함하지 않으므로 WalletLibrary 계약과 함께 ether 인출 기능을 포함한 모든 기능이 손실되었습니다. 그 결과, 이 유형의 모든 패리티 멀티시그 지갑의 모든 에테르가 즉시 분실되거나 영구적으로 복구할 수 없게 되었습니다.

# Default Visibilities
Solidity의 함수에는 호출 방법을 지정하는 가시성 지정자가 있습니다. 가시성은 어떤 기능을 이용자가 외부적으로 호출할 수 있는지, 다른 파생 계약에 의해서만 호출할 수 있는지, 내부적으로만 호출할 수 있는지 또는 외부적으로만 호출할 수 있는지를 결정한다. 가시성 지정자는 4가지이며, Solidity 문서에 자세히 설명되어 있습니다. 기능은 기본적으로 공개로 설정되므로 사용자가 외부에서 호출할 수 있습니다. 이제 가시성 지정자를 잘못 사용하면 스마트 계약에서 어떤 치명적인 취약점이 발생할 수 있는지 살펴보겠습니다.  

## The Vulnerability
함수의 기본 가시성은 공용이므로 가시성을 지정하지 않은 기능은 외부 사용자가 호출할 수 있습니다. 이 문제는 개발자가 사적인(또는 계약 자체 내에서만 호출 가능) 기능에 대한 가시성 지정자를 실수로 누락할 때 발생한다.

간단한 예를 빠르게 살펴보겠습니다.  
```
contract HashForEther {

    function withdrawWinnings() {
        // Winner if the last 8 hex characters of the address are 0
        require(uint32(msg.sender) == 0);
        _sendWinnings();
     }

     function _sendWinnings() {
         msg.sender.transfer(this.balance);
     }
}
```
이 간단한 계약은 주소 맞히기 현상금 게임 역할을 하도록 고안되었다. 계약 잔액을 확보하려면 사용자는 마지막 8개의 16진수 문자가 0인 Ethereum 주소를 생성해야 합니다. 일단 달성되면, 그들은 현상금을 받기 위해 당첨금 인출 기능을 호출할 수 있습니다.

안타깝게도 함수의 가시성이 지정되지 않았습니다. 특히 _sendWinnings 함수는 public(기본값)이므로 모든 주소가 이 함수를 호출하여 현상금을 훔칠 수 있습니다.  

## Preventative Techniques
모든 기능이 의도적으로 공개되더라도 항상 계약서에 가시성을 명시하는 것이 좋습니다. 최신 버전의 solc에서는 이 관행을 권장하기 위해 가시성이 설정되지 않은 기능에 대한 경고를 표시합니다.  

## Real-World Example: Parity Multisig Wallet (First Hack)
첫 번째 패리티 멀티시그 해킹에서는 약 3,100만 달러 상당의 에테르가 도난당했는데, 대부분 3개의 지갑에서 도난당했습니다. 하셉 쿠레시가 정확히 어떻게 했는지를 재점검해 주었다.

기본적으로 멀티시그 지갑은 핵심 기능이 포함된 라이브러리 계약을 호출하는 기본 Wallet 계약(Real-World 예제: 패리티 멀티시그 지갑(Second Hack))으로 구성됩니다. 라이브러리 계약에는 지갑을 초기화하기 위한 코드가 포함되어 있습니다(다음 토막글에서 볼 수 있음).  
```
contract WalletLibrary is WalletEvents {

  ...

  // METHODS

  ...

  // constructor is given number of sigs required to do protected
  // "onlymanyowners" transactions as well as the selection of addresses
  // capable of confirming them
  function initMultiowned(address[] _owners, uint _required) {
    m_numOwners = _owners.length + 1;
    m_owners[1] = uint(msg.sender);
    m_ownerIndex[uint(msg.sender)] = 1;
    for (uint i = 0; i < _owners.length; ++i)
    {
      m_owners[2 + i] = uint(_owners[i]);
      m_ownerIndex[uint(_owners[i])] = 2 + i;
    }
    m_required = _required;
  }

  ...

  // constructor - just pass on the owner array to multiowned and
  // the limit to daylimit
  function initWallet(address[] _owners, uint _required, uint _daylimit) {
    initDaylimit(_daylimit);
    initMultiowned(_owners, _required);
  }
}
```
두 기능 모두 가시성을 지정하지 않으므로 기본적으로 공개로 설정됩니다. initWallet 함수는 지갑의 생성자에서 호출되며, initMultiowned 함수에서 볼 수 있듯이 멀티시그 지갑의 소유자를 설정합니다. 이러한 기능이 실수로 공개되었기 때문에 공격자는 배포된 계약에서 이러한 기능을 호출하여 소유권을 공격자의 주소로 재설정할 수 있었습니다. 주인이 된 공격자는 에테르를 모두 빼냈다.  

# Entropy Illusion
Ethereum 블록체인의 모든 트랜잭션은 결정론적 상태 전환 작업입니다. 이는 모든 거래가 불확실성 없이 계산 가능한 방법으로 이더리움 생태계의 글로벌 상태를 수정한다는 것을 의미한다. 이것은 Ethereum에 엔트로피나 랜덤성의 원천이 없다는 것을 근본적으로 암시합니다. 분산 엔트로피(임의성)를 달성하는 것은 블로그 게시물 "Validator Ordering and Randomness in PoS"에서 Vitalik Buterin이 설명한 것처럼 RANDAO 또는 일련의 해시를 사용하는 등 많은 솔루션이 제안되어 온 잘 알려진 문제입니다.  

## The Vulnerability
이더리움 플랫폼에 구축된 첫 계약 중 일부는 도박에 기반을 두고 있었다. 근본적으로 도박은 불확실성(걸어야 할 것)이 필요한데, 이는 오히려 블록체인(결정론적 시스템)에 도박 시스템을 구축하는 것을 어렵게 만든다. 불확실성이 블록체인의 외부적 원천에서 오는 것이 분명합니다. 이것은 참가자 간의 베팅(예: 커밋-리빙 기법 참조)에 대해 가능하지만, "하우스"(블랙잭 또는 룰렛) 역할을 수행하는 계약을 구현하려면 훨씬 더 어렵습니다. 일반적인 함정은 미래의 블록 변수, 즉 해시, 타임스탬프, 블록 번호 또는 가스 한계와 같이 값을 아직 알 수 없는 트랜잭션 블록에 대한 정보가 포함된 변수를 사용하는 것입니다. 문제는 블록을 채굴하는 광부에 의해 통제된다는 점이며, 따라서 실제로 무작위적이지 않다는 것입니다. 예를 들어, 다음 블록 해시가 짝수로 끝나는 경우 검은색 숫자를 반환하는 로직이 포함된 룰렛 스마트 계약을 생각해 보십시오. 광부(또는 광부 풀)는 검은색에 100만 달러를 걸 수 있습니다. 다음 블록을 풀고 홀수로 끝나는 해시를 찾으면 블록 해시가 짝수인 솔루션을 찾을 때까지(블록 리워드와 수수료가 100만 달러 미만이라고 가정할 때) 블록을 게시하고 다른 블록을 마이닝할 수 없습니다. 마틴 스웬데가 자신의 훌륭한 블로그 게시물에서 보여주듯이 과거 또는 현재 변수를 사용하는 것은 더욱 파괴적일 수 있습니다. 또한 블록 변수만 사용한다는 것은 블록의 모든 트랜잭션에 대해 의사 난수(pseudo random number)가 동일하다는 것을 의미하므로 공격자는 블록 내에서 많은 트랜잭션을 수행(최대 베팅이 있는 경우)하여 승리를 곱할 수 있습니다.  

## Preventative Techniques
엔트로피(임의)의 출처는 블록체인의 외부에 있어야 합니다. 이 작업은 커밋-공개와 같은 시스템을 사용하는 동종 업체 간에 수행되거나, 신뢰 모델을 참가자 그룹(RandDAO에서처럼)으로 변경하는 방식으로 수행될 수 있습니다. 또한 랜덤성 오라클 역할을 하는 중앙 집중식 엔터티를 통해 이 작업을 수행할 수 있습니다. 블록 변수(일반적으로 일부 예외는 있음)는 마이너에 의해 조작될 수 있으므로 엔트로피를 소싱하는 데 사용해서는 안 됩니다.  

## Real-World Example: PRNG Contracts
2018년 2월 Arseny Reutov는 유사 난수 생성기(PRNG)를 사용하는 3,649건의 스마트 라이브 계약을 분석한 결과 43건의 계약이 악용될 수 있음을 발견했습니다.  

# External Contract Referencing
Ethereum "world computer"의 이점 중 하나는 코드를 재사용하고 네트워크에 이미 구축된 계약과 상호 작용할 수 있다는 것입니다. 그 결과, 많은 수의 계약은 외부 메시지 호출을 통해 외부 계약을 참조한다. 이러한 외부 메시지 호출은 분명하지 않은 방식으로 악의적인 행위자의 의도를 가릴 수 있으며, 이제 살펴보겠습니다.  

## The Vulnerability
솔리디티에서는 주소에 있는 코드가 캐스팅되는 계약 유형을 나타내는지 여부에 관계없이 모든 주소를 계약에 캐스팅할 수 있습니다. 이는 특히 계약 작성자가 악성 코드를 숨기려고 할 때 문제를 일으킬 수 있습니다. 예를 들어 설명하겠습니다.

Rot13 Encryption과 같은 코드를 생각해 보십시오.기본적으로 ROT13 암호를 구현합니다.  
Example 8. Rot13Encryption.sol
```
// encryption contract
contract Rot13Encryption {

   event Result(string convertedString);

    // rot13-encrypt a string
    function rot13Encrypt (string text) public {
        uint256 length = bytes(text).length;
        for (var i = 0; i < length; i++) {
            byte char = bytes(text)[i];
            // inline assembly to modify the string
            assembly {
                // get the first byte
                char := byte(0,char)
                // if the character is in [n,z], i.e. wrapping
                if and(gt(char,0x6D), lt(char,0x7B))
                // subtract from the ASCII number 'a',
                // the difference between character <char> and 'z'
                { char:= sub(0x60, sub(0x7A,char)) }
                if iszero(eq(char, 0x20)) // ignore spaces
                // add 13 to char
                {mstore8(add(add(text,0x20), mul(i,1)), add(char,13))}
            }
        }
        emit Result(text);
    }

    // rot13-decrypt a string
    function rot13Decrypt (string text) public {
        uint256 length = bytes(text).length;
        for (var i = 0; i < length; i++) {
            byte char = bytes(text)[i];
            assembly {
                char := byte(0,char)
                if and(gt(char,0x60), lt(char,0x6E))
                { char:= add(0x7B, sub(char,0x61)) }
                if iszero(eq(char, 0x20))
                {mstore8(add(add(text,0x20), mul(i,1)), sub(char,13))}
            }
        }
        emit Result(text);
    }
}
```
이 코드는 단순히 문자열(a–z, 유효성 검사 없이)을 가져와서 각 문자를 오른쪽으로 13자리씩 이동하여 암호화합니다(z 주위에 줄 바꿈). 즉, n으로 전환하고 x로 전환합니다. 이전 계약서의 집회는 논의되고 있는 문제를 이해하기 위해 이해될 필요가 없으므로 집회에 익숙하지 않은 독자들은 안전하게 무시할 수 있습니다.

이제 암호화 시 이 코드를 사용하는 다음 계약을 고려해 보십시오.

```
import "Rot13Encryption.sol";

// encrypt your top-secret info
contract EncryptionContract {
    // library for encryption
    Rot13Encryption encryptionLibrary;

    // constructor - initialize the library
    constructor(Rot13Encryption _encryptionLibrary) {
        encryptionLibrary = _encryptionLibrary;
    }

    function encryptPrivateData(string privateInfo) {
        // potentially do some operations here
        encryptionLibrary.rot13Encrypt(privateInfo);
     }
 }
 ```
 이 계약의 문제는 암호화 라이브러리 주소가 공용 또는 일정하지 않다는 것입니다. 따라서 계약의 시행자는 건설자에게 이 계약을 가리키는 주소를 제공할 수 있다.  
 
```
// encryption contract
contract Rot26Encryption {

   event Result(string convertedString);

    // rot13-encrypt a string
    function rot13Encrypt (string text) public {
        uint256 length = bytes(text).length;
        for (var i = 0; i < length; i++) {
            byte char = bytes(text)[i];
            // inline assembly to modify the string
            assembly {
                // get the first byte
                char := byte(0,char)
                // if the character is in [n,z], i.e. wrapping
                if and(gt(char,0x6D), lt(char,0x7B))
                // subtract from the ASCII number 'a',
                // the difference between character <char> and 'z'
                { char:= sub(0x60, sub(0x7A,char)) }
                // ignore spaces
                if iszero(eq(char, 0x20))
                // add 26 to char!
                {mstore8(add(add(text,0x20), mul(i,1)), add(char,26))}
            }
        }
        emit Result(text);
    }

    // rot13-decrypt a string
    function rot13Decrypt (string text) public {
        uint256 length = bytes(text).length;
        for (var i = 0; i < length; i++) {
            byte char = bytes(text)[i];
            assembly {
                char := byte(0,char)
                if and(gt(char,0x60), lt(char,0x6E))
                { char:= add(0x7B, sub(char,0x61)) }
                if iszero(eq(char, 0x20))
                {mstore8(add(add(text,0x20), mul(i,1)), sub(char,26))}
            }
        }
        emit Result(text);
    }
}
```
이 계약은 ROT26 암호를 구현하여 각 문자를 26자리씩 이동합니다(즉, 아무 것도 수행 안 함). 다시 말하지만, 이 계약서에서는 집회를 이해할 필요가 없습니다. 더 쉽게 말해 공격자가 다음 계약을 동일한 효과로 연결했을 수 있습니다.  
```
contract Print{
    event Print(string text);

    function rot13Encrypt(string text) public {
        emit Print(text);
    }
 }
```
이러한 계약 중 하나의 주소가 생성자에 지정된 경우 암호화되지 않은 개인 데이터를 인쇄하는 이벤트가 생성됩니다.

이 예에서는 생성자에서 라이브러리 유사 계약이 설정되었지만, 권한이 있는 사용자(예: 소유자)가 라이브러리 계약 주소를 변경할 수 있는 경우가 많습니다. 링크된 계약에 호출되는 함수가 포함되어 있지 않으면 폴백 함수가 실행됩니다. 예를 들어 EncryptionLibrary.rot13Encrypt() 행을 사용하는 경우 EncryptionLibrary에서 지정한 계약은 다음과 같습니다.  

```
 contract Blank {
     event Print(string text);
     function () {
         emit Print("Here");
         // put malicious code here and it will run
     }
 }
```
'여기'라는 문구가 적힌 사건이 터지겠죠 따라서 사용자가 계약 라이브러리를 변경할 수 있다면 원칙적으로 다른 사용자가 모르게 임의 코드를 실행하도록 할 수 있습니다.

## Preventative Techniques
앞에서 설명한 바와 같이, 안전 계약은 (어떤 경우에는) 악의적으로 행동하는 방식으로 전개될 수 있다. 감사자는 공개적으로 계약을 검증하고 소유자에게 악의적인 방식으로 계약을 배포하도록 할 수 있으며, 이로 인해 취약성 또는 악의적인 의도가 있는 공공 감사 계약이 발생할 수 있습니다.

이러한 시나리오를 방지하는 여러 가지 기술이 있습니다.

한 가지 기술은 새로운 키워드를 사용하여 계약을 만드는 것입니다. 앞의 예에서, 작성자는 다음과 같이 작성될 수 있다.  
```
constructor() {
    encryptionLibrary = new Rot13Encryption();
}
```
이렇게 하면 배포 시 참조된 계약의 인스턴스가 생성되며 배포자는 Rot13 Encryption 계약을 변경하지 않고 교체할 수 없습니다.

또 다른 해결책은 외부 계약 주소를 하드코딩하는 것입니다.

일반적으로 외부 계약을 호출하는 코드는 항상 신중하게 감사해야 합니다. 개발자로서 외부 계약을 정의할 때, 이용자들이 계약에서 참조하는 코드를 쉽게 검토할 수 있도록 계약 주소를 공개(다음 절의 허니팟 사례에서는 해당되지 않음)하는 것이 좋은 생각이 될 수 있다. 반대로, 계약에 사설 변수 계약 주소가 있는 경우에는 (실제 사례와 같이) 누군가가 악의적으로 행동하는 신호일 수 있습니다. 사용자가 외부 기능을 호출하는 데 사용되는 계약 주소를 변경할 수 있는 경우, 사용자가 변경 중인 코드를 볼 수 있도록 시간 잠금 및/또는 투표 메커니즘을 구현하거나 참가자에게 새 계약 주소를 선택할 기회를 제공하는 것이 중요할 수 있습니다.  

## Real-World Example: Reentrancy Honey Pot
최근 많은 꿀냄비가 메인넷에 공개되었다. 이러한 계약은 계약을 이용하려고 하지만 결국 이용하려는 계약에 에테르를 잃게 되는 Ethereum 해커보다 앞서려고 합니다. 한 가지 예는 예상 계약을 생성자의 악의적인 계약으로 대체하여 이 공격을 사용합니다. 코드는 다음에서 찾을 수 있습니다.  
```
pragma solidity ^0.4.19;

contract Private_Bank
{
    mapping (address => uint) public balances;
    uint public MinDeposit = 1 ether;
    Log TransferLog;

    function Private_Bank(address _log)
    {
        TransferLog = Log(_log);
    }

    function Deposit()
    public
    payable
    {
        if(msg.value >= MinDeposit)
        {
            balances[msg.sender]+=msg.value;
            TransferLog.AddMessage(msg.sender,msg.value,"Deposit");
        }
    }

    function CashOut(uint _am)
    {
        if(_am<=balances[msg.sender])
        {
            if(msg.sender.call.value(_am)())
            {
                balances[msg.sender]-=_am;
                TransferLog.AddMessage(msg.sender,_am,"CashOut");
            }
        }
    }

    function() external payable{}

}

contract Log
{
    struct Message
    {
        address Sender;
        string  Data;
        uint Val;
        uint  Time;
    }

    Message[] public History;
    Message LastMsg;

    function AddMessage(address _adr,uint _val,string _data)
    public
    {
        LastMsg.Sender = _adr;
        LastMsg.Time = now;
        LastMsg.Val = _val;
        LastMsg.Data = _data;
        History.push(LastMsg);
    }
}
```
한 rededit 사용자가 이 게시물을 통해 계약에 존재할 것으로 예상되는 재입국 버그를 이용하려고 시도하여 이 계약에서 1ether를 손실한 경위를 설명합니다.  

# Short Address/Parameter Attack
이 공격은 Solidity 계약 자체에 대해 수행되는 것이 아니라 이 계약과 상호 작용할 수 있는 타사 애플리케이션에 대해 수행됩니다. 이 섹션은 완전성과 독자에게 계약에서 매개 변수를 조작할 수 있는 방법에 대한 인식을 주기 위해 추가되었습니다.

자세한 내용은 "ERC20 짧은 주소 공격 설명", "ICO 스마트 계약 취약성: 짧은 주소 공격" 또는 이 Reddit 게시물.  

## The Vulnerability
스마트 계약에 매개 변수를 전달하면 ABI 규격에 따라 매개 변수가 인코딩됩니다. 예상되는 매개변수 길이보다 짧은 인코딩된 매개변수를 전송할 수 있습니다(예: 표준 40 16진수 문자(20바이트) 대신 38 16진수 문자(19바이트)에 불과한 주소 전송). 이러한 시나리오에서 EVM은 인코딩된 매개 변수의 끝에 0을 추가하여 예상 길이를 구성합니다.

타사 응용 프로그램이 입력의 유효성을 검사하지 않을 때 이 문제가 발생합니다. 가장 명확한 예는 사용자가 철회를 요청할 때 ERC20 토큰 주소를 확인하지 않는 교환입니다. 이 예는 Peter Vessenes의 게시물인 "ERC20 짧은 주소 공격 설명"에서 자세히 다룹니다.

표준 ERC20 전송 기능 인터페이스를 고려하여 파라미터의 순서를 확인합니다.  
```
function transfer(address to, uint tokens) public returns (bool success);
```
이제 대량의 토큰을 보유하고 있는 거래소와 100개의 토큰을 회수하려는 사용자를 고려해 보십시오. 사용자는 주소 0xdead dead dead dead dead dead dead 및 토큰 수 100을 제출합니다. 교환은 이러한 매개 변수를 전송 함수에 의해 지정된 순서대로 인코딩합니다. 즉, 주소 다음에 토큰을 인코딩합니다. 인코딩된 결과는 다음과 같습니다.  
```
a9059cbb000000000000000000000000deaddeaddea \
ddeaddeaddeaddeaddeaddeaddead0000000000000
000000000000000000000000000000000056bc75e2d63100000
```
처음 4바이트(a9059cbb)는 전송 함수 서명/선택기이고, 다음 32바이트는 주소이며, 마지막 32바이트는 unt256개의 토큰 수를 나타냅니다. 끝의 16진수 56bc75e2d63100000은 100개의 토큰에 해당합니다(REP 토큰 계약에서 지정한 대로 소수점 18자리).

이제 1바이트(16진수 2자리)가 누락된 주소를 보내면 어떻게 되는지 살펴보겠습니다. 특히 공격자가 0x dead dead dead dead dead dead deadde를 주소(마지막 두 자리 누락)로 보내고 동일한 100개의 토큰을 인출한다고 가정합니다. 교환이 이 입력의 유효성을 검사하지 않으면 다음과 같이 인코딩됩니다.  
```
a9059cbb000000000000000000000000deaddeaddea \
ddeaddeaddeaddeaddeaddeadde00000000000000
00000000000000000000000000000000056bc75e2d6310000000
```
그 차이는 미묘하다. 00는 전송된 짧은 주소를 보충하기 위해 인코딩 끝에 추가되었습니다. 이 값이 스마트 계약으로 전송되면 주소 매개 변수는 0xdeaddead dead dead dead dead dead dead dead deadde00으로 읽히고 값은 56bc75e2d6310000000으로 읽힙니다(추가 0 두 개 참조). 이 값은 이제 25600 토큰입니다(이 값에 256을 곱함). 이 예에서 교환이 이만큼의 토큰을 보유하고 있는 경우 사용자는 수정된 주소로 25600개의 토큰을 인출합니다(교환기는 사용자가 100개만 인출한다고 생각함). 이 예에서는 공격자가 수정된 주소를 소유하지 않지만 공격자가 0으로 끝나는 주소(쉽게 브루트 강제 적용할 수 있음)를 생성하고 이 생성된 주소를 사용하는 경우 의심하지 않는 교환에서 토큰을 도용할 수 있습니다.  

## Preventative Techniques
외부 응용프로그램의 모든 입력 매개변수는 블록체인으로 전송하기 전에 유효성을 검사해야 합니다. 여기서 파라미터 순서 지정은 중요한 역할을 합니다. 패딩은 끝에만 발생하므로 스마트 계약에서 매개 변수를 신중하게 정렬하면 이 공격의 일부 형식을 완화할 수 있습니다.  

# Unchecked CALL Return Values
Solidity에서는 여러 가지 방법으로 외부 통화를 수행할 수 있습니다. 에테르를 외부 계정으로 보내는 것은 일반적으로 전송 방법을 통해 수행됩니다. 그러나 전송 기능도 사용할 수 있으며, 보다 다재다능한 외부 통화의 경우 CALL opcode를 Solidity에서 직접 사용할 수 있습니다. 호출 및 전송 함수는 호출 성공 여부를 나타내는 부울을 반환합니다. 따라서 이러한 함수는 (호출 또는 송신에 의해 초기화) 외부 호출이 실패하더라도 이러한 함수를 실행하는 트랜잭션이 되돌아가지 않고, 오히려 함수가 false를 반환한다는 단순한 주의사항을 가지고 있습니다. 일반적인 오류는 개발자가 외부 호출이 실패할 경우 복구가 발생할 것으로 예상하며 반환 값을 확인하지 않는 것입니다.

자세한 내용은 DASP Top 10 of 2018 및 "Send 버그에 대한 Live Ethereum 계약 검색"에서 #4를 참조하십시오.  

## The Vulnerability
```
contract Lotto {

    bool public payedOut = false;
    address public winner;
    uint public winAmount;

    // ... extra functionality here

    function sendToWinner() public {
        require(!payedOut);
        winner.send(winAmount);
        payedOut = true;
    }

    function withdrawLeftOver() public {
        require(payedOut);
        msg.sender.send(this.balance);
    }
}
```
이것은 당첨자가 당첨금 액수의 에테르를 받게 되는 로또와 같은 계약을 의미하며, 이는 일반적으로 누구나 인출할 수 있는 약간의 잔여분이 남는다.

이 취약성은 라인 11에 있으며, 이 취약성은 응답을 확인하지 않고 전송을 사용합니다. 이 사소한 예에서, 거래가 실패한 승자는 (기름이 떨어지거나 또는 의도적으로 회수 함수를 투입하는 계약이 됨으로써) 지불을 허용한다.ether 전송 여부와 상관없이 Out을 true로 설정합니다. 이 경우, 누구나 탈퇴 LeftOver 기능을 통해 우승자의 당첨금을 회수할 수 있습니다. 

## Preventative Techniques
외부 트랜잭션이 복구되면 전송이 되돌아오므로 가능하면 전송 기능 대신 전송 기능을 사용하십시오. 전송이 필요한 경우 항상 반환 값을 확인하십시오.

좀 더 강력한 권고는 철회 패턴을 채택하는 것이다. 이 솔루션에서 각 사용자는 계약에서 에테르 발송을 처리하고 실패한 송신 트랜잭션의 결과를 처리하는 분리된 인출 기능을 호출해야 합니다. 외부 송신 기능을 나머지 코드베이스와 논리적으로 분리해 최종 사용자가 인출 기능을 호출하는 데 실패한 트랜잭션의 부담을 주자는 취지다.  


## Real-World Example: Etherpot and King of the Ether
Etherpot는 앞서 언급한 계약 사례와 크게 다르지 않은 스마트 계약 로또였다. 이 계약의 실패는 주로 블록 해시(마지막 256개 블록 해시만 사용 가능)의 잘못된 사용으로 인해 발생했습니다. Etherpot가 이 문제를 올바르게 고려하지 못한 방법에 대한 Aakil Fernandes의 게시물을 참조하십시오. 하지만, 이 계약 역시 확인되지 않은 통화 가치로 인해 어려움을 겪었습니다. 로또의 기능현금을 생각해보세요.sol: 코드 조각. 

Example 9. lotto.sol: Code snippet
```
...
  function cash(uint roundIndex, uint subpotIndex){

        var subpotsCount = getSubpotsCount(roundIndex);

        if(subpotIndex>=subpotsCount)
            return;

        var decisionBlockNumber = getDecisionBlockNumber(roundIndex,subpotIndex);

        if(decisionBlockNumber>block.number)
            return;

        if(rounds[roundIndex].isCashed[subpotIndex])
            return;
        //Subpots can only be cashed once. This is to prevent double payouts

        var winner = calculateWinner(roundIndex,subpotIndex);
        var subpot = getSubpot(roundIndex);

        winner.send(subpot);

        rounds[roundIndex].isCashed[subpotIndex] = true;
        //Mark the round as cashed
}
...
```
줄 21에서는 보내기 함수의 반환 값이 확인되지 않으며, 다음 줄은 당첨자가 자금을 보냈음을 나타내는 부울을 설정합니다. 이 버그는 승자가 에테르를 받지 못하는 상태를 허용할 수 있지만, 계약 상태는 승자가 이미 지불했음을 나타낼 수 있습니다.

더 심각한 버전의 이 버그가 에테르 왕에서 발생했다. 이 계약의 훌륭한 사후 평가서가 작성되었는데, 어떻게 확인되지 않은 전송이 계약을 공격하는 데 사용될 수 있는지 자세히 설명되어 있습니다.  

# Race Conditions/Front Running
다른 계약에 대한 외부 통화와 기본 블록 체인의 다중 사용자 특성이 결합되면 사용자가 예상치 못한 상태를 얻기 위해 코드 실행을 경쟁적으로 수행하는 다양한 Solidity 함정을 야기합니다. 재진입(이 장 앞부분에서 설명함)은 그러한 경주 조건의 한 예이다. 이 섹션에서는 Ethereum 블록체인에 발생할 수 있는 다른 종류의 인종 조건에 대해 논의하겠습니다. 이 주제에 대한 좋은 글은 이더리움 위키에서 '레이스 조건'을 비롯해 2018년 DASP TOP10에서 7위, 이더리움 스마트 계약 모범 사례 등 다양하다.  

## The Vulnerability
대부분의 블록 체인과 마찬가지로 Ethereum 노드는 트랜잭션을 풀링하고 블록으로 형성합니다. 광부가 합의 메커니즘(현재 Ethash PoW for Ethereum)을 해결한 후에만 트랜잭션이 유효한 것으로 간주됩니다. 블록을 해결하는 광부는 또한 일반적으로 각 트랜잭션의 gasPrice에 의해 주문되는 풀의 트랜잭션을 블록에 포함할지 선택합니다. 여기 잠재적인 공격 벡터가 있습니다. 공격자는 트랜잭션 풀에서 문제 해결 방법이 포함될 수 있는 트랜잭션을 감시하고 해결사의 사용 권한을 수정 또는 해지하거나 해결사에 유해한 계약 상태를 변경할 수 있습니다. 공격자는 이 트랜잭션에서 데이터를 가져와 더 높은 gasPrice로 자체 트랜잭션을 생성하여 트랜잭션을 원본보다 먼저 블록에 포함할 수 있습니다.

간단한 예를 들어 이 방법이 어떻게 작동하는지 살펴보겠습니다. Find ThisHash.sol에 나와 있는 계약을 고려하십시오.  

```
contract FindThisHash {
    bytes32 constant public hash =
      0xb5b5b97fafd9855eec9b41f74dfb6c38f5951141f9a3ecd7f44d5479b630ee0a;

    constructor() external payable {} // load with ether

    function solve(string solution) public {
        // If you can find the pre-image of the hash, receive 1000 ether
        require(hash == sha3(solution));
        msg.sender.transfer(1000 ether);
    }
}
```
이 계약서에 이더가 1,000개 들어있다고 합시다. 다음 SHA-3 해시의 사전 이미지를 찾을 수 있는 사용자: 
```
0xb5b5b97fafd9855eec9b41f74dfb6c38f5951141f9a3ecd7f44d5479b630ee0a
```
솔루션을 제출하고 1,000 이더를 회수할 수 있습니다. 한 사용자가 솔루션을 Ethereum!이라고 가정해 보겠습니다. 그들은 Ethereum!을 매개 변수로 하여 solve를 호출합니다. 안타깝게도 공격자는 영리하게 트랜잭션 풀을 감시하여 솔루션을 제출하는 모든 사용자를 대상으로 합니다. 그들은 이 솔루션을 보고 유효성을 확인한 다음, 원래의 거래보다 훨씬 더 높은 가스 가격으로 동등한 거래를 제출합니다. 블록을 해결한 광부는 가스 가격 상승으로 공격자에게 우선권을 주고, 기존 해결사보다 먼저 거래를 마이닝할 것으로 보인다. 공격자는 1,000 에테르를 가져가고 문제를 해결한 사용자는 아무것도 얻지 못합니다. 이러한 유형의 "전면적인" 취약점에서는 광부들이 공격을 직접 실행하도록 특별히 동기를 부여받거나 과도한 비용으로 이러한 공격을 실행하도록 매수될 수 있다는 점을 유념하십시오. 공격자 자체가 광부일 가능성을 과소평가해서는 안 된다.  

## Preventative Techniques
이러한 종류의 전면 실행 공격을 수행할 수 있는 행위자 유형은 두 가지입니다. 사용자(거래의 gasPrice를 수정하는 사람)와 마이너 자신(블록에서 트랜잭션을 적합하게 보는 방식으로 재주문할 수 있는 사람). 1등급(사용자)에 취약한 계약은 2등급(광부)에 취약한 1등급(광부)보다 훨씬 악화된 것으로, 광부가 블록을 해결해야 공격을 수행할 수 있어 특정 블록을 대상으로 하는 개별 광부에게는 가능성이 낮다. 여기서는 두 가지 유형의 공격자와 관련된 몇 가지 완화 조치를 나열하겠습니다.

한 가지 방법은 가스 가격에 상한을 두는 것입니다. 따라서 사용자가 가스 가격을 인상하고 상한을 넘어 우선적인 거래 주문을 받을 수 없습니다. 이 조치는 첫 번째 클래스의 공격자(임의 사용자)로부터만 보호합니다. 이 시나리오의 광부들은 기름값에 상관없이 원하는 대로 블록에서 거래를 주문할 수 있기 때문에 여전히 계약을 공격할 수 있다.  

더 강력한 방법은 커밋-베일 제도를 사용하는 것이다. 이러한 체계는 사용자가 숨겨진 정보(일반적으로 해시)를 사용하여 트랜잭션을 전송하도록 지정합니다. 트랜잭션이 블록에 포함된 후 사용자는 전송된 데이터(공개 단계)를 표시하는 트랜잭션을 보냅니다. 이 방법은 트랜잭션 내용을 결정할 수 없기 때문에 마이너와 사용자 모두 트랜잭션을 전면 실행하는 것을 방지합니다. 그러나 이 방법은 거래가치를 숨길 수 없다(일부 경우에는 숨겨야 할 귀중한 정보이다). ENS 스마트 계약은 사용자가 기꺼이 지출할 수 있는 에테르 금액을 포함하는 커밋된 데이터를 가진 거래를 전송할 수 있도록 했다. 그런 다음 사용자는 임의 값의 트랜잭션을 보낼 수 있습니다. 공개 단계에서 이용자들은 거래에서 보내진 금액과 지출 의향이 있는 금액의 차액을 환불받았다.

로렌츠 브라이덴바흐, 필 다이안, 아리 후엘스, 플로리안 트라메르의 추가 제안은 "잠수함 송신"을 사용하는 것입니다. 이 아이디어를 효율적으로 구현하려면 CREATE2 opcode가 필요합니다. 이 opcode는 현재 채택되지는 않았지만 곧 출시될 하드 포크에 포함될 것으로 보입니다.

## Real-World Examples: ERC20 and Bancor
ERC20 표준은 Ethereum에서 토큰을 구축하는 것으로 잘 알려져 있습니다. 이 표준에는 승인 기능으로 인해 발생할 수 있는 잠재적인 전면 실행 취약성이 있습니다. 미하일 블라디미로프와 드미트리 호브라토비치는 이 취약성에 대한 좋은 설명을 썼다.

표준은 승인 함수를 다음과 같이 지정합니다.  
```
function approve(address _spender, uint256 _value) returns (bool success)
```
이 기능을 사용하면 다른 사용자가 토큰을 대신 전송할 수 있습니다. 전면 실행 취약성은 사용자 Alice가 친구 Bob이 토큰 100개를 사용하도록 승인하는 시나리오에서 발생합니다. 앨리스는 나중에 밥의 토큰 100개 사용 승인을 취소하기로 결심하고 밥의 할당을 50개의 토큰으로 설정하는 거래를 만듭니다. 체인을 주의 깊게 지켜보던 밥은 이 거래를 보고 100개의 토큰을 사용하는 자신의 거래를 만든다. 그는 앨리스보다 그의 거래에 기름값을 더 높게 매기 때문에 그녀의 거래보다 그의 거래를 우선시하게 된다. 일부 승인 구현에서는 밥이 100개의 토큰을 전송할 수 있으며 앨리스의 거래가 커밋되면 밥의 승인을 50개의 토큰으로 재설정하여 사실상 밥에게 150개의 토큰에 대한 액세스 권한을 부여합니다.

또 다른 두드러진 실제 사례는 Bancor입니다. Ivan Bogatyy와 그의 팀은 Bancor의 초기 구현에 대해 수익성 있는 공격을 기록했습니다. 그의 블로그 게시물과 DevCon3 토크에서는 이 작업이 어떻게 수행되었는지를 자세히 설명합니다. 기본적으로 토큰의 가격은 거래 가치에 따라 결정되며, 사용자는 뱅코 거래의 거래 풀을 보고 가격 차이로 이익을 얻을 수 있습니다. 이번 공격은 방코르 팀이 해결했습니다.  

# Denial of Service (DoS)
이 범주는 매우 광범위하지만 기본적으로 사용자가 계약을 일정 기간 동안 또는 어떤 경우에는 영구적으로 사용할 수 없도록 렌더링할 수 있는 공격으로 구성됩니다. 이는 실제 사례: 패리티 멀티시그 월렛(Second Hack)의 경우와 같이 이러한 계약에서 영원히 걸림돌이 될 수 있습니다.  

## The Vulnerability
계약이 불가능해질 수 있는 방법은 다양하다. 여기서는 DoS 취약점으로 이어질 수 있는 명확하지 않은 몇 가지 Solidity 코딩 패턴을 강조합니다.  
외부에서 조작된 매핑 또는 어레이를 통해 루프 수행
이 패턴은 일반적으로 소유자가 다음과 같은 분배 기능을 가진 투자자에게 토큰을 분배하고자 할 때 나타난다.  
```
contract DistributeTokens {
    address public owner; // gets set somewhere
    address[] investors; // array of investors
    uint[] investorTokens; // the amount of tokens each investor gets

    // ... extra functionality, including transfertoken()

    function invest() external payable {
        investors.push(msg.sender);
        investorTokens.push(msg.value * 5); // 5 times the wei sent
        }

    function distribute() public {
        require(msg.sender == owner); // only owner
        for(uint i = 0; i < investors.length; i++) {
            // here transferToken(to,amount) transfers "amount" of
            // tokens to the address "to"
            transferToken(investors[i],investorTokens[i]);
        }
    }
}
```
이 계약의 루프는 인위적으로 부풀릴 수 있는 배열 위로 실행됩니다. 공격자는 많은 사용자 계정을 만들어 투자자 배열을 크게 만들 수 있습니다. 이는 원칙적으로 for 루프를 실행하는 데 필요한 가스가 블록 가스 한계를 초과하여 분배 기능이 작동하지 않도록 할 수 있습니다.  

### Owner operations
또 다른 일반적인 패턴은 소유자가 계약에 대한 특정 권한을 가지고 있으며 계약을 다음 상태로 진행하기 위해 일부 작업을 수행해야 하는 경우입니다. 소유자가 계약을 완료해야 토큰을 양도할 수 있는 ICO(Initial Coin Offering) 계약이 한 예입니다. 예:  
```
bool public isFinalized = false;
address public owner; // gets set somewhere

function finalize() public {
    require(msg.sender == owner);
    isFinalized = true;
}

// ... extra ICO functionality

// overloaded transfer function
function transfer(address _to, uint _value) returns (bool) {
    require(isFinalized);
    super.transfer(_to,_value)
}

...
```
이 경우 권한 있는 사용자가 개인 키를 분실하거나 비활성 상태가 되면 전체 토큰 계약이 작동하지 않게 됩니다. 이 경우 소유자가 최종적으로 호출할 수 없는 토큰은 전송할 수 없습니다. 토큰 생태계의 전체 작업은 단일 주소에 달려 있습니다.  

### Progressing state based on external calls
새 상태로 진행하려면 주소로 에테르를 보내거나 외부 소스의 입력을 기다려야 하는 계약이 작성되기도 합니다. 이러한 패턴은 외부 호출이 실패하거나 외부 이유로 인해 차단될 때 DoS 공격을 초래할 수 있습니다. ether 전송 예제에서 사용자는 ether를 허용하지 않는 계약을 만들 수 있습니다. 계약을 새로운 상태로 진행하기 위해 에테르를 철회해야 하는 경우(다시 사용할 수 있게 되기 전에 모든 에테르를 철회해야 하는 시간 잠금 계약 고려), 에테르를 수락하지 않는 사용자의 계약으로 보낼 수 없기 때문에 계약은 새로운 상태를 달성하지 못할 것입니다.  

## Preventative Techniques
첫 번째 예에서 계약은 외부 사용자가 인위적으로 조작할 수 있는 데이터 구조를 통과해서는 안 됩니다. 투자자들 각자가 토큰을 독립적으로 청구하기 위해 인출 기능을 호출하는 인출 패턴을 권장합니다.

두 번째 예에서는 권한 있는 사용자가 계약 상태를 변경해야 했습니다. 이러한 예에서는 소유자가 무력화될 경우 페일 세이프를 사용할 수 있습니다. 한 가지 해결책은 소유주를 멀티시그 계약으로 만드는 것입니다. 또 다른 솔루션은 시간 잠금을 사용하는 것입니다. 예를 들어, 온라인 5의 요구 사항에는 unlockTime에 지정된 기간 후에 사용자가 완료할 수 있는 시간 기반 메커니즘(예: require(msg.sx == 소유자 | now > > 잠금 해제 시간)이 포함될 수 있습니다. 이러한 완화 기법은 세 번째 예에서도 사용할 수 있습니다. 새로운 상태로 진행하기 위해 외부 호출이 필요한 경우, 실패 가능성을 설명하고 원하는 호출이 오지 않는 경우 시간 기반 상태 진행을 잠재적으로 추가합니다.  

## Real-World Examples: GovernMental
GovernmentMal은 꽤 많은 양의 에테르를 축적한 오래된 폰지 계획이었다. 안타깝게도 이 섹션에서 언급한 DoS 취약성에 영향을 받기 쉬웠습니다. etherik의 Reddit 게시물에는 계약에서 ether를 철회하기 위해 대형 매핑을 삭제해야 하는 방법이 설명되어 있습니다. 이 지도를 삭제한 것은 당시 가스비가 블록가스 한도를 초과해 1100에테르를 회수할 수 없었다. 계약 주소는 0xF45717552f12Ef7cb65e95476F217Ea008167Ae3이며 트랜잭션 0x0d80d67202bd9cb77df8ddd719&thinsp;0a1b0793e8ec8e719&thinsp;0b0793e8e8128128128f0506b에서 확인할 수 있습니다.  

# Block Timestamp Manipulation
블록 타임스탬프는 랜덤 숫자에 대한 엔트로피(자세한 내용은 엔트로피 일루전 참조), 일정 기간 동안의 잠금 자금 및 시간에 따라 달라지는 다양한 상태 변화 조건문과 같은 다양한 응용 프로그램에 사용되어 왔습니다. 광부는 스마트 계약에서 블록 타임스탬프를 잘못 사용할 경우 위험한 것으로 판명될 수 있는 타임스탬프를 약간 조정할 수 있습니다.

Solidity 문서와 Joris Bontje의 Ethereum Stack Exchange 관련 질문이 이에 대한 유용한 참고 자료입니다.  

## The Vulnerability
block.timestamp 와 그 가명은 이제 광부들이 조작할 인센티브가 있다면 조작할 수 있습니다. 룰렛에 나오는 간단한 게임을 구성해 봅시다.광부들의 착취에 취약할 겁니다  
```
contract Roulette {
    uint public pastBlockTime; // forces one bet per block

    constructor() external payable {} // initially fund contract

    // fallback function used to make a bet
    function () external payable {
        require(msg.value == 10 ether); // must send 10 ether to play
        require(now != pastBlockTime); // only 1 transaction per block
        pastBlockTime = now;
        if(now % 15 == 0) { // winner
            msg.sender.transfer(this.balance);
        }
    }
}
```
이 계약은 단순한 복권처럼 행동한다. 계약 잔액을 따낼 기회를 얻기 위해 블록당 한 건당 10에테르를 걸 수 있다. 여기서 'block.timestamp'의 마지막 두 자리가 균일하게 분포되어 있다는 가정입니다. 만약 그렇다면, 이 복권에 당첨될 확률은 15분의 1이 될 것입니다.

하지만, 우리가 알다시피, 광부들은 필요하다면 타임스탬프를 조정할 수 있습니다. 이 경우 계약에 충분한 에테르 풀이 있으면 블록을 해결하는 광부가 해당 block.timestamp 또는 현재 모듈 15가 0인 타임스탬프를 선택할 수 있습니다. 그렇게 함으로써 그들은 블록 보상과 함께 이 계약에서 잠긴 에테르를 획득할 수 있습니다. 블록당 베팅할 수 있는 사람이 한 명뿐이기 때문에 이 역시 전방 실행 공격에 취약합니다(자세한 내용은 경주 조건/전방 실행 참조).

실제로 블록 타임스탬프는 단조롭게 증가하므로 마이너는 임의 블록 타임스탬프를 선택할 수 없습니다(이전보다 늦어야 함). 또한 이러한 블록은 네트워크에서 거부될 가능성이 높기 때문에(노드는 시간 스탬프가 미래에 있는 블록의 유효성을 검사하지 않음) 블록 시간을 설정하는 것으로 제한됩니다.  

## Preventative Techniques
블록 타임스탬프는 엔트로피나 난수 생성에 사용해서는 안 됩니다. 즉, 게임에서 이기거나 중요한 상태를 변경하는 결정적 요소가 되어서는 안 됩니다.

계약 잠금 해제(시간 잠금 해제), 몇 주 후 ICO 완료 또는 만료 날짜 적용 등 시간에 민감한 논리가 필요한 경우가 있습니다. block.number와 평균 블록 시간을 사용하여 시간을 추정하는 것이 좋습니다. 블록 시간은 10초이고 1주는 약 60480개 블록과 같습니다. 따라서 광부가 블록 번호를 쉽게 조작할 수 없으므로 계약 상태를 변경할 블록 번호를 지정하는 것이 더 안전할 수 있습니다. BAT ICO 계약은 이 전략을 채택했습니다.

이는 블록 타임스탬프의 광부 조작과 특별히 관련이 없는 계약일 경우 불필요할 수 있지만 계약 개발 시 유의해야 할 사항입니다.  


## Real-World Example: GovernMental
위에서 언급한 오래된 폰지 계획인 GovernmentMental도 타임스탬프 기반 공격에 취약했습니다. 한 라운드에서 가장 늦게 합류한 선수(최소 1분)에게 계약금이 지급됐다. 따라서 플레이어였던 광부가 타임스탬프를 조정(미래 시간으로, 1분이 경과한 것처럼 표시)하여 1분 이상(실제로 그렇지 않더라도) 마지막으로 가입한 플레이어인 것처럼 보이게 할 수 있습니다. 이에 대한 자세한 내용은 Tanya Bahrynovska의 "Ethereum 보안 취약성, 해킹 및 이들의 수정 내역"을 참조하십시오.  

# Constructors with Care
생성자는 계약을 초기화할 때 종종 중요한 권한 있는 작업을 수행하는 특수 기능입니다. Solidity v0.4.22 이전에는 시공자가 포함된 계약과 동일한 이름을 가진 함수로 정의되었습니다. 이러한 경우 개발 과정에서 계약명 변경 시 시공자명도 변경되지 않으면 정상적인 호출 가능 함수가 됩니다. 짐작할 수 있듯이, 이는 몇 가지 흥미로운 계약 해킹으로 이어질 수 있습니다.

더 많은 통찰력을 위해, 독자는 이더넷 도전(특히 폴아웃 수준)을 시도하는 데 관심이 있을 수 있습니다.  

## The Vulnerability
계약 이름이 수정되거나 시공자 이름에 오타가 있어 계약 이름과 일치하지 않을 경우 시공자는 정상 함수처럼 동작합니다. 이는 특히 시공자가 권한 있는 작업을 수행하는 경우 심각한 결과를 초래할 수 있습니다. 다음 계약을 고려하십시오.  
```
contract OwnerWallet {
    address public owner;

    // constructor
    function ownerWallet(address _owner) public {
        owner = _owner;
    }

    // Fallback. Collect ether.
    function () payable {}

    function withdraw() public {
        require(msg.sender == owner);
        msg.sender.transfer(this.balance);
    }
}
```
이 계약은 이더를 회수하여 소유자만 인출 기능을 호출하여 인출할 수 있도록 합니다. 이 문제는 시공자의 이름이 계약과 정확히 동일하지 않기 때문에 발생합니다: 첫 글자가 다릅니다! 따라서, 모든 사용자는 OwnerWalet 함수에 전화를 걸고, 자신을 Owner로 설정한 다음, 계약에서 탈퇴를 호출하여 모든 에테르를 가져갈 수 있습니다.

## Preventative Techniques
이 문제는 Solidity 컴파일러 버전 0.4.22에서 해결되었습니다. 이 버전에서는 함수의 이름이 계약 이름과 일치하도록 요구하지 않고 생성자를 지정하는 생성자 키워드를 도입했습니다. 이름 지정 문제를 방지하려면 이 키워드를 사용하여 생성자를 지정하는 것이 좋습니다.  

## Real-World Example: Rubixi
루빅시는 이런 취약점을 드러낸 또 다른 피라미드 계획이었다. 원래는 Dynamic Pyramid로 불렸지만, 루빅시로 배치하기 전에 계약 이름이 변경되었습니다. 생성자 이름이 변경되지 않아 사용자가 생성자가 될 수 있습니다. 이 버그와 관련된 몇 가지 흥미로운 토론은 Bitcointalk에서 볼 수 있습니다. 궁극적으로, 그것은 사용자들이 피라미드 제도로부터 수수료를 청구하기 위해 제작자 지위를 위해 싸울 수 있게 했다. 이 특정 버그에 대한 자세한 내용은 "History of Ethereum Security Vulnerability, Hacks and Their Fixs"에서 확인할 수 있습니다.  

# Uninitialized Storage Pointers
EVM은 데이터를 저장 또는 메모리로 저장합니다. 계약을 개발할 때 기능의 로컬 변수에 대한 기본 유형과 정확한 방법을 이해하는 것이 좋습니다. 변수를 부적절하게 초기화하면 취약한 계약을 만들 수 있기 때문이다.

EVM의 스토리지 및 메모리에 대한 자세한 내용은 데이터 위치, 스토리지의 상태 변수 레이아웃 및 메모리의 레이아웃에 대한 Solidity 설명서를 참조하십시오.  

## The Vulnerability
함수의 로컬 변수는 유형에 따라 기본적으로 저장소 또는 메모리로 설정됩니다. 초기화되지 않은 로컬 스토리지 변수는 계약에 있는 다른 스토리지 변수의 값을 포함할 수 있습니다. 이 사실은 의도하지 않은 취약성을 발생시키거나 의도적으로 악용될 수 있습니다.

NameRegistrar.sol의 비교적 간단한 이름 등록자 계약을 고려해 보겠습니다.  
```
// A locked name registrar
contract NameRegistrar {

    bool public unlocked = false;  // registrar locked, no name updates

    struct NameRecord { // map hashes to addresses
        bytes32 name;
        address mappedAddress;
    }

    // records who registered names
    mapping(address => NameRecord) public registeredNameRecord;
    // resolves hashes to addresses
    mapping(bytes32 => address) public resolve;

    function register(bytes32 _name, address _mappedAddress) public {
        // set up the new NameRecord
        NameRecord newRecord;
        newRecord.name = _name;
        newRecord.mappedAddress = _mappedAddress;

        resolve[_name] = _mappedAddress;
        registeredNameRecord[msg.sender] = newRecord;

        require(unlocked); // only allow registrations if contract is unlocked
    }
}
```
이 간단한 이름 등록자는 하나의 기능만 가지고 있습니다. 계약이 잠금 해제되면 누구나 이름을 등록하고(바이트 32 해시로) 해당 이름을 주소에 매핑할 수 있습니다. 등록 담당자는 처음에 잠기며, 25줄의 요구 사항으로 인해 등록자가 이름 레코드를 추가할 수 없습니다. 등기부를 잠금 해제할 방법이 없어 계약을 사용할 수 없는 것 같습니다! 그러나 잠금 해제된 변수에 관계없이 이름을 등록할 수 있는 취약성이 있습니다.

이 취약성에 대해 논의하려면 먼저 Solidity에서 스토리지가 작동하는 방식을 이해해야 합니다. (적절한 기술적 세부 정보가 없는) 개괄적인 개요로서, 적절한 검토를 위해 Solidity 문서를 읽는 것이 좋습니다. 상태 변수는 계약에 나타나는 대로 슬롯에 순차적으로 저장됩니다(다 함께 그룹화할 수는 있지만 이 예에서는 그렇지 않으므로 걱정할 필요가 없습니다). 따라서 슬롯[0], 슬롯[1]에 등록된 NameRecord, 슬롯 [2] 등에 Unlocked가 존재합니다. 각 슬롯의 크기는 32바이트입니다(매핑에는 복잡성이 더해지지만 지금은 무시하겠습니다). 부울 잠금 해제는 false의 경우 0x000…0(640s, 0x 제외)으로, true의 경우 0x000…1(630s)로 표시됩니다. 보시다시피 이 예에서는 상당한 스토리지 낭비가 있습니다.

퍼즐의 다음 부분은 기본적으로 Solidity가 구조와 같은 복잡한 데이터 유형을 로컬 변수로 초기화할 때 스토리지에 넣는다는 것입니다. 따라서 newRecord on line 18은 기본적으로 저장소로 설정됩니다. 이 취약성은 newRecord가 초기화되지 않았기 때문에 발생합니다. 기본적으로 저장소로 설정되므로 현재 잠금 해제된 포인터를 포함하는 저장소 슬롯[0]에 매핑됩니다. 라인 19와 20에서는 newRecord.name을 _name으로 설정하고 newRecord.mappedAddress를 _mapedAddress로 설정합니다. 이렇게 하면 슬롯[0]과 슬롯[1]의 저장 위치가 업데이트되어 잠금 해제된 상태 및 등록된 NameRecord와 연결된 저장소 슬롯이 모두 수정됩니다.

즉, 레지스터 함수의 바이트32_name 매개 변수만으로 잠금 해제를 직접 수정할 수 있습니다. 따라서 _name의 마지막 바이트가 0이 아닌 경우 스토리지 슬롯의 마지막 바이트[0]를 수정하고 잠금 해제된 상태로 true로 직접 변경됩니다. 이러한 _name 값은 잠금 해제된 상태를 true로 설정한 것처럼 줄 25에서 required 호출이 성공하도록 합니다. 리믹스에서 한번 해보세요. 다음 형식의 _name을 사용하면 함수가 전달됩니다.
```
0x0000000000000000000000000000000000000000000000000000000000000001
```

## Preventative Techniques
Solidity 컴파일러는 의도하지 않은 스토리지 변수에 대한 경고를 표시합니다. 개발자는 스마트 계약을 작성할 때 이러한 경고에 주의해야 합니다. 현재 버전의 Mist(0.10)에서는 이러한 계약을 컴파일할 수 없습니다. 복잡한 유형을 처리할 때 메모리 또는 스토리지 지정자를 명시적으로 사용하여 해당 유형이 예상대로 작동하는지 확인하는 것이 좋습니다.

## Real-World Examples: OpenAddressLottery and CryptoRoulette Honey Pots
초기화되지 않은 이 스토리지 변수 퀴크를 사용하여 일부 해커로부터 에테르를 수집하는 OpenAddressLottery라는 허니 포트가 배포되었습니다. 계약은 다소 관여되어 있기 때문에, 공격이 상당히 명확하게 설명될 수 있는 레딧 스레드에 분석을 맡기겠습니다.

또 다른 허니팟인 크립토 룰렛도 이 트릭을 사용해 에테르를 수집했습니다. 공격이 어떻게 진행되는지 알 수 없는 경우 이 계약 등에 대한 개요는 "커플 이더리움 허니팟 계약 분석"을 참조하십시오.  

# Floating Point and Precision
이 쓰기(v0.4.24)를 기준으로 Solidity는 고정점 및 부동점 번호를 지원하지 않습니다. 즉, 부동소수 표현은 Solidity에서 정수 형식으로 구성되어야 합니다. 이로 인해 올바르게 구현되지 않으면 오류 및 취약성이 발생할 수 있습니다.  

## The Vulnerability
Solidity에는 고정점 유형이 없으므로 개발자는 표준 정수 데이터 유형을 사용하여 자체 유형을 구현해야 합니다. 이 과정에서 개발자가 부딪힐 수 있는 여러 가지 함정이 있습니다. 이 섹션에서는 이 항목 중 일부를 강조 표시하도록 하겠습니다.

코드 예부터 살펴보겠습니다(간단성을 위해 이 장 앞부분에서 설명한 오버플로/언더플로 문제는 무시함).  
```
contract FunWithNumbers {
    uint constant public tokensPerEth = 10;
    uint constant public weiPerEth = 1e18;
    mapping(address => uint) public balances;

    function buyTokens() external payable {
        // convert wei to eth, then multiply by token rate
        uint tokens = msg.value/weiPerEth*tokensPerEth;
        balances[msg.sender] += tokens;
    }

    function sellTokens(uint tokens) public {
        require(balances[msg.sender] >= tokens);
        uint eth = tokens/tokensPerEth;
        balances[msg.sender] -= tokens;
        msg.sender.transfer(eth*weiPerEth);
    }
}
```
이 간단한 토큰 구매/판매 계약에는 몇 가지 명백한 문제가 있습니다. 토큰을 사고파는 수학적 계산은 맞지만 부동 소수점 숫자가 부족하면 잘못된 결과가 나온다. 예를 들어, 8행에서 토큰을 구입할 때 값이 1ether보다 작으면 초기 나눗셈이 0이 되고 최종 곱셈의 결과는 0으로 남습니다(예: 200 wei를 1e18 WeiEth로 나누면 0이 됩니다). 마찬가지로 토큰을 판매할 때 10보다 작은 토큰의 개수도 0 에테르가 됩니다. 사실 여기서 반올림은 항상 내려가기 때문에 29개의 토큰을 팔면 2개의 에테르가 됩니다.

이 계약의 문제는 정밀도가 가장 가까운 에테르(즉, 1e18 wei)에만 해당된다는 것입니다. 더 높은 정밀도가 필요할 때 ERC20 토큰의 십진수를 처리할 때 까다로울 수 있습니다.  

## Preventative Techniques
특히 경제적 의사결정을 반영하는 비율과 요율을 처리할 때 스마트 계약에서 올바른 정밀도를 유지하는 것이 매우 중요합니다.

사용 중인 비율 또는 비율이 분수에 큰 분자를 허용하는지 확인해야 합니다. 예를 들어, 예에서는 속도 토큰PerEth를 사용했습니다. 차라리 weiPer를 사용하는 것이 나았을 것이다.토큰, 큰 숫자일 겁니다. 해당하는 토큰 수를 계산하기 위해 msg.value/weiPer를 수행할 수 있습니다.토큰. 이게 더 정확한 결과를 줄 수 있을 거야

명심해야 할 또 다른 전술은 작전 순서를 염두에 두는 것이다. 이 예에서 토큰을 구입하기 위한 계산은 msg.value/weiPerEth*tokenPerEth였습니다. 곱하기 전에 분할이 발생합니다. (일부 언어와 달리 견고성은 작성된 순서대로 작업을 수행하도록 보장합니다.) 계산에서 곱셈을 먼저 수행한 다음 나누기를 수행했다면, 즉 msg.value*tokenPerEth/weiPerEth가 더 정확했을 것입니다.

마지막으로, 숫자에 대한 임의의 정밀도를 정의할 때 값을 더 높은 정밀도로 변환하고 모든 수학 연산을 수행한 다음 마지막으로 출력에 필요한 정밀도로 다시 변환하는 것이 좋습니다. 일반적으로 unt256s가 가스 사용에 최적화되어 있으며, 이 중 일부는 수학적 연산의 정밀도에 사용될 수 있는 약 60 오더의 크기를 가진다. Solidity에서 모든 변수를 높은 정밀도로 유지하고 외부 앱에서 더 낮은 정밀도로 다시 변환하는 것이 좋습니다(기본적으로 ERC20 토큰 계약에서 소수점 변수가 작동하는 방식임). 이를 수행하는 방법의 예를 보려면 DS-Math를 살펴보는 것이 좋습니다. 이것은 약간의 펑키한 이름("wads"와 "ray")을 사용하지만, 이 개념은 유용합니다.  

## Real-World Example: Ethstick
Ethstick 계약은 확장된 정밀도를 사용하지 않지만 웨이를 다루고 있습니다. 따라서, 이 계약은 반올림 문제가 있지만, 웨이의 정확성 수준에서만 문제가 발생할 것입니다. 더 심각한 결함도 있지만, 이는 블록 체인의 엔트로피를 얻기 어려운 것과 관련이 있습니다(엔트로피 일루전 참조). Ethstick 계약에 대한 자세한 내용은 Peter Vessenes의 또 다른 게시물인 "Ethereum Contracts Are Going Be Candy for Hackers"를 참조하십시오.  

# Tx.Origin Authentication
Solidity에는 전체 호출 스택을 통과하고 호출(또는 트랜잭션)을 처음 보낸 계정의 주소를 포함하는 전역 변수 tx.origin이 있습니다. 스마트 계약에서 인증에 이 변수를 사용하면 계약이 피싱과 유사한 공격에 취약해집니다.  

## The Vulnerability
tx.origin 변수를 사용하는 사용자에게 권한을 부여하는 계약은 일반적으로 사용자가 취약한 계약에 대해 인증된 작업을 수행하도록 속일 수 있는 피싱 공격에 취약합니다.

Phible.sol의 간단한 계약을 고려해보세요.  
```
contract Phishable {
    address public owner;

    constructor (address _owner) {
        owner = _owner;
    }

    function () external payable {} // collect ether

    function withdrawAll(address _recipient) public {
        require(tx.origin == owner);
        _recipient.transfer(this.balance);
    }
}
```
11번 라인에서는 계약에서 tx.origin을 사용하여 모든 인출 기능을 승인합니다. 이 계약을 통해 공격자는 다음 형식의 공격 계약을 만들 수 있습니다.  
```
import "Phishable.sol";

contract AttackContract {

    Phishable phishableContract;
    address attacker; // The attacker's address to receive funds

    constructor (Phishable _phishableContract, address _attackerAddress) {
        phishableContract = _phishableContract;
        attacker = _attackerAddress;
    }

    function () payable {
        phishableContract.withdrawAll(attacker);
    }
}
```
공격자는 이 계약을 자신의 개인 주소로 위장하고 피해자(Phible 계약의 소유자)를 사회적으로 엔지니어링하여 해당 주소로 거래를 보낼 수 있습니다(아마도 이 계약에는 일정량의 에테르를 보낼 수 있습니다. 주의하지 않는 한 공격자는 공격자의 주소에 코드가 있다는 것을 알아차리지 못하거나 공격자가 이를 다중 서명 지갑 또는 일부 고급 스토리지 지갑으로 간주할 수 있습니다(기본적으로 공개 계약의 소스 코드를 사용할 수 없습니다).

어쨌든 피해자가 충분한 가스가 포함된 트랜잭션을 AttackContract 주소로 보내면 폴백 함수를 호출하여 매개 변수 공격자와 Phible 계약의 discludeAll 함수를 호출합니다. 이로 인해 Phible 계약에서 공격자 주소로 모든 자금이 인출됩니다. 이는 통화를 처음 시작한 주소가 피해자(즉, Phible 계약의 소유자)였기 때문입니다. 따라서 tx.origin은 소유자와 동일하며 Phible 계약의 11 라인에 대한 요구사항이 통과될 것입니다.  


## Preventative Techniques
tx.origin 은 스마트 계약에서 인증에 사용해서는 안 됩니다. tx.origin 변수를 사용하지 말라는 뜻은 아닙니다. 스마트 계약에는 몇 가지 합법적인 사용 사례가 있습니다. 예를 들어, 외부 계약이 현재 계약을 호출하는 것을 거부하려는 경우 필요한 형식의 요구 사항(tx.origin == msg.sender)을 구현할 수 있습니다.보낸 사람). 이는 현재 계약을 호출하는 데 사용되는 중간 계약을 방지하여 계약을 일반 코드 없는 주소로 제한합니다.  

# Contract Libraries
온체인을 호출 가능한 라이브러리로 배치하고 오프체인을 코드 템플릿 라이브러리로 배치하여 재사용할 수 있는 기존 코드가 많이 있습니다. 구축된 온플랫폼 라이브러리는 바이트코드 스마트 계약으로 존재하므로 프로덕션에 사용하기 전에 상당한 주의를 기울여야 합니다. 그러나 잘 구축된 기존 온플랫폼 라이브러리를 사용하면 최신 업그레이드의 혜택을 받을 수 있는 등 많은 이점이 있으며, 총 Ethereum 라이브 계약 수를 줄여 비용을 절감하고 Ethereum 생태계에 혜택을 줍니다.

Ethereum에서 가장 널리 사용되는 리소스는 OpenZeppelin 제품군입니다. OpenZeppelin 제품군은 ERC20 및 ERC721 토큰 구현부터 다양한 크라우드세일 모델, Ownable, Pausable 또는 LimitBalance와 같은 계약에서 흔히 볼 수 있는 간단한 동작에 이르기까지 다양합니다. 이 저장소의 계약은 광범위하게 테스트되었으며 경우에 따라서는 사실상의 표준 구현으로 기능하기도 합니다. 무료로 사용할 수 있으며, Zeppelin이 지속적으로 증가하는 외부 기여자 목록과 함께 구축 및 유지관리하고 있습니다.

또한 Zeppelin은 스마트 계약 애플리케이션을 안전하게 개발하고 관리하기 위한 서비스 및 툴의 오픈 소스 플랫폼인 Zeppelin OS를 제공합니다. 제플린OS는 개발자가 자체적으로 업그레이드가 가능한 검증된 계약의 온체인 라이브러리에 연결된 업그레이드 가능한 DApp을 쉽게 시작할 수 있도록 EVM 위에 계층을 제공합니다. 이러한 라이브러리의 다양한 버전이 Ethereum 플랫폼에 공존할 수 있으며, 보증 시스템을 통해 사용자는 서로 다른 방향으로 개선을 제안하거나 추진할 수 있습니다. 탈중앙화된 애플리케이션을 디버그, 테스트, 배포 및 모니터링하기 위한 일련의 오프체인 도구도 플랫폼에 의해 제공됩니다.

프로젝트 ethm은 패키지 관리 시스템을 제공함으로써 생태계에서 발전하고 있는 다양한 자원을 정리하는 것을 목표로 한다. 따라서 레지스트리는 다음과 같은 추가 예를 제공합니다.  

+ Website: https://www.ethpm.com/  

+ Repository link: https://www.ethpm.com/registry  

+ GitHub link: https://github.com/ethpm  

+ Documentation: https://www.ethpm.com/docs/integration-guide  

# Conclusion
스마트 계약 영역에 종사하는 개발자는 알고 이해해야 할 것이 많습니다. 스마트 계약 설계 및 코드 작성의 모범 사례를 따르면 심각한 함정과 함정을 피할 수 있습니다.

가장 기본적인 소프트웨어 보안 원칙은 신뢰할 수 있는 코드의 재사용을 극대화하는 것일 수 있습니다. 암호학에서 이것은 매우 중요하기 때문에 "자신의 암호화를 굴리지 말라"는 격언이 될 수 있다. 스마트계약의 경우 지역사회가 철저히 점검한 자유롭게 이용할 수 있는 도서관을 통해 최대한 많은 것을 얻는 셈이다.  

