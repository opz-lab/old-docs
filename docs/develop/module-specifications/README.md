# Terra Core modules <img src="/img/icon_core.svg" height="40px">

```{toctree}
:hidden:
spec-auth
spec-authz
spec-bank
spec-capability
spec-crisis
spec-distribution
spec-evidence
spec-feegrant
spec-governance
spec-mint
spec-params
spec-slashing
spec-staking
spec-wasm
```

The Terra Core is the official Golang reference implementation of the Terra protocol.
The Terra Core is built using the [Cosmos SDK](https://cosmos.network/sdk), which provides a robust framework for blockchains that run atop the [Tendermint](https://tendermint.com/) consensus protocol.

Before diving into the core modules, it may be useful to familiarize yourself with the [Cosmos](https://docs.cosmos.network/) and [Tendermint](https://docs.tendermint.com/master/tutorials/go.html) documentation. 

## How to use the Terra Core module specifications

Each module specification begins with a short description of the module's main function within the architecture of the system and an explanation of how it contributes to implementing Terra's features.

The body of each module specification provides a more detailed description of its main processes and algorithms alongside any concepts you might need to know. The body of each module specification also contains links to more granular information, such as specific state variables, message handlers, and other functions.

These specifications are not an exhaustive reference and are provided as a companion guide for users who need to work directly with the Terra Core codebase or understand it. Though all the important functions in each module are described, more trivial functions, such as getters and setters, are omitted for clarity. Module logic is also located in either the message handler or block transitions, such as begin-blocker and end-blocker.

The end of each module specification includes lists of various module parameters alongside their default values with a brief explanation of their purpose, associated events / tags, and errors issued by the module.

## Module architecture

The Terra Core is organized into the following individual modules that implement different parts of the Terra protocol. They are listed in the order in which they are initialized during genesis:

1. `genaccounts` - import & export genesis account
2. [`distribution`](spec-distribution.md): distribute rewards between validators and delegators
   - reward distribution
   - community pool
3. [`staking`](spec-staking.md): validators and Luna
4. [`auth`](spec-auth.md): ante handler
   - vesting accounts
5. [`bank`](spec-bank.md) - sending funds from account to account
6. [`slashing`](spec-slashing.md) - low-level Tendermint slashing (double-signing, etc)
7. [`gov`](spec-governance.md): on-chain governance
    - proposals
    - parameter updating
11. `crisis` - reports consensus failure state with proof to halt the chain
12. `genutil` - handles `gentx` commands
    - filter and handle `MsgCreateValidator` messages

### Inherited modules

Many of the modules in Terra Core inherit from the Cosmos SDK and are configured to work with Terra through customization in either genesis parameters or by augmenting their functionality with additional code.

## Block lifecycle

The following processes are executed during each block transition:

### Begin block

1\. Distribution: Issuance of rewards for the previous block.

2\. Slashing: Checking of infraction evidence or downtime of validators for double-signing and downtime penalties.

### Process messages

3\. Messages are routed to their appropriate modules and then processed by the appropriate message handlers.

### End block

4\. Crisis: Check all registered invariants and assert that they remain true.

5\. Governance: Remove inactive proposals, check active proposals whose voting periods have ended for passes, and run the registered proposal handler of the passed proposal.

6\. Staking: The new set of active validators is determined from the top 130 Luna stakers. Validators that lose their spot within the set start the unbonding process.

## Conventions

### Currency denominations

- Luna is the Terra protocol's native staking asset. Delegators earn mining rewards when they stake their Luna to an active validator. Luna is also used to make and vote on governance proposals.

The microunit ($\times 10^{-6}$) is the smallest atomic unit of Luna.

| Denomination | Micro-Unit | Code    | Value         |
| :----------- | :--------- | :------ | :------------ |
| Luna         | µLuna      | `uluna` | 0.000001 Luna |