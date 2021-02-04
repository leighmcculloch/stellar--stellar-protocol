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

This proposal provides tools to encode programs in Stellar transactions, run
programs on the Stellar network, and run the programs created by others on
other accounts.

## Working Group

TODO.

## Motivation

The Stellar network provides first-class support for many high-level features
such as trading, path payments, and two-part payments (claimable balances),
which in many financial applications removes the need for smart contracts.
However, a lack of smart contracts slows innovation. Experimental ideas must
be developed outside the chain, or if it is to occur on chain, significant
effort is required to validate new first-class functionality, implement and
test it. Adding support for logical programs creates space for
experimentation on chain. Successful and widely used experiments can still be
adopted as first-class features.

### Goals Alignment
This CAP is aligned with the following Stellar Network Goals:

- The Stellar Network should support participants innovating on liquidity
solutions.
- The Stellar Network should support participants developing new use cases.

## Abstract

The proposal adds new control structure operations `BeginIfOp`, `EndIfOp`,
`BeginWhileOp`, and `EndWhileOp`.

The proposal adds new operation `SetRegisterOp` for executing an expression
supporting arithmetic, storing the result as transient state.

The proposal adds new operation `SetParameterOp` that stores a value, literal
or otherwise, in a field of the next operation.

Transactions can be built containing logical programs by including the
control structure, register, and parameter operations directly inside the
operation list of a transaction.

The proposal adds a new type `Function`. A `Function` is a list of
`Operation`s, input parameters, and output parameters, identified and
referenced by their hash. The proposal adds a new ledger entry,
`FunctionEntry`, that stores a `Function`. The proposal adds new operations
`CreateFunctionOp`, `ExecuteFunctionOp`, and `DeleteFunctionOp`.

The proposal defines a new signer key type `SIGNER_KEY_TYPE_FUNCTION` that
contains the hash of a function. An account that adds a function as a signer
allows the function to execute operations authorized by the signers weight.

The execution of a function requires signing weight that meets any
requirements of the operations within the function, and the accounts - either
literal or parameterized - referenced by those operations.

An account may create a function for other accounts to use. An account may
permit other accounts to execute functions that operate on their account by
making the function a signer of their account.

The proposal leaves open the possibility for a scripting language to be
defined or adapted to compile to operations lists that could be defined as
functions.

## Specification

### XDR changes

This patch of XDR changes is based on the XDR files in tag/commit `v15.1.0` (`90b2780584c6390207bf09291212d606896ce9f8`) of [stellar-core].

```diff
```

### Semantics

#### If Condition Operations (`BeginIfOp`, `EndIfOp`)
#### While Loop Operations (`BeginWhileOp`, `EndWhileOp`)
#### Set Register Operation (`SetRegisterOp`)
#### Set Parameter Operation (`SetParameterOp`)
#### Function (`Function`)
#### Function Ledger Entry (`FunctionEntry`)

The `FunctionEntry` is assessed for reserve as `operations.size() *
baseReserve`.

#### Create Function Operation (`CreateFunctionOp`)

The `CreateFunctionOp` is assessed for fees as if the operation is replaced
by the number of operations in the function.

#### Execute Function Operation (`ExecuteFunctionOp`)

The `ExecuteFunctionOp` is assessed for fees as if the operation is replaced
by the number of operations in the function.

#### Delete Function Operation (`DeleteFunctionOp`)

## Design Rationale

### Exclusion of a Scripting Language

The proposal leaves open the possibility for a scripting language to be
defined or adapted to compile to operations lists that could be defined as
functions.

It is possible to add the constructs required to build logical programs by
adding new operations to the existing XDR structure, and any scripting
language can sit atop of these constructs. It is not imperative that the
scripting language be defined in this proposal as it can be layered on top.

## Protocol Upgrade Transition

### Backwards Incompatibilities

This proposal does not change the semantics of existing operations or ledger
entries and introduces no backward incompatibilities.

### Resource Utilization

This proposal may increase the number of operations that are generally
submitted to each ledger because a function may contain many operations,
making it trivial to submit transactions that execute many functions. The fee
structure proposed ensures that fees paid are proportional to the additional
operations.

## Test Cases

None yet.

## Implementation

None yet.

[stellar-core]: https://github.com/stellar/stellar-core
