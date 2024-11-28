
Auditor @mashavaverova




## Kompilling errors 

### Summary

| **Error Code/Identifier**               | **Description**                                           | **Cause**                                               | **Solution/Workaround**                                             | **Fix Status**     |
|-----------------------------------------|-----------------------------------------------------------|---------------------------------------------------------|----------------------------------------------------------------------|--------------------|
| Error 3132: Missing `emit` Keyword      | Missing `emit` keyword for event invocations               | The `emit` keyword is missing before events              | Add `emit` before events like `emit MembershipTokensSet(...)`        | Fixed   |
| Error 2271: Incompatible Types in `require` | `bool` compared with `0`, incompatible types              | `bool` value compared with an integer (`0`)              | Directly check the boolean in the `require` statement                | Fixed|
| Error 9582: Member Function Not Found   | `history` function missing in the `IBrightID` contract     | The `history` function is not defined                    | Add the `history` function or remove its usage                       | Fixed  |
| Error 5883: Event Shadowing             | `Verified` event is declared in multiple places            | Event `Verified` is declared in both `StoppableBrightID.sol` and `IBrightID.sol` | Rename one of the events or emit it from only one contract            | Fixed   |
| Warning 2519: Shadowing Warning         | `Sponsor` event shadows another declaration                | Event and interface share the same name                  | Rename either the event or the interface                             | Fixed   |
| Warning 2462: Visibility Ignored        | Visibility specifier for constructor is ignored            | Constructor visibility is not required in Solidity 0.8.x | Remove the visibility specifier or mark the contract as abstract     | Fixed  |
| Error 3881: Documented Parameter Not Found | `_claimAddress` parameter is documented but not found     | Parameter in the documentation doesn’t match function parameters | Update the comment to match the actual function parameters           | Fixed   |
| SafeMath Usage in Solidity 0.8.x        | `SafeMath` is used unnecessarily                           | Solidity 0.8.x handles overflow/underflow checks         | Remove `SafeMath` and use basic arithmetic operations                | Fixed |





### [INFO 1] SafeMath Usage in Solidity 0.8.x

**Description:** 
The SafeMath library is being used, but it’s not needed anymore for uint256 in Solidity 0.8.x because the compiler already checks for overflow.


**Recommended Mitigation:** 
<details>
<summary>Click to expand</summary>
- Remove SafeMath.
- use simple math like +, -, *, / instead.
</details>


### [INFO 2] Error 3881: Documented parameter _claimAddress not found

**Description:** 
 The error happens because there is a parameter called _claimAddress in the comments (probably using @param), but this parameter doesn’t exist in the function's actual code in BrightIDFaucetVerifier.sol.

**Recommended Mitigation:** 
<details>
<summary>Click to expand</summary>
- Check the parameter list in the comments and the function.
- Either update the comment to match the actual parameters or remove _claimAddress from the comment if it's not used.
</details>

### [INFO 3] Error 5883: Shadowing Warning 

**Description:** 
In the Sponsor.sol contract, the event called Sponsor is causing a conflict with an existing declaration because there’s also an interface with the same name. This creates a shadowing issue, where the two declarations overlap.

**Recommended Mitigation:** 
<details>
<summary>Click to expand</summary>
- Rename either the event or the interface to avoid the conflict.
</details>

### [INFO 4] Error 5883: Shadowing Warning 

**Description:** 
 The Verified event is declared twice with the same name and parameter types in both StoppableBrightID.sol and IBrightID.sol. Solidity doesn’t allow the same event to be declared in two places.

**Recommended Mitigation:** 
<details>
<summary>Click to expand</summary>
- Rename one of the events, for example, in StoppableBrightID.sol you could change it to AddressVerified.
- Alternatively, if both contracts depend on the same event, only declare it in one contract and emit it from there.
</details>

### [INFO 5] Error 2271: Incompatible Types in require Statement (Error 2271)

**Description:** 
The function brightid.isVerified(beneficiary) returns a boolean (bool), but the code is trying to compare it with 0, which is an integer. In Solidity, you can’t compare a bool with an integer.

**Recommended Mitigation:** 
<details>
<summary>Click to expand</summary>
- Remove the comparison with 0 and check the boolean value directly.
- For example, change the statement to:

```
require(brightid.isVerified(beneficiary), "beneficiary is not verified");

```
</details>


### [INFO 6] Error 9582: Member Function Not Found 

**Description:** 
The history function is not found in the IBrightID contract. This happens because the function is either not defined in IBrightID.sol or isn’t supposed to be there.

**Recommended Mitigation:**
<details>
<summary>Click to expand</summary> 
- If the function is not needed, remove 
</details>

### [INFO 7] Error 2462: Visibility for Constructor is Ignored

