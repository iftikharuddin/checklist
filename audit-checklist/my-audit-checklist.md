# Checklist before auditing protocols

Did you ran slither? Aderyn? or any other static analysis tool? They can help a lot believe me!!

## 22 Vulnerabilities I Use To Find Criticals FAST!

1 - Frontrunning & Backrunning 
    - Time dependent
    - Get-or-create pattern functionality is prone to frontrunning attacks.	
    - Ensure the frontrunning does not cause user loss or unexpected problems.
    - Actions that require two separate transactions may be at risk of frontrunning, where an attacker can intervene between the two calls.	
    - Attackers may cause legitimate transactions to fail by front-running with transactions of negligible amounts.	
    
2 - Using very small amounts
    -  rounding and precision issues
    - can be covered by fuzzing/invariant

3 - Passing zero as input
    - it can be zero address, zero uint value, empty lists, lists of zero values

4 - Using contracts that can't accept ETH
    - let say a 2 way trx, one send a trx and keeper runs it and then send it back to the contract. How about inserting a contract in between which don't accept ETH? DOS!!!
  
5 - Gas Griefing with External Calls
    - any aritrary call to an untrusted contract/address that is NOT controlled by the caller (the person who is paying for gas)
        - this address (untrusted) can spend a huge gas from the caller
        - always set gas limit when calling to untrusted address
       
6 - WEIRD ERC20 Tokens
    - ERC777 tokens (reentrancy)
    - Fee on transfer tokens
    - Blacklisted tokens
   
7 - Price Manipulation
    - 

8 - Blacklisted ERC20 Addresses
    - USDC/USDT has blacklist mappings
    
9 - Underflow/Overflow
    - casting can have issues
    
10 - Block re-orgs
    - ????
    
11 - Reentrancy 

