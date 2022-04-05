# LITDAO

## LITStaking

In this section we will describe the first dAPP created by LIT. Called LITStaking and the core component for LITDAO.

`Solidity version: 0.8.4`

### Libraries used

- OpenZeppelin: IERC20
- OpenZeppelin: SafeERC20
- OpenZeppelin: Ownable

### Storage variables

***Structs***

`UserInfo: amount(uint256), rewardDebt(uint256)`

This stores deposits on the blockchain. As you can see this struct only calculates values. We will see later how it tracks who made those deposits.

`PoolInfo: lpToken(IERC20), allocPoint(uint256), lastRewardBlock(uint256), accVLitPerShare(uint256)`

This stores the allowed tokens to deposit and receive rewards. Have in mind that we can have several LPs of $LIT. LIT/ETH, LIT/WETH, LIT/USDC, etc. 

***Variables***

`IERC20` - `_VLIT`: The token address for vLIT.

`uint256` - `vLITPerBlock`: You need to storage how many vLIT will be created per block.

`array` - `PoolInfo[]`: This is an `array`. Think of it as a list of the allowed LPs to stake LIT. We saw that there's a struct with this info, think of that struct as a template to fill 

the info inside this list.

`mapping` - `userInfo`: As you can see, we have a `UserInfo` struct. Solidity is case sensitive so these are two different variables. The struct is the template which interacts with this mapping. A mapping is a key-value variable. Key-Values are used since the beginning of Databases, where if I give you a key you will return a certain value. In this 

case, this is a nested mapping. So it will need two keys to return the expected value. 

The first one is the `poolId` of the staked asset. The second one is the user `address`. After inputing those two keys, the smart contract returns the info stored in the mapping, 

with the structure of the struct.

`uint256` - `totalAllocPoint`: Variable needed to calculate rewards.

`uint256` - `startBlock`: The block where vLIT starts to be minted.

## Events

Events are a form of alert that smart contracts have. They allow frontend enviroments to listen to the blockchain in an efficient way and they are also a cheaper form of storage. 

The way they work is indexing data that was previously set.

We have three events here:

- `Deposit`
- `Withdraw`
- `EmergencyWithdraw` - More about this one below

## Functions

Functions are the actions that can be called from users or other smart contracts. If you see the `onlyOwner` tag that means it can only be executed by the owner of the contract. 

In this case litwtf: Deployer 

`add: params(_allocPoint uint256, _lpToken IERC20, _withUpdate bool) onlyOwner`

This function allows a certain LP to be staked. It also sets the share of rewards the pool has.

`set: params(_pid, _allocPoint, _withUpdate bool) onlyOwner`

This function allows the LP and its allocation points to be updated by the owner.

`pendingVLIT: params(_pid uint256, _user address)` 

This is a `getter`. A `getter` is a type of action that returns a value but doesn't perform any changes. It allows the frontend to see how many rewards that user has pending.

`massUpdatePools`: 

As you can see this function does not require any parameters. Is used as an inside function on the `set` function, to update all the pools together. This function is gas sensitive 

due it iterates through all the pools, and that requires a lot of computation. It also uses `updatePools` as an inside function. It doesn't affect users.

`updatePool: params(_pid uint256)`

This updates a specific pool. Same as massupdate pools but for a specific pool. It applys logic and safeMath to calculate rewards.

`deposit: params(_pid uint256, _amount256)`

This is the cashier. It will use all the variables described before so it can keep track of your funds safely. It emits the `Deposit` event so you receive your confirmation on the 

frontend.

`Withdraw: params(_pid uint256, _amount uint256)`

Same as deposit but backwards. The only difference is that it adds rewards to the calculation. It emits the `Withdraw` event to the frontend confirms you received your funds 
safely.

`emergencyWithdraw: params(_pid uint256)`

This function is only for emergencies. It will withdraw all the tokens the user deposited on the pool without caring about rewards. This is very useful and safe in case of an 

attack. Unfortunately, due to malicious actors, these functions are needed. 

`safeVLitTransfer` - `internal`

This function is internal. It can only be called by the smart contract and it's used to transfer vLIT.

`RewardPerBlockSet onlyOwner: params(amount uint256)`

This is the function that updates the `vLITperBlock` variable.