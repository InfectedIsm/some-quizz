Note: All 8 questions in this quiz are based on the InSecureumToken contract shown below. This is the same contract you see for all the 8 questions in this quiz. The InSecureumToken contract implements a token contract which allows users to buy tokens by depositing Ether (at a certain conversion ratio), and transfer tokens.

```solidity
pragma solidity 0.7.0;

contract InSecureumToken {

mapping(address => uint) private balances;

uint public decimals = 10**18; // decimals of the token
uint public totalSupply; // total supply
uint MAX_SUPPLY = 100 ether; // Maximum total supply

event Mint(address indexed destination, uint amount);

function transfer(address to, uint amount) public {
   // save the balance in local variables
   // so that we can re-use them multiple times
   // without paying for SLOAD on every access
   uint balance_from = balances[msg.sender];
   uint balance_to = balances[to];
   require(balance_from >= amount);
   balances[msg.sender] = balance_from - amount;
   balances[to] = safeAdd(balance_to, amount);
}


/// @notice Allow users to buy token. 1 ether = 10 tokens
/// @dev Users can send more ether than token to be bought, to donate a fee to the protocol team.
function buy(uint desired_tokens) public payable {
   // Check if enough ether has been sent
   uint required_wei_sent = (desired_tokens / 10) * decimals;
   require(msg.value >= required_wei_sent);

   // Mint the tokens
   totalSupply = safeAdd(totalSupply, desired_tokens);
   balances[msg.sender] = safeAdd(balances[msg.sender], desired_tokens);
   emit Mint(msg.sender, desired_tokens);
}


/// @notice Add two values. Revert if overflow
function safeAdd(uint a, uint b) pure internal returns(uint) {
   if (a + b < a) {
      revert();
   }
   return a + b;
}
}
```

</details>

___
#### Q1 The InSecureumToken contract strictly follows the specification of
- [ ] A) ERC20
- [ ] B) ERC777
- [ ] C) ERC721
- [ ] D) None of the above

<details>
	<summary>Click the reveal the answer</summary>
D
</details>

___
#### Q2 To avoid lock of funds, the following feature(s) MUST be implemented before contract deployment
- [ ] A) A `transferFrom()` function
- [ ] B) A `burn()` function
- [ ] C) A way to withdraw/exchange/use Ether from the contract
- [ ] D) None of the above

<details>
	<summary>Click the reveal the answer</summary>
C
</details>

___
#### Q3 Which of the following assertion(s) is/are true (without affecting the security posture of the contract)?
- [ ] A) `buy()` does not need payable keyword
- [ ] B) balances must be private
- [ ] C) `transfer()` can be external
- [ ] D) `safeAdd()` can be public

<details>
	<summary>Click the reveal the answer</summary>
C,D
</details>

___
#### Q4 The total supply is limited by
- [ ] A) 10**18
- [ ] B) 100 * 10**18
- [ ] C) 100
- [ ] D) None of the above

<details>
	<summary>Click the reveal the answer</summary>
D
</details>

___
#### Q5 The following issue(s) is/are present in the codebase
- [ ] A) An integer underflow allows one to drain Ether
- [ ] B) Unsafe rounding allows one to receive new tokens for free
- [ ] C) A division by zero allows one to trap/freeze the system
- [ ] D) None of the above

<details>
	<summary>Click the reveal the answer</summary>
B
</details>

___
#### Q6 The following issue(s) is/are present in the codebase
- [ ] A) A front-running allows one to pay less than expected for tokens
- [ ] B) A lack of access control allows one to receive tokens for free
- [ ] C) Incorrect balance update allows one to receive new tokens for free
- [ ] D) None of the above

<details>
	<summary>Click the reveal the answer</summary>
C
</details>

___
#### Q7 The following issue(s) is/are present in the codebase
- [ ] A) A reentrancy allows one to drain Ether
- [ ] B) A reentrancy allows one to drain the tokens
- [ ] C) A reentrancy allows one to receive new tokens for free
- [ ] D) None of the above

<details>
	<summary>Click the reveal the answer</summary>
D
</details>

___
#### Q8 The following issue(s) is/are present in the codebase
- [ ] A) An integer overflow allows one to drain Ether
- [ ] B) An integer overflow allows one to receive new tokens for free
- [ ] C) An integer overflow allows one to trap/freeze the system
- [ ] D) None of the above

<details>
	<summary>Click the reveal the answer</summary>
D or B or C or B,C<br/>
Note: While the initial platform-specified correct answer for Q8 was D, it was determined that this Q&A had some latent ambiguity with answer choices B & C. Therefore, all answer combinations indicated above were considered as valid and scores adjusted accordingly.
</details>