12 - Sybil attacks on incentives
    - Step wise jumps ( https://youtu.be/-9VmITcdm3c )
    - Reward Compounds (donations)
    - Interest rate updates
    - Always check setters of rewards
    - Inflation attack ( relaying on balanceOf )
    
13 - Flash loans
    
    
## Lending/Borrowing DeFi Attacks
 
Lending & Borrowing DeFi platforms display common sets of vulnerabilities

- No Incentive To Liquidate Small Positions e.g [Issue](https://github.com/Cyfrin/2023-07-foundry-defi-stablecoin/issues/1096)
- Liquidation Before Default e.g [Issue](https://github.com/sherlock-audit/2023-03-teller-judging/issues/92)
- Borrower Can't Be Liquidated e.g [Issue](https://dacian.me/exploiting-developer-assumptions#heading-unchecked-return-values)
- Borrower Repayment Only Partially Credited e.g [Issue](https://github.com/sherlock-audit/2022-10-astaria-judging/issues/190)
- Debt Closed Without Repayment e.g [Issue](https://dacian.me/exploiting-developer-assumptions#heading-unexpected-empty-inputs)
- Repayments Paused While Liquidations Enabled e.g [Issue](https://github.com/sherlock-audit/2023-02-blueberry-judging/issues/290)
- Token Disallow Stops Existing Repayment & Liquidation
- Borrower Immediately Liquidated After Repayments Resume
- Liquidator Takes Collateral With Insufficient Repayment
- Infinite Loan Rollover e.g [Issue](https://github.com/sherlock-audit/2023-01-cooler-judging/issues/215)
- Repayment Sent to Zero Address e.g [Issue](https://github.com/sherlock-audit/2023-01-cooler-judging/issues/33)
- Borrower Permanently Unable To Repay Loan e.g [Issue](https://github.com/sherlock-audit/2022-10-union-finance-judging/issues/133)
- Borrower Repayment Only Partially Credited e.g [Issue](https://github.com/sherlock-audit/2022-10-astaria-judging/issues/190)


Credits and thanks to: [Dacian](https://dacian.me/) & [mixbytes](https://mixbytes.io/blog/vulnerable-spots-of-lending-protocols)

## Chainlink CCIP

- Before calling the Router's `ccipSend` function, ensure that your code allows users to send CCIP messages to trusted destination chains.
- When implementing the `ccipReceive` method in a contract residing on the destination chain, ensure to verify the source chain of the incoming CCIP message. This verification ensures that CCIP messages can only be received from trusted source chains.
- When implementing the ccipReceive method in a contract residing on the destination chain, it's important to validate the sender of the incoming CCIP message. This check ensures that CCIP messages are received only from trusted sender addresses.
- Setting `gasLimit`
- Validate the sender, when data is encoded on Primary chain it must be decoded on secondary chains

https://blog.chain.link/ccip-risk-management-network/

ref https://cll-devrel.gitbook.io/ccip-masterclass-2/ccip-masterclass/exercise-1-tiny-token-transferor

- CCIP router address cannot be updated
    - ref https://www.codehawks.com/finding/clrunirpg00o4dc6tusyc0jt1 
## Oracles

- Assuming Oracle Price Precision [Stackoverflow](https://ethereum.stackexchange.com/questions/92508/do-all-chainlink-feeds-return-prices-with-8-decimals-of-precision)
    - assuming a fixed decimal precision (8 decimals) for price feeds, which may not hold true for all chains and pairs (reported in Superform)
    
- [M-01] The Chainlink price feed's input is not properly validated ( check for stale and negative returns )

- [M-02] Deviation in oracle price could lead to arbitrage in high LLTV markets 
    - all price oracles are susceptible to front-running as their prices tend to lag behind an asset's real-time price. More specifically:
    - Chainlink oracles are updated after the change in price crosses a deviation threshold, (eg. 2.5% in ETH / USD), which means a price feed could return a value slightly smaller/larger than an asset's actual price under normal conditions.
    - Uniwap V3 TWAP returns the average price over the past X number of blocks, which means it will always lag behind the real-time price.
    - An attacker could exploit the difference between the price reported by an oracle and the asset's actual price to gain a profit by front-running the oracle's price update.
    - Consider implementing a borrowing fee to mitigate against arbitrage opportunities. Ideally, this fee would be larger than the oracle's maximum price deviation so that it is not possible to profit from arbitrage.
       

### Chainlink Oracle Security Considerations

- Not Checking For Stale Prices
- Not Checking For Down L2 Sequencer e.g [Issue](https://github.com/Cyfrin/2023-07-foundry-defi-stablecoin/issues/1046)
- Unhandled Oracle Revert Denial Of Service

### [](https://www.beirao.xyz/blog/Security-checklist#vault)Vault

[V-01] - Can transferring ERC20 or ETH directly break something ?

[V-02] - Is the vault balance tracked internally ?

[V-03] - Can the 1st deposit raise a problem ? [here](https://mixbytes.io/blog/overview-of-the-inflation-attack#rec558234839)

[V-04] - Is this vault taking into consideration that some ERC20 token are not 18 decimals ?

[V-05] - Is the fee calculation correct ?

[V-06] - What if only 1 wei remain in the pool ?

[V-07] - On vault with strategies implemented :

-   flash deposit-harvest-withdraw attacks are possible? First depositor can steal funds of others [here](https://mixbytes.io/blog/overview-of-the-inflation-attack#rec558234839)
-   How the vault behave when locked fund are put in a strategy?
-   Are losses handled ? (always should)
-   What happen in case of a black swan event ? (protocol implemented in the strategy get hacked)
-   Look at token-specific/protocol risks implemented in strategies :
    -   For protocol
        -   Pause ?
        -   Emergency withdrawal ?
        -   Depreciation ?
    -   For tokens
        -   All weird implementations

[V-08] - Can we manipulate the conversion rate between shares and underlying ? ([here](https://mixbytes.io/blog/yield-aggregators-common-pitfalls#rec515086866))

- Is it possible to withdraw in the same transaction of deposit?
    - Protocols often provide various benefits to the depositors based on the deposit amount. This can lead to flashloan-deposit-harvest-withdraw attack cycle.	
    - Ensure the withdrawal is protected for some blocks after deposit.

## Do Not Miss These 5 Upgradeability Vulnerabilities

- Storage Gaps
    - Storage collisions
    - Ownable Upgradable
    - **Tip:** If protocol team created a contract and other contracts are inherited from it, make sure the higher in hierarchy (Primary)
        - are using storage gaps between storage variables
        - using namespace storage
    - If not it can cause storage collisions during updradebility  ( https://www.codehawks.com/finding/clrunirt300ocdc6tvlc9qyv0 )
    
- UUPS
    - Ownable
    - **Tip:** If the UUPS is using OZ library make sure it uses the upgradeable version of it
    - e.g If UUPS is using Ownable it will cause issues, it should use OwnableUpgradable

- Init functions
    - Initialize is missing initializer modifier
    - 
- _authorizeUpgrade must be access controlled once override 
    
- `pause/unpause` functionalities not implemented in many pausable contracts ( PausableUpgradeable )
    - ref https://solodit.xyz/issues/m-02-pauseunpause-functionalities-not-implemented-in-many-pausable-contracts-code4rena-stader-labs-stader-labs-git 
    

## Bridges DeFi Attacks

- 

ref https://github.com/ComposableSecurity/SCSVS/blob/master/2.0/0x200-Components/0x205-C5-Bridge.md 

## Cross Chain Attacks

- Verify that the protocol uses solidity-examples package to import LayerZero contracts and does not copy-paste them directly from the repository.

ref https://github.com/ComposableSecurity/SCSVS/blob/master/2.0/0x300-Integrations/0x304-I4-Cross-Chain.md 

- Should expose the proposed time in function getFailedDeposits #281 (Cantina)
    - the function should also expose failed deposit proposed time to inform external integration the proposed time and whether the failed deposit is ready for rescue or dispute
      
     

## Extra known issues
- [H-1]: Arbitrary from passed to transferFrom (or safeTransferFrom)
    - Passing an arbitrary `from` address to `transferFrom` (or `safeTransferFrom`) can lead to loss of funds, because anyone can transfer tokens `from` the from address if an approval is made.
    - use msg.sender instead of arbitrary from  
    
- Whenever you see division (/) in code think of 2 bugs
    - division by zero can occur?
    - rounding errors can occur?    

- Array length mismatch check.

- In rewards distribution or any funds handling check for dust.

- Check if borrow allows to create small positions which leads to more bad debt
    - set a minimum limit for how much someone can borrow in each situation
    - This way, even if the borrowed amount is small, it won't be so small that it becomes unprofitable for others to step in and manage the situation

- An old version of the isContract function is used [ M ]
    - The most recent version of this function in the OpenZeppelin contracts project uses `extcodehash` instead of `extcodesize` to give better guarantees and work with more cases.
    - ref https://solodit.xyz/issues/m10-not-using-openzeppelin-contracts-openzeppelin-celo-contracts-audit-markdown
    
- [L-02] It's possible to use a flawed compiler version
Solidity version 0.8.13 & 0.8.14 have a security vulnerability related to assembly blocks that write to memory.
The issue is fixed in version 0.8.15 and is explained [here](https://soliditylang.org/blog/2022/06/15/solidity-0.8.15-release-announcement/).
While the problem is with the legacy optimizer, it is still correct to enforce latest Solidity version (0.8.21) or at least the one enforced in other popular library code as OpenZeppelin (0.8.19).

- Reports: https://ottersec.notion.site/Sampled-Public-Audit-Reports-a296e98838aa4fdb8f3b192663400772

- Use abi.encodeCall() instead of abi.encodeWithSignature()/abi.encodeWithSelector()	

- [G-12] Use of emit inside a loop
    - Emitting an event inside a loop performs a LOG op N times, where N is the loop length. Consider refactoring the code to emit the event only once at the end of loop. Gas savings should be multiplied by the average loop length.
    
- [L] `PUSH0` might not be supported on all chains, leading to potential incompatibility issues.

- [L] use of assert in production is NOT recommended
    - When an assert statement fails, it consumes all remaining gas, and the state changes made up to that point in the transaction are rolled back. This behavior is different from require statements, which consume only the gas stipulated at the time of failure and allow for a more graceful handling of conditions.

- [L] Remove Token With Non-zero Balance
    - The `removeToken()` function does not check if the token balance of the contract is zero or not. If the balance of a
      token is not zero and this token is removed, the owner should add it again to the contract to withdraw the remaining
      amount of tokens.
    - Consider adding a require statement in removeToken() that checks for the token contract balance.
    
- Use of Solidity version 0.8.13 which has two known issues ( ABI Encoding )

- Always check package.json for packages versions
    - most OZ versions are vulnerable

- Missing contract-existence checks before low-level calls
    - Low-level calls return success if there is no code present at the specified address, and this could lead to unexpected scenarios.
    - Ensure that the code is initialized by checking <address>.code.length > 0.

- Uncleared approval, gives access to transfer token.
    - always check for approvals, usually the approval is not removed and can lead to issues (e.g https://www.codehawks.com/finding/clruniruv00ogdc6t52hhlwtf )

- `stETH` can be paused
    - https://solodit.xyz/issues/steth-can-be-paused-mixbytes-none-lido-markdown
    
## Re-entrancy issues

- Common reentrancy
    - check if state is updating after external call?
    - move the state update above external call
    
- Functions reentrancy
- Contracts reentrancy
- Read only reentrancy
    
- use of strict euality (!= , == ) should always be avoided in funds equations specially.
    - use >= or <=    
## IERC20 Issues

- Medium - Unsafe use of transfer() with IERC20
    - Recommendation: Use OpenZeppelin's SafeERC20 library instead of transfer.
    
- Is there a direct approval to a non-zero value?
    - Description: Some ERC20 tokens do not work when changing the allowance from an existing non-zero allowance value. For example Tether (USDT)'s approve() function will revert if the current approval is not zero, to protect against front-running changes of approvals.	    
    - Solution: Set the allowance to zero before increasing the allowance and use safeApprove/safeIncreaseAllowance.

## AMM

- Why You Should Stop Using block.timestamp as Deadline in Swaps
    - https://web.archive.org/web/20230525014603/https://blog.bytes032.xyz/p/why-you-should-stop-using-block-timestamp-as-deadline-in-swaps
    
## Informational issues    

- #I-1 : internal function name doesn't start with an '_'. They should start with _ ( best case )
    - Note: internal library function don't start with an `_`
    
- #-2: Hash Collision Vulnerability   
    - replace the usage of abi.encodePacked() with abi.encode()
    
- #-3: Events missing `indexed` fields for 
    - Index event fields make the field more quickly accessible to off-chain tools that parse events   
     
- #-4: Use of licensed code without credit
    - The OZ code is BSD-licensed, therefore copying it without including the original license and crediting OZ is a breach of intellectual property law.

- #-5: Waste of gas due to passing 0x00 as calldata
    - the contract passes "0x00". In fact, this would correspond to 4 non-zero bytes which cost gas as they are part of the calldata. It is recommended to pass "" unless there is a specific reason to do so.