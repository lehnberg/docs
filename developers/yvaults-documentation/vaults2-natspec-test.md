# Yearn Token Vault

Yearn Token Vault. Holds an underlying token, and allows users to interact with the Yearn ecosystem through Strategies connected to the Vault. Vaults are not limited to a single Strategy, they can have as many Strategies as can be designed (however the withdrawal queue is capped at 20.) Deposited funds are moved into the most impactful strategy that has not already reached its limit for assets under management, regardless of which Strategy a user's funds end up in, they receive their portion of yields generated across all Strategies. When a user withdraws, if there are no funds sitting undeployed in the Vault, the Vault withdraws funds from Strategies in the order of least impact. (Funds are taken from the Strategy that will disturb everyone's gains the least, then the next least, etc.) In order to achieve this, the withdrawal queue's order must be properly set and managed by the community (through governance). Vault Strategies are parameterized to pursue the highest risk-adjusted yield. There is an "Emergency Shutdown" mode. When the Vault is put into emergency shutdown, assets will be recalled from the Strategies as quickly as is practical (given on-chain conditions), minimizing loss. Deposits are halted, new Strategies may not be added, and each Strategy exits with the minimum possible damage to position, while opening up deposits to be withdrawn by users. There are no restrictions on withdrawals above what is expected under Normal Operation. For further details, please refer to the specification: https://github.com/iearn-finance/yearn-vaults/blob/master/SPECIFICATION.md

## __init__(address,address,address,string,string)
Initializes the Vault, this is called only once, when the contract is deployed. The performance fee is set to 4.5% of yield, per Strategy. The management fee is set to 2%, per year. There is no initial deposit limit.

{% hint style="info" %} If `_nameOverride` is not specified, the name will be 'yearn' combined with the name of _token. If `_symbolOverride` is not specified, the symbol will be 'y' combined with the symbol of _token. {% endhint %}
| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _token | The token that may be deposited into this Vault. |
| Input | 1 | | _governance | The address authorized for governance interactions. |
| Input | 2 | | _rewards | The address to distribute rewards to. |
| Input | 3 | | _nameOverride | Specify a custom Vault name. Leave empty for default choice. |
| Input | 4 | | _symbolOverride | Specify a custom Vault symbol name. Leave empty for default choice. |
## apiVersion()
Used to track the deployed version of this contract. In practice you can use this version number to compare with yEarn's GitHub and determine which version of the source matches this deployed contract.

| | | type | parameter | desc |
|---|---|---|---|---|
| Output | 0 | | _0 | API_VERSION which holds the current version of this contract. |
## setGovernance(address)
Nominate a new address to use as governance. The change does not go into effect immediately. This function sets a pending change, and the governance address is not updated until the proposed governance address has accepted the responsibility. This may only be called by the current governance address.

| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _governance | The address requested to take over Vault governance. |
## acceptGovernance()
Once a new governance address has been proposed using setGovernance(), this function may be called by the proposed address to accept the responsibility of taking over governance for this contract. This may only be called by the proposed governance address.

{% hint style="info" %} setGovernance() should be called by the existing governance address, prior to calling this function. {% endhint %}
| | | type | parameter | desc |
|---|---|---|---|---|
## setRewards(address)
Changes the rewards address. Any distributed rewards will cease flowing to the old address and begin flowing to this address once the change is in effect. This will not change any Strategy reports in progress, only new reports made after this change goes into effect. This may only be called by governance.

| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _rewards | The address to use for collecting rewards. |
## setDepositLimit(uint256)
Changes the maximum amount of tokens that can be deposited in this Vault. Note, this is not how much may be deposited by a single depositor, but the maximum amount that may be deposited across all depositors. This may only be called by governance.

| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _limit | The new deposit limit to use. |
## setPerformanceFee(uint256)
Used to change the value of `performanceFee`. This may only be called by governance.

| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _fee | The new performance fee to use. |
## setManagementFee(uint256)
Used to change the value of `managementFee`. This may only be called by governance.

| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _fee | The new management fee to use. |
## setGuardian(address)
Used to change the address of `guardian`. This may only be called by governance or the existing guardian.

| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _guardian | The new guardian address to use. |
## setEmergencyShutdown(bool)
Activates or deactivates Vault mode where all Strategies go into full withdrawal. During Emergency Shutdown: 1. No Users may deposit into the Vault (but may withdraw as usual.) 2. Governance may not add new Strategies. 3. Each Strategy must pay back their debt as quickly as reasonable to minimally affect their position. 4. Only Governance may undo Emergency Shutdown. See contract level note for further details. This may only be called by governance or the guardian.

| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _active | If true, the Vault goes into Emergency Shutdown. If false, the Vault goes back into Normal Operation. |
## setWithdrawalQueue(address[20])
Updates the withdrawalQueue to match the addresses and order specified by `_queue`. There can be fewer strategies than the maximum, as well as fewer than the total number of strategies active in the vault. `withdrawalQueue` will be updated in a gas-efficient manner, assuming the input is well- ordered with 0x0 only at the end. This may only be called by governance.

{% hint style="info" %} This is order sensitive, specify the addresses in the order in which funds should be withdrawn (so `_queue`[0] is the first Strategy withdrawn from, `_queue`[1] is the second, etc.) This means that the least impactful Strategy (the Strategy that will have its core positions impacted the least by having funds removed) should be at `_queue`[0], then the next least impactful at `_queue`[1], and so on. {% endhint %}
| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _queue | The array of addresses to use as the new withdrawal queue. This is order sensitive. |
## transfer(address,uint256)
Transfers shares from the caller's address to `_to`. This function will always return true, unless the user is attempting to transfer shares to this contract's address, or to 0x0.

| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _to | The address shares are being transferred to. Must not be this contract's address, must not be 0x0. |
| Input | 1 | | _value | The quantity of shares to transfer. |
| Output | 0 | | _0 | True if transfer is sent to an address other than this contract's or 0x0, otherwise the transaction will fail. |
## transferFrom(address,address,uint256)
Transfers `_value` shares from `_from` to `_to`. This operation will always return true, unless the user is attempting to transfer shares to this contract's address, or to 0x0. Unless the caller has given this contract unlimited approval, transfering shares will decrement the caller's `allowance` by `_value`.

| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _from | The address shares are being transferred from. |
| Input | 1 | | _to | The address shares are being transferred to. Must not be this contract's address, must not be 0x0. |
| Input | 2 | | _value | The quantity of shares to transfer. |
| Output | 0 | | _0 | True if transfer is sent to an address other than this contract's or 0x0, otherwise the transaction will fail. |
## approve(address,uint256)
{% hint style="info" %} Approve the passed address to spend the specified amount of tokens on behalf of `msg.sender`. Beware that changing an allowance with this method brings the risk that someone may use both the old and the new allowance by unfortunate transaction ordering. See https://github.com/ethereum/EIPs/issues/20#issuecomment-263524729 {% endhint %}
| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _spender | The address which will spend the funds. |
| Input | 1 | | _value | The amount of tokens to be spent. |
## increaseAllowance(address,uint256)
{% hint style="info" %} Increase the allowance of the passed address to spend the total amount of tokens on behalf of msg.sender. This method mitigates the risk that someone may use both the old and the new allowance by unfortunate transaction ordering. See https://github.com/ethereum/EIPs/issues/20#issuecomment-263524729 {% endhint %}
| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _spender | The address which will spend the funds. |
| Input | 1 | | _value | The amount of tokens to increase the allowance by. |
## decreaseAllowance(address,uint256)
{% hint style="info" %} Decrease the allowance of the passed address to spend the total amount of tokens on behalf of msg.sender. This method mitigates the risk that someone may use both the old and the new allowance by unfortunate transaction ordering. See https://github.com/ethereum/EIPs/issues/20#issuecomment-263524729 {% endhint %}
| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _spender | The address which will spend the funds. |
| Input | 1 | | _value | The amount of tokens to decrease the allowance by. |
## totalAssets()
Returns the total quantity of all assets under control of this Vault, whether they're loaned out to a Strategy, or currently held in the Vault.

