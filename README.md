# Digital Twin



# Table of Contents



- [x] Geth

  - [x] Install Geth
  - [x] Account Management
    - [x] What is Keystore?
  - [x] Setup Genesis Block
  - [x] Setup Bootstrap Node
  - [x] Javascript Console
    - [x] Console Via IPC
    - [x] List of Accounts
    - [x] Get Account Balance
    - [x] Send Transaction
    - [x] Transaction Hash
    - [x] Extract ENR
  - [x] Setup Members Node
  - [x] Setup HTTP-RPC Server
  - [x] Curl
    - [x] List of Accounts
    - [x] Get Account Balance
- [x] Projects Private Blockchain
  - [x] Install Node.js
    - [x] REST API Backend Blockchain (Node.js)

  - [x] Install Nginx
    - [x] Start Nginx
    - [x] Setup Reverse Proxy

  - [x] Start Geth
  - [x] Start Miner
    - [x] Setup Coinbase
    - [x] Start Mining

  - [x] Test Curl
  - [x] Test Metamask
  - [x] Test Remix

- [x] Solidity
  - [x] Fungible Token (ERC20)
  - [x] Non-fungible Token (ERC721)
  - [x] Semi-fungible Token (ERC1155)




---



# Geth

Geth is one of the ethereum clients.

<img src="assets\Ethereum Clients.png" style="zoom:110%;" />

Geth is :

- Software to create Ethereum Node
- Ethereum Node to store blockchain data
- Ethereum Node to create wallet
- Ethereum Node to create transaction

Here is the visualization of Externally Owned Account (EOA) generation on Geth :



<img src="assets\EOAs.png" style="zoom:110%;" />



----



## Install Geth

Execute this command :

```
sudo apt-get install software-properties-common
sudo add-apt-repository -y ppa:ethereum/ethereum
sudo apt-get update
sudo apt-get install ethereum
```

Note :

Data Directory
Linux: ~/.ethereum



----



## Account Management

To create new account :

```
$ geth account new --datadir keystore
```

Fill the password and account data will be stored here :

```
(/datadir/keystore)
```

If you want to create keystore with custom path add this flag :

```
--keystore <path>
```

Here is an example with custom path :

```
$ geth account new --datadir keystore
```

e.g generated account :

```
Public address of the key:   0xdA3a3503D4a671BC4aAbF2A84a174b6840D12F26
Path of the secret key file: keystore/keystore/UTC--2022-11-28T08-00-29.777223046Z--da3a3503d4a671bc4aabf2a84a174b6840d12f26
```



---



### What is keystore?

Keystore is an encrypted private key.



---



## Setup Genesis Block

Create genesis.json

```
nano keystore/genesis.json
```

Genesis Block Content

```
{
  "config": {
    "chainId": 5758,
    "homesteadBlock": 0,
    "eip150Block": 0,
    "eip155Block": 0,
    "eip158Block": 0,
    "byzantiumBlock": 0,
    "constantinopleBlock": 0,
    "petersburgBlock": 0,
    "istanbulBlock": 0,
    "berlinBlock": 0,
    "ethash": {}
  },
  "difficulty": "1",
  "gasLimit": "8000000",
  "alloc": {
    "0xdA3a3503D4a671BC4aAbF2A84a174b6840D12F26": { "balance": "1000000000000000000000000" },
    "0x4C1f4c7f17d827B72adE9BfCca906f4Ad35A34b1": { "balance": "2000000000000000000000000" },
  }
}
```



---



## Initiate Genesis Block

Initiate before running geth

```
$ geth init --datadir keystore keystore/genesis.json
```



---



## Setup Bootstrap Node

Minimum setup :

```
$ geth --datadir keystore --networkid 575800 --nat extip:34.204.143.145
```



---



## Javascript Console

Here is the visualization of how javascript console on geth :

<img src="assets\JSON-RPC.png" style="zoom:110%;" />



---



### Console via IPC



<img src="assets\IPC-HTTP.png" style="zoom:110%;" />

Here command to initiate javascript console via IPC :

```
$ geth attach keystore/geth.ipc
```



---



### List of Accounts

```
> eth.accounts
```

Or we can access account by array :

```
> eth.accounts[0]
```



---



### Get Account Balance

To get balance in Ether 

```
> web3.fromWei(eth.getBalance(account[0]), "ether")
```



---



