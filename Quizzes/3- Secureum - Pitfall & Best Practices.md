https://github.com/x676f64/secureum-mind_map/tree/master/quizzes

#### Q1 The use of pragma in the given contract snippet

```(solidity)
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


___
#### Q2 The given contract snippet has

```(solidity)
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


___
#### Q3 The given contract snippet has

```(solidity)
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

___
#### Q4 In the given contract snippet

```(solidity)
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


___
#### Q5 The security concern(s) in the given contract snippet is/are

```(solidity)
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

___
#### Q6 The vulnerability/vulnerabilities present in the given contract snippet is/are

```(solidity)
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


___
#### Q7 The security concern(s) in the given contract snippet is/are

```(solidity)
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

___
#### Q8 The security concern(s) in the given contract snippet is/are

```(solidity)
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

___
#### Q9 The given contract snippet is vulnerable because of

```(solidity)
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


___
#### Q10 In the given contract snippet, the require check will

```(solidity)
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

___
#### Q11 The security concern(s) in the given contract snippet is/are

```(solidity)
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

___
#### Q12 The security concern(s) in the given contract snippet is/are

```(solidity)
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

___
#### Q13 The security concern(s) in the given proxy-based implementation contract snippet is/are

```(solidity)
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

___
#### Q14 The security concern(s) in the given contract snippet is/are

```(solidity)
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


___
#### Q15 The vulnerability/vulnerabilities present in the given contract snippet is/are

```(solidity)
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

___
#### Q16 The security concern(s) in the given contract snippet is/are

```(solidity)
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

