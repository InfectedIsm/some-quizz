# 03 - Solidity 201

**Q1 Which of the following is/are true about abstract contracts and interfaces?**

A) Abstract contracts have at least one function undefined

B) Interfaces can have some functions defined

C) Unimplemented functions in abstract contracts need to be declared virtual

D) All functions are implicitly virtual in interfaces

> If a contract has at least one `virtual` function it must be defined as `abstract`, which means an inheriting contract will have to implement this function. But a contract can be declared abstract and not having a virtual function.
Interfaces are another type of contract, they can be seen as header files in C. Functions are not defined, but their prototype is, making it easier to interact with an already deployed contract. rather than using `call()`. For this, a deployed contract address can be casted using the interface, giving the developer access to the declared method in the interface. 
Also good to not, functions in interfaces can be overriden (they are implicitly `virtual`)
[Solidity doc reference](https://docs.soliditylang.org/en/v0.6.2/contracts.html#abstract-contracts)
> 

---

**Q2 Libraries are contracts**

A) That cannot have state variables

B) That cannot be inherited

C) That always require a delegatecall

D) That are not meant to receive Ether

> Libraries are meant to be used with `delegatecall` , because of the nature of this call (executing in the caller storage context), they shouldn’t and cannot (compiler error) have non constant state variables as this could lead to unexpected results in caller.
This make the use of `constant` variable possible as they are hardcoded in bytecode, and same for `memory` variables.
Libraries cannot be inherited, this is not what they are made for. But they can be imported, and then library functions can be called like you would call a function from a contract ( `Lib.func()`)
Finally, library functions cannot be `payable`
[Solidity doc reference](https://docs.soliditylang.org/en/v0.6.2/contracts.html#libraries)
> 

---

**Q3 Storage layout**

A) Refers to the layout of state variables in storage

B) Is organized in 256-byte slots

C) Is packed for value types that use less than 32 bytes

D) Always starts on a new slot for reference types

> Everything is present in the first part of this [doc](https://docs.soliditylang.org/en/v0.8.17/internals/layout_in_storage.html)
Details on why references types are stored in a new slot [here](https://docs.soliditylang.org/en/v0.8.17/internals/layout_in_storage.html#mappings-and-dynamic-arrays) 
But to make it simple, as dynamic variables have unpredictable size, they cannot be stored in a packed way, the values they reference to are stored in a data location calculated using [the hash of the slot + other values], where the reference object is stored. If 2 different dynamic variables were stored in a same slot, there’s a possibility that [the hash of their slot + other values] are the same, thus implying the values they references to will overlap
> 

---

**Q4 For contract A {uint256 i; bool b1; bool b2; address a1;} the number of storage slots used is**

A) 4

B) 3

C) 2

D) 1

> Variables are packed in [32-bytes wide](https://docs.soliditylang.org/en/v0.8.17/internals/layout_in_storage.html) (or 256-bits) slots. This means `i` will take 1 slot for himself first. Adresses take 20-bytes, and for bool, well, I had to check by myself as I couldn’t find the answer, and seems they take 2-bytes as shown by these Remix screenshot I took (we see 2 storages slots, and we can see the values in there) :
> 
> 
> ![Untitled](./images/3-Solidity_101_Q4_1.png)
> 
> ![Untitled](./images/3-Solidity_101_Q4_2.png)
> 

---

**Q5 Which of the following is/are generally true about storage layouts?**

A) The number of slots used for a contract depends on the ordering of state variable declarations

B) The slots for struct elements are consecutive

C) The slot s for dynamic array contains the length with individual elements stored consecutively in slots starting at keccak256(s)

D) The slot s for mapping is empty with individual values stored consecutively in slots starting at keccak(h(k).s), where k is the first key and h is a hash function that depends on type of k

> Slots are 32-bytes wide, and variables are packed following [this rule](https://docs.soliditylang.org/en/v0.8.17/internals/layout_in_storage.html), so state variable ordering is important to optimize storage size, thus gas usage when deploying the contract.
Struct elements always starts a new slot, and their items are packed accordingly to the storage rules.
Rules for dynamic arrays and mappings are [here](https://docs.soliditylang.org/en/v0.8.17/internals/layout_in_storage.html#mappings-and-dynamic-arrays)
> 

---

**Q6 EVM memory**

A) Is linear and byte-addressable

B) Is reserved by Solidity until 0x7f