### Send Transaction

```
> eth.sendTransaction({
    from: eth.accounts[0],
    to: "0x4c1f4c7f17d827b72ade9bfcca906f4ad35a34b1",
    value: web3.toWei(50000, "ether")
})
```

Note :

If you are not unlock the account first you will get an error :

```
Error: authentication needed: password or unlock
```

Here is an example to send transaction using personal namespace :

```
> personal.sendTransaction({ from: eth.accounts[0], to: eth.accounts[1], value: web3.toWei(5000, "ether")}, "twin")
```



---



### Transaction Hash

To get transaction hash execute this command :

```
> eth.getTransaction("0x....")
```



---



### Extract ENR

To Extract ENR from bootstrap node, execute this command :

```
$ geth attach --exec admin.nodeInfo.enr data/geth.ipc
```



---



## Setup Members Node

Initiate with same genesis block and then execute this command :

```
$ geth --datadir keystore --networkid 575800 --port 30303 --bootnodes "enr:-KO4QN5idA3-5gqCBDs9fY3yQzJdbOV9lFMY7XI0DcO_gR7TaQyN6HWg3Wk5h-A8dfgPODBOjZPws_DpzkaseyamH5qGAYS9U9S6g2V0aMfGhGy3DSKAgmlkgnY0gmlwhCLMj5GJc2VjcDI1NmsxoQMGmold5HWo_9mUlM5ceXgkDe9NqCrUygjnOkp3ErvN9IRzbmFwwIN0Y3CCdl-DdWRwgnZf"

```



---



## Setup HTTP-RPC Server

Flag --http expose localhost:8545 and Geth reject request from the outside.

```
$ geth --datadir keystore --networkid 575800 --nat extip:34.204.143.145 -http
```



---



## Curl



### List of Accounts 

```
curl -X POST http://127.0.0.1:8545 \
    -H "Content-Type: application/json" \
   --data '{"jsonrpc":"2.0", "method":"eth_accounts","params":[], "id":1}'
```



---



### Get Account Balance

```
curl -X POST http://127.0.0.1:8545 \
  -H "Content-Type: application/json" \
  --data '{"jsonrpc":"2.0", "method":"eth_getBalance", "params":["0xca57f3b40b42fcce3c37b8d18adbca5260ca72ec","latest"], "id":1}'
```



---



# Project Private Blockchain



## Install Node.js

Install node.js

```
curl -fsSL https://deb.nodesource.com/setup_current.x | sudo -E bash -
sudo apt-get install -y nodejs
```



---



### REST API Backend Blockchain

Here is the REST API Backend Blockchain