| | | type | parameter | desc |
|---|---|---|---|---|
| Output | 0 | | _0 | The total assets under control of this Vault. |
## balanceSheetOfStrategy(address)
Provide an accurate estimate for the total amount of assets (principle + return) that `_strategy` is currently managing, denominated in terms of `_token`. This total is the total realizable value that could *actually* be obtained from this Strategy if it were to divest its entire position based on current on-chain conditions.

| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _strategy | The Strategy to estimate the realizable assets of. |
| Output | 0 | | _0 | An estimate of the total realizable assets in `_strategy`. |
## totalBalanceSheet(address[40])
Measure the total balance sheet of this Vault, using the list of strategies given above. (2x the expected maximum is used to ensure completeness.) NOTE: The safety of this function depends *entirely* on the list of strategies given as the function argument. Care should be taken to choose this list to ensure that the estimate is accurate. No additional checking is used. NOTE: Guardian should use this value vs. `totalAssets()` to determine if a condition exists where the Vault is experiencing a dangerous 'balance sheet' attack, leading Vault shares to be worth less than what their price on paper is (based on their debt)

| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _strategies | A list of strategies managed by this Vault, which will be included in the balance sheet calculation. |
| Output | 0 | | _0 | The total balance sheet of this Vault. |
## deposit()
Deposits `_amount` `token`, issuing shares to `_recipient`. If the Vault is in Emergency Shutdown, deposits will not be accepted and this call will fail.

{% hint style="info" %} Measuring quantity of shares to issues is based on the total outstanding debt that this contract has ("expected value") instead of the total balance sheet it has ("estimated value") has important security considerations, and is done intentionally. If this value were measured against external systems, it could be purposely manipulated by an attacker to withdraw more assets than they otherwise should be able to claim by redeeming their shares. On deposit, this means that shares are issued against the total amount that the deposited capital can be given in service of the debt that Strategies assume. If that number were to be lower than the "expected value" at some future point, depositing shares via this method could entitle the depositor to *less* than the deposited value once the "realized value" is updated from further reports by the Strategies to the Vaults. Care should be taken by integrators to account for this discrepancy, by using the view-only methods of this contract (both off-chain and on-chain) to determine if depositing into the Vault is a "good idea". {% endhint %}
| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _amount | The quantity of tokens to deposit, defaults to all. |
| Input | 1 | | _recipient | The address to issue the shares in this Vault to. Defaults to the caller's address. |
| Output | 0 | | _0 | The issued Vault shares. |
## deposit(uint256)
{% hint style="info" %} Measuring quantity of shares to issues is based on the total outstanding debt that this contract has ("expected value") instead of the total balance sheet it has ("estimated value") has important security considerations, and is done intentionally. If this value were measured against external systems, it could be purposely manipulated by an attacker to withdraw more assets than they otherwise should be able to claim by redeeming their shares. On deposit, this means that shares are issued against the total amount that the deposited capital can be given in service of the debt that Strategies assume. If that number were to be lower than the "expected value" at some future point, depositing shares via this method could entitle the depositor to *less* than the deposited value once the "realized value" is updated from further reports by the Strategies to the Vaults. Care should be taken by integrators to account for this discrepancy, by using the view-only methods of this contract (both off-chain and on-chain) to determine if depositing into the Vault is a "good idea". {% endhint %}
| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _amount | The quantity of tokens to deposit, defaults to all. |
| Input | 1 | | _recipient | The address to issue the shares in this Vault to. Defaults to the caller's address. |
| Output | 0 | | _0 | The issued Vault shares. |
## deposit(uint256,address)
{% hint style="info" %} Measuring quantity of shares to issues is based on the total outstanding debt that this contract has ("expected value") instead of the total balance sheet it has ("estimated value") has important security considerations, and is done intentionally. If this value were measured against external systems, it could be purposely manipulated by an attacker to withdraw more assets than they otherwise should be able to claim by redeeming their shares. On deposit, this means that shares are issued against the total amount that the deposited capital can be given in service of the debt that Strategies assume. If that number were to be lower than the "expected value" at some future point, depositing shares via this method could entitle the depositor to *less* than the deposited value once the "realized value" is updated from further reports by the Strategies to the Vaults. Care should be taken by integrators to account for this discrepancy, by using the view-only methods of this contract (both off-chain and on-chain) to determine if depositing into the Vault is a "good idea". {% endhint %}
| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _amount | The quantity of tokens to deposit, defaults to all. |
| Input | 1 | | _recipient | The address to issue the shares in this Vault to. Defaults to the caller's address. |
| Output | 0 | | _0 | The issued Vault shares. |
## maxAvailableShares()
Determines the total quantity of shares this Vault can provide, factoring in assets currently residing in the Vault, as well as those deployed to strategies.

