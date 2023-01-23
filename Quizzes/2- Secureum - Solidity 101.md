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
[Since The Merge](https://ethereum.stackexchange.com/a/138595/105102), there’s no more things as coinbase transaction, but before The Merge a contract could receive ETH this way.
> 

---

### Q9 receive() and fallback() functions

- [ ]  A) Can rely only on 2300 gas in the worst case
- [ ]  B) May receive Ether with payable mutability
- [ ]  C) Are mandatory for all contracts
- [ ]  D) Must have external visibility

> `receive`and `fallback`functions when called using `transfer` or `send` will rely on 2300 gas. But if called using `call` will get forwarded all the remaining gas. The latter can be dangerous if not used with re-entrancy guards, as `fallback` and `receive` are a common source of re-entrancy.
 `receive` must be payable as this function is called when ETH are sent without msg.data, but fallback do not need to be payable. 
These functions must be external. As they are called by external contracts, this make no sense for them to be public/internal/private.
A contract is valid even if none of these 2 functions are implemented.
> 

---

### Q10 Which of the below are value types?

- [ ]  A) Address
- [ ]  B) Enum
- [ ]  C) Struct
- [ ]  D) Contract

> There’s basically 2 big group of types in Solidity : Values and References.
Because Arrays, Mappings and Struct stores multiple values, a reference (pointer) to their first element is more suitable to use if we want to access to only a portion of them.
Contracts are a special type, each contract define its own type, and are part of the value types group
>
___
### Q11 The default value of

- [ ]  A) Bool is false
- [ ]  B) Address is 0
- [ ]  C) Statically-sized array depends on the underlying type
- [ ]  D) Enum is its first member

> The information can be found by testing in remix. Worth noting that the `delete var` operator assign the default value to `var`
> 

---

### Q12 Address types

- [ ]  A) Can always receive Ether
- [ ]  B) Have members for balance, call, code
- [ ]  C) Can be converted to uint160 or contract types
- [ ]  D) Can be added and subtracted

> Address type can be declared as address [payable](https://docs.soliditylang.org/en/develop/types.html#members-of-addresses), which adds to it the send and transfer members. But `call`is accessible on non payable address, which makes it still possible to send Ether to it using `address.call{value:amount}("")`. But we must not forget that behind addresses, can be present either an EOA or a contract. While EOA can always receive Ether using `call`, this is not true for contract which needs to implement at least a `receive` function or a payable function.
As an address is encoded on 20 bytes, it can explicitly be converted to `uint160` or `bytes20`.
Also, an address can be converted to a contract, and a contract can be converted to an address ([link](https://docs.soliditylang.org/en/develop/types.html#contract-types))
Finally, address are not compatible with arithmetic operators, you first need to convert the address to a uint
>

___
### Q13 transfer and send primitives

- [ ]  A) Are used for Ether transfers
- [ ]  B) Trigger the receive() or fallback() functions of address
- [ ]  C) Always return a value to be checked
- [ ]  D) Provide only 2300 gas

> Both functions are used to transfer Ether to a recipient. As these function do not target any methods in a contract, the contract needs to have [at least](https://solidity-by-example.org/sending-ether/) the `fallback()` function implemented, but it is a better practice to have both as they can handle 2 different cases.
While transfer and send have the same gas behavior (provide only 2300 gas for re-entrancy protection), they return a different value : transfer throw error if there’s an issue, while send return a bool.
> 

---

### Q14 Which of the following is/are true for call/delegatecall/staticcall primitives?

- [ ]  A) They are used to call contracts
- [ ]  B) They only revert without returning success/failure
- [ ]  C) Delegatecall retains the msg.sender and msg.value of caller contract
- [ ]  D) Staticcall reverts if the called contract reads contract state of caller

> these three function are used by a contract to call either other’s contract function, or why not its own functions.
These 3 functions have a different use that you can read [here](https://solidity-by-example.org/call/), [here](https://solidity-by-example.org/delegatecall/) and [here](https://medium.com/coinmonks/call-staticcall-and-delegatecall-1f0e1853340)
>
___
### Q15 If we have an array then its data location can be

- [ ]  A) memory and its persistence/scope will be the function of declaration
- [ ]  B) storage and its persistence/scope will be the entire contract
- [ ]  C) calldata and it will only be readable
- [ ]  D) None of the above