C) Can be accessed in bytes using MLOAD8/MSTORE8

D) Is non-volatile or persistent

> A linear memory model refers to a memory organized in a single contiguous address space, which is the case for EVM, where each elements are uint8 (its a byte array under the hood) Elements can be stored in 32 bytes or 1 byte chunks, but only read in 32 bytes chunks.
> 
> 
> Solidity’s memory layout reserves four 32-byte slots:
> 
> 0x00 - 0x3f (64 bytes): scratch space
> 
> 0x40 - 0x5f (32 bytes): free memory pointer
> 
> 0x60 - 0x7f (32 bytes): zero slot
> 
> Memory is temporary place used during function calls and only persist during the call
> 

---

**Q7 EVM inline assembly has**

A) Its own language called Yul

B) Safety checks just like Solidity

C) Access to all variables in the contract and function where present

D) References to variables as their addresses not values

> When there’s need to optimize a portion of code, inline assembly must be used. Its language is called Yul, and must be written inside this block : `assembly { ... }` .
This is a low level language close to the EVM opcodes, hence it should be use with caution as no safety guard are present.
It is possible to access to any variable in the contract as described [in the doc](https://docs.soliditylang.org/en/v0.8.17/assembly.html)
Depending on the type of the variable, the evaluated value can either be the variable value or its address
> 

---
**Q8 Zero address check is typically recommended because**

A) The use of zero address for transfers will trigger an EVM exception

B) Ether/tokens sent to zero address will be inaccessible

C) Ether/tokens sent to zero address can be accessed by anyone

D) Address 0 is the Ethereum Masternode account and is forbidden for access

> Address zero must be checked because because any tokens or ETH sent to that address will be lost. This is error could occur either because :
>- a contract interacting with the tokens fails to correctly fill the parameter
>- a user fails when typing the address in the UI
>- a front app fails to correctly fill the parameter
If anybody get the private key to that address, no question [he will be rich](https://etherscan.io/address/0x0000000000000000000000000000000000000000) ! (good luck though)
> 

---

**Q9 ERC20 transferFrom(address sender, address recipient, uint256 amount) (that follows the ERC20 spec strictly)**

A) Transfers token amount from sender to recipient

B) sender must have given caller (msg.sender) approval for at least amount or more

C) Deducts amount from sender’s allowance

D) Deducts amount from caller’s (msg.sender’s) allowance

> transferFrom is called by a caller, to transfer tokens on behalf of sender, to the recipient. For this to be possible, sender must have approved the caller at least for the amount the caller wants to transfer. Once amount is transfered, the allowance of the caller in regard to the sender is reduced by the amount.
> 

---

**Q10 OpenZeppelin SafeERC20 is generally considered safer to use than ERC20 because**

A) It adds integer overflow/underflow checks

B) It adds return value/data checks

C) It adds pause/unpause capability

D) It adds race-condition checks

> SafeERC20 is a wrapper around ERC20 functions (for example {SafeERC20.SafeTransfer} is wrapped around {ERC20.transfer}) and makes sure that :
(1). the call reverts if the function return false
(2). calls is successful for function no returning values (if not using SafeERC20 this could lead to a revert if not handled properly)
[contract here](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/utils/SafeERC20.sol)
>
---

**Q11 OpenZeppelin ERC20Pausable**

A) Adds ability to pause token transfers

B) Adds ability to pause token minting and burning

C) Provides modifiers whenPaused and whenNotPaused

D) None of the above

> OpenZeppelin ERC20Pausable add some sort of security layer that allow authorized addresses (this can be the owner, or specific roles if conbined with [AccessControl](https://docs.openzeppelin.com/contracts/4.x/api/access#AccessControl) module) to pause and unpause any function of the contract, as far as these function have the `whenPause` and `whenNotPaused` modifier
>

---
**Q12 OpenZeppelin ERC721**

A) Implements the NFT standard
B) safeTransferFrom(..) checks for zero-addresses
C) approve(..) is susceptible to race-condition just like ERC20
D) setApprovalForAll(address operator, bool _approved) approves/removes operator for all of caller’s tokens

