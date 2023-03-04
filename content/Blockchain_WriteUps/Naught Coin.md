+++
title = "Naught Coin - Ethernaut"
date = "2022-01-06"
author = "Dimitris Vagiakakos"
+++
So now we have the challenge "Naught Coin". Naught Coin is an ERC20 token and we are already holding all of them.
The catch is that we will only be able to transfer them after a 10 year lockout period. Can we figure out how to get them out to another address so that we can transfer them freely?
So, we have NaughtCoins locked for a 10 year period. We have to unlock them and then, get them to an another address. Let's check the code
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import '@openzeppelin/contracts/token/ERC20/ERC20.sol';

 contract NaughtCoin is ERC20 {

  // string public constant name = 'NaughtCoin';
  // string public constant symbol = '0x0';
  // uint public constant decimals = 18;
  uint public timeLock = now + 10 * 365 days;
  uint256 public INITIAL_SUPPLY;
  address public player;

  constructor(address _player) 
  ERC20('NaughtCoin', '0x0')
  public {
    player = _player;
    INITIAL_SUPPLY = 1000000 * (10**uint256(decimals()));
    // _totalSupply = INITIAL_SUPPLY;
    // _balances[player] = INITIAL_SUPPLY;
    _mint(player, INITIAL_SUPPLY);
    emit Transfer(address(0), player, INITIAL_SUPPLY);
  }
  
  function transfer(address _to, uint256 _value) override public lockTokens returns(bool) {
    super.transfer(_to, _value);
  }

  // Prevent the initial owner from transferring tokens until the timelock has passed
  modifier lockTokens() {
    if (msg.sender == player) {
      require(now > timeLock);
      _;
    } else {
     _;
    }
  } 
} 
```


First of all, we should mention some features of the interfaces:
* They cannot inherit from other contracts, but they can inherit from other interfaces.
* All declared functions must be external.
* They cannot declare a constructor, state variables or modifiers.
* Contracts can inherit interfaces as they would inherit other contracts.

So, we can see that the contract NaughtCoin inherits all ERC20 contract functions and standards. As a result, we have to study about how ERC20 tokens are working too.
On ERC20's github repository, we can find more information about its' methods.  As a result, we have to study about how ERC20 tokens are working too.
https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20.md

We can read that in order for a token to be compatible with the ERC20 specification, it must have implementations for the following interface:
```
contract ERC20Interface {
function totalSupply() public constant returns (uint);
function balanceOf(address tokenOwner) public constant returns (uint balance);
function allowance(address tokenOwner, address spender) public constant returns (uint remaining);
function transfer(address to, uint tokens) public returns (bool success);
function approve(address spender, uint tokens) public returns (bool success);
function transferFrom(address from, address to, uint tokens) public returns (bool success);
event Transfer(address indexed from, address indexed to, uint tokens);
event Approval(address indexed tokenOwner, address indexed spender, uint tokens);
}
```

This specification is one of the most popular, which makes finding further documentation much easier. Maybe we should use some of these functions to transfer the tokens.
To begin identifying where we should look at first, we found that looking at ERC20 interface gave us many methods that we would want to look further into. In particular, the first function that jumped out to us was the transferFrom() function. We had identified that NaughtCoin ‘s transfer implementation was rendered useless by the lockTokens modifier, but NaughtCoin did not implement the transferFrom() function, which means that the StandardToken implementation of transferFrom is being called.
Upon reading the source code of the transferFrom function, it appears that it allows a third party to transfer a designated amount of tokens from one account to another account. This is derived from reading the requirements for transfer.

---
If you want to learn more about Smart Contract Security, please check our paper and eCourses:
## Smart-Contract Security:

* [Smart Contract Security - Educational eLearning Series in Greek on YouTube - Click Here](https://www.youtube.com/playlist?list=PLZa7COjIxKWzLcMxI9cRNSzOtdR0xvXB7)


* [Smart Contract Security - Educational eLearning Series in Greek on LBRY - Click Here](https://odysee.com/@TuxHouse:1/Ethereum-Hacking-Series-%28Greek%29:b?r=D1QgYeP81GoKPkW5T1jP96zxGA4GMfho&lid=b0b540e62d96ed2811b776519fc460617e4c40747)
