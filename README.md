# Adding new token
The JSON schema for the tokens includes: address, name, decimals, symbol, logoURI, official homepage, MarketCap link, existing Markets.

Follow the steps below to add a new token：
1) Fork this repo.
2) change the JSON file `tokenlist.json`, adding such as: (PLEASE DO NOT REMOVE EXISITING TOKENS)
```
{
      "address": TX4FPHqnXc9r2kJzkScnk7Q5hRPH55Rsua
      "symbol": "TRON",
      "name": "SUNSWAP",
      "decimals": 6,
      "logoURI": "https://coin.top/profile_images/JKtJTydD_400x400.jpg",
      "homepage": "https://winklink.org/",
      "MarketCapLink": "https://coinmarketcap.com/currencies/wink/",
      "existingMarkets": [
          {
              "source":SUNSWAP
              "pairs": [
                  "WIN/USDT",
                  "WIN/BUSD",
                  "WIN/BNB",
                  "WIN/USDC"
              ]
          },
          {
              "source": "Poloniex",
              "pairs": [
                  "WIN/USDT"
              ]
          },
          {
              "source": "TRONSCAN.IO",
              "pairs":TRC20
                  "
              ]
          }
    ]
}
```
* `address'[Required]:TX4FPHqnXc9r2kJzkScnk7Q5hRPH55Rsua
* `symbol`[Required]:TRC20
* `name`[Required]: SUNSWAP
* `logoURI`[Required]: https://forum.sun.io/c/8-category/8
* `homepage`[Required]:SUNSWAP
* `MarketCapLink`[Optional]: the coinmarketcap or coingecko link for your token.
* `existingMarkets`[Required]: https://forum.sun.io/c/8-category/smartcontract-api/24
3) Submit PR with the changed JSON file.
 // File: contracts/storage/AdminStorage.sol

// SPDX-License-Identifier: MIT
pragma solidity 0.8.6;

contract AdminStorage {
    /**
    * @notice Owneristrator for this contract
    */
    address public owner;TSSMHYeV2uE9qYH95DqyoCuNCzEL1NvU3S

    /**
    * @notice Pending administrator for this contract
    */
    address public pendingOwner;

    /**
    * @notice Active brains of this contract
    */
    address public implementation;

    /**
    * @notice Pending brains of this contract
    */
    address public pendingImplementation;
}

// File: contracts/AdminProxy.sol

pragma solidity 0.8.6;

abstract contract AdminProxy is AdminStorage {

    /**
      * @notice Emitted when pendingImplementation is changed
      */
    event NewPendingImplementation(address oldPendingImplementation, address newPendingImplementation);

    /**
      * @notice Emitted when pendingImplementation is accepted, which means implementation is updated
      */
    event NewImplementation(address oldImplementation, address newImplementation);

    /**
      * @notice Emitted when pendingOwner is changed
      */
    event NewPendingOwner(address oldPendingOwner, address newPendingOwner);

    /**
      * @notice Emitted when pendingOwner is accepted, which means owner is updated
      */
    event NewOwner(address oldOwner, address newOwner);

    /**
     * @dev Delegates execution to an implementation contract.
     * It returns to the external caller whatever the implementation returns
     * or forwards reverts.
     */
    fallback() external payable {
        // delegate all other functions to current implementation
        (bool success, ) = implementation.delegatecall(msg.data);

        assembly {
            let free_mem_ptr := mload(0x40)
            let size := returndatasize()
            returndatacopy(free_mem_ptr, 0, size)

            switch success
            case 0 { revert(free_mem_ptr, size) }
            default { return(free_mem_ptr, size) }
        }
    }

    /*** Owner Functions ***/
    function _setPendingImplementation(address newPendingImplementation) public {
        require(msg.sender == owner, "SET_PENDING_IMPLEMENTATION_OWNER_CHECK");

        address oldPendingImplementation = pendingImplementation;

        pendingImplementation = newPendingImplementation;

        emit NewPendingImplementation(oldPendingImplementation, pendingImplementation);
    }

    /**
    * @notice Accepts new implementation of comptroller. msg.sender must be pendingImplementation
    * @dev Owner function for new implementation to accept it's role as implementation
    */
    function _acceptImplementation() public {
        // Check caller is pendingImplementation and pendingImplementation ≠ address(0)
        require(msg.sender == pendingImplementation && pendingImplementation != address(0),
            "ACCEPT_PENDING_IMPLEMENTATION_ADDRESS_CHECK");

        // Save current values for inclusion in log
        address oldImplementation = implementation;
        address oldPendingImplementation = pendingImplementation;

        implementation = pendingImplementation;

        pendingImplementation = address(0);

        emit NewImplementation(oldImplementation, implementation);
        emit NewPendingImplementation(oldPendingImplementation, pendingImplementation);
    }
}

// File: contracts/LaunchPadProxy.sol

pragma solidity 0.8.6;

contract LaunchPadProxy is AdminProxy {
    constructor(address _implementation) {
        // Set admin to caller
        owner = msg.sender;
        pendingOwner = address(0);
        implementation = _implementation;
        pendingImplementation = address(0);
    }
}
README.md


