# Smart Contract Attack Vectors

The goal of this repository is to create the most clear and concise collection of smart contract attack vectors, to improve overall ecosystem security.

## Attacks

- [Front-Running AKA Transaction-Ordering Dependence](/attacks/front-running.md)
- [DoS with Block Gas Limit](/attacks/dos-gas-limit.md)
- [DoS with (Unexpected) revert](/attacks/dos-revert.md)
- [Forcibly Sending Ether to a Smart Contract](/attacks/forcibly-sending-ether.md)
- [Insufficient Gas Griefing](/attacks/insufficient-gas-griefing.md)
- [Reentrancy](/attacks/reentrancy.md)
- [Honeypot](/attacks/honeypot.md)

## Vulnerabilities

- [Integer Overflow and Underflow](/vulnerabilities/overflow-underflow.md)
- [Timestamp Dependence](/vulnerabilities/timestamp-dependence.md)
- [Authorization Through tx.origin](/vulnerabilities/authorization-txorigin.md)
- [Floating Pragma](/vulnerabilities/floating-pragma.md)
- [Function Default Visibility](/vulnerabilities/function-default-visibility.md)
- [Outdated Compiler Version](/vulnerabilities/outdated-compiler-version.md)
- [Unchecked Call Return Value](/vulnerabilities/unchecked-call-return-value.md)
- [Unprotected Ether Withdrawal](/vulnerabilities/unprotected-ether-withdrawal.md)
- [Unprotected Selfdestruct Instruction](/vulnerabilities/unprotected-selfdestruct.md)
- [State Variable Default Visibility](/vulnerabilities/state-variable-default-visibility.md)
- [Uninitialized Storage Pointer](/vulnerabilities/uninitialized-storage-pointer.md)
- [Assert Violation](/vulnerabilities/assert-violation.md)
- [Use of Deprecated Functions](/vulnerabilities/use-of-deprecated-functions.md)
- [Delegatecall to Untrusted Callee](/vulnerabilities/delegatecall-untrusted-callee.md)
- [Signature Malleability](/vulnerabilities/signature-malleability.md)
- ~[Incorrect Constructor Name](/vulnerabilities/incorrect-constructor.md)~ fixed since 0.4.22
- [Shadowing State Variables](/vulnerabilities/shadowing-state-variables.md)
- [Weak Sources of Randomness from Chain Attributes](/vulnerabilities/weak-sources-randomness.md)
- [Missing Protection against Signature Replay Attacks](/vulnerabilities/missing-protection-signature-replay.md)
- [Requirement Validation](/vulnerabilities/requirement-violation.md)
- [Write to Arbitrary Storage Location](/vulnerabilities/arbitrary-storage-location.md)
- [Incorrect Inheritance Order](/vulnerabilities/incorrect-inheritance-order.md)
- [Arbitrary Jump with Function Type Variable](/vulnerabilities/arbitrary-jump-function-type.md)
- [Presence of Unused Variables](/vulnerabilities/unused-variables.md)
- [Unexpected Ether Balance](/vulnerabilities/unexpected-ether-balance.md)
- [Unencrypted Secrets](/vulnerabilities/unencrypted-secrets.md)
- [Faulty Contract Detection](/vulnerabilities/faulty-contract-detection.md)
- [Unclogged Blockchain Reliance](/vulnerabilities/unclogged-blockchain-reliance.md)
- [Inadherence to Standards](/vulnerabilities/inadherence-to-standards.md)
- [Unprotected Callback](/vulnerabilities/unprotected-callback.md)
- [Asserting EOA from Code Size](/vulnerabilities/asserting-eoa-from-code-size.md)

## Further Reading

- https://github.com/ethereum/wiki/wiki/Safety
- https://swcregistry.io/
- https://eprint.iacr.org/2016/1007.pdf
- https://www.dasp.co/
- https://consensys.github.io/smart-contract-best-practices/
- https://github.com/sigp/solidity-security-blog
- https://solidity.readthedocs.io/en/latest/bugs.html
- https://github.com/pcaversaccio/reentrancy-attacks

## Contributions

If you notice any mistakes, typos or missing attacks/vulnerabilities, please feel free to open an issue or pull request. All contributions are very much appreciated.