```javascript
const express = require("express");
const app = express();
const port = process.env.PORT || 3000;
const Web3 = require("web3");
const web3HTTP = new Web3(
  new Web3.providers.HttpProvider(
    "https://nd-157-795-816.p2pify.com/f11026e08a311d7f19b7959e065ed5c2"
  )
);
const web3Websocket = new Web3(
  new Web3.providers.WebsocketProvider(
    "wss://ws-nd-157-795-816.p2pify.com/f11026e08a311d7f19b7959e065ed5c2"
  )
);
const axios = require("axios");

const contractABI = JSON.parse(
  '[{"inputs":[{"internalType":"string","name":"name_","type":"string"},{"internalType":"string","name":"symbol_","type":"string"}],"stateMutability":"nonpayable","type":"constructor"},{"anonymous":false,"inputs":[{"indexed":true,"internalType":"address","name":"owner","type":"address"},{"indexed":true,"internalType":"address","name":"spender","type":"address"},{"indexed":false,"internalType":"uint256","name":"value","type":"uint256"}],"name":"Approval","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"internalType":"address","name":"previousOwner","type":"address"},{"indexed":true,"internalType":"address","name":"newOwner","type":"address"}],"name":"OwnershipTransferred","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"internalType":"address","name":"from","type":"address"},{"indexed":true,"internalType":"address","name":"to","type":"address"},{"indexed":false,"internalType":"uint256","name":"value","type":"uint256"}],"name":"Transfer","type":"event"},{"inputs":[{"internalType":"address","name":"owner","type":"address"},{"internalType":"address","name":"spender","type":"address"}],"name":"allowance","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"address","name":"spender","type":"address"},{"internalType":"uint256","name":"amount","type":"uint256"}],"name":"approve","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"address","name":"account","type":"address"}],"name":"balanceOf","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"address","name":"account","type":"address"},{"internalType":"uint256","name":"amount","type":"uint256"}],"name":"burningCTToken","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[],"name":"decimals","outputs":[{"internalType":"uint8","name":"","type":"uint8"}],"stateMutability":"pure","type":"function"},{"inputs":[{"internalType":"address","name":"spender","type":"address"},{"internalType":"uint256","name":"subtractedValue","type":"uint256"}],"name":"decreaseAllowance","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"address","name":"spender","type":"address"},{"internalType":"uint256","name":"addedValue","type":"uint256"}],"name":"increaseAllowance","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[],"name":"name","outputs":[{"internalType":"string","name":"","type":"string"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"owner","outputs":[{"internalType":"address","name":"","type":"address"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"symbol","outputs":[{"internalType":"string","name":"","type":"string"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"totalSupply","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"address","name":"to","type":"address"},{"internalType":"uint256","name":"amount","type":"uint256"}],"name":"transfer","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"address","name":"from","type":"address"},{"internalType":"address","name":"to","type":"address"},{"internalType":"uint256","name":"amount","type":"uint256"}],"name":"transferFrom","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"address","name":"newOwner","type":"address"}],"name":"transferOwnership","outputs":[],"stateMutability":"nonpayable","type":"function"}]'
);
const contractAddress = "0xac856ad608878226999fd0b27181db9b1b39eac3";
const privateKey =
  "YOUR PRIVATE KEY";
const xToken = new web3HTTP.eth.Contract(contractABI, contractAddress);
const fromAddress = "0xD8Db962fC4193E6dc221b9105b27a7a95392c700";

let contract = new web3HTTP.eth.Contract(contractABI, contractAddress, {
  from: fromAddress,
});

let contractWS = new web3Websocket.eth.Contract(contractABI, contractAddress, {
  from: fromAddress,
});

const options = {
  filter: {
    value: [],
  },
  fromBlock: 0,
};

contractWS.events
  .Transfer(options)
  .once("data", (res) => {
    console.log("q-data : ", res);
  })
  .once("changed", (res) => {
    console.log("q-changed : ", res);
  })
  .once("error", (res) => {
    console.log("q-error : ", res);
  })
  .once("connected", (res) => {
    console.log("q-connected : ", res);
  });

// Parse JSON bodies for this app.
app.use(express.json());

app.get("/v1/getBlockNumber", async (req, res) => {
  res.json({ blockNumber: `${await web3HTTP.eth.getBlockNumber()}` });
});

app.post("/v1/getBalance", async (req, res) => {
  // JavaScript object containing the parse JSON
  const { address } = req.body;
  // The current balance for the given address in wei.
  let balance = await web3HTTP.eth.getBalance(address);
  const etherValue = Web3.utils.fromWei(balance, "ether");
  res.json({ balance: `${etherValue}` });
});

app.post("/v1/getTokenBalance", async (req, res) => {
  const { tokenAddress } = req.body;
  const getData = () =>
    new Promise((resolve, reject) => {
      xToken.methods.balanceOf(tokenAddress).call(function (err, res) {
        if (err) {
          reject(err);
          return;
        }
        resolve(res);
      });
    });
  const result = await getData();
  const inEtherValue = Web3.utils.fromWei(result, "ether");
  res.json({ tokenBalance: inEtherValue });
});

app.post("/v1/transfer", async (req, res) => {
  // JavaScript object containing the parse JSON
  const { toAddress, amountToken } = req.body;
  let amount = web3HTTP.utils.toHex(web3HTTP.utils.toWei(amountToken));
  let data = contract.methods.transfer(toAddress, amount).encodeABI();
  web3HTTP.eth
    .getTransactionCount("0xD8Db962fC4193E6dc221b9105b27a7a95392c700")
    .then(console.log);
  let result = await sendErcToken(data, contractAddress, fromAddress);
  res.json(result?.transactionHash);
});

app.post("/v1/passEvent", async (req, res) => {
  const options = {};
});

app.listen(port, () => {
  console.log("CTT dApps API Ready: " + port);
});

function sendErcToken(data, contractAddress, fromAddress) {
  return new Promise((resolve, reject) => {
    let txObj = {
      gas: web3HTTP.utils.toHex(100000),
      to: contractAddress,
      value: "0x00",
      data: data,
      from: fromAddress,
    };

    web3HTTP.eth.accounts.signTransaction(
      txObj,
      privateKey,
      (err, signedTx) => {
        if (err) {
          reject(err);
          return;
        } else {
          // console.log(signedTx);

          web3HTTP.eth
            .sendSignedTransaction(signedTx.rawTransaction, (err, res) => {
              if (err) {
                console.log(err);
              }
            })
            .once("sending", function (payload) {
              // console.log("On Sending");
              // console.log(payload);
            })
            .once("sent", function (payload) {
              // console.log("On Sent");
              // console.log(payload);
            })
            .once("transactionHash", function (hash) {
              // console.log("On Transaction Hash");
              // console.log(hash);
            })
            .once("receipt", function (receipt) {
              // console.log("On Receipt");
              // console.log(receipt);
            })
            .once(
              "confirmation",
              function (confNumber, receipt, latestBlockHash) {
                console.log("On Confirmation " + confNumber);
                // console.log(receipt);
                // console.log(latestBlockHash);
              }
            )
            .then(function (receipt) {
              // console.log(receipt);
              resolve(receipt);
            });
        }
      }
    );
  });
}

async function testGetBlockNumber() {
  const res = await axios
    .get("http://localhost:3000/v1/getBlockNumber")
    .catch((err) => console.log(err));
  console.log(res.data);
}

async function testGetBalance() {
  const res = await axios
    .post("http://localhost:3000/v1/getBalance", {
      address: "0xD8Db962fC4193E6dc221b9105b27a7a95392c700",
    })
    .catch((err) => console.log(err));
  console.log(res.data);
}

async function testTransfer() {
  const res = await axios
    .post("http://localhost:3000/v1/transfer", {
      toAddress: "0xb93e72F826Ad7f3d63Fc50ee29775939B65CAdeb",
      amountToken: "1000",
    })
    .catch((err) => console.log(err));
  console.log(res.data.transactionHash);
}

// testGetBlockNumber();
// testGetBalance();
//testTransfer();

```



