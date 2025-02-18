# Auth

:::{Important}
Terra's auth module inherits from Cosmos SDK's [`auth`](https://docs.cosmos.network/master/modules/auth/) module. This document is a stub and covers mainly important Terra-specific notes about how it is used.
:::

Terra's Auth module extends the functionality from Cosmos SDK's `auth` module with a modified ante handler, which applies basic transaction validity checks, such as signatures, nonces, and auxiliary fields. This module also defines a special vesting account type that handles the logic for token vesting from the Luna presale.

## Gas Fee

Like all transactions on the Terra blockchain, [`MsgSend`](spec-bank.md#msgsend) and [`MsgMultiSend`](spec-bank.md#msgmultisend) incur gas fees. These fees are determined by a validator's minimum gas price and the complexity of the transaction. More complex transactions incur higher fees. Gas fees are specified by the sender when a transaction is outbound. For more information on how gas is calculated, see [fees](../terrad/using-terrad.md#fees).

## Parameters

The subspace for the Auth module is `auth`.

```go
type Params struct {
  MaxMemoCharacters      uint64 `json:"max_memo_characters" yaml:"max_memo_characters"`
  TxSigLimit             uint64 `json:"tx_sig_limit" yaml:"tx_sig_limit"`
  TxSizeCostPerByte      uint64 `json:"tx_size_cost_per_byte" yaml:"tx_size_cost_per_byte"`
  SigVerifyCostED25519   uint64 `json:"sig_verify_cost_ed25519" yaml:"sig_verify_cost_ed25519"`
  SigVerifyCostSecp256k1 uint64 `json:"sig_verify_cost_secp256k1" yaml:"sig_verify_cost_secp256k1"`
}
```

### Genesis parameters

The genesis parameters outlined in the [Genesis Builder Script](https://github.com/terra-money/genesis-tools/blob/6ec96530820f7055cce6dd767a1d8c321d702fab/src/genesis_builder.py#L75) are as follows:

``` py
    # Auth: set max memo characters to 512
    genesis['app_state']['auth']['params']['max_memo_characters'] = '512'
```

### MaxMemoCharacters

The maximum permitted number of characters in the memo of a transaction.

- type: `uint64`
- genesis: `512`

### TxSigLimit

The maximum number of signers in a transaction. A single transaction can have multiple messages and multiple signers.

- type: `uint64`
- default: `7`

### TxSizeCostPerByte

The cost per byte used to compute the gas consumption of a transaction. `TxSizeCostPerByte * txsize`.

- type: `uint64`
- default: `10`

### SigVerifyCostED25519

The gas cost for verifying ED25519 signatures.

- type: `uint64`
- default: `590`

### SigVerifyCostSecp256k1

The gas cost for verifying Secp256k1 signatures.

- type: `uint64`
- default: `1000`
