# ERC-777

## Problems Solved by ERC-777

The principal intent for ERC-777 is to solve some of the shortcomings of ERC-20 while maintaining backward compatibility with ERC-20, and avoiding the problems and vulnerabilities of EIP-223.

### Lifecycle

More than just sending tokens, ERC-777 defines the entire lifecycle of a token, starting with the minting process, followed by the sending process, and terminating with the burn process.

Having a lifecycle clearly defined is important for consistency and accuracy, especially when value is derived from scarcity. In contrast, when looking at some ERC-20 tokens, a discrepancy can be observed between the value returned by the `totalSupply` and the actual circulating supply, as the standard does not clearly define a process to create and destroy tokens.

### Data

The mint, send and burn processes can all make use of `data` and `operatorData` fields which are passed to any movement (mint, send or burn). Those fields may be empty for simple use cases, or they may contain valuable information related to the movement of tokens, similar to information attached to a bank transfer by the sender or the bank itself.

### Hooks

In most cases, ERC-20 requires two calls to safely transfer tokens to a contract without locking them. A call from the sender, using the `approve` function and a call from the recipient using `transferFrom`. Furthermore, this requires extra communication between the parties which is not clearly defined. Finally, holders can get confused between `transfer` and `approve`/`transferFrom`. Using the former to transfer tokens to a contract will most likely result in locked tokens.

Hooks allow streamlining of the sending process and offer a single way to send tokens to any recipient. Thanks to the `tokensReceived` hook, contracts are able to react and prevent locking tokens upon reception.

### Greater Control For Holders

The `tokensReceived` hook also allows holders to reject the reception of some tokens. This gives greater control to holders who can accept or reject incoming tokens based on some parameters, for example, located in the `data` or `operatorData` fields.

Following the same intentions, the `tokensToSend` hook was added to give control over and prevent the movement of outgoing tokens.

### Operators

The standard defines the concept of operators as any address which moves tokens. While intuitively every address moves its own tokens, separating the concepts of holder and operator allows for greater flexibility. Primarily, this originates from the fact that the standard defines a mechanism for holders to let other addresses become their operators. Moreover, unlike the approve calls in ERC-20 where the role of an approved address is not clearly defined, ERC-777 details the intent of and interactions with operators, including an obligation for operators to be approved, and an irrevocable right for any holder to revoke operators.

#### Default Operators

Default operators are pre-approved operators which are approved for all holders by default. For obvious security reasons, the list of default operators is defined at the token contract creation time, and cannot be changed. Any holder still has the right to revoke default operators. One of the obvious advantages of default operators is to allow ether-less movements of tokens. Default operators offer other usability advantages, such as allowing token providers to offer functionality in a modular way, and to reduce the complexity for holders to use features provided through operators.

## Issues with ERC-777:

### Reentrancy Risks

ERC-777 can introduce reentrancy vulnerabilities, especially if the hooks are not implemented correctly.

### Complexity

While powerful, the advanced features of ERC-777 add layers of complexity, which can lead to potential developer errors.

### Unpredictable Interactions

The interactions of ERC-777 with other contracts, especially during token transfers, can be unpredictable, leading to potential security risks.

# ERC-1363

ERC-1363 defines a token interface for ERC-20 tokens that supports executing recipient code after `transfer` or `transferFrom`, or spender code after `approve`.

## Problems Solved by ERC-1363

There is no way to execute code after an ERC-20 transfer or approval (i.e., making a payment), so to make an action it is required to send another transaction and pay GAS twice.

ERC-1363 wants to make token payments easier and works without the use of any other listener. It allows to make a callback after a transfer or approval in a single transaction.

There are many proposed uses of Ethereum smart contracts that can accept ERC-20 payments.

Examples could be

- to create a token payable crowdsale
- selling services for tokens
- paying invoices
- making subscriptions

For these reasons ERC-1363 is often referred to as a “**Payable Token**”.

One can use it for specific utilities or for any other purposes that require the execution of a callback after a transfer or approval is received.

The functions below represent the ERC-1363 standard functions a token contract and contracts working with tokens can implement to make a token "payable".

`transferAndCall` and `transferFromAndCall` will call an `onTransferReceived` on a `ERC1363Receiver` contract.

`approveAndCall` will call an `onApprovalReceived` on an `ERC1363Spender` contract.

# Comparison between ERC-777 and ERC-1363:

While both ERC-777 and ERC-1363 aim to improve upon the ERC-20 standard, ERC-1363 focuses more on the notification mechanism. On the other hand, ERC-777, with its advanced features, can introduce complexities and potential vulnerabilities if not implemented with care.