> ERC721 is an implementation of [EIP-721](https://eips.ethereum.org/EIPS/eip-721), this EIP defines an interface that must be followed by a contract to be ERC721-compliant. Worth to note that to be ERC721-comp, the contract must also implement [ERC165 interface](https://eips.ethereum.org/EIPS/eip-165)
`SafeTransferFrom` checks for zero-address as it calls `_transfer` where the this verification is done. If the recipient is a contract, it also checks if the contract is capable of receiving the NFT (to prevent it to be stuck forever) with the `_checkOnERC721Received` function. The recipient contract must implement the `[IERC721Receiver](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC721/IERC721Receiver.sol)`interface to be considered valid.
`approve` is not susceptible to [race-condition](https://swcregistry.io/docs/SWC-114) as it only approve one tokenID
`setApprovalForAll` should be used with caution as it allow the operator to control all caller’s token
>
---

**Q13 ERC777 may be considered as an improved version of ERC20 because**

A) Hooks allow reacting to token mint/burn/transfer
B) It can help avoid separate approve and transferFrom transactions
C) It can help prevent tokens getting stuck in contracts
D) It removes reentrancy risk

> ERC777 aim to improve ERC20 by adding (1) hooks : `tokenToSend` and `tokenReceived`, which are functions that are called by the ERC777 token when received by a contract which can enable multiple functionality, as atomic purchase (the contract can react to the transfer and directly use the token), rejecting some tokens (by reverting the call), redirecting the token, …
An ERC777 token can chose to revert if the recipient address cannot receive properly the token, which prevent from them being stuck forever
> But this should trigger in your mind that hooks bring with them reentrancy, so be careful and don’t forget to implement reentrancy guards !

---

**Q14 The OpenZeppelin library that provides onlyOwner modifier**

A) Is Ownable
B) Provides role based access control
C) Provides a function to renounce ownership
D) None of the above

> Inheriting from Ownable.sol will add to the contract a private variable `_owner` and functions operate it (set and renounce), plus an `onlyOwner` modifier.
> 

---

**Q15 OpenZeppelin’s (role-based) AccessControl library**

A) Provides support only for two specific roles: Owner and User
B) Provides support for different roles with different authorization levels
C) Provides support for granting and revoking roles
D) None the above

> AccessControl only add 2 specifics roles which are described in this question. The owner has the ability to grant or revoke these roles. If you need more roles, you should use AccessControlEnumerable
> 

---

**Q16 If OpenZeppelin’s isContract(address) returns false for an address then**

A) Address is guaranteed to not be a contract
B) Codesize at address is 0 at time of invocation
C) Both A & B
D) Neither A nor B

> `isContract` function is part of the OZ Address library and checks code size equal 0 using `address.code.length` to know if its an EOA or a contract, as an EOA do not have code. But there’s cases where this variable is 0. For example during contract construction, or if the contract has been selfdestructed
> 

---

**Q17 CREATE2**

A) Deploys two contracts proxy and implementation concurrently
B) Deploys contract at an address that can be predetermined
C) Uses a salt and contract creationCode
D) None of the above

> `create2` has been added after `create` to give the ability to developer to predetermine the resulting address. With `create` the calculation of the address uses the nonce and msg.sender, which makes it unpredictable. While `create2` uses a salt and the contract creation code. This means that for an exact same code, salt and msg.sender, the resulting address will always be the same (using contract creation code prevent a malicious developper to destroy its contract and replace it by a dangerous one without the users noticing it !)
> 

---

**Q18 OpenZeppelin ECDSA**

A) Implements functions for signature creation & verification
B) Is susceptible to signature malleability
C) Both A & B
D) Neither A nor B