Special thanks to [RobertMCForster](https://github.com/RobertMCForster) for many excellent contributions.

# Common Web3 Security Issues

## External calls
- Check for reentrancy risks
- Check whether the external call specifies the 1. the proper function and 2. the proper function arguments in the proper order for that function.

## Swap is performed
- Check for frontrunning and sandwich attack of the swap. This can happen when interacting with any liquidity pool (Uniswap, Sushi, Curve, Balancer, etc.)
- If a one-sided swap is performed before depositing into the liquidity pool (often named a "zap"), there may be [dust left over](https://blog.alphaventuredao.io/onesideduniswap/) because the first one-sided swap alters the balance and price of the liquidity pool assets.

## transferFrom is used
- If tokens are transferred from a contract address, that contract must approve the tokens before the transferFrom is called, otherwise it will revert

## Overflows
- Safe math in Solidity 0.8.X does not protect against integer casting overflows and bitshift overflows

## Gas griefing / denial of service
- Check all `require` and `revert` logic in case of unintentional reverts causing the user loss of gas

## ecrecover
- Standard solidity ecrecover function is used without checking if the result is the zero address. Must check for zero address or use OZ `recover` from ECDSA library.

## ERC20
- If an ERC20 token list is used it must consider double entry point tokens, with past issues of this type [here](https://medium.com/chainsecurity/trueusd-compound-vulnerability-bc5b696d29e2) and [here](https://forum.balancer.fi/t/medium-severity-bug-found/3161)

## ERC721
- The ERC721 tokenId value must be unique. If the tokenId value is not a simple incrementing counter and instead uses a formula to calculate the tokenId, duplicate values may occur and lead to a revert
- If safeMint is used, check for reentrancy on the callback hook

## Access controls
- If any access controls are used (often in the form of modifiers) check functions that do not have any modifier to see if the modifier may have been forgotten.

## External price data is used
- If spot price of a liquidity pool is used... [just no](https://shouldiusespotpriceasmyoracle.com/)
- Chainlink oracle data may be stale. The roundId and timestamp should be checked.

## AMM/DEX
- Any purchase or swap function should have slippage protection. This is normally a user-specified function argument.

## Compound forks
- Compound does not strictly follow the checks-effects-interactions pattern to avoid reentrancy. This can lead to reentrancy problems [if tokens with callbacks](https://twitter.com/Hacxyk/status/1520370424680304640) (ERC721, ERC777, etc.) are used.
- Compound whitelists tokens and can avoid ERC20 tokens with unusual behavior. If a fork of compound does not whitelist tokens, issues can exist with [fee-on-transfer tokens](https://github.com/d-xo/weird-erc20#fee-on-transfer) among others.

## Proxies
<!-- markdown-link-check-disable-next-line -->
- UUPS proxies MUST be initialized after deployment. Forgetting to initialize the proxy led to [the first $10 million bug](https://medium.com/immunefi/wormhole-uninitialized-proxy-bugfix-review-90250c41a43a) from Immunefi and [many other past bugs](https://twitter.com/transmissions11/status/1527699663322697728).
- State variable layout must be followed when delegatecall is used, otherwise this [leads to problems](https://solidity-by-example.org/hacks/delegatecall)

## OpenZeppelin Upgradeable imports
- Many OpenZeppelin upgradeable contracts need to be initialized in the importing contract's constructor.

## Voting and rewards/yield
- Does the snapshot process prevent a user from flashloaning tokens to make a large number of votes or receive a lot of yield?
- Does the snapshot process prevent a user from voting (or redeeming yield), transferring tokens, and voting again (or redeeming yield)?
- If the voting happens off-chain, is proper planning and integration with snapshot.org applied? Which voting strategy is used? Will [voting delegation be added](https://docs.snapshot.org/guides/delegation#with-a-smart-contract) with the `setDelegate` function?

## Loan-related protocol
- Is there a way that a user can become undercollateralized, reducing the incentive to pay their debt?

## NFT projects
- If cryptopunks are supported (which requires custom code), it should protect against [this frontrun attack vector](https://blog.nftx.io/nftx-v2-punk-incident-post-mortem/).


## A lot more findings
https://secureum.substack.com/p/audit-findings-101
