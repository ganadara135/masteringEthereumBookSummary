Now let’s look at the FibonacciBalance contract. Storage slot[0] now corresponds to the fibonacciLibrary address, and slot[1] corresponds to calculatedFibNumber. It is in this incorrect mapping that the vulnerability occurs. delegatecall preserves contract context. This means that code that is executed via delegatecall will act on the state (i.e., storage) of the calling contract.

# Smart Contract Security
스마트컨트랙트 프로그래밍에서 실수를 돌이킬 수 없는 결과를 만듦  
본 챕터에서는 보안 최고 예제와 디자인 패턴, 보안 대응 패턴을 알아 볼 것임  

프로그래밍은 개발자가 의도하지는 않았지만, 코딩한대로 작동한다  
또한, 모든 스마트컨트랙트는 퍼블릭이므로 누구나 트랜잭션을 통해서 상호 작동시킬 수 있음  
따라서, 코드가 가진 취약점을 극대화해서 공격 받을 수 있으므로 베스트 예제나 사전에 테스테된 라이브러리를 사용하는 게 좋음  

# Security Best Practices

### Minimalism/simplicity


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

