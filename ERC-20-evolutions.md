# ERC-777

## Problems Solved by ERC-777:

The principal intent for ERC-777 is to solve some of the shortcomings of ERC-20 while maintaining backward compatibility with ERC-20, and avoiding the problems and vulnerabilities of EIP-223.

### Lifecycle

More than just sending tokens, ERC-777 defines the entire lifecycle of a token, starting with the minting process, followed by the sending process and terminating with the burn process.

Having a lifecycle clearly defined is important for consistency and accuracy, especially when value is derived from scarcity. In contrast when looking at some ERC-20 tokens, a discrepancy can be observed between the value returned by the `totalSupply` and the actual circulating supply, as the standard does not clearly define a process to create and destroy tokens.

### Data

The mint, send and burn processes can all make use of a `data` and `operatorData` fields which are passed to any movement (mint, send or burn). Those fields may be empty for simple use cases, or they may contain valuable information related to the movement of tokens, similar to information attached to a bank transfer by the sender or the bank itself.

### Hooks

In most cases, ERC-20 requires two calls to safely transfer tokens to a contract without locking them. A call from the sender, using the `approve` function and a call from the recipient using `transferFrom`. Furthermore, this requires extra communication between the parties which is not clearly defined. Finally, holders can get confused between `transfer` and `approve`/`transferFrom`. Using the former to transfer tokens to a contract will most likely result in locked tokens.

Hooks allow streamlining of the sending process and offer a single way to send tokens to any recipient. Thanks to the `tokensReceived` hook, contracts are able to react and prevent locking tokens upon reception.

### Greater Control For Holders

The `tokensReceived` hook also allows holders to reject the reception of some tokens. This gives greater control to holders who can accept or reject incoming tokens based on some parameters, for example located in the `data` or `operatorData` fields.

Following the same intentions, the `tokensToSend` hook was added to give control over and prevent the movement of outgoing tokens.

### Operators

The standard defines the concept of operators as any address which moves tokens. While intuitively every address moves its own tokens, separating the concepts of holder and operator allows for greater flexibility. Primarily, this originates from the fact that the standard defines a mechanism for holders to let other addresses become their operators. Moreover, unlike the approve calls in ERC-20 where the role of an approved address is not clearly defined, ERC-777 details the intent of and interactions with operators, including an obligation for operators to be approved, and an irrevocable right for any holder to revoke operators.

#### Default Operators

Default operators were added based on community demand for pre-approved operators. That is operators which are approved for all holders by default. For obvious security reasons, the list of default operators is defined at the token contract creation time, and cannot be changed. Any holder still has the right to revoke default operators. One of the obvious advantages of default operators is to allow ether-less movements of tokens. Default operators offer other usability advantages, such as allowing token providers to offer functionality in a modular way, and to reduce the complexity for holders to use features provided through operators.

## Issues with ERC-777:

### Reentrancy Risks

ERC-777 can introduce reentrancy vulnerabilities, especially if the hooks are not implemented correctly.

### Complexity

While powerful, the advanced features of ERC-777 add layers of complexity, which can lead to potential developer errors.

### Unpredictable Interactions

The interactions of ERC-777 with other contracts, especially during token transfers, can be unpredictable, leading to potential security risks.

## ERC1363

### Why was ERC1363 introduced?
ERC1363 was introduced to address some of the limitations and challenges of previous token standards, especially around token transfers. The primary motivation was:
- **Token Transfer Notifications**: ERC1363 allows for the notification of both the spender/operator and the recipient. This is crucial for preventing tokens from being sent to contracts that don't know how to handle them.

### Problems Solved by ERC1363:
- **Improved UX**: ERC1363 provides a better user experience by allowing tokens to notify the receiving contract, addressing the limitation where a smart contract can't know it has received an ERC20 token.
- **Flexibility**: It offers flexibility with functions like `transferAndCall` that allow for more complex and informed token transfer scenarios.

### Comparison with ERC777:
While both ERC777 and ERC1363 aim to improve upon the ERC20 standard, ERC1363 focuses more on the notification mechanism, ensuring that tokens are only sent to intended recipients. On the other hand, ERC777, with its advanced features, can introduce complexities and potential vulnerabilities if not implemented with care.

---

**Note**: Developers should always prioritize security and understand the nuances of each token standard before adoption.