**Description:** 
In Solidity 0.8.x, the visibility specifier (like public or external) for constructors is no longer needed. The visibility setting is ignored, and constructors are public by default.

**Recommended Mitigation:**
<details>
<summary>Click to expand</summary>
- Remove the visibility specifier from the constructor
- If the contract should be non-deployable, mark it as `abstract` by adding abstract before the contract definition
</details>

### [INFO 8] Error 3132: Missing emit Keyword for Events

**Description:** 
 Since Solidity 0.4.21, event invocations must include the emit keyword before the event name. The code is missing the emit keyword before some event calls.

**Recommended Mitigation:** 
<details>
<summary>Click to expand</summary>
- Add the emit keyword before event calls.
</details>



##  Runtime Errors and Warnings 

### Summary

| Category | No. of Issues |
| --- | --- |
| High | 1 |
| Low | 4 |
| Info | 2 |



### H-1: Contract Name Reused in Different Files

<details><summary>2 Found Instances</summary>


- Found in src/BrightID.sol, Line: 7

	```solidity
	contract BrightID is Ownable, IBrightID {
	```

- Found in src/BrightIDFaucetVerifier.sol, Line: 9

	```solidity
	contract BrightID is Ownable {
	```

</details>


### L-1: `ecrecover` is susceptible to signature malleability
The `ecrecover` function is susceptible to signature malleability. This means that the same message can be signed in multiple ways, allowing an attacker to change the message signature without invalidating it. This can lead to unexpected behavior in smart contracts, such as the loss of funds or the ability to bypass access control. Consider using OpenZeppelin's ECDSA library instead of the built-in function.

<details><summary>3 Found Instances</summary>


- Found in src/BrightID.sol, Line: 76

	```solidity
	        address signer = ecrecover(message, v, r, s);
	```

- Found in src/BrightIDFaucetVerifier.sol, Line: 90

	```solidity
	        address signer = ecrecover(message, v, r, s);
	```

- Found in src/StoppableBrightID.sol, Line: 132

	```solidity
	        address signer = ecrecover(message, v, r, s);
	```

</details>

### L-2: Unsafe ERC20 Operations should not be used

ERC20 functions may not behave as expected. For example: return values are not always meaningful. It is recommended to use OpenZeppelin's SafeERC20 library.

<details><summary>1 Found Instances</summary>


- Found in src/Distribution.sol, Line: 32

	```solidity
	        beneficiary.transfer(amount);
	```

</details>

### L-3: Missing checks for `address(0)` when assigning values to address state variables

Check for `address(0)` when assigning values to address state variables.

<details><summary>9 Found Instances</summary>


- Found in src/BrightID.sol, Line: 28

	```solidity
	        verifierToken = _verifierToken;
	```

- Found in src/BrightID.sol, Line: 56

	```solidity
	        verifierToken = _verifierToken;
	```

- Found in src/BrightIDFaucetVerifier.sol, Line: 43

	```solidity
	        verifierToken = _verifierToken;
	```

- Found in src/BrightIDFaucetVerifier.sol, Line: 6

	```solidity
	        verifierToken = _verifierToken;
	```

- Found in src/Distribution.sol, Line: 20

	```solidity
	        brightid = IBrightID(addr);
	```

- Found in src/StoppableBrightID.sol, Line: 49

	```solidity
	        supervisorToken = _supervisorToken;
	```

- Found in src/StoppableBrightID.sol, Line: 50

	```solidity
	        proposerToken = _proposerToken;
	```

- Found in src/StoppableBrightID.sol, Line: 81

	```solidity
	        supervisorToken = _supervisorToken;
	```

- Found in src/StoppableBrightID.sol, Line: 82

	```solidity
	        proposerToken = _proposerToken;
	```

</details>

### L-4: State variable changes but no event is emitted.

State variable changes in this function but no event is emitted.

<details><summary>3 Found Instances</summary>


- Found in src/Distribution.sol [Line: 15](src/Distribution.sol#L15)

	```solidity
	    function setClaimable(uint256 _claimable) public onlyOwner {
	```

- Found in src/Distribution.sol [Line: 19](src/Distribution.sol#L19)

	```solidity
	    function setBrightid(address addr) public onlyOwner {
	```

- Found in src/Distribution.sol [Line: 23](src/Distribution.sol#L23)

	```solidity
	    function claim(address payable beneficiary, uint256 amount) public {
	```

</details>




### [INFO 1]: Inconsistency in declaring uint256/uint (or) int256/int variables within a contract. Use explicit size declarations (uint256 or int256).

Consider keeping the naming convention consistent in a given contract. Explicit size declarations are preferred (uint256, int256) over implicit ones (uint, int) to avoid confusion.


### [INFO 2]: Event is missing `indexed` fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.