> Q10 talks about value types, in this question we are talking about reference types. When using one of these types, a location must be assigned to them. The three locations are listed here.
More details on the [solidity doc](https://docs.soliditylang.org/en/v0.8.17/types.html#data-location-and-assignment-behaviour)
> 

---

### Q16 The impact of data location of reference types on assignments is

- [ ]  A) storage assigned to storage (local variable) makes a copy
- [ ]  B) memory assigned to memory makes a copy
- [ ]  C) memory assigned to storage creates a reference
- [ ]  D) None of the above

> If you follow the link in Q15 you will find the answer written. But basically, copy are made when assigning a data from a location to another. This is pretty logical as these are located in different space on the network
> 

---

### Q17 Which of the following is/are valid control structure(s) in Solidity (excluding YUL)?

- [ ]  A) if
- [ ]  B) else
- [ ]  C) elif
- [ ]  D) switch

> elif and switch do not exists in solidity, but [else if](https://solidity-by-example.org/if-else/) works !
> 

---

### Q18 The gas left in the current transaction can be obtained with

- [ ]  A) tx.gas()
- [ ]  B) gasleft()
- [ ]  C) msg.gas()
- [ ]  D) block.gaslimit()

> `tx.gas()` do not exists, but `tx.gasprice` exists.
`gasleft()` return the remaining gas, and has replaced `msg.gas` (without parenthesis) which is deprecated since `0.4.21`
Finally, `block.gaslimit()` does not exists, this is not a function but a global variable, it should be called using `block.gaslimit`
> 

---

### Q19 Which of the following is/are valid function specifier(s)?

- [ ]  A) internal
- [ ]  B) pure
- [ ]  C) payable
- [ ]  D) immutable

> immutable is a specifier for variables only, as is constant
> 

---

### Q20 Integer overflows/underflows in Solidity

- [ ]  A) Are never possible because of the language design
- [ ]  B) Are possible but prevented by compiler added checks (version dependent)
- [ ]  C) Are possible but prevented by correctly using certain safe math libraries
- [ ]  D) Are possible without any mitigation whatsoever

> `0.8.0` introduced [automatic overflow/underflow checks](https://docs.soliditylang.org/en/v0.8.17/080-breaking-changes.html). This introduce a slight increase in gas usage, but if you’re sure your value cannot overflow (could be in a for loop where i is known) you can use the `unchecked{...}` structure to use the previous behavior.
Before `0.8.0` developers needed to use safe maths libararies like Open Zeppelin’s one.
> 

---

### Q21 Arrays in Solidity

- [ ]  A) Can be fixed size or dynamic
- [ ]  B) Are zero indexed
- [ ]  C) Have push, pop and length members
- [ ]  D) None of the above

> Array in solidity can either be fixed size by declaring their number of elements `array[N]` or dynamic by not declaring it `array[]` 
Their index starts at zero (you hear me Matlab ?), but push/pop/length are members of dynamic sized array only.
> 

---

### Q22 Structs in Solidity

- [ ]  A) Are user-defined type
- [ ]  B) Are reference types
- [ ]  C) Can contain or be contained in arrays and mappings
- [ ]  D) None of the above

> Structs are references types as are arrays, thus developers needs to assign them a location. 
When declaring a struct `A`, this in fact create a new type `A`
A struct can be composed of any other types (uint/strings/arrays/mappings/…), but not their own type.
>


___
### Q23 Which of the following is true about mapping types in mapping(_KeyType => _ValueType)?

- [ ]  A) _KeyType can be any value or reference type
- [ ]  B) _ValueType can be any value or reference type
- [ ]  C) Can only have storage (not memory) as data location
- [ ]  D) Can be iterated over natively (i.e. without implementing another data structure)

> This does not really make sense to have a “reference type” key, as the keys is a getter to get the value. [See doc here](https://docs.soliditylang.org/en/v0.8.17/types.html#mapping-types) for more details
> 

---

### Q24 if a = 1 then which of the following is/are true?

- [ ]  A) a += 1 makes the value of a = 2
- [ ]  B) b = ++a makes the value of b = 1
- [ ]  C) a -= 1 makes the value of a = 1
- [ ]  D) b = a-- makes the value of b = 1

> Everything is [explained here](https://docs.soliditylang.org/en/v0.8.17/types.html#compound-and-increment-decrement-operators) in solidity doc :
`a += e` is equivalent to `a = a + e
a++` and `a--` are equivalent to `a += 1` / `a -= 1` but the expression itself still has the previous value of `a`
In contrast, `--a` and `++a` have the same effect on `a` but return the value after the change.
> 

---

### Q25 delete varName; has which of the following effects?

- [ ]  A) varName becomes 0 if varName is an integer
- [ ]  B) varName becomes true if varName is a boolean
- [ ]  C) No effect if varName is a mapping
- [ ]  D) Resets all struct members to their default values irrespective of their types

