# WormholeRelayer

The WormholeRelayer (also sometimes referred to as the automatic or generic relayer) allows integrators to leverage external parties known as delivery providers, to relay messages emitted on a given source chain to the intended target chain.

This frees integrators, who are building a cross-chain app, from the cumbersome and painful task of having to run relaying infrastructure themselves (and thus e.g. dealing with the headache of having to acquire gas tokens for the target chain).

Messages include, but aren't limited to: Wormhole attestations (VAAs), Circle attestations (CCTP)

Delivery providers provide a quote for the cost of a delivery on the source chain and also take payment there. This means the process is not fully trustless (delivery providers can take payment and then fail to perform the delivery), however the state of the respective chains always makes it clear whether a delivery provider has done their duty for a given delivery and delivery providers can't maliciously manipulate the content of a delivery.

## Example Usage

[HelloWormhole - Simple cross-chain message sending application](https://github.com/wormhole-foundation/hello-wormhole)

[HelloToken - Simple cross-chain token sending application](https://github.com/wormhole-foundation/hello-token)

[HelloUSDC - Simple cross-chain USDC sending application using CCTP](https://github.com/wormhole-foundation/hello-usdc)

## SDK Summary

- Includes interfaces to interact with contracts in the Wormhole ecosystem ([src/interfaces](https://github.com/wormhole-foundation/wormhole-solidity-sdk/tree/main/src/interfaces))
- Includes the base class ‘Base’ with helpers for common actions that will typically need to be done within ‘receiveWormholeMessages’:
  - [`onlyWormholeRelayer()`](https://github.com/wormhole-foundation/wormhole-solidity-sdk/blob/main/src/WormholeRelayer/Base.sol#L9): Checking that msg.sender is the wormhole relayer contract
    Sometimes, Cross-chain applications may be set up such that there is one ‘spoke’ contract on every chain, which sends messages to the ‘hub’ contract. If so, we’d ideally only want to allow messages to be sent from these spoke contracts. Included are helpers for this:

    - [`setRegisteredSender(uint16 sourceChain, bytes32 sourceAddress)`](https://github.com/wormhole-foundation/wormhole-solidity-sdk/blob/main/src/WormholeRelayer/Base.sol#L45): Setting the specified sender for ‘sourceChain’ to be ‘sourceAddress’
    - [`isRegisteredSender(uint16 sourceChain, bytes32 sourceAddress)`](https://github.com/wormhole-foundation/wormhole-solidity-sdk/blob/main/src/WormholeRelayer/Base.sol#L30): Checking that the sender who requested the delivery is the registered address for that chain

    Look at test/Counter.t.sol for an example usage of Base

- Included are also:
  - The ‘[TokenSender](https://github.com/wormhole-foundation/wormhole-solidity-sdk/blob/main/src/WormholeRelayer/TokenBase.sol#L24)’ and ‘[TokenReceiver](https://github.com/wormhole-foundation/wormhole-solidity-sdk/blob/main/src/WormholeRelayer/TokenBase.sol#L158)’ base classes with helpers for smart contracts that wish to send and receive tokens using Wormhole’s TokenBridge. See ‘[HelloToken](https://github.com/wormhole-foundation/hello-token)’ for example usage.
  - The ‘[CCTPSender](https://github.com/wormhole-foundation/wormhole-solidity-sdk/blob/main/src/WormholeRelayer/CCTPBase.sol#L59)’ and ‘[CCTPReceiver](https://github.com/wormhole-foundation/wormhole-solidity-sdk/blob/main/src/WormholeRelayer/CCTPBase.sol#L177)’ base classes with helpers for smart contracts that wish to send and receive both tokens using Wormhole’s TokenBridge as well as USDC using CCTP. See ‘[HelloUSDC](https://github.com/wormhole-foundation/hello-usdc)’ for example usage.
  - Or a combination of both - [CCTPAndTokenBase](https://github.com/wormhole-foundation/wormhole-solidity-sdk/blob/main/src/WormholeRelayer/CCTPAndTokenBase.sol).
  - Helpers for setting up a local forge testing environment. See ‘[HelloWormhole](https://github.com/wormhole-foundation/hello-wormhole)’ for example usage.

**Note: This code is meant to be used as starter / reference code. Feel free to modify for use in your contracts, and also make sure to audit any code used from here as part of your contracts before deploying to mainnet.**