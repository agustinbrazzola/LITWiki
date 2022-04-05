# LITDAO Utils

## Contract Overview

### vLIT

vLIT contract is a util for the staking mechanism. 

It creates the $vLIT token which acts as a receipt of the $LIT staked, so the smart contract can keep track of the amount each user staked. 

When the user wants to withdraw the tokens the smart contract will request the equivalent amount of vLIT that the user wants to exchange for $LIT or LP tokens.

### vLITBurner

vLITBurner is a util for the staking mechanism as well.

It's the tool that helps the minter/owner of the contract in the case that supply of vLIT needs to be reduced.