{% hint style="info" %} Regarding how shares are calculated, see dev note on `deposit`. {% endhint %}
| | | type | parameter | desc |
|---|---|---|---|---|
| Output | 0 | | _0 | The total quantity of shares this Vault can provide. |
## withdraw()
Withdraws the calling account's tokens from this Vault, redeeming amount `_shares` for an appropriate amount of tokens. See note on `setWithdrawalQueue` for further details of withdrawal ordering and behavior.

{% hint style="info" %} Measuring the value of shares is based on the total outstanding debt that this contract has ("expected value") instead of the total balance sheet it has ("estimated value") has important security considerations, and is done intentionally. If this value were measured against external systems, it could be purposely manipulated by an attacker to withdraw more assets than they otherwise should be able to claim by redeeming their shares. On withdrawal, this means that shares are redeemed against the total amount that the deposited capital had "realized" since the point it was deposited, up until the point it was withdrawn. If that number were to be higher than the "expected value" at some future point, withdrawing shares via this method could entitle the depositor to *more* than the expected value once the "realized value" is updated from further reports by the Strategies to the Vaults. Under exceptional scenarios, this could cause earlier withdrawals to earn "more" of the underlying assets than Users might otherwise be entitled to, if the Vault's estimated value were otherwise measured through external means, accounting for whatever exceptional scenarios exist for the Vault (that aren't covered by the Vault's own design.) {% endhint %}
| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _shares | How many shares to redeem for tokens, defaults to all. |
| Input | 1 | | _recipient | The address to issue the shares in this Vault to. Defaults to the caller's address. |
| Output | 0 | | _0 | The quantity of tokens redeemable for `_shares`. |
## withdraw(uint256)
{% hint style="info" %} Measuring the value of shares is based on the total outstanding debt that this contract has ("expected value") instead of the total balance sheet it has ("estimated value") has important security considerations, and is done intentionally. If this value were measured against external systems, it could be purposely manipulated by an attacker to withdraw more assets than they otherwise should be able to claim by redeeming their shares. On withdrawal, this means that shares are redeemed against the total amount that the deposited capital had "realized" since the point it was deposited, up until the point it was withdrawn. If that number were to be higher than the "expected value" at some future point, withdrawing shares via this method could entitle the depositor to *more* than the expected value once the "realized value" is updated from further reports by the Strategies to the Vaults. Under exceptional scenarios, this could cause earlier withdrawals to earn "more" of the underlying assets than Users might otherwise be entitled to, if the Vault's estimated value were otherwise measured through external means, accounting for whatever exceptional scenarios exist for the Vault (that aren't covered by the Vault's own design.) {% endhint %}
| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _shares | How many shares to redeem for tokens, defaults to all. |
| Input | 1 | | _recipient | The address to issue the shares in this Vault to. Defaults to the caller's address. |
| Output | 0 | | _0 | The quantity of tokens redeemable for `_shares`. |
## withdraw(uint256,address)
{% hint style="info" %} Measuring the value of shares is based on the total outstanding debt that this contract has ("expected value") instead of the total balance sheet it has ("estimated value") has important security considerations, and is done intentionally. If this value were measured against external systems, it could be purposely manipulated by an attacker to withdraw more assets than they otherwise should be able to claim by redeeming their shares. On withdrawal, this means that shares are redeemed against the total amount that the deposited capital had "realized" since the point it was deposited, up until the point it was withdrawn. If that number were to be higher than the "expected value" at some future point, withdrawing shares via this method could entitle the depositor to *more* than the expected value once the "realized value" is updated from further reports by the Strategies to the Vaults. Under exceptional scenarios, this could cause earlier withdrawals to earn "more" of the underlying assets than Users might otherwise be entitled to, if the Vault's estimated value were otherwise measured through external means, accounting for whatever exceptional scenarios exist for the Vault (that aren't covered by the Vault's own design.) {% endhint %}
| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _shares | How many shares to redeem for tokens, defaults to all. |
| Input | 1 | | _recipient | The address to issue the shares in this Vault to. Defaults to the caller's address. |
| Output | 0 | | _0 | The quantity of tokens redeemable for `_shares`. |
## pricePerShare()
Gives the price for a single Vault share.