---



## Install Nginx

Install nginx

```
sudo apt install software-properties-common -y
sudo add-apt-repository ppa:ondrej/nginx
sudo apt update
sudo apt install nginx -y
```



---



### Start Nginx

Execute this command :

```
sudo /etc/init.d/nginx start
```

Make sure nginx is working :

```
sudo systemctl status nginx
```



---



### Setup Reverse Proxy

Edit default configuration :

```
sudo nano /etc/nginx/sites-enabled/default
```

Configure reverse proxy :

```
server {
    listen 80 default_server;
    
    location / {
        proxy_pass http://127.0.0.1:8545/;
        proxy_set_header Host $host;
    }
}
```

Make sure everything is work properly :

```
sudo nginx -t
```

Restart nginx :

```
sudo systemctl restart nginx
```



## Start Geth 

Start Bootstrap node with HTTP-RPC 

```
$ geth --datadir keystore --networkid 575800 --nat extip:34.204.143.145 -http --http.api web3,eth,debug,personal,net --ws --ws.api web3,eth,debug,personal,net
```

With Web Socket-RPC

```
$ geth --datadir keystore --networkid 575800 --nat extip:34.204.143.145 -http --http.api web3,eth,debug,personal,net --ws --ws.api web3,eth,debug,personal,net --ws --was.api web3,eth,debug,personal,net
```



---



## Start Miner

First you need to activate the console :

```
$ geth attach keystore/geth.ipc
```



### Setup Coinbase

Configure coinbase

```
> miner.setEtherbase("address")
```



### Start Mining

```
> miner.start(1)
```



---



### Test Curl

```
curl -X POST http://34.204.143.145:80 \
--data '{"jsonrpc":"2.0","method":"eth_accounts","params":[],"id":1}' \
-H 'Content-Type: application/json'
```



---



### Test Metamask

Configuration Network on metamask :

<img src="D:\Reinstate 2022\Digital Twin\assets\Metamask.JPG" style="zoom:110%;" /> 



---



### Test Remix

Go to :

```
https://remix.ethereum.org/
```

Click deploy & run transaction menu, on environment pick injected provider metamask :

<img src="D:\Reinstate 2022\DTwin\assets\Remix.JPG" style="zoom:110%;" />

You can compile and deploy



---



# Solidity



## Fungible Token (ERC20) 

