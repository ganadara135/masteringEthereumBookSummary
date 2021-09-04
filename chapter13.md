# The Ethereum Virtual Machine
이더리움 프로토콜 및 운영의 핵심은 이더리움 가상 머신(Ethereum Virtual Machine, 줄여서 EVM)입니다.   
이름에서 짐작할 수 있듯이, 이것은 Microsoft의 .NET Framework의 가상 머신이나 Java와 같은 다른 바이트코드 컴파일 프로그래밍 언어의 인터프리터와 크게 다르지 않은 계산 엔진입니다.   
이 장에서는 이더리움 상태 업데이트 컨텍스트 내에서 명령어 세트, 구조 및 작동을 포함하여 EVM을 자세히 살펴봅니다.  

# What Is the EVM?
EVM은 스마트 계약 배포 및 실행을 처리하는 Ethereum의 일부입니다.   
하나의 EOA에서 다른 EOA로의 단순한 가치 이전 트랜잭션은 실질적으로 이를 포함할 필요가 없지만 다른 모든 것은 EVM에 의해 계산된 상태 업데이트를 포함합니다.   
높은 수준에서 이더리움 블록체인에서 실행되는 EVM은 각각 고유한 영구 데이터 저장소가 있는 수백만 개의 실행 가능한 개체를 포함하는 글로벌 분산 컴퓨터로 생각할 수 있습니다.  

EVM은 스마트 계약 배포 및 실행을 처리하는 Ethereum의 일부입니다.   
하나의 EOA에서 다른 EOA로의 단순한 가치 이전 트랜잭션은 실질적으로 이를 포함할 필요가 없지만 다른 모든 것은 EVM에 의해 계산된 상태 업데이트를 포함합니다.   
높은 수준에서 이더리움 블록체인에서 실행되는 EVM은 각각 고유한 영구 데이터 저장소가 있는 수백만 개의 실행 가능한 개체를 포함하는 글로벌 분산 컴퓨터로 생각할 수 있습니다.  

EVM은 quasi–Turing-complete state 머신입니다.   
모든 실행 프로세스가 주어진 스마트 계약 실행에 사용할 수 있는 가스의 양에 따라 유한한 계산 단계 수로 제한되기 때문에 "quasi"입니다.   
따라서 중지 문제는 "해결"되고(모든 프로그램 실행이 중지됨) 실행이 (우연히 또는 악의적으로) 영원히 실행되어 이더리움 플랫폼이 완전히 중지될 수 있는 상황이 방지됩니다  

EVM은 스택 기반 아키텍처를 사용하여 모든 메모리 내 값을 스택에 저장합니다.   
256비트의 워드 크기로 작동하며(주로 기본 해싱 및 타원 곡선 작업을 용이하게 하기 위해) 주소 지정 가능한 여러 데이터 구성 요소가 있습니다.  
+ 실행할 스마트 계약의 바이트 코드와 함께 로드된 변경할 수 없는 프로그램 코드 ROM    
+ 모든 위치가 명시적으로 0으로 초기화 된 휘발성 메모리  
+ 이더리움 상태의 일부인 영구 저장소 , 역시 0으로 초기화됨  

실행 중에 사용할 수 있는 환경 변수 및 데이터 세트도 있습니다. 이 장의 뒷부분에서 더 자세히 살펴보겠습니다.  
EVM(Ethereum Virtual Machine) 아키텍처 및 실행 컨텍스트는 EVM 아키텍처 및 실행 컨텍스트를 보여준다  

