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

> This token does not implement any of the above standards. It is a custom token contract as none of these standard have a `buy()` function in the first place.
> The best way to implement a token/NFT contract is to use one of the existing and extensively tested implementation like OpenZeppelin's one.
> ERC777 is a token standard based on ERC20, but adding some advanced features like hooks and operators, but its getting deprecated becase of the complexity and vulnerabilities it adds.
> ERC721 is the standard for NFTs.

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

> It happens sometimes that users sends ether to the token contract by mistake, thinking this will allow them to buy tokens. This is not the case, and the ether will be locked forever in the contract. In this example, the only way to buy tokens is to use the `buy()` function.
> To solve this issue, the contract should implement a way to withdraw Ether from the contract and send them to the poor user who sent them by mistake. 
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

> - The `buy()` function needs to be payable because it will receive Ether from the user.
> - There's no need to make `balances` private, and it is actually better to make it public so that users can check their balance (but this would save gas if it was private as a private variable has no getter function generated)
> - `transfer()` can be external as it does not need to be called by the contract itself, this would save gas as public visibility generally costs more gas because arguments in public functions are copied to `memory`, while functions with external visibility can read arguments directly from `calldata`

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

> If the contract was developped correctly, the total supply should be limited to 100 tokens. But we see that the buy function never check that the total supply is not greater than MAX_SUPPLY.
> 1 ether in solidity is 10\**18 wei, so 100 ether is 100 * 10\**18

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

> No underflow are possible as the require statement checks that the balance is greater than the amount to transfer. This means balance_from - amount will always be greater than 0.
> Unsafe rounding is present, this is because of the integer nature of solidity numbers. When a division is performed, the result is rounded to the floor. To avoid this, the [division should be done after the multiplication](https://soliditydeveloper.com/solidity-design-patterns-multiply-before-dividing).

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

> There is no front-running risk here as the price is fixed and cannot be manimpulated.
> There's clearly no function that would need access control here.
> From the previous question about the rounding, we know that the price is not correctly calculated. Here an example to understand the issue:
> - let's say we want to calculate `received = desired_tokens / 10 * decimals`
> - if `desired_tokens = 1009` and `decimals = 5` we will have `floor(1009 / 10) * 5 = 100 * 5 = 500`
> - if we multiply first we will have `floor((1009 * 5)/10) = 504`
> This means the wei to send (in the first case) is less that the value of the tokens that will be added. This is a bug that can be fixed by doing the division after the multiplication.
> But worse than that, there's no protection against overflow when required_wei_sent is calculated, which can result in a massive theft.



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

> reentrancy occurs when there's an external call, or ether sent to a contract. In this case, there's no external call, and no ether sent to the contract, so there's no reentrancy risk.

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

> The solidity version is 0.7.0, meaning overflow/underflow can occur. In this case, it can occur at require_wei_sent in the `buy()` function. If desired_tokens is chosen carrefully, result can be very low because of the overflow, meaning the attacker will be allowed to send a very low msg.value, but see its balance granted to the desired_tokens amount.    

<details>
	<summary>Click the reveal the answer</summary>
D or B or C or B,C<br/>
Note: While the initial platform-specified correct answer for Q8 was D, it was determined that this Q&A had some latent ambiguity with answer choices B & C. Therefore, all answer combinations indicated above were considered as valid and scores adjusted accordingly.
</details>