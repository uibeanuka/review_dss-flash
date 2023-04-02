## __Using Pragma solidity 0.6.12Introduction__
 __Author__ :   __DECODE_PRAGMA__
<p>
This is a smart contract for flash loans using the ERC-3156 standard. The contract allows borrowers to borrow a fixed amount of DAI from the contract, with zero fees. The contract uses a reentrancy guard to prevent malicious borrowers from taking advantage of the flash loan feature. The contract also implements the IVatDaiFlashLender interface to enable borrowers to borrow from the Dai Vat, and to repay the loan in Dai to the contract.</p>

### __Table of Contents__
* Functions
* Security Vulnerabilities
* Possible Improvements
* References
### __Functions__
*__rely__*

<p>The rely function adds an address to the list of authorized addresses that can perform administrative functions in the contract. Only authorized addresses can call the auth modifier functions.</p>

### *__deny__*

The deny function removes an address from the list of authorized addresses.

### *__auth modifier__*
The auth modifier checks if the caller is an authorized address, and reverts the transaction if it is not.

### *__constructor__*
The constructor function initializes the contract with the addresses of the DaiJoin contract and the Dai token contract. It also sets the contract owner as an authorized address.

The file function updates the maximum borrowable amount of Dai, which can be borrowed by borrowers. It also emits a File event.

### *__maxFlashLoan__*

The maxFlashLoan function returns the maximum amount of Dai that can be borrowed by borrowers.

### *__flashFee__*
The flashFee function returns the flash loan fee for a given amount of Dai. Since this contract does not charge any fees, this function always returns 0.

### *__flashLoan__*
function flashLoan(
        IERC3156FlashBorrower receiver,
        address token,
        uint256 amount,
        bytes calldata data
    ) external override lock returns (bool) { ... }
The flashLoan function performs the flash loan. It checks if the borrower has requested to borrow Dai, and if the requested amount does not exceed the maximum borrowable amount. The function then transfers the requested amount of Dai to the borrower's contract, and invokes the onFlashLoan function of the borrower's contract. After the borrower has completed their desired actions with the Dai, they repay the borrowed amount of Dai plus any accrued interest to the contract.

### *__vatDaiFlashLoan__*

function vatDaiFlashLoan(
        IVatDaiFlashBorrower receiver,
        uint256 amount,
        bytes calldata data
    ) external override lock returns (bool) { ... }
The vatDaiFlashLoan function performs a flash loan from the Dai Vat. The function transfers the requested amount of Dai to the borrower's contract, and invokes the onVatDaiFlashLoan function of the borrower's contract. After the borrower has completed their desired actions with the Dai, they repay the borrowed amount of Dai plus any accrued interest.

## __Vulnerabilities__
The following are potential vulnerabilities in the DssFlash smart contract:

* __Reentrancy Attack__: There is a locked variable that serves as a reentrancy guard, but it is not clear if this provides complete protection against reentrancy attacks.
* __No Fee__: DssFlash does not charge a fee for flash loans, which means that flash loan arbitrage is possible.
* __Limited Funds__ : DssFlash limits the maximum amount of Dai that can be borrowed to prevent breaking the technical assumptions of Dai. However, this also limits the usefulness of the smart contract.
## __Improvements__
To improve the security of DssFlash, the following changes could be made:

* __Reentrancy Attack__: The contract should use the [OpenZeppelin ](https://docs.openzeppelin.com/contracts/3.x/api/utils#ReentrancyGuard) ReentrancyGuard to prevent reentrancy attacks.
* __Fees__: Implement a fee system that charges a fee for every flash loan. This will make it less profitable for arbitrageurs to exploit the smart contract.
* __Increase Maximum Loan Amount__: Increase the maximum amount of Dai that can be borrowed to make the smart contract more useful.


# INTERFACES

```solidity
 import "./interface/IERC3156FlashLender.sol";
import "./interface/IERC3156FlashBorrower.sol";
import "./interface/IVatDaiFlashLender.sol";

interface DaiLike {
    function balanceOf(address) external returns (uint256);
    function transferFrom(address, address, uint256) external returns (bool);
    function approve(address, uint256) external returns (bool);
}

interface DaiJoinLike {
    function dai() external view returns (address);
    function vat() external view returns (address);
    function join(address, uint256) external;
    function exit(address, uint256) external;
}

interface VatLike {
    function hope(address) external;
    function dai(address) external view returns (uint256);
    function live()
    ```
