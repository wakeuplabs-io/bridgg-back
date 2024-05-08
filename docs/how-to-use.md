---
  title: How to use the platform
  sidebar_position: 4
---

# How to use the platform

## Deposits

Deposits refer to the users transferring tokens from Layer 1 to Layer 2. Brid.gg admits deposits from Ethereum to any OP chain supported in our interface.
### Operating Principle
Depositing assets is the most common way of transferring assets into an OP Chain. Users can use our UI to call any specified deposit functions, which will lock the tokens in their expected escrow contract. A message is sent through `L1CrossDomainMessenger`, which prompts `OptimismPortal` to emit an event that is digested by rollup nodes. The message is then included (and finalized) by the sequencer in a near future L2 block. This process usually takes 1-3 minutes to complete, and users only need to perform one transaction.
Most ERC20s are stored in the L1 `StandardBridge` contract but may vary depending on the token list used. Examples of tokens that use different escrow contracts include but are not limited to wstETH, DAI, and SNX. Conversely, Brid.gg also supports some tokens using their own transfer protocol, such as USDC, which goes through Circle’s CCTP.
Check out the [latest OP Stack specs](https://specs.optimism.io/protocol/deposits.html) to better understand the underlying [deposit process](https://specs.optimism.io/protocol/deposits.html).
### User flow
The flow to deposit into a supported OP Chain is easy:
1. Connect your wallet.
2. Choose the from (Ethereum) and to (e.g. Base) networks.
3. Select an asset from the token list and put an amount.
4. Verify the bridging method. Most tokens will go through the canonical bridge, but there are exceptions like USDC, which goes through Circle’s own CCTP.
5. Specify a destination address. The interface defines the source one as the default choice.
6. Review and execute the transaction.

And it’s done! The tokens will arrive shortly. Users can track deposit statuses through the **Account History** section. Replay transactions are possible if needed.


## Withdrawals

Withdrawals refer to the users transferring tokens from Layer 2 back to Layer 1. Brid.gg admits withdrawals from any OP chain supported in our interface back to Ethereum. Withdrawals are more complex than deposits, but through Brid.gg, we ensure the experience is as easy as intuitive.

### Operating Principle
Withdrawing assets through the canonical bridge is the most secure way to reclaim your assets on L1. Due to the current security model of OP Chains, the whole process takes seven days to complete. Withdrawals are initiated on L2 via a call to the `L2toL1MessagePasser` contract, and then their inclusion on L1 is proved via a call to the `OptimismPortal`. Afterward, withdrawals are finalized on L1 via a call to the `OptimismPortal` contract, which verifies that the fault challenge period has passed since the withdrawal message has been proved.
Check out the [latest OP Stack specs](https://specs.optimism.io/protocol/withdrawals.html) to better understand the underlying [withdrawal process](https://specs.optimism.io/protocol/withdrawals.html).

### User flow
The flow to withdraw to Ethereum consists in:
1. Connect your wallet.
2. Choose the from (e.g. OP Mainnet) and to (Ethereum) networks.
3. Select an asset from the token list and put an amount.
4. Verify the bridging method. Most tokens will go through the canonical bridge, but there are exceptions like USDC, which goes through Circle’s own CCTP.
5. Specify a destination address. The interface defines the source one as the default choice.
6. Review and execute the first of three transactions.

At this point, if the OP Standard Bridge is used, it will require users to submit three (3) transactions manually:
1. Withdrawal **initiating** transaction, which the user submits on L2.
2. Withdrawal **proving** transaction, which the user submits on L1 after the next state root is posted, to prove that the withdrawal is legitimate. This step can normally execute some minutes up to several hours after the initiating transaction is confirmed, depending on the regularity with which state roots are commonly posted.
3. Withdrawal **finalizing** transaction, which the user submits on L1 after the fault challenge period, to claim the assets in L1. This step can be executed after seven days, counting since the proving transaction is confirmed.

And it’s done! Tokens will be sent to the wallet. Users can track their withdrawal status through the **Account History** section.


## Expert mode

> ⚠️ **Warning**: This expert mode is for advanced users only. Please use it only if you understand how it works and its purpose.

Despite most bridge interfaces only allowing for token bridging, communication across networks in the Superchain goes beyond that. We are the first to introduce Expert Mode, where users can take advantage of the extended functionality in the `CrossDomainMessenger` and `OptimismPortal` contracts. This includes three new features: **Custom transactions**, **Force transfers**, and **Force withdrawals**.

### Custom transactions
Call any L2 contract with arbitrary data from L1. The user must enter the target L2 contract address, paste the respective ABI, choose an available function, and enter the required inputs. It also allows them to enter custom data.

### Force transfers
Move assets from one address to another, initiated from L1, and automatically trigger it on L2, just as you would a standard L2 transaction. As a result, the deposit transaction is guaranteed to be included in a future L2 block, serving as a medium of censorship resistance. Currently, we provide support for ETH and some bridged L1 tokens; more token support will be in the future.

### Force withdrawals
It is similar to force transfers but specified to initiate withdrawals, this time from L1. This mechanism is akin to how escape hatches have worked in other designs. Currently, we provide support for ETH and some bridged L1 tokens.