```solidity
// SPDX-License-Identifier: GPL-3.0

pragma solidity 0.8.17;

abstract contract Ownable {
    address private _owner;

    event OwnershipTransferred(address indexed previousOwner, address indexed newOwner);
 
    constructor() {
        _transferOwnership(msg.sender);
    }
 
    function owner() public view virtual returns (address) {
        return _owner;
    }
 
    modifier onlyOwner() {
        require(owner() == msg.sender, "Ownable: caller is not the owner");
        _;
    }
 
    function transferOwnership(address newOwner) public virtual onlyOwner {
        require(newOwner != address(0), "Ownable: new owner is the zero address");
        _transferOwnership(newOwner);
    }
 
    function _transferOwnership(address newOwner) internal virtual {
        address oldOwner = _owner;
        _owner = newOwner;
        emit OwnershipTransferred(oldOwner, newOwner);
    }
}

interface IERC20 {
    
    function name() external view returns (string memory);
    
    function symbol() external view returns (string memory);
    
    function decimals() external pure returns (uint8);
    
    function totalSupply() external view returns (uint256);
    
    function balanceOf(address account) external view returns (uint256);
    
    function transfer(address to, uint256 amount) external returns (bool);
    
    function allowance(address owner, address spender) external view returns (uint256);
    
    function approve(address spender, uint256 amount) external returns (bool);
    
    function transferFrom(
        address from,
        address to,
        uint256 amount
    ) external returns (bool);
    
    event Transfer(address indexed from, address indexed to, uint256 value);
    
    event Approval(address indexed owner, address indexed spender, uint256 value);
}

contract CashTreeToken is IERC20, Ownable{
    mapping(address => uint256) private _balances;
    mapping(address => mapping(address => uint256)) private _allowances;
    uint256 private _totalSupply; 
    string private _name;
    string private _symbol;

    constructor(string memory name_, string memory symbol_) {
        _name = name_;
        _symbol = symbol_; 
        _mint(msg.sender, 5000000000 ether); 
    }

    function name() public override view returns (string memory) {
        return _name;
    }

    function symbol() public override view returns (string memory) {
        return _symbol;
    }

    function decimals() public override pure returns (uint8) {
        return 18;
    }

    function totalSupply() public override view returns (uint256) {
        return _totalSupply;
    }

    function balanceOf(address account) public override view returns (uint256) {
        return _balances[account];
    }

    function transfer(address to, uint256 amount) public override returns (bool) {
        address owner = msg.sender;
        _transfer(owner, to, amount);
        return true;
    }

    function allowance(address owner, address spender) public override view returns (uint256) {
        return _allowances[owner][spender];
    }

    function approve(address spender, uint256 amount) public override returns (bool) {
        address owner = msg.sender;
        _approve(owner, spender, amount);
        return true;
    }

    function transferFrom(
        address from,
        address to,
        uint256 amount
    ) public override returns (bool) {
        address spender = msg.sender;
        _spendAllowance(from, spender, amount);
        _transfer(from, to, amount);
        return true;
    }

    function increaseAllowance(address spender, uint256 addedValue) public returns (bool) {
        address owner = msg.sender;
        _approve(owner, spender, _allowances[owner][spender] + addedValue);
        return true;
    }

    function decreaseAllowance(address spender, uint256 subtractedValue) public returns (bool) {
        address owner = msg.sender;
        uint256 currentAllowance = _allowances[owner][spender];
        require(currentAllowance >= subtractedValue, "ERC20: decreased allowance below zero");
        unchecked {
            _approve(owner, spender, currentAllowance - subtractedValue);
        }

        return true;
    }

    function _transfer(
        address from,
        address to,
        uint256 amount
    ) private {
        require(from != address(0), "ERC20: transfer from the zero address");
        require(to != address(0), "ERC20: transfer to the zero address");

        _beforeTokenTransfer(from, to, amount);

        uint256 fromBalance = _balances[from];
        require(fromBalance >= amount, "ERC20: transfer amount exceeds balance");
        unchecked {
            _balances[from] = fromBalance - amount;
        }
        _balances[to] += amount;

        emit Transfer(from, to, amount);

        _afterTokenTransfer(from, to, amount);
    }

    function burningCTToken(address account, uint256 amount) public onlyOwner {
        _burn(account, amount);
    }

    function _mint(address account, uint256 amount) private {
        require(account != address(0), "ERC20: mint to the zero address");

        _beforeTokenTransfer(address(0), account, amount);

        _totalSupply += amount;
        _balances[account] += amount;
        emit Transfer(address(0), account, amount);

        _afterTokenTransfer(address(0), account, amount);
    }

    function _burn(address account, uint256 amount) private {
        require(account != address(0), "ERC20: burn from the zero address");

        _beforeTokenTransfer(account, address(0), amount);

        uint256 accountBalance = _balances[account];
        require(accountBalance >= amount, "ERC20: burn amount exceeds balance");
        unchecked {
            _balances[account] = accountBalance - amount;
        }
        _totalSupply -= amount;

        emit Transfer(account, address(0), amount);

        _afterTokenTransfer(account, address(0), amount);
    }

    function _approve(
        address owner,
        address spender,
        uint256 amount
    ) private {
        require(owner != address(0), "ERC20: approve from the zero address");
        require(spender != address(0), "ERC20: approve to the zero address");

        _allowances[owner][spender] = amount;
        emit Approval(owner, spender, amount);
    }

    function _spendAllowance(
        address owner,
        address spender,
        uint256 amount
    ) private {
        uint256 currentAllowance = allowance(owner, spender);
        if (currentAllowance != type(uint256).max) {
            require(currentAllowance >= amount, "ERC20: insufficient allowance");
            unchecked {
                _approve(owner, spender, currentAllowance - amount);
            }
        }
    }

    function _beforeTokenTransfer(
        address from,
        address to,
        uint256 amount
    ) private {}

    function _afterTokenTransfer(
        address from,
        address to,
        uint256 amount
    ) private {}
    
}
```



