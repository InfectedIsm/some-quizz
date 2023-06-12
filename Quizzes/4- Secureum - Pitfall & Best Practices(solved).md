This is a writeup for Secureum quizzes.
You can find the answers here : https://github.com/x676f64/secureum-mind_map/tree/master/quizzes

#### Q1 The use of pragma in the given contract snippet

```solidity
pragma solidity ^0.6.0;

contract test {
   // Assume other required functionality is correctly implemented
   // Assume this contract can work correctly without modifications across 0.6.x/0.7.x/0.8.x compiler versions
}
```

- [ ] A) Is incorrect and will cause a compilation error
- [ ] B) Allows testing with 0.6.11 but accidental deployment with buggy 0.6.5
- [ ] C) Is illustrative of risks from using a much older Solidity version (assume current version is 0.8.9)
- [ ] D) None of the above

> The use of ^ before a version indicate that the contract can be compiled for versions 0.6.X where X can be any number. In solidity, versioning is a bit different as in other langage, because the second number is increased when a major change is added to the langage/compiler, while in other langage it is for the 1st number.
> It must be noted that the version [0.6.5 fixed an important bug](https://blog.soliditylang.org/2020/04/06/solidity-0.6.5-release-announcement/) and introduced immutables. 
> All over its life, Solidity will be updated to fix bugs, its better then to use the latest version of the compiler to avoid any security issue if possible.
> If not possible, try to check the [solidity release notes](https://blog.soliditylang.org/category/releases/) to see if there is any security issue that could affect your contract.

___
#### Q2 The given contract snippet has

```solidity
pragma solidity 0.8.4;

contract test {

   // Assume other required functionality is correctly implemented

   function kill() public {
      selfdestruct(payable(0x0));
   }
}
```

- [ ] A) Unprotected call to selfdestruct allowing anyone to destroy this contract
- [ ] B) Dangerous use of zero address leading to burning of contract balance
- [ ] C) A compiler error because of the use of the kill reserved keyword
- [ ] D) None of the above

> selfdestruct delete all the contract bytecode from the blockchain, and send the balance to the address given as parameter. If the address is 0x0, the balance is burned.
> This function do not have any role modifier, so anyone can call it and destroy the contract.
> Please not that the as per version 0.8.18, `SELFDESTRUCT` still exists, but it will probably be deprecated and be replaced by ``SENDALL`` in the future, [see EIP-4758 for details](https://eips.ethereum.org/EIPS/eip-4758).
___
#### Q3 The given contract snippet has

```solidity
pragma solidity 0.8.4;

contract test {
    
    // Assume other required functionality is correctly implemented
    
    modifier onlyAdmin() {
        // Assume this is correctly implemented
        _;
    }
    
    function transferFunds(address payable recipient, uint amount) public {
        recipient.transfer(amount);
   }
}
```

- [ ] A) Missing use of `onlyAdmin` modifier on transferFunds
- [ ] B) Missing return value check on transfer
- [ ] C) Unprotected withdrawal of funds
- [ ] D) None of the above

> Usually, function that allow to transfer funds from the contract should be role protected. In this case, the `onlyAdmin` modifier is missing. 
> Regarding the return value check, the `transfer()` function will revert if the transfer failed. So it is not necessary to check the return value. But the `send()` function will not revert, so it is necessary to check the return value. [Examples here](https://solidity-by-example.org/sending-ether/)
___
#### Q4 In the given contract snippet

```solidity
pragma solidity 0.8.4;

contract test {
    
    // Assume other required functionality is correctly implemented
    
    mapping (uint256 => address) addresses;
    bool check;
    
    modifier onlyIf() {
        if (check) {
            _;
        }
    }
    
    function setAddress(uint id, address addr) public {
        addresses[id] = addr;
    }
    
    function getAddress(uint id) public onlyIf returns (address) {
        return addresses[id];
    }
}
```

- [ ] A) getAddress returns the expected addresses if check is true
- [ ] B) getAddress returns zero address if check is false
- [ ] C) getAddress reverts if check is false
- [ ] D) None of the above

> When a modifier is applied to a function, the function code is inserted where the `_;` is in the modifier code. In this case, this means for the `getAddress()` function that the code will be inside the if statement. 
> If the `check` variable is false, the return statement will not be executed. So the function will return the default value for the return type, which is 0x0 for an address as described in the [solidity documentation](https://docs.soliditylang.org/en/v0.8.17/contracts.html#return-variables).
___
#### Q5 The security concern(s) in the given contract snippet is/are

```solidity
pragma solidity 0.8.4;

contract test {
    
    // Assume other required functionality is correctly implemented
    
    modifier onlyAdmin {
     // Assume this is correctly implemented
        _;
    }
    
    function delegate (address addr) external {  addr.delegatecall(abi.encodeWithSignature("setDelay(uint256)"));
    }
}
```

- [ ] A) Potential controlled delegatecall risk
- [ ] B) delegatecall return value is not checked
- [ ] C) `delegate()` may be missing `onlyAdmin` modifier
- [ ] D) `delegate()` does not check for contract existence at addr