> OpenZeppelin ECDSA provides functions for recovering and managing Ethereum account ECDSA signature + solves the issue of [signature malleability](https://twitter.com/0xOwenThurm/status/1619151598877577216) (to make it really quick, it exists two different couple of values that would verify a same signature, ECDSA forces one of theses values to be fixed, making this trick impossible)
>

---

**Q19 OpenZeppelin SafeMath**

A) Prevents integer overflows/underflows at compile-time
B) Is not required if using Solidity compiler version >= 0.8.0
C) Both A & B
D) Neither A nor B

> SafeMath is not needed anymore with version ≥0.8.0 of Solidity. Although, I found [that answer on StackExchange](https://ethereum.stackexchange.com/a/115534/105102) pretty interesting, showing it can still be useful for meaningful revert messages.
> 

---

**Q20 OpenZeppelin’s proxy implementations**

A) Typically have a proxy contract and an implementation contract
B) Use delegatecall's from proxy to implementation
C) Cannot support upgradeable proxies
D) None of the above

> We use a proxy architecture to make contracts “upgradable”. By separating the logic (implementation contract) and the proxy (delegating calls to the implementation contract), we can then change whenever needed the address of the logic contract called by the proxy ! There’s multiple proxies architecture, one of them is the TransparentUpgradableProxy which make it possible for an admin to upgrade the proxy from the implementation
> 

---

**Q21 Proxied contracts**

A) Should use constructors in implementation contract to initialize the proxy’s state variables
B) Should use an external/public initialize() function
C) Should have their initialize() function called only once
D) All of the above

> Proxied contract shouldn’t use constructors, the reason behind that is  explained in [Open Zeppelin Upgrades Plugins doc](https://docs.openzeppelin.com/upgrades-plugins/1.x/proxies#the-constructor-caveat), as this is already perfectly phrased, I’m copying it here : *“In Solidity, code that is inside a constructor or part of a global variable declaration is not part of a deployed contract’s runtime bytecode. This code is executed only once, when the contract instance is deployed. As a consequence of this, the code within a logic contract’s constructor will never be executed in the context of the proxy’s state. To rephrase, proxies are completely oblivious to the existence of constructors. It’s simply as if they weren’t there for the proxy.”*
> 

---

**Q22 Dappsys provides**

A) A proxy implementation
B) A floating-point implementation with wad & ray
C) A flexible authorization implementation
D) All of the above

> All of the above, you can find their documentation here if interested : [https://dappsys.readthedocs.io/en/latest/](https://dappsys.readthedocs.io/en/latest/)
> 

---

**Q23 WETH is**

A) An ERC20 pre-compile for Wrapped Ether built into Ethereum protocol
B) Wrap Ether for super-fast Ether transfers
C) Wrapped Ether to convert Ether into an ERC721 NFT
D) None of the above

> WETH has been deployed to wrap ETH into an ERC20, because ETH isn’t an ERC20 at first, but the the native crypto of the Ethereum network, thus giving him different properties in regard to ERC20 tokens. By wrapping ETH into WETH, this make it way more easy and straightforward to use it in DeFi protocols
>

---

**Q24 Name collision error with inheritance happens when the following pairs have the same name within a contract**

A) Function & modifier
B) Function & event
C) Function & function
D) Event & modifier

> Answer of the quizz says A,B and D, but from this is a but tricky. Let’s say contract B inherits from contract A, and they have a same function name. While it is not possible to have a function with a [same signature](https://ethereum.stackexchange.com/questions/135205/what-is-a-function-signature-and-function-selector-in-solidity-and-evm-language), it is still possible to have 2 functions with same name, but different input parameters (which change its signature)
> 

---

**Q25 Which of the following is/are not allowed?**

A) Function overriding
B) Function overloading
C) Modifier overloading
D) Modifier overriding

> Overriding occurs when a contract inherit a `virtual` function/modifier from another contract and `override` it. Overloading means having a function with a same name, but different input/output parameters. This works for functions, but not for modifiers. Seems like this question [is still discussed](https://github.com/ethereum/solidity/issues/72) on the solidity github
> 

---

**Q26 Solidity supports**

A) Multiple inheritance
B) Polymorphism
C) Contract overloading
D) Function overloading

