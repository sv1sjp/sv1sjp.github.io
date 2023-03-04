
----
+++
title = "War Of Array"
date = "2023-03-04"
author = "Dimitris Vagiakakos"
+++
On this challenge, we have the smart-contract WarOfArray and we are requested to claim the ownership of the contract, and then add 2 numbers on the "myArray" array. 
Using Remix IDE from remix.ethereum.org is recommended on this challenge.
```
// SPDX-License-Identifier: GPL-3.0-or-later
pragma solidity 0.8.18;

contract WarOfArray {
  uint[] public myArray;
  address public owner;

  constructor() {
    owner = address(0x1234567890123456789012345678901234567890);
}


  function addToMyArray(uint _value) public {
    require(msg.sender == owner, "Only the contract owner can add to the array");
    myArray.push(_value);
  }

  function isOwner() public view returns (bool) {
    return (msg.sender == owner);
  }

  function hasTwoElements() public view returns (bool) {
    require(msg.sender == owner, "Only the contract owner can check the array length");
    return (myArray.length >= 2);
  }

  function changeOwnership(address newOwner) public {
    owner = newOwner;
}
}
```
____
### How to deploy WarOfArray?
Paste this smart-contract as a new contract to Remix IDE with the name WarOfArray. Then, on the third tab, click compile and then, in the fourth tab, click deploy in any testnet or directly via browser.

Remix, is the official online IDE provided by Ethereum.org and it is used to create and deploy Ethereum smart contracts using Solidity programming language. It is one of the tools that helped us to deploy our attacks to other smart-contracts or to call contract functions. It has several built-in Solidity compiler versions andit runs through the web browser. Furthermore, it is used to provide a connection with Web3.js to interact with wallets like Metamask in Ethereum Mainet or Testnets. 
___
First of all, let's talk about the constructor. Constructor is an optional function and is used to initialize state variables of a contract. State variables are variables whose values are permanently stored in smart-contract's storage. We have to mention here that each contract can have only one constructor. A constructor code is executed once when a contract is created and it is used to initialize contract state. After a constructor code executed, the final code is deployed to blockchain. 
On this smart-contract, constructor sets a specific address as the owner of this smart-contract.
Furthermore, we can see an array and some functions. However, in order to call them. being the owner is required. But not all of those.  




Unfortunately, the developer of this smart-contract created by mistake a function changeOwnership(address), which as it is public, everyone can call  it and then gain ownership. Using Remix IDE, we can very easily, copy our wallet's public address and then paste it on the changeOwnership() field and then press the button. 

Then, we call the function isOwner() by clicking the button from Remix, and this time, it returns "TRUE". Now we own this smart-contract. 

As we own the smart-contract right now, we can call any function which requires ownership (msg.sender == owner). So all we have to do now is just add 2 numbers on the array by calling the function "addToArray()".

Then, we call the function "hasTwoElements(): and it returns "TRUE" and we have successfully finished the challenge. 

This challenge was a pretty easy one, however it is a nice motivation to try to use Remix IDE to audit and exploit vulnerabilites in smart-contracts.



---
If you want to learn more about Smart Contract Security, please check our paper and eCourses:
## Smart-Contract Security:


* [Smart Contract Security - Educational eLearning Series in Greek on YouTube - Click Here](https://www.youtube.com/playlist?list=PLZa7COjIxKWzLcMxI9cRNSzOtdR0xvXB7)
* [Smart Contract Security - Educational eLearning Series in Greek on LBRY - Click Here](https://odysee.com/@TuxHouse:1/Ethereum-Hacking-Series-%28Greek%29:b?r=D1QgYeP81GoKPkW5T1jP96zxGA4GMfho&lid=b0b540e62d96ed2811b776519fc460617e4c40747)

