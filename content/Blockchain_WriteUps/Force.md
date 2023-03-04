+++
title = "Force - Ethernaut"
date = "2022-01-02"
author = "Dimitris Vagiakakos"
+++
We have an empty smart-contract without any functions, constructor or even Fallback Function. Our goal is to send some Ether to it.

Ethereum smart-contracts typically they have 3 ways to receive ether from an another address.
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Force {/*

                   MEOW ?
         /\_/\   /
    ____/ o o \
  /~____  =ø= /
 (______)__m_m)

*/} 



```

* As we have already mentioned, by using a payable function. We don't have any kind of function on this smart-contract so we cannot use this way.
* By receiving mining rewards. A smart-contract can be designed to be able to receive mining block rewards. Maybe it's not the best solution to our problem right now.
* By destroying an other smart-contract  using the selfdestruct() method in order to force send all of it's ether to an another address or smart contract. Maybe this method solves our problem.

So, it's time to create a new smart-contract which has a public payable function on it with a selfdestruct() command.

```
pragma solidity ^0.8.11;

contract SelfDestructingDapp{
    constructor() public payable{

    }

    function attack(address payable  _contractAddr) payable public {
        
        selfdestruct(_contractAddr);
    }
}
```

Now, we compile and deploy this smart-contract to Ethereum Blockchain with some amount of ether on it. 
Time to call the attack function and give as a parameter the smart-contract's address.
As previously, we use the Console from our web browser to interact with the original smart contract and we type:
```
await contract.address()
```
As we want to find smart-contract's address, we copy the address and then we give it as a parameter in the attack function on SelfDestructing Dapp. 
We wait until the block to be mined and now we can check the balance of the original smart-contract by typing in console:

```
await getBalance(contract.address)
```
And now we can see that it has the amount of ether that the SelfDestructing Dapp had.
Noticeably, when we use the selfdestruct() function and we send the amount of ether in an another address, if this address is not existing or we sent it to an smart-contract which hasn't any transfer() or withdraw() function, it is lost forever. It is of high importance to not burn ether like this, specially in the mainnet as we loose real world money and deflate the Ethereum Network. Additionally, we should never create a contract which uses as a fallback function with a requirement of some ether but code the contract to support transfer only from the owner. Someone can still use selfdestruct() in order to send an amount of ether to the contract and trigger the fallback function without being the owner.


---
If you want to learn more about Smart Contract Security, please check our paper and eCourses:
## Smart-Contract Security:

* [Smart Contract Security - Educational eLearning Series in Greek on YouTube - Click Here](https://www.youtube.com/playlist?list=PLZa7COjIxKWzLcMxI9cRNSzOtdR0xvXB7)


* [Smart Contract Security - Educational eLearning Series in Greek on LBRY - Click Here](https://odysee.com/@TuxHouse:1/Ethereum-Hacking-Series-%28Greek%29:b?r=D1QgYeP81GoKPkW5T1jP96zxGA4GMfho&lid=b0b540e62d96ed2811b776519fc460617e4c40747)