> It is possible for a contract to inherit from multiple contracts, as far there is no collision (as described in Q24) 
Polymorphism is linked to overriding, if a child contract has a function with same name as an ancestor, calling it will call its own function, and not the ancestors’ ones. It is still possible to call the function higher in the hierarchy using `super.functionName()` or `contractName.functionName()`.
Contract overloading do not exists, and I’m not sure what this would be.
> 

---

**Q27 Which of the following EVM instruction(s) do(es) not touch EVM storage?**

A) SLOAD
B) MSTORE8
C) SSTORE
D) SWAP

> SLOAD and SSTORE starts with an S for Storage. MSTORE8 starts with an M for Memory. SWAP starts with an S, but this is the whole word “swap” which swap 2 items from the stack. You can find [all opcodes here](https://www.evm.codes/?fork=arrowGlacier)
> 

---

**Q28 Which of the following is/are true about Solidity compiler 0.8.0?**

A) ABI coder v2 is made the default
B) No opt-out primitives for default checked arithmetic
C) Failing assert returns the gas left instead of consuming all gas
D) Exponentiation is made right associative

> Before `0.8.0`, we had to use the `pragma experimental ABIEncoderV2` instruction to use this encoder which was still experimental. V2 make it possible to return structs, nested and dynamic variables from function and emitted by events
Also, arithmetic operations are by default checked.
Finally, before this version exponentiation was left associative, which was not common compared to other programming langage. Right associative means `a**b**c`
 is parsed as `a**(b**c)`
List of changes [here](https://docs.soliditylang.org/en/v0.8.0/080-breaking-changes.html)
> 

---

**Q29 OpenZeppelin SafeCast**

A) Prevents underflows while downcasting
B) Prevents overflows while downcasting
C) Prevents underflows while upcasting
D) Prevents overflows while upcasting

> When downcasting a `uint32 a = 0x12345678` to a `uint16 b = uint16(a)` b will be equal to `0x5678` which act as an overflow. This is call [explicit conversion](https://docs.soliditylang.org/en/v0.8.18/types.html#explicit-conversions). SafeCast reverts if there’s an overflow
> 

---

**Q30 OpenZeppelin’s ReentrancyGuard library mitigates reentrancy risk in a contract**

A) For all its functions by simply deriving/inheriting from it
B) Only for functions that apply the nonReentrant modifier
C) By enforcing a checks-effects-interactions pattern in its functions
D) None of the above

> OZ [ReentrancyGuard](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/security/ReentrancyGuard.sol) add a nonReentrant modifier, which sets a flag when entering the function, and unset it when exiting the function, while requiring that the flag is always unset when entering the function. This way, no re-entrancy is possible in function using that modifier. 
Still, developer should implement the check-effects-interactions pattern as a good habit.
> 

---

**Q31 Assuming all contracts C1, C2 and C3 define explicit constructors and contract C1 is C2, C3 {…} and both C2 and C3 don’t inherit contracts, the number & order of constructor(s) executed is/are**

A) One, that of C1
B) Three, in the order C2, C3, C1
C) One, that of C3
D) Three, in the order C1, C2, C3

> Languages that allow multiple inheritance have to deal with several problems. One is the [Diamond Problem](https://en.wikipedia.org/wiki/Multiple_inheritance#The_diamond_problem). Solidity is similar to Python in that it uses “[C3 Linearization](https://en.wikipedia.org/wiki/C3_linearization)
” to force a specific order. 
This is explained in [Solidity doc](https://docs.soliditylang.org/en/v0.8.18/contracts.html#multiple-inheritance-and-linearization).
> 

---

**Q32 Which of the following is/are true for a function f that has a modifier m?**

A) Function f cannot have another modifier because every function can have at most one modifier
B) Function f's code is inlined at the point of _ within modifier m
C) Function f reverts if _ is not executed in the modifier m
D) None of the above

> It is possible to have multiple modifiers for a function, the rule it follow is well described in [this discussion](https://ethereum.stackexchange.com/questions/29608/whats-the-order-of-execution-for-multiple-function-modifiers). Modifiers uses the `_;` statement, which acts as a placeholder for the code calling the modifier
>