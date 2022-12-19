**Question 2/40**
**What does require(msg.sender == tx.origin) do?**

1. It will only accept transactions from smart contracts
2. It will accept transactions from smart contracts only if it is called from the constructor
3. It will only accept transactions from regular wallets
4. It will block all transactions since tx.origin is never equal to msg.sender
5. It doesn't do anything, tx.origin is the deprecated version of msg.sender

> msg.sender = address of the last executer of the transaction
tx.origin = adrdess of the first executer of the transaction, thus the signer of the tx. It is always a regular wallet as only EOA can sign a transaction, SC can only execute them.
If a user A sends a tx calling a smart contract B, msg.sender == tx.origin == user A address. If contract B makes an external call to contract C then msg.sender == contract B while tx.origin is still user A.
> 

**Question 6/40**

**Which of the following is true about generating random numbers on Ethereum? (multiple possible)**

1. using block.timestamp is unreliable because the miner can manipulate it ([link](https://cryptomarketpool.com/block-timestamp-manipulation-attack/))
2. combining the blockhash with other random data like block.difficulty (pre merge), ~~block.coinbase~~ and ~~msg.sender~~ makes it safer
3. using the blockhash is reliable
4. randomness is difficult to generate securely because the blockchain is deterministic
5. a commit reveal scheme can produce safe randomness under certain assumptions
6. as long as randomness relies on private variables, it is unpredictable

> randomness cannot be achieved onchain because of the deterministic nature of the blockchain. The best way to achieve randomness is to use an oracle like [Chainlink VRF](https://docs.chain.link/docs/vrf/v2/introduction/).

Thus, using blockhash and other “pseudo-random” will make it harder (but not impossible) for an attacker to predict the value, as he the attacker needs to mine the block and to verify the hash to know if he can attack. 
The length of the hash make the attack surface pretty small, so the attack will be interesting only if the prize is big enough to compensate for the loss generated by mining and throwing blocks with wrong hash.

block.coinbase (miner node address) and msg.sender (attack contract address) are not random at all for a miner as it is a constant value.
> 

**Question 7/40**

**What is true about block.timestamp?**

1. The number of milliseconds since 00:00:00 UTC 1 January 1970
2. The number of seconds since 1 January 1970 in the user's timezone
3. The number of seconds since 00:00:00 UTC 1 January 1970
4. The number of milliseconds since 1 January 1970 in the user's timezone
5. The timestamp is determined by the miner or validator

**Question 8/40**

**EIP 1967 Proxy Storage Slots specifies which storage slots?**

1. An implementation address
2. A delegate address
3. A proxy address
4. A beacon contract address
5. An admin address

> EIP 1967 define where in storage (address) proxies contracts stores the address of the logic contract they delegate to. This was proposed (and accepted) so that block explorers (like etherscan) can easily retrieve the logic contract
proxy contract —> logic contract (delegate address)
**or, only if** logic contract is empty :
proxy contract —> beacon contract 
Changes to these addresses **should** be notified by a standardized event
> 

**Question 9/40**

```solidity
valueToSend = totalValue / contributors.length * 1
```
**What is wrong with this code?**

1. nothing is wrong with the code
2. arithmetic underflow
3. the valueToSend might be zero, even if totalValue is not
4. totalValue might not be divisible by contributors.length
5. arithmetic overflow

> As every number is an integer in Solidity, A/B where A<B gives 0
Also, as in any programming language, divising by 0 cause an error



**Question 10/40**

```solidity
contract Sample {
	uint128 public x;
	uint128 public y;
	uint256 public z;
}
```
**See the smart contract below. What is the storage slot of y?**

1. 1
2. It is the keccack256 of the smart contract address concatenated with "y"
3. 0 (zero)
4. 3
5. It is the keccack256 of the smart contract address concatenated with 0x01
6. 2

> State variable are stored contiguously  and sequentially, where the first state variable is placed at slot 0. Slots are 32 bytes wide, if 2 state variable can fit in this 32 bytes slot, the compiler will put them together. So, y is in slot 0 in this example ([link](https://docs.soliditylang.org/en/v0.8.17/internals/layout_in_storage.html))
Note that this is what makes private variables not so much private : if the contract code is public, one can get the value of the private variable using function loke web3.eth.getStorageAt
> 

**Question 11/40**

**What is the difference between a view and pure function in Solidity?**

1. A pure function cannot have any side effects, but a view function can
2. Pure functions cannot make calls to arbitrary smart contracts
3. Pure functions will revert if they access blockchain state
4. Pure functions cannot return any data, but view functions can
5. Pure functions cannot access blockchain state, but view functions can

> In Solidity, a function that doesn’t read or write a state variable is called Pure. It can still use local variable that are declared inside, or passed through arguments.
While view function can still read a state variable, but not change its value.
A contract will not compile if a pure function potentially access or change the state.
For the list of what can change the state, please refer at [this section](https://docs.soliditylang.org/en/v0.8.17/contracts.html#state-mutability) of the documentation
> 

**Question 12/40**

**What is Ethereum's primary hash function?**

1. sha2
2. sha1
3. md5
4. keccak256
5. sha256

> Most of the time you will see keccak256 used with abi.encode or abi.encodePacked to generate a hash of a set of data
> 

**Question 13/40**

```solidity
function vote(uint256 choice) external payable {
	require(!closed);
	require(msg.value == 1 ether);
	require(choice < HIGHEST_VOTE_INDEX);
	
	vote[choice] += 1;

	if(votes[choice] > THRESHOLD) {
		closed = true;
	}
}
```
**What is the highest value the free memory pointer can achieve in this function?**

1. 0x140
2. 0x200
3. 0x60
4. 0x80
5. There is no limit
6. 0x160
7. 0x180
8. 0x120
9. 0x40
10. 0x100

> The free memory pointer is a starts at 0x40 and stops at 0x5f
> 

**Question 14/40**

**What is true about ERC721?**

1. _mint() is potentially re-entrant but _safeMint() is not
2. both _mint() and _safeMint() are potentially re-entrant
3. neither _mint() or _safeMint are potentially re-entrant
4. _safeMint() is potentially re-entrant but _mint() is not

> ERC721 is based on [EIP-721](https://eips.ethereum.org/EIPS/eip-721) do not specify the code for the standard, but rather the interface of the standard.
Moreover, these 2 methods are not defined in the ERC721 token.
So it is up to the developer to protect these function from re-entrancy, usually developper will use as a base the [OpenZeppelin ERC721 library](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC721/ERC721.sol)
> 

**Question 15/40**

```solidity
function rollDice() external payable {
	require(msg.value == 1 ether, "you must bet one ether);
	if (uint256(blockhash(block.number -1)) % 6 == 3) {
		payable(msg.sender).transfer(2 ether);
	}
}
```

**You have only one ether (plus a little to pay for gas). Can you win this game and get two ether?**

1. You can reliably win this game
2. If the contract doesn't have 2 ether in it, you will lose the 1 ether you payed to play, even if you get the correct number
3. You have a 1 in 6 chance of winning, so you probably shouldn't play
4. If the contract doesn't have 1 ether in it, you will lose the 1 ether you payed to play, even if you get the correct number

> blockhash of a block is derived from the datas it contains, this means that you cannot infer blockhash of a future block. But this is still possible to trick this function.
To win this game you must be a validator/miner, on each block you will compute `uint(256(blockhash(block.number - 1)) % 6 == 3)` and only integrate the transaction in the block if you know the result is true. Although the requirement is not that simple to fulfill, if the price is worth, this will probably be exploited by someone else, so be careful when building something like this.
> 

**Question 16/40**

**What limits the block size in Ethereum as of 2022?**

1. There cannot be more than 150 transactions per block
2. 15 million gas
3. There cannot be more than 400 transactions per block
4. 30 million gas
5. 4 megabyte size
6. 2 megabyte data size
7. There cannot be more than 300 transactions per block
8. 1 megabyte data size
9. 12.5 million gas

> Blocks have a target at 15 million gas, and it can grow up to 30 million to adapt to the network demands. But the bigger the new blocks stays, the higher the base gas fee become for the following block. This mechanism tend to reduce the incoming transactions (as tx become more expensive), thus reducing the incoming txs. The same way, if the block size is <15m, then the base fee get cheaper. More details [here](https://ethereum.org/en/developers/docs/gas/#block-size)
> 

Q**uestion 17/40**

```solidity
contract Difference {
	uint256 val;
	uint256 val2;

	function a(uint256 x) external {
			require(x < 10);
			val = x;
			val2 = x - 1;
	}

	function b(uint256 x) external {
			val = x;
			require(x < 10);
			val2 = x - 1;
	}
```

**What is the difference between these two functions?**

1. Function a will potentially save the user gas compared to b
2. Function b will potentially save the user gas compared to a
3. If x is greater than 10, the values won't change in either function.
4. If x is greater than 10, the values of val and val2 won't change in function a but val will change in function b.
5. There may be unexpected behavior if x = 0

> First, function “a” will cost less gas than function “b” because when a revert happens, only the remaining gas is refunded. In function “b”, a value is written to state variable `val` , which cost xxx gas.
Also, when a revert occurs, all the transaction is canceled and the contract is rolled back to its initial state, so val and val2 will not be changed if x si greater than 10. 
Finally, of x=0, two behavior can happen : 
- solidity ≥0.8.0 = revert because of underflow
- solidity <0.8.0 = underflow of val2, making its value equal to 2^256-1
> 

**Question 18/40**

```solidity
mapping(address => uint256) balance;

function withdraw() external {
    (bool success, ) = tx.origin.call{value: balance[tx.origin]}("");
    require(success, "transfer failed);
    balance[tx.origin] = 0;
}

function deposit() external payable {
    balance[tx.origin] = msg.value;
}
```

**What is wrong with this code and/or what could be improved?**

1. withdraw should be payable
2. tx.origin can be a security vulnerability
3. the code is re-entrant
4. withdraw will fail unless the code is changed to payable(tx.origin)
5. the variable balance should have visibility set explicitly

> First we should verify that balance is not zero, we do not want to call the function if nothing is withdraw as this would cost gas for nothing
tx.origin is known to be vulnerable for authorization, but in this case tx.origin is not vulnerable as there is no impersonation possible, only the EOA have access to its balance.  
Finally, the deposit function is wrong, it equalize the balance to msg.value, while it should add it.
> 

**Question 19/40**

**How can a smart contract change it's bytecode?**

1. After the contract has already been constructed, and extcodesize is greater than zero, solidity can use extcodecopy to modify its own bytecode
2. A proxy contract can change its bytecode
3. Smart contracts bytecode can never be changed. Proxy contracts refer to other contracts which themselves are immutable, but a proxy contract can point to an arbitrary new contract.
4. Although smart contracts are technically immutable, their bytecode does change when storage variables are changed, but only according to how the variables are configured.
5. If the constructor loads external bytecode, it can be redeployed with create2 and different bytecode after self destruct

> smart contract are immutable by nature, the only way to “update” a smart contract is through a proxy architecture. The proxy contract remains the same, but will point at a new version of contract that could be an updated version of the previous smart contract
Create2 do not permit to re-deploy a smart contract to a same address after destroying it, because the value is function of the EoA address and its nonce (number of transactions)
> 

**Question 20/40**

**What is the difference between transfer and transferFrom in ERC20?**

1. transferFrom can be used by an approved spender on behalf of another address
2. transferFrom can only be used by the token owner
3. transferFrom could be re-entrant according to ERC20
4. transfer could be re-entrant according to ERC20
5. transfer can only be used by the token owner
6. transfer can be used by an approved spender on behalf of another address

> There is no re-entrancy risk with transfer and transferFrom, as re-entrancy is happening when sending ETH to a smart contract, which cause to automatically call the `receive()` or `fallback()` fonctions of the smart contract. [More about re-entrancy here](https://solidity-by-example.org/hacks/re-entrancy)
> 

**Question 21/40**

```solidity
pragma solidity 0.8.7;

contract A { }

contract B is A {

    function boom() external {
        selfdestruct(payable(msg.sender));
    }
}

contract C is B{ }
```

**Contract C is deployed. When boom() is called. Which of the following is true?**

1. There are three contracts, B is destroyed, but A and C are not.
2. There are three contracts, A and B and C are destroyed.
3. Boom cannot be called because B was not deployed
4. There are three contracts, A and B are destroyed, but C is not.
5. There is one contract. C is destroyed.
6. There are three contracts, B and C are destroyed but A is not.

> In this example, contract B inherit from contract A, meaning it will automatically integrate the same functions and state variables as A. Same for C which inherit from B, meaning it will integrate function and states variables from B (and so, A)
When deploying C, only one contract is deployed, and this contract will automatically have a `boom()` function. Calling it will then destroy C.
> 

**Question 22/40**
**What is the difference in the calldata between function myFunction(uint64 x) and myFunction(uint256 x)?**

1. it will depend on the number x holds, but myFunction(uint256 x) can create larger calldata
2. There is no difference
3. it depends on which ethereum hardfork is being used, but as of 2022 the representation is the same
4. the calldata for myFunction(uint64 x) will be smaller than myFunction(uint256 x)
5. it depends on which version of solidity is being used

> Calldata can have an “unlimited” size and specifies the input data of the message call. It is organized in a bytes layout : the first 4 bytes correspond to the selector of the function signature, while the remaining bytes correspond to the input parameters of the function. 
Each input argument is always 32 bytes long. If its type is smaller than 32 bytes, the argument is padded (from [this article](https://betterprogramming.pub/solidity-tutorial-all-about-calldata-aebbe998a5fc#c451))
In this example, there’s only 1 argument in both function, so there will be 4+32 bytes in the calldata.
You can easily check that using Remix : when deploying a contract, if you develop the function call you can copy to clipboard the calldata it would generate
> 

**Question 23/40**

**What solidity version introduced arithmetic underflow and overflow protection?**

1. 0.7.6
2. 0.7.0
3. 0.8.0
4. 0.6.0
5. None, you should always use SafeMath if you are worried about overflow and underflow

> SafeMath is not needed anymore with version ≥0.8.0 of Solidity. Although, I found [that answer on StackExchange](https://ethereum.stackexchange.com/a/115534/105102) pretty interesting, showing it can still be useful for meaningful revert messages.
> 

**Question 24/40**
**What does the staticcall opcode do?**

1. It is deprecated an not recommended for use, it is an older version of call
2. It is used for functions that have a fixed gas cost
3. It makes a function call and reverts if a state change occurs
4. It returns constant variables
5. It is only used for calls within a smart contract

> To increase smart contract security, Vitalik proposed integrating this new opcode into the EVM through the [EIP214](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-214.md).
Staticcall disallow any modification to the state during the call (and its subcalls), if it happens that a state will be modified, the function return an exception instead of performing the modification.
> 

**Question 25/40**

**What does selfdestruct do?**

1. sends all the ether in a contract to the address that initiated the self destruct
2. sends all the ether to the address specified in the first argument
3. will revert (not self destruct) if the recipient address does not exist or cannot receive ether
4. removes a smart contract's bytecode and storage variables from the blockchain
5. after the transaction with selfdestruct completes, all function calls to the smart contract will revert

> When [selfdesctruct](https://docs.soliditylang.org/en/v0.8.17/introduction-to-smart-contracts.html#deactivate-and-self-destruct) is called from a smart contract, it will delete the smart contract bytecode from the blockchain, and sends its ether balance to the address specified as argument
A vulnerability based on selfdestruct ether transfer behavior can be exploited, more info [here](https://solidity-by-example.org/hacks/self-destruct/) 
Also, be careful when using delegatecall, as you could call a malicious contract containing a selfdestruct in its fallback function, causing you to execute it in the context of your contract and destroy your own contract !..
> 

**Question 26/40**

**Why does the gas cost of a public or external variable change when the name of the function changes?**

1. The function selector may have more leading zeros
2. Its position in the bytecode changes relative to other functions
3. The longer the function name, the more space that is required to store it after compilation
4. The name of the function doesn't affect the gas cost, but if the arguments to the function change, the gas cost may change

> When compiled, Solidity code is converted into [EVM bytecode](https://ethereum.org/en/glossary/#bytecode), which is an abstract instruction set designed for efficient execution by the EVM. Its done so that the EVM can understand and execute it. The bytecode is composed of opcodes (represented by 2 bytes), variables values and function selectors. [The function selector](https://solidity-by-example.org/function-selector/#:~:text=When%20a%20function%20is%20called,is%20called%20a%20function%20selector) of a contract’s function is determined by 4-first bytes of the keccak256 hash of its prototype. 
And, the execution of ‘0’ bytes by the EVM cost less gas than the execution of ‘non-0’ bytes. But this should be taken into account as the gas saving is really low, and finding a function hash that has a lot of trailing zeros will always result in a bad function name.
> 

**Question 27/40**

**Which of the following are valid uint sizes?**

1. uint8
2. uint24
3. uint16
4. uint32
5. uint40

> As far as the uint size can be divided 8, it is valid
> 

**Question 28/40**
**Which of the following variable types can be stored in memory?**

1. struct
2. address
3. uint
4. array
5. the output of a hash
6. boolean

> Memory is one of the 3 area where data can be stored (next to calldata and storage).
It is a non persistent space to hold temporary values, and is ereased between external function calls, making it cheaper to use (in term of gas, and more generally for the network)
Struct and array are designed as “complex data types” are they can be of an arbitrary size, and assigning a variable from an area to another can be quite expensive depending on these conditions. 
More information in the [Solidity documentation](https://docs.soliditylang.org/en/v0.4.21/types.html#reference-types)
> 

**Question 29/40**

**What is true about virtual functions?**

1. It is used only in abstract contracts
2. Virtual functions must be overriden by a child contract
3. Virtual functions are less gas efficient than regular functions
4. The virtual keyword has no effect after solidity 0.7.0
5. It allows the function to be modified by a child contract

**Question 30/40**
**Which of the following is true if 51% of the network is malicious?**

- [ ]  They can conduct a denial of service
- [ ]  They can fork the protocol (but if and only if they maintain more than 51% control of the hash power or staked ether)
- [ ]  The nodes can censor you
- [ ]  The nodes can steal your ether

**Question 31/40**

[https://images.testportal.net/eyJidWNrZXQiOiJ0ZXN0cG9ydGFsLW5ldC1hc3NldHMiLCJrZXkiOiJNdmdsT05vVEcyVWgxSWJBZWJ1MHBRL2E3NDZmMDhhNmRjNDllNWVmMjAxMTA3YTdkNGRjN2RlODUxZDIwNjY1Mjk0MDEwYzRhYjU5OTQzMzhlNjcwNzciLCJlZGl0cyI6eyJyZXNpemUiOnsid2lkdGgiOjY5Miwid2l0aG91dEVubGFyZ2VtZW50Ijp0cnVlfX19](https://images.testportal.net/eyJidWNrZXQiOiJ0ZXN0cG9ydGFsLW5ldC1hc3NldHMiLCJrZXkiOiJNdmdsT05vVEcyVWgxSWJBZWJ1MHBRL2E3NDZmMDhhNmRjNDllNWVmMjAxMTA3YTdkNGRjN2RlODUxZDIwNjY1Mjk0MDEwYzRhYjU5OTQzMzhlNjcwNzciLCJlZGl0cyI6eyJyZXNpemUiOnsid2lkdGgiOjY5Miwid2l0aG91dEVubGFyZ2VtZW50Ijp0cnVlfX19)

**What is the minimum valid calldatasize for this function?**

- [ ]  256
- [ ]  64
- [ ]  128
- [ ]  68
- [ ]  224
- [ ]  260
- [ ]  132

**Question 32/40**

**What is true about private, internal, external, and public functions?**

- [ ]  Some of these of these are related to inheritance
- [ ]  external functions can only be called by smart contracts
- [ ]  public functions can be called by other smart contracts
- [ ]  All of these except external could be used to describe variables in addition to functions
- [ ]  There is no such thing as an internal function, it should be called a protected function

**Question 33/40**

**How is an ethereum address created?**

- [ ]  An ethereum address is the hash of the public key
- [ ]  An ethereum address is the public key of an ECDSA signature scheme
- [ ]  An ethereum address is the last 20 bytes of the hash of the public key
- [ ]  An ethereum address is a random string. It's relationship to the secret phrase is kept in the user account
- [ ]  An ethereum address is the hash of the block number the wallet was created on, and the public key

**Question 34/40**

**The DAO hack in 2016, that resulted in the split of Ethereum Classic, was hacked with what kind of exploit?**

- [ ]  Compromised private keys
- [ ]  Arithmetic overflow
- [ ]  Arithmetic underflow
- [ ]  Insufficient access controls
- [ ]  Re-entrancy

**Question 35/40**

**What is the difference between create and create2?**

- [ ]  create is for creating one smart contract in a single transaction
create2 can create multiple smart contracts.
- [ ]  create2 takes a salt parameter
- [ ]  the user has more control over the address in create compared to create2
- [ ]  the user can predict the address in create2 but not create
- [ ]  the user has more control over the address in create2 compared to create

**Question 36/40**

[https://images.testportal.net/eyJidWNrZXQiOiJ0ZXN0cG9ydGFsLW5ldC1hc3NldHMiLCJrZXkiOiJNdmdsT05vVEcyVWgxSWJBZWJ1MHBRLzJhNzNlYzQzNGQ0MzFjOTFiOTliZTgxZjdjNDEyNDVkMDM2MzAzNWEwZDYxNGI5MGYxMmRmYmUzYmY0OTVlYzkiLCJlZGl0cyI6eyJyZXNpemUiOnsid2lkdGgiOjY5Miwid2l0aG91dEVubGFyZ2VtZW50Ijp0cnVlfX19](https://images.testportal.net/eyJidWNrZXQiOiJ0ZXN0cG9ydGFsLW5ldC1hc3NldHMiLCJrZXkiOiJNdmdsT05vVEcyVWgxSWJBZWJ1MHBRLzJhNzNlYzQzNGQ0MzFjOTFiOTliZTgxZjdjNDEyNDVkMDM2MzAzNWEwZDYxNGI5MGYxMmRmYmUzYmY0OTVlYzkiLCJlZGl0cyI6eyJyZXNpemUiOnsid2lkdGgiOjY5Miwid2l0aG91dEVubGFyZ2VtZW50Ijp0cnVlfX19)

**What is the layout of y in memory right after line 55 executes?**

- [ ]  0x
0001
0002
0003
0004
- [ ]  0x01020304
- [ ]  0x1234
- [ ]  0x
0000000000000000000000000000000000000000000000000000000000000001
0000000000000000000000000000000000000000000000000000000000000002
0000000000000000000000000000000000000000000000000000000000000003
0000000000000000000000000000000000000000000000000000000000000004

**Question 37/40**
**Which of the following is most costly in terms of gas?**

- [ ]  Reading msg.sender
- [ ]  Reading immutable variables
- [ ]  Reading storage variables
- [ ]  Reading variables in memory
- [ ]  Reading calldata

**Question 38/40**

**What is true about transferFrom and safeTransferFrom in ERC721?**

- [ ]  transferFrom may be re-entrant, but safeTransferFrom is not re-entrant
- [ ]  safeTransferFrom checks if the recipient is a smart contract
- [ ]  transferFrom checks if the recipient is a smart contract
- [ ]  safeTransferFrom allows you to transfer multiple NFTs, but transferFrom only allows single transfers
- [ ]  safeTransferFrom can be called by another smart contract, but transferFrom cannot be called by another smart

**Question 39/40**
**As of 2022, what is the maximum smart contract size?**

- [ ]  Smart contracts do not have a size limit
- [ ]  The maximum size of a smart contract is determined by the gas block limit
- [ ]  Smart contracts do not have a size limit, but they become prohibitively expensive to deploy as they get large
- [ ]  32 kb
- [ ]  24 kb
- [ ]  12 kb
- [ ]  20 kb

**Question 40/40**

**What happens if a program enters an infinite loop in Ethereum?**

- [ ]  The transaction will revert
- [ ]  The ethereum client will warn the user and not allow them to initiate the transaction
- [ ]  The transaction will update the state of the blockchain until it runs out of gas. It will preserve any changes before the gas ran out.
- [ ]  The validator will reject the transaction after seeing it hasn't terminated past a certain time
- [ ]  The compiler does not allow you to create programs with infinite loops
