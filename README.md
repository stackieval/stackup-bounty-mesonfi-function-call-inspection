# Introduction

#### Protocol Name: <strong>Meson.fi</strong>
#### Category: <strong>DeFi</strong>.
#### Smart Contract: ``0x25aB3Efd52e6470681CE037cD546Dc60726948D3`` \<ProxyToMeson\>

## Function Analysis

#### Function Name: 
``_safeTransfer``

#### Block Explorer Link: 
https://etherscan.io/address/0x25ab3efd52e6470681ce037cd546dc60726948d3#code

#### Function Code:
```js
function _safeTransfer(
    address token,
    address recipient,
    uint256 amount,
    uint8 tokenIndex
  ) internal {
    require(Address.isContract(token), "The given token address is not a contract");

    if (_needAdjustAmount(tokenIndex)) {
      amount *= 1e12;
    }
    (bool success, bytes memory data) = token.call(abi.encodeWithSelector(
      ERC20_TRANSFER_SELECTOR,
      recipient,
      amount
    ));
    require(success && (data.length == 0 || abi.decode(data, (bool))), "transfer failed");

    // The above do not support Tron, so need to switch to the next line if deploying to Tron
    // IERC20Minimal(token).transfer(recipient, amount);
  }
```

#### Used Encoding/Decoding or Call Method: 
``call``.

## Explanation

#### Purpose: 
The very purpose of the function is to safely transfers tokens from Meson contract to a recipient and is called from multiple functions: 
<ul>
    <li><strong>cancelSwap</strong> which is use to cancel the swap and withdraw the funds from the swap</li>
    <li><strong>executeSwap</strong> which is use to execute the swap and perform the transfer of funds between liquidity pools</li>
    <li><strong>withdraw</strong> which is use to withdraw the tokens from the liquidity pool</li>
</ul>

#### Detailed Usage: 
The function accepts token address, recipient address, amount, and tokenIndex as arguments. It first check if provided token address is a contract. It has also a handy function that check for the token decimal and adjust the amount based on that. Then it performs the token transfer using the <strong>call</strong> and <strong>abi.encodeWithSelector</strong> low-level function calls. And returns <strong>"transfer failed"</strong> if the transfer failed.

#### Impact: 
The _safeTransfer function plays a crucial role in the Meson.fi smart contract by ensuring the secure transfer of tokens. 

The function first checks if the provided token address is a contract using ``Address.isContract(token)``. This prevents accidental transfers to non-contract addresses, which could lead to loss of funds. 

By using low-level ``call`` and ``abi.encodeWithSelector``, the function ensures that the transfer adheres to the ERC-20 standard. It also checks the success of the transfer and decodes the returned data to confirm the transfer was successful.

The function adjusts the amount based on the token's decimal places if necessary ``(_needAdjustAmount(tokenIndex))``. This ensures compatibility with tokens that have different decimal configurations, making the function versatile for various tokens.

The function is called from several other functions within the protocol, such as ``cancelSwap``, ``executeSwap``, and ``withdraw``. This centralizes the token transfer logic, reducing code duplication and potential errors.

In summary, the ``_safeTransfer`` function is integral to the Meson.fi protocol, providing a secure, flexible, and centralized mechanism for token transfers, which is essential for the protocol's operations.