![evm](https://github.com/ethereumbook/ethereumbook/blob/develop/images/evm-architecture.png)  
### Comparison with Existing Technology
"가상 머신"이라는 용어는 종종 실제 컴퓨터의 가상화에 적용되는데,  일반적으로 VirtualBox 또는 QEMU와 같은 "하이퍼바이저" 또는 Linux의 KVM과 같은 전체 운영 체제 인스턴스의 가상화에 사용된다.   
이들은 각각 실제 하드웨어, 시스템 호출 및 기타 커널 기능에 대한 소프트웨어 추상화를 제공해야 합니다.  

"가상 머신"이라는 용어는 일반적으로 VirtualBox 또는 QEMU와 같은 "하이퍼바이저" 또는 Linux의 KVM과 같은 전체 운영 체제 인스턴스에 의해 실제 컴퓨터의 가상화에 적용됩니다.   
이들은 각각 실제 하드웨어, 시스템 호출 및 기타 커널 기능에 대한 소프트웨어 추상화를 제공해야 합니다.  

EVM은 훨씬 더 제한된 영역에서 작동합니다.   
이는 단지 계산 엔진일 뿐이므로 예를 들어 JVM(Java Virtual Machine) 사양과 유사한 계산 및 저장의 추상화를 제공합니다.   
높은 수준의 관점에서 JVM은 기본 호스트 OS 또는 하드웨어에 구애받지 않는 런타임 환경을 제공하도록 설계되어 다양한 시스템에서 호환성을 가능하게 합니다.   
Java 또는 Scala(JVM 사용) 또는 C#(.NET 사용)과 같은 고급 프로그래밍 언어는 해당 가상 머신의 바이트코드 명령어 세트로 컴파일됩니다.   
같은 방식으로 EVM은 LLL, Serpent, Mutan 또는 Solidity와 같은 고급 스마트 계약 프로그래밍 언어가 컴파일되는 자체 바이트코드 명령어 세트(다음 섹션에서 설명)를 실행합니다.  

따라서 EVM에는 실행 순서가 외부적으로 구성되어 있기 때문에 스케줄링 기능이 없습니다.   
이더리움 클라이언트는 검증된 블록 트랜잭션을 실행하여 실행해야 하는 스마트 계약과 순서를 결정합니다.   
이런 의미에서 이더리움 세계의 컴퓨터는 자바스크립트와 같은 단일 스레드입니다.   
EVM에는 "시스템 인터페이스" 처리나 "하드웨어 지원"도 없습니다.   
인터페이스할 물리적 시스템이 없습니다. Ethereum 세계 컴퓨터는 완전히 가상입니다.  

### The EVM Instruction Set (Bytecode Operations)
EVM 명령어 세트는 다음을 포함하여 예상할 수 있는 대부분의 작업을 제공합니다.
+ 산술 및 비트 논리 연산  
+ 실행 컨텍스트 조회  
+ 스택, 메모리 및 스토리지 액세스  
+ 제어 흐름 작업  
+ 로깅, 호출 및 기타 연산자  
일반적인 바이트 코드 작업 외에도 EVM은 계정 정보(예: 주소 및 잔액) 및 블록 정보(예: 블록 번호 및 현재 가스 가격)에 액세스할 수 있습니다.  
사용 가능한 opcode와 그 기능을 살펴보고 EVM에 대한 탐색을 더 자세히 시작하겠습니다.   
예상할 수 있듯이 모든 피연산자는 스택에서 가져오고 결과(해당되는 경우)는 종종 스택의 맨 위에 다시 놓입니다.

### Arithmetic operations
산술 opcode 명령어:  
```
ADD        //Add the top two stack items
MUL        //Multiply the top two stack items
SUB        //Subtract the top two stack items
DIV        //Integer division
SDIV       //Signed integer division
MOD        //Modulo (remainder) operation
SMOD       //Signed modulo operation
ADDMOD     //Addition modulo any number
MULMOD     //Multiplication modulo any number
EXP        //Exponential operation
SIGNEXTEND //Extend the length of a two's complement signed integer
SHA3       //Compute the Keccak-256 hash of a block of memory
```
모든 산술은 모듈로 2 256 으로 수행되며 (달리 언급되지 않는 한) 0의 0승인 0 0 은 1로 간주됩니다.  
### Stack operations
스택, 메모리 및 스토리지 관리 지침:  
```
POP     //Remove the top item from the stack
MLOAD   //Load a word from memory
MSTORE  //Save a word to memory
MSTORE8 //Save a byte to memory
SLOAD   //Load a word from storage
SSTORE  //Save a word to storage
MSIZE   //Get the size of the active memory in bytes
PUSHx   //Place x byte item on the stack, where x can be any integer from
        // 1 to 32 (full word) inclusive
DUPx    //Duplicate the x-th stack item, where x can be any integer from
        // 1 to 16 inclusive
SWAPx   //Exchange 1st and (x+1)-th stack items, where x can be any
        // integer from 1 to 16 inclusive
```
### Process flow operations
```
STOP      //Halt execution
JUMP      //Set the program counter to any value
JUMPI     //Conditionally alter the program counter
PC        //Get the value of the program counter (prior to the increment
          //corresponding to this instruction)
JUMPDEST  //Mark a valid destination for jumps
```
### System operations
프로그램을 실행하는 시스템의 opcode:  
```
LOGx          //Append a log record with x topics, where x is any integer
              //from 0 to 4 inclusive
CREATE        //Create a new account with associated code
CALL          //Message-call into another account, i.e. run another
              //account's code
CALLCODE      //Message-call into this account with another
              //account's code
RETURN        //Halt execution and return output data
DELEGATECALL  //Message-call into this account with an alternative
              //account's code, but persisting the current values for
              //sender and value
STATICCALL    //Static message-call into an account
REVERT        //Halt execution, reverting state changes but returning
              //data and remaining gas
INVALID       //The designated invalid instruction
SELFDESTRUCT  //Halt execution and register account for deletion
```
### Logic operations
비교 및 비트 논리를 위한 Opcode:  
```
LT     //Less-than comparison
GT     //Greater-than comparison
SLT    //Signed less-than comparison
SGT    //Signed greater-than comparison
EQ     //Equality comparison
ISZERO //Simple NOT operator
AND    //Bitwise AND operation
OR     //Bitwise OR operation
XOR    //Bitwise XOR operation
NOT    //Bitwise NOT operation
BYTE   //Retrieve a single byte from a full-width 256-bit word
```
### Environmental operations
실행 환경 정보를 다루는 Opcode:  
```
GAS            //Get the amount of available gas (after the reduction for
               //this instruction)
ADDRESS        //Get the address of the currently executing account
BALANCE        //Get the account balance of any given account
ORIGIN         //Get the address of the EOA that initiated this EVM
               //execution
CALLER         //Get the address of the caller immediately responsible
               //for this execution
CALLVALUE      //Get the ether amount deposited by the caller responsible
               //for this execution
CALLDATALOAD   //Get the input data sent by the caller responsible for
               //this execution
CALLDATASIZE   //Get the size of the input data
CALLDATACOPY   //Copy the input data to memory
CODESIZE       //Get the size of code running in the current environment
CODECOPY       //Copy the code running in the current environment to
               //memory
GASPRICE       //Get the gas price specified by the originating
               //transaction
EXTCODESIZE    //Get the size of any account's code
EXTCODECOPY    //Copy any account's code to memory
RETURNDATASIZE //Get the size of the output data from the previous call
               //in the current environment
RETURNDATACOPY //Copy data output from the previous call to memory
```
### Block operations
현재 블록에 대한 정보에 액세스하기 위한 opcode:  
```
BLOCKHASH  //Get the hash of one of the 256 most recently completed
           //blocks
COINBASE   //Get the block's beneficiary address for the block reward
TIMESTAMP  //Get the block's timestamp
NUMBER     //Get the block's number
DIFFICULTY //Get the block's difficulty
GASLIMIT   //Get the block's gas limit
```

## Ethereum State
EVM의 역할은 이더리움 프로토콜에 정의된 스마트 계약 코드 실행의 결과로 유효한 상태 전환을 계산하여 이더리움 상태를 업데이트하는 것입니다.   
이 측면은 외부 행위자(예: 계정 소유자 및 채굴자)가 트랜잭션을 생성, 수락 및 주문함으로써 상태 전환을 시작한다는 사실을 반영 하는 트랜잭션 기반 상태 머신 으로 이더리움에 대한 설명으로 이어집니다.   
이 시점에서 무엇이 이더리움 상태를 구성하는지 고려하는 것이 유용합니다.

최상위 레벨에는 Ethereum 세계 상태가 있습니다.   
세계 상태는 이더리움 주소(160비트 값)를 계정에 매핑한 것입니다 .   
하위 수준에서 각 이더리움 주소는 이더 잔액 (계정이 소유한 wei 수로 저장됨), nonce (EOA일 경우 이 계정에서 성공적으로 전송된 거래 수 또는 계약 계정일 경우 이 계정에서 생성된 계약 수), 계정의 저장소 (영구적인 데이터 저장소이며 스마트 계약에서만 사용됨) 및 계정의 프로그램 코드 (다시 말하지만 계정이 스마트 계약 계정인 경우에만).   
EOA에는 항상 코드와 빈 저장소가 없습니다.

트랜잭션으로 인해 스마트 계약 코드가 실행되면 생성 중인 현재 블록 및 처리 중인 특정 트랜잭션과 관련하여 필요한 모든 정보로 EVM이 인스턴스화됩니다.   
특히, EVM의 프로그램 코드 ROM은 호출되는 계약 계정의 코드와 함께 로드되고, 프로그램 카운터는 0으로 설정되고, 스토리지는 계약 계정의 스토리지에서 로드되고, 메모리는 모두 0으로 설정되고, 모든 블록은 및 환경 변수가 설정됩니다.   
주요 변수는 이 실행을 위한 가스 공급이며 트랜잭션 시작 시 발신자가 지불한 가스의 양으로 설정됩니다.   
코드 실행이 진행됨에 따라 실행된 작업의 가스 비용에 따라 가스 공급이 감소합니다.   
어느 시점에서든 가스 공급이 0으로 줄어들면 " Out of Gas"(OOG) 예외가 발생합니다.   
실행이 즉시 중단되고 트랜잭션이 중단됩니다. 이더리움 상태에 대한 변경 사항은 적용되지 않습니다.   
단, 발신자의 nonce가 증가하고 이더 잔액이 감소하여 코드를 정지 지점까지 실행하는 데 사용되는 리소스에 대해 블록의 수혜자에게 지불해야 합니다.   
이 시점에서 어떤 이유로든 실행을 완료할 수 없는 경우 이 샌드박스 버전이 완전히 폐기되는 이더리움 세계 상태의 샌드박스 복사본에서 실행되는 EVM을 생각할 수 있습니다.   
그러나 실행이 성공적으로 완료되면 실제 상태가 샌드박스 버전과 일치하도록 업데이트됩니다.  

스마트 계약 자체가 트랜잭션을 효과적으로 시작할 수 있기 때문에 코드 실행은 재귀적 프로세스입니다.   
계약은 다른 계약을 호출할 수 있으며, 각 호출은 호출의 새 대상 주위에 다른 EVM이 인스턴스화되도록 합니다.   
각 인스턴스화에는 상위 수준의 EVM 샌드박스에서 초기화된 샌드박스 세계 상태가 있습니다.   
각 인스턴스화는 또한 가스 공급을 위해 지정된 양의 가스가 제공되며(물론 위 레벨에 남아 있는 가스의 양을 초과하지 않음) 실행을 완료하기 위해 너무 적은 가스가 제공되는 예외를 제외하고 자체적으로 중지될 수 있습니다.   
다시 말하지만, 이러한 경우 샌드박스 상태는 폐기되고 실행은 상위 수준의 EVM으로 돌아갑니다.  

## Compiling Solidity to EVM Bytecode
Solidity 소스 파일을 EVM 바이트코드로 컴파일하는 것은 여러 방법을 통해 수행할 수 있습니다.    
[intro_chapter] 에서 우리는 온라인 리믹스 컴파일러를 사용했다.   
이 장에서는 명령줄에서 실행 가능한 solc를 사용합니다.   
옵션 목록을 보려면 다음 명령을 실행하십시오.  
```
$ solc --help
```
--opcodes 명령줄 옵션을 사용하면 Solidity 소스 파일의 원시 opcode 스트림을 쉽게 생성할 수 있습니다.   
이 opcode 스트림은 일부 정보를 생략하지만(--asm 옵션은 전체 정보를 생성함) 이 논의에는 충분합니다.   
예를 들어, 예제 Solidity 파일 Example.sol 을 컴파일 하고 opcode 출력을 BytecodeDir 이라는 디렉토리로 보내는 것은 다음 명령으로 수행됩니다.  
```
$ solc -o BytecodeDir --opcodes Example.sol
```
or
```
$ solc -o BytecodeDir --asm Example.sol
```
다음 명령은 예제 프로그램에 대한 바이트 코드 바이너리를 생성합니다.  
```
$ solc -o BytecodeDir --bin Example.sol
```
생성된 출력 opcode 파일은 Solidity 소스 파일에 포함된 특정 계약에 따라 달라집니다.  
간단한 Solidity 파일 Example.sol 에는 example이라는 이름의 계약이 하나만 있습니다.  
```
pragma solidity ^0.4.19;

contract example {

  address contractOwner;

  function example() {
    contractOwner = msg.sender;
  }
}
```
보시다시피 이 계약이 하는 모든 일은 이 계약을 실행할 마지막 계정의 주소로 설정된 하나의 영구 상태 변수를 보유하는 것입니다.  
BytecodeDir 디렉토리 를 보면 예제 계약의 EVM opcode 지침이 포함된 opcode 파일 example.opcode 를 볼 수 있습니다.   
텍스트 편집기에서 example.opcode 파일을 열면 다음이 표시됩니다.  
```
PUSH1 0x60 PUSH1 0x40 MSTORE CALLVALUE ISZERO PUSH1 0xE JUMPI PUSH1 0x0 DUP1
REVERT JUMPDEST CALLER PUSH1 0x0 DUP1 PUSH2 0x100 EXP DUP2 SLOAD DUP2 PUSH20
0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF MUL NOT AND SWAP1 DUP4 PUSH20
0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF AND MUL OR SWAP1 SSTORE POP PUSH1
0x35 DUP1 PUSH1 0x5B PUSH1 0x0 CODECOPY PUSH1 0x0 RETURN STOP PUSH1 0x60 PUSH1
0x40 MSTORE PUSH1 0x0 DUP1 REVERT STOP LOG1 PUSH6 0x627A7A723058 KECCAK256 JUMP
0xb9 SWAP14 0xcb 0x1e 0xdd RETURNDATACOPY 0xec 0xe0 0x1f 0x27 0xc9 PUSH5
0x9C5ABCC14A NUMBER 0x5e INVALID EXTCODESIZE 0xdb 0xcf EXTCODESIZE 0x27
EXTCODESIZE 0xe2 0xb8 SWAP10 0xed 0x
```
--asm 옵션으로 예제를 컴파일하면 우리의 BytecodeDir 디렉토리에 apple.evm이라는 파일이 생성된다.  
여기에는 EVM 바이트 코드 지침에 대한 약간 더 높은 수준의 설명과 몇 가지 유용한 주석이 포함되어 있다.  
```
/* "Example.sol":26:132  contract example {... */
  mstore(0x40, 0x60)
    /* "Example.sol":74:130  function example() {... */
  jumpi(tag_1, iszero(callvalue))
  0x0
  dup1
  revert
tag_1:
    /* "Example.sol":115:125  msg.sender */
  caller
    /* "Example.sol":99:112  contractOwner */
  0x0
  dup1
    /* "Example.sol":99:125  contractOwner = msg.sender */
  0x100
  exp
  dup2
  sload
  dup2
  0xffffffffffffffffffffffffffffffffffffffff
  mul
  not
  and
  swap1
  dup4
  0xffffffffffffffffffffffffffffffffffffffff
  and
  mul
  or
  swap1
  sstore
  pop
    /* "Example.sol":26:132  contract example {... */
  dataSize(sub_0)
  dup1
  dataOffset(sub_0)
  0x0
  codecopy
  0x0
  return
stop

sub_0: assembly {
        /* "Example.sol":26:132  contract example {... */
      mstore(0x40, 0x60)
      0x0
      dup1
      revert

    auxdata: 0xa165627a7a7230582056b99dcb1edd3eece01f27c9649c5abcc14a435efe3b...
}
```
--bin-runtime 옵션은 기계가 읽을 수 있는 16진수 바이트코드를 생성합니다.  
```
60606040523415600e57600080fd5b336000806101000a81548173
ffffffffffffffffffffffffffffffffffffffff
021916908373
ffffffffffffffffffffffffffffffffffffffff
160217905550603580605b6000396000f3006060604052600080fd00a165627a7a7230582056b...
```
The EVM Instruction Set (Bytecode Operations)에 제공된 opcode 목록을 사용하여 여기에서 무슨 일이 일어나고 있는지 자세히 조사할 수 있습니다 .   
그러나 이는 상당히 어려운 작업이므로 처음 네 가지 지침을 검토하는 것으로 시작하겠습니다.  
```
PUSH1 0x60 PUSH1 0x40 MSTORE CALLVALUE
```
여기에 PUSH1 값이 0x60인 원시 바이트가 있습니다.   
이 EVM 명령어는 프로그램 코드에서 opcode 뒤에 오는 단일 바이트를 (리터럴 값으로) 스택에 푸시합니다.   
다음과 같이 최대 32바이트 크기의 값을 스택에 푸시할 수 있습니다.  
```
PUSH32 0x436f6e67726174756c6174696f6e732120536f6f6e20746f206d617374657221
```
example.opcode 의 두 번째 PUSH1 opcode 는 스택의 맨 위에 0x40을 저장합니다(이미 존재하는 0x60을 한 슬롯 아래로 푸시).  

다음은 EVM의 메모리에 값을 저장하는 메모리 저장 작업인 MSTORE입니다.   
두 개의 인수를 취하며 대부분의 EVM 작업과 마찬가지로 스택에서 인수를 가져옵니다.   
각 인수에 대해 스택이 "팝업"됩니다.   
즉, 스택의 최상위 값이 제거되고 스택의 다른 모든 값이 한 위치 위로 이동합니다.   
MSTORE의 첫 번째 인수는 저장할 값이 저장될 메모리의 단어 주소입니다.   
이 프로그램의 경우 스택 맨 위에 0x40이 있으므로 스택에서 제거되고 메모리 주소로 사용됩니다.   
두 번째 인수는 저장할 값으로 여기에서는 0x60입니다.  
MSTORE 작업이 실행된 후 스택은 다시 비어 있지만 메모리 위치 0x40에 값 0x60(십진수 96)이 있습니다.
다음 opcode는 CALLVALUE로, 이 실행을 시작한 메시지 호출과 함께 전송된 이더(wei로 측정)의 양을 스택의 맨 위에 푸시하는 환경 opcode입니다.
이 코드가 영향을 미치는 저수준 상태 변경을 완전히 이해할 때까지 이 방법으로 이 프로그램을 계속 진행할 수 있지만 이 단계에서는 도움이 되지 않습니다.   
이 장의 뒷부분에서 다시 다루겠습니다.  

## Contract Deployment Code
이더리움 플랫폼에서 새 계약을 생성하고 배포할 때 사용되는 코드와 계약 자체의 코드 사이에는 중요하지만 미묘한 차이가 있습니다.   
새 계약을 생성하려면 to 필드가 특수 0x0 주소로 설정되고 데이터 필드가 계약의 개시 코드로 설정된 특수 트랜잭션이 필요합니다.   
이러한 계약 생성 트랜잭션이 처리 될 때, 새로운 계약 계정 코드는 없다 트랜잭션의 데이터 필드에 있는 코드.   
대신 EVM은 프로그램 코드 ROM에 로드된 트랜잭션 데이터 필드의 코드로 인스턴스화되고 해당 배포 코드 실행의 출력은 새 계약 계정의 코드로 사용됩니다.   
이는 배포 시 이더리움 세계 상태를 사용하여 새 계약을 프로그래밍 방식으로 초기화하고, 계약 저장소에 값을 설정하고, 이더를 보내거나 추가로 새 계약을 생성할 수 있도록 하기 위한 것입니다.
예를 들어 명령줄에서 solc를 사용하여 오프라인으로 계약을 컴파일할 때 배포 바이트 코드 또는 런타임 바이트 코드 를 가져올 수 있습니다.  

배포 바이트 코드는 트랜잭션이 이 새 계약을 호출할 때 실제로 실행되는 바이트 코드(즉, 런타임 바이트 코드)와 계약의 모든 것을 초기화하는 코드를 포함하여 새 계약 계정 초기화의 모든 측면에 사용됩니다.  
반면에 런타임 바이트코드는 정확히 새 계약이 호출될 때 실행되는 바이트코드이며 그 이상은 아닙니다.   
배포 중 계약을 초기화하는 데 필요한 바이트 코드는 포함되지 않습니다.  
이전에 생성한 간단한 Faucet.sol 계약을 예로 들어 보겠습니다.  
```
// Version of Solidity compiler this program was written for
pragma solidity ^0.4.19;

// Our first contract is a faucet!
contract Faucet {

  // Give out ether to anyone who asks
  function withdraw(uint withdraw_amount) public {

      // Limit withdrawal amount
      require(withdraw_amount <= 100000000000000000);

      // Send the amount to the address that requested it
      msg.sender.transfer(withdraw_amount);
    }

  // Accept any incoming amount
  function () external payable {}

}
```
배포 바이트 코드를 얻으려면 solc --bin Faucet.sol.   
대신 런타임 바이트코드만 원하면.  
solc --bin-runtime Faucet.sol
이러한 명령의 출력을 비교하면 런타임 바이트 코드가 배포 바이트 코드의 하위 집합임을 알 수 있습니다.   
즉, 런타임 바이트 코드는 배포 바이트 코드 내에 완전히 포함됩니다.  

## Disassembling the Bytecode
EVM 바이트코드를 분해하는 것은 고수준 Solidity가 EVM에서 어떻게 작동하는지 이해하는 좋은 방법입니다.   
이를 위해 사용할 수 있는 몇 가지 디스어셈블러가 있습니다.  
+ Porosity 은 인기 있는 오픈 소스 디컴파일러입니다.  
+ Ethersplay 는 디스어셈블러인 Binary Ninja를 위한 EVM 플러그인입니다.  
+ IDA-Evm 은 또 다른 디스어셈블러인 IDA용 EVM 플러그인입니다.  
이 섹션에서는 Binary Ninja용 Ethersplay 플러그인을 사용 하고 Faucet 런타임 바이트코드 분해 를 시작 합니다.   
Faucet.sol 의 런타임 바이트코드를 가져온 후 Ethersplay 플러그인을 로드한 후 Binary Ninja에 입력하여 EVM 명령어가 어떻게 생겼는지 확인할 수 있습니다.  
![disassemble](https://github.com/ethereumbook/ethereumbook/raw/develop/images/Faucet_disassembled.png)  

![jumpi](https://github.com/ethereumbook/ethereumbook/raw/develop/images/Faucet_jumpi_instruction.png)  
ABI 호환 스마트 계약(모든 계약이 있다고 가정할 수 있음)에 트랜잭션을 보낼 때 트랜잭션은 먼저 스마트 계약의 디스패처 와 상호 작용 합니다.   
디스패처는 트랜잭션의 데이터 필드를 읽고 관련 부분을 적절한 기능으로 보냅니다.   
분해된 Faucet.sol 런타임 바이트코드 의 시작 부분에서 디스패처의 예를 볼 수 있습니다.   
익숙한 MSTORE 명령 후에 다음 지침이 표시됩니다.  
```
PUSH1 0x4
CALLDATASIZE
LT
PUSH1 0x3f
JUMPI
```
우리가 보았듯이 PUSH1 0x4는 스택의 맨 위에 0x4를 배치합니다.   
그렇지 않으면 비어 있습니다.   
CALLDATASIZE는 트랜잭션( calldata 라고 함 )과 함께 전송된 데이터의 크기를 바이트 단위로 가져오고 해당 숫자를 스택에 푸시합니다.   
이러한 작업이 실행된 후 스택은 다음과 같습니다.  
| Stack |  
|----|  
|<length of calldata from tx>|  
| Ox4  |  
|----|

# Turing Completeness and Gas

## Gas
## Gas Accounting During Execution
## Gas Accounting Considerations
## Gas Cost Versus Gas Price

### Negative gas costs
## Block Gas Limit
### Who decides what the block gas limit is?
# Conclusions