> `delegatecall()` is a low level function that allows to execute code from another contract. It is similar to `call()` but the code is executed in the context (operate on its storage and msg.sender is caller address) of the calling contract.
> this function must then be used with a lot of care, as it can be used to execute malicious code.
> In this case, the `delegate()` function is not protected by the `onlyAdmin` modifier, so anyone can call it.
> The `delegate()` function does not check if the `addr` address is a contract. So if the `addr` address is not a contract, the `delegatecall()` will fail, and as the return value is not checked, there is no way to know that the delay has not been set.

___
#### Q6 The vulnerability/vulnerabilities present in the given contract snippet is/are

```solidity
pragma solidity 0.7.0;
import "@openzeppelin/contracts/security/ReentrancyGuard.sol";
// which works with 0.7.0

contract test {

 // Assume other required functionality is correctly implemented
 // For e.g. users have deposited balances in the contract
 // Assume nonReentrant modifier is always applied
  
    mapping (address => uint256) balances;
    
    function withdraw(uint256 amount) external nonReentrant {
        msg.sender.call{value: amount}("");
        balances[msg.sender] -= amount;
    }
}
```

- [ ] A) Reentrancy
- [ ] B) Integer underflow leading to wrapping
- [ ] C) Missing check on user balance in `withdraw()`
- [ ] D) All of the above

> the nonReentrant modifier prevent a malicious user to call the `withdraw()` function multiple times, and thus withdraw more funds than he should. But if we think more generally about this kind of situation, this does not prevent the user to call another function in the contract that can have an effect on this function. [Example here](https://quillaudits.medium.com/decoding-220k-read-only-reentrancy-exploit-quillaudits-30871d728ad5)
> Also, the balance of the user should be checked before the value transfer to prevent the execution of the call.
> Finally, the solidity version used is 0.7.0, which had not overflow/underflow protection. So the `balances[msg.sender] -= amount;` can lead to an integer underflow, which will wrap the value to a very high number, and the user will be able to withdraw more funds than he should.

___
#### Q7 The security concern(s) in the given contract snippet is/are

```solidity
pragma solidity 0.8.4;

contract test {

 // Assume other required functionality is correctly implemented
    
    uint256 private constant secret = 123;
    
    function diceRoll() external view returns (uint256) {
        return (((block.timestamp * secret) % 6) + 1);
    }
}
```

- [ ] A) `diceRoll()` visibility should be public instead of external
- [ ] B) The private variable secret is not really hidden from users
- [ ] C) `block.timestamp` is an insecure source of randomness
- [ ] D) Integer overflow

> Constant variables are not really private, as they are stored in the contract bytecode. So anyone can read the value of the variable if he can access to the bytecode. This can easily be done by using a decompiler. Also, block.timestamp is not a secure source of randomness as it can be manipulated by miners. Before The Merge, miners could set the block.timestamp global variable value to any value, as far as it +-15s from the real one. [After the Merge](https://ethereum.stackexchange.com/questions/135445/miner-modifiability-of-block-timestamp-after-the-merge), the block.timestamp is always 12s after the previous block. This means it is really easy to predict the value of the timestamp, and thus the value of the dice roll.
___
#### Q8 The security concern(s) in the given contract snippet is/are

```solidity
pragma solidity 0.8.4;

contract test {
    
    // Assume other required functionality is correctly implemented
    // Contract admin set to deployer in constructor (not shown)
    address admin;
    
    modifier onlyAdmin {
        require(tx.origin == admin);
        _;
    }
    
    function emergencyWithdraw() external payable onlyAdmin {
        msg.sender.transfer(address(this).balance);
    }
}
```

- [ ] A) Incorrect use of `transfer()` instead of using `send()`
- [ ] B) Potential man-in-the-middle attack on admin address authentication
- [ ] C) Assumption on contract balance might cause a revert
- [ ] D) Missing event for critical `emergencyWithdraw()` function

