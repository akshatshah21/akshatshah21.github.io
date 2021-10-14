# Making a Simple ERC20 Token

In this blog post, I'll walk through creating a simple ERC20 token on Ethereum. This was one of my lab assignments this semester, and I thought I better make a blog post about this, because ~~I want to write consistently over here~~ why not?

A token in general is basically simply a digital representation of
potentially anything. For example, a token can represent:

-   reputation points in an online platform

-   skills of a character in a game

-   lottery tickets

-   financial assets like a share in a company

-   a fiat currency like USD

-   an ounce of gold

A token can also represent access rights to a blockchain or blockchain
app, and tokens can also be used to automate \"friction points\" in
various industries.

Utility tokens, are tokens that have a specific \"use\" on the
blockchain or an app based on that. Utility tokens are also called \"app
coins\" because they are explicitly designed for a certain app or
blockchain.

# ERC20 Standard


The ERC-20 ([Ethereum Request for Comments 20](https://eips.ethereum.org/EIPS/eip-20)), proposed by Fabian
Vogelsteller in November 2015, is a Token Standard that implements an
API for tokens within Smart Contracts. The motivation for this EIP was
to make \"a standard interface allows any tokens on Ethereum to be
re-used by other applications: from wallets to decentralized
exchanges.\"

It provides functionalities like transfering tokens from one account to
another, to get the current token balance of an account and also the
total supply of the token available on the network. Besides these it can allow an owner of some tokens to approve spending those tokens by a third party account.

## Methods and Events Defined in ERC20

```solidity
function name() public view returns (string)

function symbol() public view returns (string)

function decimals() public view returns (uint8)

function totalSupply() public view returns (uint256)

function balanceOf(address _owner) public view returns (uint256 balance)

function transfer(address _to, uint256 _value) public returns (bool success)

function transferFrom(address _from, address _to, uint256 _value) public returns (bool success)

function approve(address _spender, uint256 _value) public returns (bool success)

function allowance(address _owner, address _spender) public view returns (uint256 remaining)
```

-   The `name`, `symbol`, `decimals` are state
    constants that identify the ERC20 token and the number of decimal
    places used for user representation.

-   The `totalSupply` function returns the total supply of the
    ERC20 token.

-   The `balanceOf` function returns the balance of the address
    passed as a argument.

-   The `transfer` function transfers specified number of tokens
    from the sender to the specified receiver, provided the sender has
    sufficient balance.

-   The `approve` function is used by the owner of some tokens
    (specified by the `_from` argument) to approve a delegate
    account to withdraw and spend some allowed number of tokens.

-   The `allowance` function returns the current approved number
    of tokens by an owner to a specific delegate, as set in the approve
    function.

-   The `transferFrom` function allows a delegate approved for
    withdrawal to transfer owner funds, provided the funds specified are
    less than or equal to the allowance of the delegate, and less than
    or equal to the owner's balance.

For our implementation of the ERC20 token, we add some features apart
from the standard:

-   The `burn` function allows the sender to burn some specified
    number of tokens, provided they have sufficient balance. Successful
    burning of tokens emits a `Transfer` event to address
    0x000\...000.

-   The `burnFrom` function allows a delegate to burn some
    specified number of tokens from an account they have sufficient
    allowance from. Successful burning of tokens emits a
    `Transfer` event to address 0x000\...000.

-   The `isMinter` function allows anyone to check if the
    specified address has minting privileges (or has the minter role).

-   The `addMinter` function allows the creator of the token to
    add an address as a minter. Successful addition emits a
    `MinterAdded` event.

-   The `removeMinter` function allows the creator of the token
    to remove an address from the minter role. Successful removal emits
    a `MinterRemoved` event.

-   The `renounce` function allows a minter to renounce their
    minting privileges. Successful renunciation emits a
    `MinterRemoved` event.

-   The `mint` function allows a minter to mint an arbitrary
    amount of tokens and take ownership of those tokens. Successful
    minting emits a `Transfer` event with 0x000\...000 as the
    from address.

-   The `_isMinter` mapping maintains a mapping to identify
    whether an address has minting privileges or not.

# Implementation
We make this contract in [Solidity](https://soliditylang.org/), and a simple way to start writing Ethereum contracts in Solidity is to use the [Remix IDE](https://remix.ethereum.org) It's browser-based, no setup required.

## Token Contract

```solidity
pragma solidity >=0.7.0 <0.9.0;
import {SafeMath} from "../lib/SafeMath.sol";

contract AkshatToken {
    
    string public constant name = "Akshat's Token";
    string public constant symbol = "AKT";
    uint8 public constant decimals = 18;
    address public creator;
    
    uint256 _totalSupply;
    mapping(address => uint256) _balances;
    mapping(address => mapping(address => uint256)) _allowed;
    mapping(address => bool) _isMinter;
    
    event Transfer(address indexed from, address indexed to, uint256 indexed tokens);
    event Approval(address indexed owner, address indexed delegate, uint256 indexed tokens);
    event MinterAdded(address indexed minter, address indexed addedBy);
    event MinterRemoved(address indexed minter, address indexed removedBy);
    
    using SafeMath for uint256;
    
    modifier onlyCreator {
        require(
            msg.sender == creator,
            "Only creator can call this function"
        );
        _;
    }
    
    modifier onlyMiner {
        require(
            msg.sender == creator || _isMinter[msg.sender],
            "Only a minter or the creator can call this function"
        );
        _;
    }
    
    constructor(uint256 total) {
        _totalSupply = total;
        _balances[msg.sender] = total;
        creator = msg.sender;
    }
    
    function totalSupply() public view returns (uint256) {
        return _totalSupply;
    }
    
    function balanceOf(address owner) public view returns (uint256) {
        return _balances[owner];
    }
    
    function allowance(address owner, address delegate) public view returns (uint256) {
        return _allowed[owner][delegate];
    }
    
    function transfer(address to, uint256 tokens) public returns (bool) {
        require(tokens <= _balances[msg.sender], "Insufficient tokens");
        _balances[msg.sender] = _balances[msg.sender].sub(tokens);
        _balances[to] = _balances[to].add(tokens);
        emit Transfer(msg.sender, to, tokens);
        return true;
    }
    
    function approve(address delegate, uint256 tokens) public returns (bool) {
        _allowed[msg.sender][delegate] = tokens;
        emit Approval(msg.sender, delegate, tokens);
        return true;
    }
    
    function transferFrom(address from, address to, uint256 tokens) public returns (bool) {
        require(tokens <= _allowed[from][msg.sender], "Insufficient tokens approved for delegation");
        require(tokens <= _balances[from], "Insufficient tokens");
        _balances[from] = _balances[from].sub(tokens);
        _allowed[from][msg.sender] = _allowed[from][msg.sender].sub(tokens);
        _balances[to] = _balances[to].add(tokens);
        emit Transfer(from, to, tokens);
        return true;
    }
    
    function burn(uint256 tokens) public returns (bool) {
        require(tokens <= _balances[msg.sender], "Insufficient tokens");
        _balances[msg.sender] = _balances[msg.sender].sub(tokens);
        _totalSupply = _totalSupply.sub(tokens);
        emit Transfer(msg.sender, address(0), tokens);
        return true;
    }
    
    function burnFrom(address from, uint256 tokens) public returns (bool) {
        require(tokens <= _allowed[from][msg.sender], "Insufficient tokens approved for delegation");
        require(tokens <= _balances[from], "Insufficient tokens");
        _allowed[from][msg.sender] = _allowed[from][msg.sender].sub(tokens);
        _balances[from] = _balances[from].sub(tokens);
        _totalSupply = _totalSupply.sub(tokens);
        emit Transfer(from, address(0), tokens);
        return true;
    }
    
    function isMinter(address _address) public view returns (bool) {
        return _isMinter[_address];
    }
    
    function addMinter(address _address) public onlyCreator returns (bool) {
        require(_isMinter[_address] == false, "Already a minter");
        _isMinter[_address] = true;
        emit MinterAdded(_address, msg.sender);
        return true;
    }
    
    function removeMinter(address _address) public onlyCreator returns (bool) {
        require(_isMinter[_address] == true, "Already not a minter");
        _isMinter[_address] = false;
        emit MinterRemoved(_address, msg.sender);
        return true;
    }
    
    function renounceMinter() public onlyMiner returns (bool) {
        _isMinter[msg.sender] = false;
        emit MinterRemoved(msg.sender, msg.sender);
        return true;
    }
    
    function mint(uint256 tokens) public onlyMiner returns (bool) {
        _balances[msg.sender] = _balances[msg.sender].add(tokens);
        _totalSupply = _totalSupply.add(tokens);
        emit Transfer(address(0), msg.sender, tokens);
        return true;
    }
}

```

## Token Vendor Contract

To allow buying and selling tokens (for ether), we create a Vendor
contract, that allows buyers and sellers of the tokens to call functions
for the same.

-   The payable `buyTokens` function allows the sender to send
    some ether in exchange for tokens.

-   The `sellTokens` function allows the sender to sell the
    tokens for ether. Successful sale of tokens will require that the
    sender has delegated a minimum of that amount of tokens to the
    contract account using the `approve` function defined in the
    token contract, so that the Vendor contract can transfer the
    specified number of tokens on the sender's behalf.

```solidity
pragma solidity >=0.7.0 <0.9.0;

import {AkshatToken} from "./AkshatToken.sol";
import {SafeMath} from "../lib/SafeMath.sol";

contract AkshatTokenVendor {
    AkshatToken token;
    uint256 public tokensPerEth = 100;
    
    event BuyTokens(address buyer, uint256 eth, uint256 tokens);
    event SellTokens(address seller, uint256 eth, uint256 tokens);
    
    using SafeMath for uint256;
    
    constructor (address tokenAddress) {
        token = AkshatToken(tokenAddress);
    }
    
    receive() external payable {}
    
    function buyTokens() public payable returns (uint256) {
        require(msg.value > 0, "Insufficient ether sent");
        uint256 eth = msg.value / (1 ether);
        uint256 tokens = eth.mul(tokensPerEth);
        require(token.balanceOf(address(this)) >= tokens, "Vendor contract has insufficient tokens");
        bool sent = token.transfer(msg.sender, tokens);
        require(sent, "Failure in transferring tokens");
        emit BuyTokens(msg.sender, msg.value, tokens);
        return tokens;
    }
    
    // requires delegation approval
    function sellTokens(uint256 tokens) public returns (bool) {
        require(tokens > 0, "Need to sell non-zero tokens");
        require(token.balanceOf(msg.sender) >= tokens, "Insufficient tokens");
        uint256 eth = tokens.div(tokensPerEth) * (1 ether);
        require(address(this).balance >= eth, "Vendor contract has insufficient ether");
        bool sent = token.transferFrom(msg.sender, address(this), tokens);
        require(sent, "Failure in transferring tokens, check approval");
        (sent, ) = msg.sender.call{value: eth}("");
        require(sent, "Failure in transferring ether");
        return true;
    }
}

```

We define arithmetic operations explicitly to secure our ERC20 Token
from integer overflow attacks. We check for integer overflows while
performing these operations. This logic is implemented in a separate
library called `SafeMath`.

```solidity
pragma solidity >=0.7.0 <0.9.0;

library SafeMath {
    function sub(uint256 a, uint256 b) internal pure returns (uint256) {
        assert(b <= a);
        return a - b;
    }
    
    function add(uint256 a, uint256 b) internal pure returns (uint256) {
        uint256 c = a + b;
        assert(c >= a);
        return c;
    }
    
    function mul(uint256 a, uint256 b) internal pure returns (uint256) {
        if (a == 0) {
            return 0;
        }

        uint256 c = a * b;
        require(c / a == b, "SafeMath: multiplication overflow");

        return c;
    }
    
    function div(uint256 a, uint256 b) internal pure returns (uint256) {
        require(b > 0);
        uint256 c = a / b;
        // assert(a == b * c + a % b); // There is no case in which this doesn't hold

        return c;
    }
}

```

# Execution


The following steps show how the contracts can be deployed, following a
demonstration of the different functionalities coded in the contracts.
We run a local [Geth](https://geth.ethereum.org/) client running a private blockchain, and use the Remix IDE to call the contracts.

1.  First, we initialize a local test ethereum network using the genesis
    block given below, and start the node, allowing RPC CORS from Remix.
    Attach a console to the running geth client via IPC. Create three
    accounts, and start mining with one so that we can deploy a contract
    using that account.
	
    ```json
    { 
        "config": { 
            "chainId": 2021, 
            "homesteadBlock": 0, 
            "eip150Block": 0, 
            "eip150Hash": "0x0000000000000000000000000000000000000000000000000000000000000000",
            "eip155Block": 0, 
            "eip158Block": 0,
            "byzantiumBlock": 0,
            "constantinopleBlock": 0,
            "petersburgBlock": 0,
            "istanbulBlock": 0
        }, 
        "nonce": "0x0000000000000042", 
        "timestamp": "0x00", 
        "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
        "extraData": "0x00",
        "gasLimit": "0x4c4b40",
        "difficulty": "0x0004",
        "mixhash": "0x0000000000000000000000000000000000000000000000000000000000000000",
        "coinbase": "0x0000000000000000000000000000000000000000",
        "alloc": {}
    }
    ```

    ```bash
    $ geth --datadir .ethereum/net init genesis.json
    $ geth --rpc --rpccorsdomain "https://remix.ethereum.org" --datadir .ethereum/net --allow-insecure-unlock
    $ geth attach ipc:<DATADIR>/geth.ipc
    ```


    {{<image src="img/geth-init.png" title="Initializing the local test ethereum network" alt="Initializing the local test ethereum network: terminal screenshot" caption="Initializing the local test ethereum network">}}

    ```javascript
        > personal.newAccount()  // to create an account
        > miner.start()  // to start mining
    ```

    {{<image src="img/accounts-mining.png" title="Create accounts and start mining" alt="Create accounts and start mining: geth console screesnshot" caption="Create accounts and start mining">}}

2. In Remix IDE, switch to the *Deploy & run transactions* window.
    Select *Web3 Provider* as the *Environment*. When the Remix IDE
    connects to the Geth client, the three addresses should be visible
    in the *Account* input.

    
    {{<image src="img/remix-ready.png" title="Remix IDE connected to local test network" alt="Remix IDE connected to local test network" caption="Remix IDE connected to local test network">}}

3.  We can now deploy the contract by selecting the contract, providing
    the `total` argument. We select 1000 tokens as the initial
    supply. Unlock an account which has some ether using the geth
    console by running
    `personal. unlockAccount(eth.accounts[0])`. Click on
    *Deploy*. After we deploy the contract, the contract will appear
    under *Deployed Contracts*, with functions available to use.

    {{<image src="img/deployed-1.png" title="The Token Contract, deployed" caption="The Token Contract, deployed" alt="The Token Contract, deployed">}}

    {{<image src="img/deployed-2.png" title="The Token Contract, deployed. These are the view functions." caption="The Token Contract, deployed. These are the _view_ functions." alt="The Token Contract, deployed. These are the view functions.">}}

    {{<image src="img/deployed-3.png" title="Transaction receipt of deploying the contract" alt="Transaction receipt of deploying the contract" caption="Transaction receipt of deploying the contract">}}

4.  Try running the different *view* functions first, and check whether
    the output is as expected. For example, the `balanceOf`
    function with the contract creator's address should return 1000
    tokens initially. The `name`, `symbol`,
    `decimals`, `creator` and `isMinter` functions
    should similarly return expected values.

    {{<image src="img/view-1.png" title="The view functions return values as expected" alt="The view functions return values as expected" caption="The _view_ functions return values as expected">}}

    {{<image src="img/view-2.png" title="The view functions return values as expected" alt="The view functions return values as expected" caption="The _view_ functions return values as expected">}}

5.  If we try minting from the second account, an error will be
    returned, since the second account does not have minting privileges.


    {{<image src="img/mint-fail.png" alt="Trying to mint from an address that does not have minting privileges. Remix shows the *require* error while gas estimation." caption="Trying to mint from an address that does not have minting privileges. Remix shows the *require* error while gas estimation." title="Trying to mint from an address that does not have minting privileges. Remix shows the *require* error while gas estimation.">}}

6.  Now we can try adding the second account as a minter using the
    `addMinter` method from the creator's account.

    {{<image src="img/addMinter.png" alt="Call to addMinter function specifying address" title="Call to addMinter function specifying address" caption="Call to `addMinter` function specifying address">}}

    {{<image src="img/isMinter.png" alt="Checking if the address now has minter privileges returns true" title="Checking if the address now has minter privileges returns true" caption="Checking if the address now has minter privileges returns true">}}

    {{<image src="img/MinterAddedEvent.png" alt="MinterAdded event emitted, specifying who added whom as a minter" title="MinterAdded event emitted, specifying who added whom as a minter" caption="`MinterAdded` event emitted, specifying who added whom as a minter">}}

7.  If we now call the `isMinter` method from the second account,
    we'll get true as the return value.

8.  We can try minting some tokens from the second account by (first
    changing the account in Remix) calling `mint` method.

    {{<image src="img/mint.png" alt="Call to mint function" title="Call to mint function" caption="Call to `mint` function">}}

    {{<image src="img/mint-transfer.png" title="Transfer event of 500 tokens from 0x000...000" alt="Transfer event of 500 tokens from 0x000...000" caption="Transfer event of 500 tokens from `0x000...000`">}}

    {{<image src="img/mint-balance.png" title="New balance of minter" alt=" New balance of minter" caption="New balance of minter">}}

    {{<image src="img/mint-totalsupply.png" title="New total supply of tokens" alt="New total supply of tokens" caption="New total supply of tokens">}}

9.  We can transfer tokens from second account to third account by
    calling the `transfer` method from the second account.

    {{<image src="img/transfer-call.png" alt="Call to transfer function" title="Call to transfer function" caption="Call to `transfer` function">}}

    {{<image src="img/TransferEvent.png" title="Transfer event of 200 tokens with from and to addresses" alt="Transfer event of 200 tokens with from and to addresses" caption="Transfer event of 200 tokens with from and to addresses">}}

    {{<image src="img/Transfer-sender-balance.png" title="New balance of sender (500 → 300)" alt="New balance of sender (500 → 300)" caption="New balance of sender (500 → 300)">}}

    {{<image src="img/Transfer-receiver-balance.png" title="New balance of receiver (0 → 200)" alt="New balance of receiver (0 → 200)" caption="New balance of receiver (0 → 200)">}}

10.  Let's approve the third account for delegation of 50 tokens by
    calling the `approve` method from the second account. We can
    then check the allowance by calling the `allowance` method.

{{<image src="img/approve.png" title="Call to approve function" alt="Call to approve function" caption="Call to `approve` function">}}

{{<image src="img/allowance.png" title="Subsequent call to `allowance` function" alt="Subsequent call to allowance function" caption="Subsequent call to `allowance` function">}}

{{<image src="img/ApprovalEvent.png" title="Approval event emitted, specifying the owner, delegate and the allowance" alt="Approval event emitted, specifying the owner, delegate and the allowance" caption="Approval event emitted, specifying the owner, delegate and the allowance">}}

1.  Let's transfer 20 tokens belonging to the second address to the
    first address from by calling `transferFrom` method from the
    third account.

    {{<image src="img/transferFrom.png" alt="Call to transferFrom function" title="Call to transferFrom function" caption="Call to `transferFrom` function">}}

    {{<image src="img/transferFrom-updated-allowance.png" title="Updated allowance from 0x1a8b3b to 0x08b16E (50 → 30)" alt="Updated allowance from 0x1a8b3b to 0x08b16E (50 → 30)" caption="Updated allowance from `0x1a8b3b` to `0x08b16E` (50 → 30)">}}

    {{<image src="img/transferFrom-updated-sender-balance.png" title="Updated balance of sender 0x1a8b3b" alt="Updated balance of sender 0x1a8b3b" caption="Updated balance of sender `0x1a8b3b`">}}

    {{<image src="img/transferFrom-updated-receiver-balance.png" title="Updated balance of receiver 0x02138 (1000 → 1020)" alt="Updated balance of receiver 0x02138 (1000 → 1020)" caption="Updated balance of receiver `0x02138` (1000 → 1020)">}}

    {{<image src="img/transferFrom-TransferEvent.png" title="Transfer Event with from, to and tokens transferred" alt="Transfer Event with from, to and tokens transferred" caption="Transfer Event with from, to and tokens transferred">}}
2.  Let's burn 200 tokens from creator's account.

    {{<image src="img/burn.png" title="Call to burn function with 200 tokens" alt="Call to burn function with 200 tokens" caption="Call to `burn` function with 200 tokens">}}

    {{<image src="img/burn-sender-balance.png" title="Updated balance of account (1020 → 820)" alt="Updated balance of account (1020 → 820)" caption="Updated balance of account (1020 → 820)">}}

    {{<image src="img/burn-totalsupply.png" title="Updated total supply (1500 → 1300)" alt="Updated total supply (1500 → 1300)" caption="Updated total supply (1500 → 1300)">}}

    {{<image src="img/burn-TransferEvent.png" title="Transfer Event of 200 tokens to address 0x000...000" alt="Transfer Event of 200 tokens to address 0x000...000" caption="Transfer Event of 200 tokens to address 0x000...000">}}

3.  Let's burn the remaining 30 tokens from the allowance delegated from
    second address to the third address.

    {{<image src="img/burnFrom.png" title="Call to burnFrom function with 30 tokens, delegated from 0x1a8b3b to 0x08b16E" alt="Call to burnFrom function with 30 tokens, delegated from 0x1a8b3b to 0x08b16E" caption="Call to `burnFrom` function with 30 tokens, delegated from 0x1a8b3b to 0x08b16E">}}

    {{<image src="img/burnFrom-sender-balance.png" title="Updated balance of account `0x1a8b3b` (280 →250)" alt="Updated balance of account `0x1a8b3b` (280 →250)" caption="Updated balance of account `0x1a8b3b` (280 →250)">}}

    {{<image src="img/burnFrom-totalsupply.png" title="Updated total supply (1300 → 1270)" alt="Updated total supply (1300 → 1270)" caption="Updated total supply (1300 → 1270)">}}

    {{<image src="img/burnFrom-TransferEvent.png" title="Transfer Event of 30 tokens to address 0x000...000" alt="Transfer Event of 30 tokens to address 0x000...000" caption="Transfer Event of 30 tokens to address `0x000...000`">}}

4.  Now let's deploy the Token Vendor contract in the same manner as we
    deployed the Token contract. We will create the vendor contract from
    the creator address again, and then send some ether to the contract
    (using Metamask, any other wallet or even the geth console) for its
    funds, so that it can buy tokens from some address who sells tokens
    to it for ether. Select the token vendor contract and deploy it,
    with the address of the token contract as a parameter to the
    constructor.


    {{<image src="img/vendor-contract-creation.png" alt="Deploying the Token Vendor contract. The functions available to call are shown" caption="Deploying the Token Vendor contract. The functions available to call are shown on the left" title="Deploying the Token Vendor contract. The functions available to call are shown on the left">}}



    {{<image src="img/ether-to-vendo.png" alt="Sending 1000 ETH to the Vendor contract using Metamask" caption="Sending 1000 ETH to the Vendor contract using Metamask" title="Sending 1000 ETH to the Vendor contract using Metamask">}}

5.  If we now try selling some tokens to the contract, we will get an
    error, since we first have to delegate that amount of tokens to the
    Vendor contract to allow it to transfer those tokens to itself.
    Hence, first approve some amount of tokens to the vendor contract,
    and then call the `sellTokens` function with some tokens less
    than or equal to the allowance.


    {{<image src="img/sell-tokens-delegation-fail.png" title="Trying to sell tokens without approval of those tokens to the vendor contract" alt="Trying to sell tokens without approval of those tokens to the vendor contract" caption="Trying to sell tokens without approval of those tokens to the vendor contract">}}

6.  After approving the vendor contract, we can successfully sell
    tokens. The image below shows the second account selling 100 tokens,
    for which it gets back 1ETH, since the exchange rate (defined as a
    constant) is 100 tokens per ether.

    {{<image src="img/sell-tokens.png" alt="Call to sellTokens function with 100 tokens" caption="Call to `sellTokens` function with 100 tokens" title="Call to `sellTokens` function with 100 tokens">}}

    {{<image src="img/sell-tokens-sender-balance-tokens.png" alt="Updated balance of account 0x1a8b3b (250 → 150)" caption="Updated balance of account `0x1a8b3b` (250 → 150)" title="Updated balance of account 0x1a8b3b (250 → 150)">}}

    {{<image src="img/sell-tokens-contract-balance.png" alt="Updated balance of vendor contract (0 → 100)" caption="Updated balance of vendor contract (0 → 100)" title="Updated balance of vendor contract (0 → 100)">}}

    {{<image src="img/sell-tokens-TransferEvent.png" alt="Transfer Event of 100 tokens" caption="Transfer Event of 100 tokens" title="Transfer Event of 100 tokens">}}



7.  For buying tokens, we simply need to call the `buyTokens`
    function and send some ether along with it. According to the token
    exchange rate, some number of tokens will be bought.

    {{<image src="img/buyTokens-eth-value.png" alt="Call to buyTokens function with 1 ether value" caption="Call to `buyTokens` function with 1 ether value" title="Call to buyTokens function with 1 ether value">}}

    {{<image src="img/buyTokens-contract-balance.png" alt="Updated balance of vendor contract (100 → 0)" caption="Updated balance of vendor contract (100 → 0)" title="Updated balance of vendor contract (100 → 0)">}}

    {{<image src="img/buyTokens-buyer-balance.png" alt="Updated balance of buyer 0x1a8b3b (150 → 250)" caption="Updated balance of buyer `0x1a8b3b` (150 → 250)" title="Updated balance of buyer 0x1a8b3b (150 → 250)">}}

    {{<image src="img/buyTokens-TransferEvent.png" alt="Transfer Event of 100 tokens" caption="Transfer Event of 100 tokens" title="Transfer Event of 100 tokens">}}

8.  We can also use a wallet like Metamask to deal with ERC20 tokens.
    Click on *Add Token* in Metamask. Fill in the token address, and
    Metamask will retrieve the symbol and decimals of the token. Click
    on *Next* and then *Add Token*.

    {{<image src="img/metamask-add-token.png" alt="Adding the AKT token in Metamask" caption="Adding the AKT token in Metamask" title="Adding the AKT token in Metamask">}}

    {{<image src="img/metamask-akt-18.png" alt="Balance of AKT in an account. The representation is 18 decimal places" caption="Balance of AKT in an account. The representation is 18 decimal places" title="Balance of AKT in an account. The representation is 18 decimal places">}}

    {{<image src="img/metamask-transfer.png" alt="Transferring AKT tokens using Metamask" caption="Transferring AKT tokens using Metamask" title="Transferring AKT tokens using Metamask">}}

    {{<image src="img/metamask-kat-transfer-receipt.png" alt="Transfer Event of 100 tokens" caption="Transfer Event of 100 tokens" title="Transfer Event of 100 tokens">}}

# Conclusion

We saw how an Ethereum smart contract can be used to
implement a fungible token using the ERC20 standard, along with some
extensions to allow burning and minting tokens. Then, we developed a
Vendor contract to allow users to buy and sell tokens. Remix IDE was
used to develop and test the contracts, Geth client was used to simulate
a test blockchain locally, and Remix and Metamask was used to deal with
tokens.

ERC20 is somewhat limited by its simplicity, even though we added on the minting and burning functions. A newer standard for fungible tokens, [ERC777](https://ethereum.org/en/developers/docs/standards/tokens/erc-777/) ([EIP](https://eips.ethereum.org/EIPS/eip-777)) has a lot more functionality, and is backward compatible with ERC20. For non-fungible tokens, there's the [ERC721](https://ethereum.org/en/developers/docs/standards/tokens/erc-721/) standard, often used for collectibles and games.  

# References
* [ERC-20 Token Standard | ethereum.org](https://ethereum.org/en/developers/docs/standards/tokens/erc-20/)
* [ERC 20 - OpenZeppelin Docs](https://docs.openzeppelin.com/contracts/3.x/api/token/erc20)
* [ERC20 Token Tutorial by Gilad Halmov](https://www.toptal.com/ethereum/create-erc20-token-tutorial)

