# CAP-xxxx: Functions

## Preamble

```text
CAP: xxxx
Title: Functions
Working Group:
    Owner: Leigh McCulloch <@leighmcculloch>
    Authors: Leigh McCulloch <@leighmcculloch>
    Consulted: TBD
Status: Draft
Created: 2020-02-03
Discussion: TBD
Protocol version: TBD
```

## Simple Summary

This proposal provides tools to encode programs in Stellar transactions, run programs on the Stellar network, and run the programs created by others on other accounts.

## Working Group

TODO.

## Motivation

TODO.

### Goals Alignment
This CAP is aligned with the following Stellar Network Goals:

TODO.

## Abstract

The proposal adds new control structure operations `BeginIfOp`, `EndIfOp`, `BeginWhileOp`, and `EndWhileOp`.

The proposal adds new operation `SetRegisterOp` for executing an expression supporting arithmetic, storing the result as transient state.

The proposal adds new operation `SetParameterOp` that stores a value, literal or otherwise, in a field of the next operation.

Transactions can be built containing logical programs by including the control structure, register, and parameter operations directly inside the operation list of a transaction.

The proposal adds a new type `Function`. A `Function` is a list of `Operation`s, input parameters, and output parameters, identified and referenced by their hash. The proposal adds a new ledger entry, `FunctionEntry`, that stores a `Function`. The proposal adds new operations `CreateFunctionOp`, `ExecuteFunctionOp`, and `DeleteFunctionOp`.

The proposal defines a new signer key type `SIGNER_KEY_TYPE_FUNCTION` that contains the hash of a function. An account that adds a function as a signer allows the function to execute operations authorized by the signers weight.

The execution of a function requires signing weight that meets any requirements of the operations within the function, and the accounts - either literal or parameterized - referenced by those operations.

An account may create a function for other accounts to use. An account may permit other accounts to execute functions that operate on their account by making the function a signer of their account.

## Specification

### XDR changes

This patch of XDR changes is based on the XDR files in tag/commit `v15.1.0` (`90b2780584c6390207bf09291212d606896ce9f8`) of [stellar-core].

```diff
```

### Semantics

#### 

## Design Rationale

## Protocol Upgrade Transition

### Backwards Incompatibilities

 
### Resource Utilization


## Test Cases

None yet.

## Implementation

None yet.

[stellar-core]: https://github.com/stellar/stellar-core