> Seems like these questions are taken in the same order [as the documentation](https://docs.soliditylang.org/en/v0.8.17/types.html#delete) on types !
But in a nutshell, `delete` set variables to their default value, which does not works for mappings getting their nature. For structs, this works the same way as variables, in a sense that each element of the struct is set to its default value
> 

---

### Q26 Conversions in Solidity have the following behavior

- [ ]  A) Implicit conversions are never allowed
- [ ]  B) Explicit conversion of uint16 to uint8 removes higher-order bits
- [ ]  C) Explicit conversion of uint16 to uint32 adds lower-order padding
- [ ]  D) Explicit conversions are checked by compiler for safety

> Explicit conversion means casting a value to another one using `type(value)` , for example `uint16(x)`. This is allowed in most case but developers must do it carefully as this could lead to an unexpected result. 
Implicit conversion is allowed but not always depending on the cases, when it can lead to unexpected results its usually disallowed, for example when converting `int` to `uint`
> 

---

### Q27 If the previous block number was 1000 on Ethereum mainnet, which of the following is/are true?

- [ ]  A) block.number is 1001
- [ ]  B) blockhash(1) returns 0
- [ ]  C) block.chainID returns 1
- [ ]  D) block.timestamp returns the number of seconds since last block

> All these values are called [globally available variables](https://docs.soliditylang.org/en/v0.8.17/units-and-global-variables.html) and are described in the linked documentation
> 

---

### Q28 User from EOA A calls Contract C1 which makes an external call (CALL opcode) to Contract C2. Which of the following is/are true?

- [ ]  A) tx.origin in C2 returns A’s address
- [ ]  B) msg.sender in C2 returns A’s address
- [ ]  C) msg.sender in C1 returns A’s address
- [ ]  D) msg.value in C2 returns amount of Wei sent from A

> 2 types of calls regarding execution context :
- CALL : if A calls B, the call executes in A’s context
- DELEGATECALL : if A calls B, the call executes in B’s context
But tx.origin will always returns the EOA’s address
> 

---

### Q29 For error handling

- [ ]  A) require() is meant to be used for input validation
- [ ]  B) require() has a mandatory error message string
- [ ]  C) assert() is meant to be used to check invariants
- [ ]  D) revert() will abort and revert state changes

> `require()` , `assert()` and `revert()` are the 3 ways a [contract can revert](https://docs.soliditylang.org/en/v0.8.17/control-structures.html#assert-and-require). But they should be used differently. Since [version 0.8.4](https://blog.soliditylang.org/2021/04/21/custom-errors/) custom [errors](https://docs.soliditylang.org/en/v0.8.17/structure-of-a-contract.html#errors) can be used with the `revert` statement, and can be more gas efficient and dynamic than using strings
> 

---

### Q30 The following is/are true about ecrecover primitive

- [ ]  A) Takes a message hash and ECDSA signature values as inputs
- [ ]  B) Recovers and returns the public key of the signature
- [ ]  C) Is susceptible to malleable signatures
- [ ]  D) None of the above

> `ecrecover(bytes32 hash, uint8 v, bytes32 r, bytes32 s) returns (address)` return the Ethereum address associated with the public key. This function should be used with caution because of [signature malleability](https://medium.com/immunefi/intro-to-cryptography-and-signatures-in-ethereum-2025b6a4a33d)
> 

---

### Q31 When Contract A attempts to make a delegatecall to Contract B but a prior transaction to Contract B has executed a selfdestruct

- [ ]  A) The delegatecall reverts
- [ ]  B) The delegatecall returns a failure
- [ ]  C) The delegatecall returns a success
- [ ]  D) This scenario is not practically possible

> delegatecall returns 2 values : `(bool success, bytes data)` , the success will be true if the function execute without reverting [OR if the account has no code](https://www.evm.codes/#f1?fork=arrowGlacier) (EOA do not have code) 
If a contract has selfdestructed, then its bytecode is deleted, which means it has no code, which returns true
> 

---

### Q32 In Solidity, selfdestruct(address)

- [ ]  A) Destroys the contract whose address is given as argument
- [ ]  B) Destroys the contract executing the selfdestruct
- [ ]  C) Sends address’s balance to the calling contract
- [ ]  D) Sends executing contract’s balance to the address

> `selfdestruct` delete the contract executing it, so be careful when using delegatecall, as [it would destroy the calling contract](https://ethereum.stackexchange.com/questions/86576/how-to-call-selfdestruct-if-not-written-in-contract-as-documentation-says-its)
>