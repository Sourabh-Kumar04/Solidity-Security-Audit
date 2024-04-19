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

# Solidity-Security-Audit

## Security Audit for Solidity
- **Slither**
- **Solgraph**

## Slither 
```
$ sudo apt install software-properties-common

$ sudo add-apt-repository ppa:ethereum/ethereum

$ sudo apt install solc

$ pip3 install solc-select

**Using pip**

$ pip3 install slither-analyzer

**Using GitHub**

$ git clone <https://github.com/crytic/slither.git> && cd slither

$ python3 setup.py install

**Using Docker**

$ docker pull trailofbits/eth-security-toolbox
```
#### Make a file
```
touch contractname.sol
```
```
nano
```
- Write a contract
```
pragma solidity ^0.4.15;

contract CrowdFundBad {
  address[] private refundAddresses;
  mapping(address => uint) public refundAmount;

  function refundDos() public {
    for(uint i; i < refundAddresses.length; i++) {
      require(refundAddresses[i].transfer(refundAmount[refundAddresses[i]]));
    }
  }
}

contract CrowdFundPull {
  address[] private refundAddresses;
  mapping(address => uint) public refundAmount;

  function withdraw() external {
    uint refund = refundAmount[msg.sender];
    refundAmount[msg.sender] = 0;
    msg.sender.transfer(refund);
  }
}


//This is safe against the list length causing out of gas issues
//but is not safe against the payee causing the execution to revert
contract CrowdFundSafe {
  address[] private refundAddresses;
  mapping(address => uint) public refundAmount;
  uint256 nextIdx;
  
  function refundSafe() public {
    uint256 i = nextIdx;
    while(i < refundAddresses.length && msg.gas > 200000) {
      refundAddresses[i].transfer(refundAmount[i]);
      i++;
    }
    nextIdx = i;
  }
}
```




Local copy of a contract file
```
$ solc-select use Solidity-compilerversion(like 0.5.15)

$ slither filename.sol
```


## Solgraph
#### Pre-requisite
- Install Docker
- Pull devopstestlab/solgraph:
  ```
  $ docker pull devopstestlab/solgraph
  ```
  #### Create the Smart Contract in Solidity
  ```
  $ sudo mkdir data
  $ cd data
  $ sudo vi MyContract.sol
  ```
  Run this Contract in the docker image we just pull:
  ```
  $ docker run -it -v $PWD:/data devopstestlab/solgraph
  ```
  View the image using:
  - For Ubuntu/Linux
    ```
    $ xdg-open MyContract.sol.png
    ```
  - Wsl Ubuntu
    ```
    $ eog MyContract.sol.png
    ```
![image](https://github.com/Sourabh-Kumar04/Solidity-Security-Audit/assets/155216316/d4de0fa4-815c-4458-b617-a195e225606f)


## Myhtril
### Installation setup
Get it up Docker
```
$ docker pull mythril/myth
```
Install from Pypi (Python 3.7-3.10):
```
$ pip3 install mythril
```
Creating directory and file
```
mkdir Contract
```
```
vi MyContract.sol
```
Contract
```
contract Exceptions {

    uint256[8] myarray;
    uint counter = 0;
    function assert1() public pure {
        uint256 i = 1;
        assert(i == 0);
    }
    function counter_increase() public {
        counter+=1;
    }
    function assert5(uint input_x) public view{
        require(counter>2);
        assert(input_x > 10);
    }
    function assert2() public pure {
        uint256 i = 1;
        assert(i > 0);
    }

    function assert3(uint256 input) public pure {
        assert(input != 23);
    }

    function require_is_fine(uint256 input) public pure {
        require(input != 23);
    }

    function this_is_fine(uint256 input) public pure {
        if (input > 0) {
            uint256 i = 1/input;
        }
    }

  
  function this_is_find_2(uint256 index) public view {
        if (index < 8) {
            uint256 i = myarray[index];
        }
    }

}
```
















