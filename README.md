
# Rubicon Finance contest details

- Join [Sherlock Discord](https://discord.gg/MABEWyASkp)
- Submit findings using the issue page in your private contest repo (label issues as med or high)
- [Read for more details](https://docs.sherlock.xyz/audits/watsons)

# Q&A

### Q: On what chains are the smart contracts going to be deployed?
Optimism, Arbitrum, Base, Polygon, Ethereum
___

### Q: Which ERC20 tokens do you expect will interact with the smart contracts? 
any, excluding custom implementations (e.g., "fee-on-transfer" tokens)
___

### Q: Which ERC721 tokens do you expect will interact with the smart contracts? 
none
___

### Q: Do you plan to support ERC1155?
nope
___

### Q: Which ERC777 tokens do you expect will interact with the smart contracts? 
none
___

### Q: Are there any FEE-ON-TRANSFER tokens interacting with the smart contracts?

no
___

### Q: Are there any REBASING tokens interacting with the smart contracts?

no
___

### Q: Are the admins of the protocols your contracts integrate with (if any) TRUSTED or RESTRICTED?
contracts don't integrate with other protocols
___

### Q: Is the admin/owner of the protocol/contracts TRUSTED or RESTRICTED?
TRUSTED

___

### Q: Are there any additional protocol roles? If yes, please explain in detail:
1) Proxy admin - can perform contract upgrades.
2) Admin of the fee-controller - can only interact with `RubiconFeeController`, he can set both base and pair-based fees, which are applied to the output of each order during its execution. Additionally, the admin can set the addresses of the `feeRecipient` and `gladiusReactor`. It's essential to note that the reactor will invoke `getFeeOutputs` with every order execution. A potential concern and non-intended outcome is that if the admin sets invalid parameters, such as an applied fee that exceeds the global `MAX_FEE`, the protocol may be susceptible to a DoS attack due to unexpected reverts in the `getFeeOutputs`.
___

### Q: Is the code/contract expected to comply with any EIPs? Are there specific assumptions around adhering to those EIPs that Watsons should be aware of?
`PartialFillLib` contains structured data that should be compatible with EIP-712.
___

### Q: Please list any known issues/acceptable risks that should not result in a valid finding.
From OZ audit - https://github.com/Uniswap/UniswapX/blob/main/audit/v1.1/OpenZeppelin.pdf
1) Fillers Can Incur a Loss Without a Chance to Revert.
2) Fee Controller Can DOS Trading Activity. Note, that, as said above, the resulting output shouldn't overflow `MAX_FEE`, but other possibilities of reverts are known/acceptable.
___

### Q: Please provide links to previous audits (if any).
Note, that these audits cover only parts of the contracts, on which ours depend, while contracts listed in scope for this contest aren't audited:
1) OZ audit of UniswapX v1.1 - https://github.com/Uniswap/UniswapX/blob/main/audit/v1.1/OpenZeppelin.pdf
2) ABDK audit of UniswapX v1.1 - https://github.com/Uniswap/UniswapX/blob/main/audit/v1.1/ABDK.pdf
3) ABDK audit of UniswapX v1.0 - https://github.com/Uniswap/UniswapX/blob/main/audit/v1/ABDK.pdf
___

### Q: Are there any off-chain mechanisms or off-chain procedures for the protocol (keeper bots, input validation expectations, etc)?
Yep, there are a couple of off-chain mechanisms/procedures:
1) Orders are submitted via HTTP POST request to the off-chain server.
2) Orders are stored in an off-chain database.
3) Stored orders can be queried using HTTP GET request.
4) Orders are executed by `fillers`, who are expected to run an off-chain logic, and pick orders, that are profitable for them to execute.
5) Orders can be matched together by an off-chain matching engine, through the `executeBatch*` call.
6) Cancellation of orders are implemented off-chain and intended to work only if either matching-engine or Rubicon-owned filler bot is set as an `exclusiveFiller` and an additional validation is applied to them to ensure strict exclusivity.
___

### Q: In case of external protocol integrations, are the risks of external contracts pausing or executing an emergency withdrawal acceptable? If not, Watsons will submit issues related to these situations that can harm your protocol's functionality.
nope
___

### Q: Do you expect to use any of the following tokens with non-standard behaviour with the smart contracts?
1) Missing return values
2) Tokens with Blocklists
___

### Q: Add links to relevant protocol resources
1) Gladius intergration guide - https://rubicondefi.notion.site/Rubicon-Gladius-Integration-Guide-ee55cbe3ccdf4b88a6780d8ef090599f
2) UniswapX documentation - https://docs.uniswap.org/contracts/uniswapx/overview

___



# Audit scope


[gladius-contracts-internal @ ef95e2500c3f836479a7679eb7617812ccceaaa3](https://github.com/RubiconDeFi/gladius-contracts-internal/tree/ef95e2500c3f836479a7679eb7617812ccceaaa3)
- [gladius-contracts-internal/src/fee-controllers/RubiconFeeController.sol](gladius-contracts-internal/src/fee-controllers/RubiconFeeController.sol)
- [gladius-contracts-internal/src/lens/GladiusOrderQuoter.sol](gladius-contracts-internal/src/lens/GladiusOrderQuoter.sol)
- [gladius-contracts-internal/src/lib/DSAuth.sol](gladius-contracts-internal/src/lib/DSAuth.sol)
- [gladius-contracts-internal/src/lib/PartialFillLib.sol](gladius-contracts-internal/src/lib/PartialFillLib.sol)
- [gladius-contracts-internal/src/lib/ProxyConstructor.sol](gladius-contracts-internal/src/lib/ProxyConstructor.sol)
- [gladius-contracts-internal/src/reactors/BaseGladiusReactor.sol](gladius-contracts-internal/src/reactors/BaseGladiusReactor.sol)
- [gladius-contracts-internal/src/reactors/GladiusReactor.sol](gladius-contracts-internal/src/reactors/GladiusReactor.sol)