> `tx.origin` should not be use of role authentication, as it can easily bypassed. [Example here](https://quillaudits.medium.com/tx-origin-is-not-a-reliable-authentication-method-quillaudits-1e1b2b2b2b2b)
> `transfer()` should not be used anymore because it reverts if more than 2300 gas is used by the fallback function. As the EVM is in constant evolution, this gas limit can change in the future, and thus the `transfer()` calls could become irreversibly failing.
> Also, it is a good practice to emit an event when a critical function is called.  

___
#### Q9 The given contract snippet is vulnerable because of

```solidity
pragma solidity 0.8.4;

contract test {
    
    // Assume other required functionality is correctly implemented

    uint256 private constant MAX_FUND_RAISE = 100 ether;
    mapping (address => uint256) contributions;
  
    function contribute() external payable {
        require(address(this).balance != MAX_FUND_RAISE);
        contributions[msg.sender] += msg.value;
    }
}
```

- [ ] A) Integer overflow leading to wrapping
- [ ] B) Overly permissive function visibility of `contribute()`
- [ ] C) Incorrect use of `msg.sender`
- [ ] D) Use of strict equality (`!=`) may break the `MAX_FUND_RAISE` constraint

> Why does the `require` check that the maximum with an inequality? This does not make sense. If after a contribution the balance is let's say `100.00000001 ether`, then the require will pass everytime and the MAX_FUND_RAISE will not be respected. 

___
#### Q10 In the given contract snippet, the require check will

```solidity
pragma solidity 0.8.4;

contract test {
    
    // Assume other required functionality is correctly implemented
    
    function callMe (address target) external {
        (bool success, ) = target.call("");
        require(success);
    }
}
```

- [ ] A) Pass only if target is an existing contract address
- [ ] B) Pass for a non-existent contract address
- [ ] C) Pass always
- [ ] D) Fail always

> the returned boolean value of `call()` is `false`only is the call reverts. This means that if the target address is a non-existent contract address, the `call()` will not revert, it will directly return, and the `require` will pass.

___
#### Q11 The security concern(s) in the given contract snippet is/are

```solidity
pragma solidity 0.8.4;

contract test {
    
    // Assume other required functionality is correctly implemented
    // Assume admin is set correctly to contract deployer in constructor
    address admin;
    
    function setAdmin (address _newAdmin) external {
        admin = _newAdmin;
    }
}
```

- [ ] A) Missing access control on critical function
- [ ] B) Missing zero-address validation
- [ ] C) Single-step change of critical address
- [ ] D) Missing event for critical function

> There's a lot of issue in this function. First, there is no access control on the function, so anyone can call it. 
> Second, there is no check on the `_newAdmin` address, so it can be set to the zero address. 
> Third, this is a good practice to have a two-step process when critical changes are made to the contract. This means that the admin should first call a function to set a new admin, and then call a function to confirm the change. This confirmation can be protected by requiring a certain amount of time to pass before the change is confirmed, or by requiring more than 1 vote from other admins. 
> Finally, it is a good practice to emit an event when a critical function is called.
___
#### Q12 The security concern(s) in the given contract snippet is/are

```solidity
pragma solidity 0.8.4;

contract test {

    // Assume other required functionality is correctly implemented 

    address admin;
    address payable pool;

   constructor(address _admin) {
        admin = _admin;
    }    

    modifier onlyAdmin {
        require(msg.sender == admin);
        _;
    }
    
    function setPoolAddress(address payable _pool) external onlyAdmin {
        pool = _pool;
    }

    function addLiquidity() payable external {
        pool.transfer(msg.value);
    }
}
```

- [ ] A) Uninitialized pool storage variable which assumes `setPoolAddress()` will be called before addLiquidity()
- [ ] B) Incorrect use of modifier onlyAdmin on `setPoolAddress()`
- [ ] C) Missing zero-address validation for _pool in `setPoolAddress()`
- [ ] D) Transaction order dependence risk from admin front-running with pool address change

> `pool` address should be initialized in the constructor, otherwise it could be forgotten that it's still address(0) and the `addLiquidity()` function will burn the funds.
> For the same reason, the zero-address check should be done in the `setPoolAddress()` function.
> Finally, let's say that someone want to add A LOT of liquidity to the pool, but the admin is malicious and wants to steal the funds. The admin could front-run the user transaction and change the pool address to his own address and receive the funds.
> A way to prevent the last case would be to have a two-step process for the pool address change as described in Q11.
___
#### Q13 The security concern(s) in the given proxy-based implementation contract snippet is/are

```solidity
pragma solidity 0.8.4;
import "https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/proxy/utils/Initializable.sol";

contract test is Initializable {
    
    // Assume other required functionality is correctly implemented
    
    address admin;
    uint256 rewards = 10;
    
    modifier onlyAdmin {
        require(msg.sender == admin);
        _;
    }
    
    function initialize (address _admin) external {
        require(_admin != address(0));
        admin = _admin;
    }
    
    function setRewards(uint256 _rewards) external onlyAdmin {
        rewards = _rewards;
    }
}
```

