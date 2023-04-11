# Gas and Fees

This document describes how Greenfield charge fee to different transaction types.

## Introduction to `Gas` and `Fees`

In the Cosmos SDK, `gas` is a special unit that is used to track the consumption of resources during execution. 

However, on application-specific blockchains like Greenfield, the primary factor determining the transaction fee 
is no longer the computational cost of storage, but rather the incentive mechanism of Greenfield. For example, 
creating and deleting a storage object consumes similar I/O and computational resources, but Greenfield 
incentives users to delete unused storage objects to free up more storage space, resulting in much cheaper 
transaction fees for the latter.

Therefore, we abandoned the gas meter design of cosmos-sdk and redesigned the gashub module to determine the gas 
consumption based on the type and content of the transaction, rather than the consumption of storage and computational resources.


## GasHub
All transaction types need to register their gas calculation logic to gashub. Currently, four types of calculation logic 
are supported:

```go
type MsgGasParams_FixedType struct {
	FixedType *MsgGasParams_FixedGasParams 
}
type MsgGasParams_GrantType struct {
	GrantType *MsgGasParams_DynamicGasParams 
}
type MsgGasParams_MultiSendType struct {
	MultiSendType *MsgGasParams_DynamicGasParams 
}
type MsgGasParams_GrantAllowanceType struct {
	GrantAllowanceType *MsgGasParams_DynamicGasParams 
}
```

### Block Gas Meter

`ctx.BlockGasMeter()` is the gas meter used to track gas consumption per block and make sure it does not go above a certain limit. 

However, Greenfield may charge a substantial fee for certain types of transactions, resulting in significant gas 
consumption. As a result, Greenfield does not impose any limitations on the gas usage of a block. Greenfield limits the 
block size to under 1MB in order to prevent excessively large blocks.

## Fee Table

Please note that the following information can be updated at any time and may not be immediately reflected in the 
documentation.

| Msg Type                                    | Gas Used           | Gas Price | Expected Fee(assuming BNB $300) |
|---------------------------------------------|--------------------|-----------|---------------------------------|
| authz.MsgExec                               | 1.20E+03           | 5 gwei    | $0.0018                         |
| authz.MsgRevoke                             | 1.20E+03           | 5 gwei    | $0.0018                         |
| bank.MsgSend                                | 1.20E+03           | 5 gwei    | $0.0018                         |
| distribution.MsgSetWithdrawAddress          | 1.20E+03           | 5 gwei    | $0.0018                         |
| distribution.MsgWithdrawDelegatorReward     | 1.20E+03           | 5 gwei    | $0.0018                         |
| distribution.MsgWithdrawValidatorCommission | 1.20E+03           | 5 gwei    | $0.0018                         |
| feegrant.MsgRevokeAllowance                 | 1.20E+03           | 5 gwei    | $0.0018                         |
| gov.MsgDeposit                              | 1.20E+03           | 5 gwei    | $0.0018                         |
| gov.MsgSubmitProposal                       | 2.00E+08           | 5 gwei    | $300                            |
| gov.MsgVote                                 | 2.00E+07           | 5 gwei    | $30                             |
| gov.MsgVoteWeighted                         | 2.00E+07           | 5 gwei    | $30                             |
| oracle.MsgClaim                             | 1.00E+03           | 5 gwei    | $0.0015                         |
| slashing.MsgUnjail                          | 1.20E+03           | 5 gwei    | $0.0018                         |
| staking.MsgBeginRedelegate                  | 1.20E+03           | 5 gwei    | $0.0018                         |
| staking.MsgCancelUnbondingDelegation        | 1.20E+03           | 5 gwei    | $0.0018                         |
| staking.MsgCreateValidator                  | 2.00E+08           | 5 gwei    | $300                            |
| staking.MsgDelegate                         | 1.20E+03           | 5 gwei    | $0.0018                         |
| staking.MsgEditValidator                    | 2.00E+07           | 5 gwei    | $30                             |
| staking.MsgUndelegate                       | 1.20E+03           | 5 gwei    | $0.0018                         |
| bridge.MsgTransferOut                       | 1.20E+03           | 5 gwei    | $0.0018                         |
| sp.MsgDeposit                               | 1.20E+03           | 5 gwei    | $0.0018                         |
| sp.MsgEditStorageProvider                   | 2.00E+07           | 5 gwei    | $30                             |
| staking.MsgCreateStorageProvider            | 2.00E+08           | 5 gwei    | $300                            |
| storage.MsgCopyObject                       | 1.20E+03           | 5 gwei    | $0.0018                         |
| storage.MsgDeleteObject                     | 1.20E+03           | 5 gwei    | $0.0018                         |
| storage.MsgCreateBucket                     | 2.40E+03           | 5 gwei    | $0.0036                         |
| storage.MsgCreateGroup                      | 2.40E+03           | 5 gwei    | $0.0036                         |
| storage.MsgCreateObject                     | 1.20E+03           | 5 gwei    | $0.0018                         |
| storage.MsgDeleteBucket                     | 1.20E+03           | 5 gwei    | $0.0018                         |
| storage.MsgDeleteGroup                      | 1.20E+03           | 5 gwei    | $0.0018                         |
| storage.MsgLeaveGroup                       | 1.20E+03           | 5 gwei    | $0.0018                         |
| storage.MsgRejectSealObject                 | 1.20E+04           | 5 gwei    | $0.018                          |
| storage.MsgSealObject                       | 1.20E+02           | 5 gwei    | $0.00018                        |
| storage.MsgUpdateGroupMember                | 1.20E+03           | 5 gwei    | $0.0018                         |
| storage.MsgCreatePaymentAccount             | 2.00E+05           | 5 gwei    | $0.3                            |
| storage.MsgPutPolicy                        | 2.40E+03           | 5 gwei    | $0.0036                         |
| storage.MsgDeletePolicy                     | 1.20E+03           | 5 gwei    | $0.0018                         |
| storage.MsgWithdraw                         | 1.20E+03           | 5 gwei    | $0.0018                         |
| storage.MsgDisableRefund                    | 1.20E+03           | 5 gwei    | $0.0018                         |
| authz.MsgGrant                              | 8e2 + 8e2 per item | 5 gwei    | $0.0012 per item                |
| bank.MsgMultiSend                           | 8e2 + 8e2 per item | 5 gwei    | $0.0012 per item                |
| feegrant.MsgGrantAllowance                  | 8e2 + 8e2 per item | 5 gwei    | $0.0012 per item                |
