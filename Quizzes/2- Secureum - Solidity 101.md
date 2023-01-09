https://github.com/x676f64/secureum-mind_map/tree/master/quizzes

# 02 - Solidity 101

### Q1 Solidity language is

- [ ]  A) Statically typed
- [ ]  B) Object-oriented
- [ ]  C) Supports inheritance
- [ ]  D) Supports inline assembly

> All the above answer are true for Solidity.
>- Statically typed means a developer must define for each variable a specific type (while langage like python or javascript define types on runtime)
>- Object oriented and inheritance usually comes together, but inheritance isn’t required for a langage to be considered OO. In Solidity, contract can inherit from other contracts.
Finally, Solidity support inline assembly using the assembly block `assembly {...}`. The assembly code is written in Yul.
> 

---

### Q2 Which of the following is/are correct?

- [ ]  A) A Solidity file with pragma solidity ^0.6.5; can be compiled with compiler version 0.6.6
- [ ]  B) A Solidity file with pragma solidity 0.6.5; can only be compiled with compiler version 0.6.5
- [ ]  C) A Solidity file with pragma solidity ^0.6.5; can be compiled with compiler version 0.7.0
- [ ]  D) A Solidity file with pragma solidity >0.6.5 <0.7.0; can be compiled with compiler version 0.7.0

> pragma are used to restrict compilation to certain compiler version.
>- If a version is written without ^ or <>, only this version is compatible.
>- If its using ^X.Y.Z , it can be compiled for any version Z, for X and Y fixed.
>- if using >M<N, version must be comprised between M (included) and N (not included). It is also possible to use >= and <=
> 

---

### Q3 Which of the following is/are true?

- [ ]  A) Constant state variables can be initialized within a constructor
- [ ]  B) Immutable state variables are allocated a storage slot
- [ ]  C) Gas costs for constant and immutable variables is lower
- [ ]  D) Only value types can be immutable

> Constant state variable are set at compile time, and their value must be initialized when declared
Immutable state variable **can** be declared uninitialized, but if its the case, they **must** be set within the constructor
Immutable and constant variable have been introduced with version 0.6.5, and have the big advantage that reading to them is really cheap as they are not stored in storage but in bytecode. This means during compilation, every time this variable is read, the code will replace the read by its plain value.
In fact, if a function only “reads” from a constant variable, it can be defined as pure. This is not the case for immutables ([read this](https://ethereum.stackexchange.com/questions/120612/why-are-functions-that-access-immutable-variables-not-pure))
>

___
### Q4 Solidity functions

- [ ]  A) Can be declared only inside contracts
- [ ]  B) Can have named return variables
- [ ]  C) Can have unnamed parameters
- [ ]  D) Can be recursive

> Function can also be declared inside a [Library](https://solidity-by-example.org/library/)
It is possible to have named parameters, this allow us to return the named parameters automatically, without calling `return` at the end
Its also possible have unnamed parameters, and it is possible to use inline assembly to get values out the calldata space
Finally, it is possible to use recursivity inside a function
> 

---

### Q5 Function visibility

- [ ]  A) Goes from private-internal-external-public in decreasing restrictive order (i.e. private being the most restrictive)
- [ ]  B) Goes from internal-private-external-public in decreasing restrictive order (i.e. internal being the most restrictive)
- [ ]  C) May be omitted to default to internal in the latest 0.8.0+ compiler versions
- [ ]  D) None of the above

> public - any contract and account can call 
private - only inside the contract that defines the function 
internal- only inside contract that inherits an internal function 
external - only other contracts and accounts can call State variables can be declared as public, private, or internal but not external
Visibility must be declared for function, while it can be omitted for variable, which will be defaulted to public
> 

---

### Q6 Function foo() uses block.number. Which of the following is/are always true about foo()?

- [ ]  A) It should be marked as pure
- [ ]  B) It should be marked as view
- [ ]  C) It should be marked as payable
- [ ]  D) Cannot determine mutability based only on this information

> block.number is a global state variable, reading this value makes it at least `view`, pure is not possible as it does not allow neither to write or read from state variable
> 

---

### Q7 Which of the following is/are true above events?

- [ ]  A) Events are meant for off-chain applications
- [ ]  B) Events can be accessed only by the emitting contract
- [ ]  C) Indexing event parameters creates searchable topics
- [ ]  D) A maximum of three events can have indexed parameters

> Events have the role of informing services outside of the blockchain to let users know that something has happened.
Events can have up to 3 `indexed` parameters, making them indexes to search through the event log
> 

---

### Q8 A contract can receive Ether via

- [ ]  A) msg.value to payable functions
- [ ]  B) selfdestruct destination
- [ ]  C) coinbase transaction
- [ ]  D) receive() or fallback() functions

> If a function is declared as payable, it can receive ether. If it does not have payable functions, then it is still possible to send ether using their `receive()` or `fallback()` functions.
If none of them are implemented, a contract can still receive ether by being the recipient of the `selfdestruct(address recipient)` function. This is in fact a possible [vulnerability](https://consensys.github.io/smart-contract-best-practices/development-recommendations/general/external-calls/#dont-delegatecall-to-untrusted-code) in a contract
Finally, it can also receive ETH from a coinbase transaction
>
___
#### Q9 receive() and fallback() functions

- [ ] A) Can rely only on 2300 gas in the worst case

- [ ] B) May receive Ether with payable mutability

- [ ] C) Are mandatory for all contracts

- [ ] D) Must have external visibility


___
#### Q10 Which of the below are value types?

- [ ] A) Address

- [ ] B) Enum

- [ ] C) Struct

- [ ] D) Contract



___
#### Q11 The default value of

- [ ] A) Bool is false

- [ ] B) Address is 0