{% hint style="info" %} See dev note on `withdraw`. {% endhint %}
| | | type | parameter | desc |
|---|---|---|---|---|
| Output | 0 | | _0 | The value of a single share. |
## addStrategy(address,uint256,uint256,uint256)
Add a Strategy to the Vault. This may only be called by governance.

{% hint style="info" %} The Strategy will be appended to `withdrawalQueue`, call `setWithdrawalQueue` to change the order. {% endhint %}
| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _strategy | The address of the Strategy to add. |
| Input | 1 | | _debtLimit | The quantity of assets `_strategy` can manage. |
| Input | 2 | | _rateLimit | How many assets per block this Vault may deposit to or withdraw from `_strategy`. |
| Input | 3 | | _performanceFee | The fee the strategist will receive based on this Vault's performance. |
## updateStrategyDebtLimit(address,uint256)
Change the quantity of assets `_strategy` may manage. This may only be called by governance.

| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _strategy | The Strategy to update. |
| Input | 1 | | _debtLimit | The quantity of assets `_strategy` may now manage. |
## updateStrategyRateLimit(address,uint256)
Change the quantity assets per block this Vault may deposit to or withdraw from `_strategy`. This may only be called by governance.

| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _strategy | The Strategy to update. |
| Input | 1 | | _rateLimit | The quantity of assets `_strategy` may now manage. |
## updateStrategyPerformanceFee(address,uint256)
Change the fee the strategist will receive based on this Vault's performance. This may only be called by governance.

| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _strategy | The Strategy to update. |
| Input | 1 | | _performanceFee | The new fee the strategist will receive. |
## migrateStrategy(address,address)
Migrates a Strategy, including all assets from `_oldVersion` to `_newVersion`. This may only be called by governance.

{% hint style="info" %} Strategy must successfully migrate all capital and positions to new Strategy, or else this will upset the balance of the Vault. The new Strategy should be "empty" e.g. have no prior commitments to this Vault, otherwise it could have issues. {% endhint %}
| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _oldVersion | The existing Strategy to migrate from. |
| Input | 1 | | _newVersion | The new Strategy to migrate to. |
## revokeStrategy()
Revoke a Strategy, setting its debt limit to 0 and preventing any future deposits. This function should only be used in the scenario where the Strategy is being retired but no migration of the positions are possible, or in the extreme scenario that the Strategy needs to be put into "Emergency Exit" mode in order for it to exit as quickly as possible. The latter scenario could be for any reason that is considered "critical" that the Strategy exits its position as fast as possible, such as a sudden change in market conditions leading to losses, or an imminent failure in an external dependency. This may only be called by governance, the guardian, or the Strategy itself. Note that a Strategy will only revoke itself during emergency shutdown.

| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _strategy | The Strategy to revoke. |
## revokeStrategy(address)
| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _strategy | The Strategy to revoke. |
## addStrategyToQueue(address)
Adds `_strategy` to `withdrawalQueue`. This may only be called by governance.

{% hint style="info" %} The Strategy will be appended to `withdrawalQueue`, call `setWithdrawalQueue` to change the order. {% endhint %}
| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _strategy | The Strategy to add. |
## removeStrategyFromQueue(address)
Remove `_strategy` from `withdrawalQueue`. This may only be called by governance.

{% hint style="info" %} We don't do this with revokeStrategy because it should still be possible to withdraw from the Strategy if it's unwinding. {% endhint %}
| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _strategy | The Strategy to add. |
## debtOutstanding()
Determines if `_strategy` is past its debt limit and if any tokens should be withdrawn to the Vault.

| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _strategy | The Strategy to check. Defaults to the caller. |
| Output | 0 | | _0 | The quantity of tokens to withdraw. |
## debtOutstanding(address)
| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _strategy | The Strategy to check. Defaults to the caller. |
| Output | 0 | | _0 | The quantity of tokens to withdraw. |
## creditAvailable()
Amount of tokens in Vault a Strategy has access to as a credit line. This will check the Strategy's debt limit, as well as the tokens available in the Vault, and determine the maximum amount of tokens (if any) the Strategy may draw on. In the rare case the Vault is in emergency shutdown this will return 0.

| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _strategy | The Strategy to check. Defaults to caller. |
| Output | 0 | | _0 | The quantity of tokens available for the Strategy to draw on. |
## creditAvailable(address)
| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _strategy | The Strategy to check. Defaults to caller. |
| Output | 0 | | _0 | The quantity of tokens available for the Strategy to draw on. |
## expectedReturn()
Provide an accurate expected value for the return this `_strategy` would provide to the Vault the next time `report()` is called (since the last time it was called).

| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _strategy | The Strategy to determine the expected return for. Defaults to caller. |
| Output | 0 | | _0 | The anticipated amount `_strategy` should make on its investment since its last report. |
## expectedReturn(address)
| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _strategy | The Strategy to determine the expected return for. Defaults to caller. |
| Output | 0 | | _0 | The anticipated amount `_strategy` should make on its investment since its last report. |
## report(uint256)
Reports the amount of assets the calling Strategy has free (usually in terms of ROI). This may only be called by a Strategy managed by this Vault.

{% hint style="info" %} For approved strategies, this is the most efficient behavior. The Strategy reports back what it has free, then Vault "decides" whether to take some back or give it more. Note that the most it can take is `_return`, and the most it can give is all of the remaining reserves. Anything outside of those bounds is abnormal behavior. All approved strategies must have increased diligence around calling this function, as abnormal behavior could become catastrophic. {% endhint %}
| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _return | Amount Strategy has made on it's investment since its last report, and is free to be given back to Vault as earnings |
| Output | 0 | | _0 | Amount of debt outstanding (iff totalDebt > debtLimit). |
## sweep(address)
Removes tokens from this Vault that are not the type of token managed by this Vault. This may be used in case of accidentally sending the wrong kind of token to this Vault. Tokens will be sent to `governance`. This will fail if an attempt is made to sweep the tokens that this Vault manages. This may only be called by governance.

| | | type | parameter | desc |
|---|---|---|---|---|
| Input | 0 | | _token | The token to transfer out of this vault. |