- [ ] A) Imported contracts are not upgradeable
- [ ] B) Multiple `initialize()` calls possible which allows anyone to reset the admin
- [ ] C) rewards will be 0 in the proxy contract before `setRewards()` is called by it
- [ ] D) All the above

> This example is based on the [OpenZeppelin Upgrades plugin](https://docs.openzeppelin.com/upgrades-plugins/1.x/writing-upgradeable), which is based on the proxy-pattern implementation.
In proxy-pattern, the logic is separated from the storage (which is in the proxy contract), and the logic contract operates on the storage of the proxy contract through delegatecalls. 
This makes the logic contract "upgradable", as the proxy can chose to delegatecall to a different logic contract.
> There's multiple issues in this contract. First, the `initialize()` function is not marked as `initializer`, so it can be called multiple times, which allows anyone to reset the admin.
> No need for role-based access control here, as the [OpenZeppelin Initializable](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/proxy/utils/Initializable.sol#L85) module will take care of that
> Finally, even though the `rewards` variable is initialized in the logic contract, it will be 0 in the proxy contract before `setRewards()` is called by it.

___
#### Q14 The security concern(s) in the given contract snippet is/are

```solidity
pragma solidity 0.8.4;

import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/IERC20.sol";

contract test {
    
    // Assume other required functionality is correctly implemented
    
    address admin;
    address token;

    constructor(address _admin, address _token) {
        require(_admin != address(0));
        require(_token != address(0));
        admin = _admin;
        token = _token;
    }
    
    modifier onlyAdmin {
        require(msg.sender == admin);
        _;
    }
    
    function payRewards(address[] calldata recipients, uint256[] calldata amounts) external onlyAdmin {
        for (uint i; i < recipients.length; i++) {
            IERC20(token).transfer(recipients[i], amounts[i]);
        }
    }
}
```

- [ ] A) Potential out-of-gas exceptions due to unbounded external calls within loop
- [ ] B) ERC20 `approve()` race condition
- [ ] C) Unchecked return value of `transfer()` (assuming it returns a boolean/other value and does not revert on failure)
- [ ] D) Potential reverts due to mismatched lengths of recipients and amounts arrays

> Care should be taken when loops, as they can potentially cause out-of-gas exceptions depending on the gas limit and usage of the inside function.
> Also, it is better to use `safeTransfer()` instead of `transfer()` if you interact with a contract that does not return a boolean value (because with `transfer()` it will always return true)
> Adding to this, the return value should be checked to make sure that the transfer was successful.
> Finally, if recipients and amounts arrays are not of the same length, the loop will revert, and all gas will be consumed. So its better to check the lengths before the loop to avoid this.
___
#### Q15 The vulnerability/vulnerabilities present in the given contract snippet is/are

```solidity
pragma solidity 0.8.4;
import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/security/ReentrancyGuard.sol";

contract test {

 // Assume other required functionality is correctly implemented
 // For e.g. users have deposited balances in the contract
   
    mapping (address => uint256) balances;
    
    function withdrawBalance() external {
        msg.sender.call{value: balances[msg.sender]}("");
        balances[msg.sender] = 0;
    }
}
```

- [ ] A) Reentrancy
- [ ] B) Integer overflow leading to wrapping
- [ ] C) Integer underflow leading to wrapping
- [ ] D) None of the above

> We see here that the RentrancyGuard is imported but not used. This means that the contract is vulnerable to reentrancy attacks. The `nonReentrant` modifier should be used in the `withdrawBalance()` function.
___
#### Q16 The security concern(s) in the given contract snippet is/are

```solidity
pragma solidity 0.8.4;

contract test {
// Assume other required functionality is correctly implemented
// Assume that hash is the hash of a message computed elsewhere in contract
// Assume that the contract does not make use of chainID or nonces in its logic

function verify(address signer, bytes32 memory hash, bytes32 sigR, bytes32 sigS, uint8 sigV) internal view returns (bool) {
   return signer == ecrecover(hash, sigV, sigR, sigS);
}
```

- [ ] A) Signature malleability risk of ecrecover
- [ ] B) Missing use of nonce in message hash may allow replay attacks across transactions
- [ ] C) Missing use of chainID in message hash may allow replay attacks across chains
- [ ] D) Missing zero-address check for ecrecover return value may allow invalid signatures

> The `ecrecover()` function is vulnerable to signature malleability, which means that the same signature can be used to recover 2 different addresses. 
> also, if no nonce (an incremental counter each time a call is made) is used in the message hash, the same signature can be used to replay the transaction multiple times
> Also, if no chainID is used in the message hash, the same signature can be used to replay the transaction on a different chain
> Finally, if no zero-address check is done on the return value of `ecrecover()`, an invalid signature can be used as the function will return the zero-address. The attacker only have to set `signer` to the zero-address in the `verify()` and garbage values for the other parameters.