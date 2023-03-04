+++
title = "King - Ethernaut"
date = "2022-01-07"
author = "Dimitris Vagiakakos"
+++
We have a smart contract game called Kings. This contract is a classic example of a ponzi scheme. 
Whoever sends an amount of ether that is more than the current prize becomes the new king. On such an event, the overthrown king gets paid the new prize, making a bit of ether in the process.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract King {

  address payable king;
  uint public prize;
  address payable public owner;

  constructor() public payable {
    owner = msg.sender;  
    king = msg.sender;
    prize = msg.value;
  }

  receive() external payable {
    require(msg.value >= prize || msg.sender == owner);
    king.transfer(msg.value);
    king = msg.sender;
    prize = msg.value;
  }

  function _king() public view returns (address payable) {
    return king;
  }
}
```

It should be mentioned here that when we will try to submit King.sol instance back to level, Ethernaut will call the fallback function to regain Kingship. As we can understand, we have to find a way to guarantee that all Ethernaut's transactions will fail, so we can remain as a King. Let's check the code into fallback function:
```
receive() external payable {
    require(msg.value >= prize || msg.sender == owner);
    king.transfer(msg.value);
    king = msg.sender;
    prize = msg.value;
  }
 ```
 
 The fallback is an external payable and has as a requirement to be called that the sender sends a higher amount of ether than it is the current price and the msg.sender to be the owner. Then, all of the sended ether is transfered to king and we become the king and our value of ether is sets us the current prize. They key here is the "king.transfer()" method which can fail if the current king is a malicious contract and refuses to withdraw.
There are multiple ways for transactions to fail, but most of the time we have these 3 options:
* Out Of Gas Errors: The receiving contract has a malicious payable function that consumes a large amount of gas, which fails the transaction or over-consumes the gas limit. However cannot make the internal call here as there is not really something we can control.
* Arbitrary Error from FallBack Function. If our transaction ends up executing code from a contract that can always fail.
* Transact with Contract with no fallback function or a non payable fallback function. When a transaction is made towards a contract address without including any data the fallback function of that contract is called. If a contract does not have a fallback function, it will fail. Also, It will fall if  we send some ether in a transaction without a payable modifier in the fallback.
* So, we can create a contract with a function that will send ether to the original contract, however without a fallback function so that the king contract cannot send money back.
We create and deploy a malicious smart contract with at least 1 Ether on it.
 
```
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.10;

contract KingAttack {

  function doYourThing(address _target) public payable {
    (bool result,) = _target.call{value:msg.value}("");
    if(!result) revert("MyBad");
  }

  
}
```

This script has a payable function which will send an ether to the contract. As the original smart contract has a payable external fallback function, but it has requirements in order to be executed, it will fail as it will can't handle the 1 ether that we sent it from the attacking contract. As a result, we will become the king forever.

---
If you want to learn more about Smart Contract Security, please check our paper and eCourses:
## Smart-Contract Security:

* [Smart Contract Security - Educational eLearning Series in Greek on YouTube - Click Here](https://www.youtube.com/playlist?list=PLZa7COjIxKWzLcMxI9cRNSzOtdR0xvXB7)


* [Smart Contract Security - Educational eLearning Series in Greek on LBRY - Click Here](https://odysee.com/@TuxHouse:1/Ethereum-Hacking-Series-%28Greek%29:b?r=D1QgYeP81GoKPkW5T1jP96zxGA4GMfho&lid=b0b540e62d96ed2811b776519fc460617e4c40747)
