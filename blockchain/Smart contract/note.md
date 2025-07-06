# Smart Contract

# What is Ethereum

* set of nodes running block chain
* for programmers - web3.js
* for clients - Metamask, Mist Browser



* user account has three main things

  * account address (only one per account for all network)
  * public key
  * private key

* Transaction

  * process
    1. submit transaction
    2. send info to server
    3. Web3.js handles transaction
    4. send transaction to network (e.g. goerli)
    5. wait server to confirm transaction - (validating or mining)
       * it uses nonce to hash the input and get condition-satisfied hashed number
       * if not satisfied, nounce++ and try again
       * time to find a nonce = block time
       * this condition varies based on block time
         * means system can adjust difficulty of condition to manage speed
         * [chart of block time](https://etherscan.io/chart/blocktime)
    6. send transation confirmation back to client

  * transaction is recorded with
    * <u>nonce</u> - how many times the sender has sent a transaction (counter variable)
    * <u>to</u> - target account address
    * <u>value</u> - amount of ether
    * <u>gasPrice</u> - amount of ether the sender is willing to pay per unit gas
    * <u>startGas/gasLimit</u> - units of gas that this transaction can consume
    * <u>v</u> 
    * <u>r</u>
    * <u>s</u>
      * **v r s** are cryptographic pieces of data that can be used to generate the senders account address. **Generated from the sender's private key**. It is not reversible.

  * Demo - [link](https://andersbrownworth.com/blockchain)
    * introduction of blockchain - [link](https://www.udemy.com/course/ethereum-and-solidity-the-complete-developers-guide/learn/lecture/8953586#overview)

# Smart Contract

* an account controlled by code
  * fields
    * balance - amount of ether 
    * storage - data storage for this contract - related to application we are trying to build
    * Code - raw machine code for this contract
      * unreadable for machine code - need interpreter to decode
  
* contract account is only belonged to specific network
  * which is different from user account (external account or client account)
  
* Contract can be deployed to specific network(s)

* lanuage - Solidity

  * .sol files

  * strongly typed language

  * similar to js (not that much similar)

  * a lot gotchas (trickies)

  * contract definition will be written by solidity

  * than compiled by solidity complier

    * generate two things
      * Byte code ready for deployment
      * application binary interface - ABI
        * ABI is human-readable and used in code to interact with contract

  * example contract - remix.ethereum.org

    * ```solidity
      pragma solidity ^0.4.17; // specify version of compiler
      
      contract Inbox { // similar to class
          string private message; // member or variable
      
      		// constructor - old
          function Inbox(string initialMessage) public { 
              message = initialMessage;
          }
          // constructor - new
          constructor(string memory initialMessage) {
              message = initialMessage;
          }
      
      		// setter function
          function setMessage(string newMessage) public {
              message = newMessage;
          }
      
      		// getter function
      		// public view - function type
      		//    view - this function returns data and does not modify the contract's data (same as `constant` keyword, idk why they have two same meaning keywords)
      		// returns (string) - return type
          function getMessage() public view returns (string) {
              return message;
          }
      }
      ```
      
    * Types of function
    
      * <u>public, private</u> - privacy control
      * <u>View, constant</u> - only viewing data instead of editing
      * <u>Pure</u> - not modify and review data
      * <u>payable</u> - might send ether
      * etc...

## Create contract is super similar with transaction

* Nonce - How many times the sender has sent a transaction
* to - empty
* **data** - compiled bytecode of the contract - code part **(key part we care)**
* value - Amount of 'Wei' to send the target address
* gasPrice - Amount of 'Wei' the sender is willing to pay per unit gas to get this transaction processed
* startGas/gasLimit - Units of gas that this transaction can consume
* v / r / s - encryption key

### two different kinds of funtions invoking 

* `Calling` a function - free to call
* Sending a Transaction to Function - take time
  * **15-30 secs for real network** (test network is tuned, so it is super fast)
  * return transaction hash
  * it cannot return value because it is async function
  * costs money

### wei vs ether

* 10^18 Wei = 1 Ether
* 10^9 Gwei = 1 Ether
* 1000 Finney = 1 Ether
* converting tool: https://etherscan.io/unitconverter

### Gas

* 1 gas = x Wei - gasPrice - only need to know it exists. do not  need to know why

* price to do some operations - can be varied for different operations. eg:
  * plus 3 gas
  * Minus 3 gas 
  * equality check 6 gas
  * ....
  * it is hard to be precise, so we set `startGas`/`gasLimit` to set the upper limit

### Truffle

* smart contract development https://trufflesuite.com/ - (already deprecated)
* Provider

# Development with js

* Compile

  * ```js
    // compile code will go here
    const path = require('path');
    const fs = require('fs');
    const solc = require("solc");
    
    // __dirname: current working directory
    // contracts: folder containing the contract
    // Inbox.sol: contract file
    const inboxPath = path.resolve(__dirname, 'contracts', 'Inbox.sol');
    
    const source = fs.readFileSync(inboxPath, 'utf8');
    
    // source: contract source code
    // 1: number of contracts to compile
    module.exports = solc.compile(source, 1).contracts[':Inbox'];
    ```

* Solidity

  * Basic types

    * string

    * bool

    * int
      * uint - unsigned integer (>0) 
        * Uint8, uint16, ......., uint256
        * uint == uint256

    * fixed/ufixed (decimal)

    * Address - account address ?

  * reference types

    * fixed array
    * dynamic array
    * mapping
    * struct

  * The 'msg' global variable

    * msg.data - context from the call or transaction that invoked this function
    * msg.gas - amount of gas the current function invocation has available
    * Msg.sender - address of the account that started the current function invocation
    * Msg.value - amount of ether (in wei) that was sent along with the function invocation



# Lottery Design Demo

* one contract, set of players, one manager
  * Manager puts money in contract
  * Players buy contract
  * contract picks one player as a winner and send all money to this player
* variables 
  * manager
  * players
* functions
  * Enter - enters a player into the lottery
  * pickWinner - randomly picks a winner and sends them the prize pool