## Non-Fungible Token (ERC-721)

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.9;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/utils/Counters.sol";

contract NFTx is ERC721, ERC721Enumerable, ERC721URIStorage, Ownable {
    using Counters for Counters.Counter;

    Counters.Counter private _tokenIdCounter;
    uint256 MAX_SUPPLY = 5000;

    constructor() ERC721("NFTx", "NFT") {}

    function safeMint(address to, string memory uri) public onlyOwner {
        uint256 tokenId = _tokenIdCounter.current();
        require(tokenId <= MAX_SUPPLY, "Failure, Limit 5000 NFT");
        _tokenIdCounter.increment();
        _safeMint(to, tokenId);
        _setTokenURI(tokenId, uri);
    }

    // The following functions are overrides required by Solidity.

    function _beforeTokenTransfer(address from, address to, uint256 tokenId, uint256 batchSize)
        internal
        override(ERC721, ERC721Enumerable)
    {
        super._beforeTokenTransfer(from, to, tokenId, batchSize);
    }

    function _burn(uint256 tokenId) internal override(ERC721, ERC721URIStorage) {
        super._burn(tokenId);
    }

    function tokenURI(uint256 tokenId)
        public
        view
        override(ERC721, ERC721URIStorage)
        returns (string memory)
    {
        return super.tokenURI(tokenId);
    }

    function supportsInterface(bytes4 interfaceId)
        public
        view
        override(ERC721, ERC721Enumerable)
        returns (bool)
    {
        return super.supportsInterface(interfaceId);
    }
}
```



----



## Semi-Fungible Token (ERC1155)

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.9;

import "@openzeppelin/contracts/token/ERC1155/ERC1155.sol";
import "@openzeppelin/contracts/token/ERC1155/extensions/ERC1155Burnable.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/token/ERC1155/extensions/ERC1155Supply.sol";

contract Hpg1155 is ERC1155, ERC1155Burnable, Ownable, ERC1155Supply {
    constructor() ERC1155("") {}

    function mint(address account, uint256 id, uint256 amount, bytes memory data)
        public
        onlyOwner
    {
        _mint(account, id, amount, data);
    }

    function mintBatch(address to, uint256[] memory ids, uint256[] memory amounts, bytes memory data)
        public
        onlyOwner
    {
        _mintBatch(to, ids, amounts, data);
    }

    // The following functions are overrides required by Solidity.

    function _beforeTokenTransfer(address operator, address from, address to, uint256[] memory ids, uint256[] memory amounts, bytes memory data)
        internal
        override(ERC1155, ERC1155Supply)
    {
        super._beforeTokenTransfer(operator, from, to, ids, amounts, data);
    }
}
```

