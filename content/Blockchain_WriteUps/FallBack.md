+++
title = "Fallback - Ethernaut"
date = "2021-12-19"
author = "Dimitris Vagiakakos"
+++

We have the smart-contract Fallback and we are requested to claim the ownership of the contract, and then reduce it's balance to 0.
At first, let's read the smart contract's code and search if something interesting is going on there:

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import '@openzeppelin/contracts/math/SafeMath.sol';

contract Fallback {

  using SafeMath for uint256;
  mapping(address => uint) public contributions;
  address payable public owner;

  constructor() public {
    owner = msg.sender;
    contributions[msg.sender] = 1000 * (1 ether);
  }

  modifier onlyOwner {
        require(
            msg.sender == owner,
            "caller is not the owner"
        );
        _;
    }

  function contribute() public payable {
    require(msg.value < 0.001 ether);
    contributions[msg.sender] += msg.value;
    if(contributions[msg.sender] > contributions[owner]) {
      owner = msg.sender;
    }
  }

  function getContribution() public view returns (uint) {
    return contributions[msg.sender];
  }

  function withdraw() public onlyOwner {
    owner.transfer(address(this).balance);
  }

  receive() external payable {
    require(msg.value > 0 && contributions[msg.sender] > 0);
    owner = msg.sender;
  }
}
```
First of all, let's talk about the constructor. Constructor is an optional function and is used to initialize state variables of a contract. State variables are variables whose values are permanently stored in smart-contract's storage. We have to mention here that each contract can have only one constructor. A constructor code is executed once when a contract is created and it is used to initialize contract state. After a constructor code executed, the final code is deployed to blockchain. Taking this into account, under the deployment of the smart-contract, the constructor runs at first and sets the person who deployed the smart-contract as an owner in the public Boolean variable "owner", by the command: 
```
owner = msg.sender;
```

If we type in console:
```
await contract.owner()
```
We can see the address that deployed this smart contract and by typing:
```
contract.address
```
We can find the smart-contract's address. Each user and each smart-contract in Ethereum Blockchain has it's own address. If we type "player" we can find our wallet's address. 
So, to begin with, as we want to claim ownership of the smart-contract, we are trying to find out something that sets us as the owner. We can see that there is a receive() external function that if we pass it's requirements, it sets us as the owner of the smart-contract. It looks interesting. From Solidity's documentation, we can find out that receive() is a fallback function that is executed when we create general transactions, without calling a specific function from the contract. The receive() function is executed on a call to the contract with empty calldata. This type of functions cannot have arguments or return anything. We keep this in our minds for now.
The second requirements wants for us to be in the contributions array and having us sent an amount of ether more than 0. We check the contributions array if we are inside on it by calling:
```
await contract.contributions(player)
```

However, it returns as false. So, let's check the code again to find a way to add ourselves in "contributions". We can see that a payable function exists and requires to get an amount of ether smaller than 0.01 ether. We can simply send 1 Wei, the smallest amount of ether that we can send to someone by typing in console:
```
contract.contribute({value:1})
```

So now we added ourselves in the "contributions" array. Now we pass the one requirement. It's time to send a small amount of money (1 Wei as the requirement needs to be more than 0) in a generic transaction in order to pass the second requirement in the receive() function and trigger it. We type:
```
contract.sendTransaction({value:1})
```

After all of this, we can check again who is the owner of the smart-contract and we can see our address on this variable. We successfully own the smart-contract now. Let's reduce it's balance to zero.
The "withdraw" function can help us with that which can be executed only from the owner of the contract. But now we are the owner. It's time to withdraw all of it's ether. We type in console:
```
contract.withdraw()
```
And then, let's check the balance of the smart-contract:
```
await getBalance(contract.address)
```
We will see a balance of 0 on this smart-contract.That means that we successfully withdrawn all of it's ether. We successfully won this game!!!

---
If you want to learn more about Smart Contract Security, please check our paper and eCourses:
## Smart-Contract Security:

* [Smart Contract Security - Educational eLearning Series in Greek on YouTube - Click Here](https://www.youtube.com/playlist?list=PLZa7COjIxKWzLcMxI9cRNSzOtdR0xvXB7)


* [Smart Contract Security - Educational eLearning Series in Greek on LBRY - Click Here](https://odysee.com/@TuxHouse:1/Ethereum-Hacking-Series-%28Greek%29:b?r=D1QgYeP81GoKPkW5T1jP96zxGA4GMfho&lid=b0b540e62d96ed2811b776519fc460617e4c40747)