- [ ] C) Statically-sized array depends on the underlying type

- [ ] D) Enum is its first member


___
#### Q12 Address types

- [ ] A) Can always receive Ether

- [ ] B) Have members for balance, call, code

- [ ] C) Can be converted to uint160 or contract types

- [ ] D) Can be added and subtracted


___
#### Q13 transfer and send primitives

- [ ] A) Are used for Ether transfers

- [ ] B) Trigger the receive() or fallback() functions of address

- [ ] C) Always return a value to be checked

- [ ] D) Provide only 2300 gas


___
#### Q14 Which of the following is/are true for call/delegatecall/staticcall primitives?

- [ ] A) They are used to call contracts

- [ ] B) They only revert without returning success/failure

- [ ] C) Delegatecall retains the msg.sender and msg.value of caller contract

- [ ] D) Staticcall reverts if the called contract reads contract state of caller


___
#### Q15 If we have an array then its data location can be

- [ ] A) memory and its persistence/scope will be the function of declaration

- [ ] B) storage and its persistence/scope will be the entire contract

- [ ] C) calldata and it will only be readable

- [ ] D) None of the above



___
#### Q16 The impact of data location of reference types on assignments is

- [ ] A) storage assigned to storage (local variable) makes a copy

- [ ] B) memory assigned to memory makes a copy

- [ ] C) memory assigned to storage creates a reference

- [ ] D) None of the above

___
#### Q17 Which of the following is/are valid control structure(s) in Solidity (excluding YUL)?

- [ ] A) if

- [ ] B) else

- [ ] C) elif

- [ ] D) switch

___
#### Q18 The gas left in the current transaction can be obtained with

- [ ] A) tx.gas()

- [ ] B) gasleft()

- [ ] C) msg.gas()

- [ ] D) block.gaslimit()

___
#### Q19 Which of the following is/are valid function specifier(s)?

- [ ] A) internal

- [ ] B) pure

- [ ] C) payable

- [ ] D) immutable

___
#### Q20 Integer overflows/underflows in Solidity

- [ ] A) Are never possible because of the language design

- [ ] B) Are possible but prevented by compiler added checks (version dependent)

- [ ] C) Are possible but prevented by correctly using certain safe math libraries

- [ ] D) Are possible without any mitigation whatsoever
___
#### Q21 Arrays in Solidity

- [ ] A) Can be fixed size or dynamic

- [ ] B) Are zero indexed

- [ ] C) Have push, pop and length members

- [ ] D) None of the above

___
#### Q22 Structs in Solidity

- [ ] A) Are user-defined type

- [ ] B) Are reference types

- [ ] C) Can contain or be contained in arrays and mappings

- [ ] D) None of the above


___
#### Q23 Which of the following is true about mapping types in mapping(_KeyType => _ValueType)?

- [ ] A) _KeyType can be any value or reference type

- [ ] B) _ValueType can be any value or reference type

- [ ] C) Can only have storage (not memory) as data location

- [ ] D) Can be iterated over natively (i.e. without implementing another data structure)


___
#### Q24 if a = 1 then which of the following is/are true?

- [ ] A) a += 1 makes the value of a = 2

- [ ] B) b = ++a makes the value of b = 1

- [ ] C) a -= 1 makes the value of a = 1

- [ ] D) b = a-- makes the value of b = 1


___
#### Q25 delete varName; has which of the following effects?

- [ ] A) varName becomes 0 if varName is an integer

- [ ] B) varName becomes true if varName is a boolean

- [ ] C) No effect if varName is a mapping

- [ ] D) Resets all struct members to their default values irrespective of their types


___
#### Q26 Conversions in Solidity have the following behavior

- [ ] A) Implicit conversions are never allowed

- [ ] B) Explicit conversion of uint16 to uint8 removes higher-order bits

- [ ] C) Explicit conversion of uint16 to uint32 adds lower-order padding

- [ ] D) Explicit conversions are checked by compiler for safety


___
#### Q27 If the previous block number was 1000 on Ethereum mainnet, which of the following is/are true?

- [ ] A) block.number is 1001

- [ ] B) blockhash(1) returns 0

- [ ] C) block.chainID returns 1

- [ ] D) block.timestamp returns the number of seconds since last block


___
#### Q28 User from EOA A calls Contract C1 which makes an external call (CALL opcode) to Contract C2. Which of the following is/are true?

- [ ] A) tx.origin in C2 returns A’s address

- [ ] B) msg.sender in C2 returns A’s address

- [ ] C) msg.sender in C1 returns A’s address

- [ ] D) msg.value in C2 returns amount of Wei sent from A

___
#### Q29 For error handling

- [ ] A) require() is meant to be used for input validation

- [ ] B) require() has a mandatory error message string

- [ ] C) assert() is meant to be used to check invariants

- [ ] D) revert() will abort and revert state changes

___
#### Q30 The following is/are true about ecrecover primitive

- [ ] A) Takes a message hash and ECDSA signature values as inputs

- [ ] B) Recovers and returns the public key of the signature

- [ ] C) Is susceptible to malleable signatures

- [ ] D) None of the above

___
#### Q31 When Contract A attempts to make a delegatecall to Contract B but a prior transaction to Contract B has executed a selfdestruct

- [ ] A) The delegatecall reverts

- [ ] B) The delegatecall returns a failure

- [ ] C) The delegatecall returns a success

- [ ] D) This scenario is not practically possible

___
#### Q32 In Solidity, selfdestruct(address)

- [ ] A) Destroys the contract whose address is given as argument

- [ ] B) Destroys the contract executing the selfdestruct

- [ ] C) Sends address’s balance to the calling contract

- [ ] D) Sends executing contract’s balance to the address

