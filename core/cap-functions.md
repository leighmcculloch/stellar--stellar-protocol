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

The proposal adds new operations that provide control structures that define at apply time which operations in a transaction are applied, how many times they are applied, and perform arithmetic.

The proposal adds a new ledger entry, `FunctionEntry`, that stores a `Function`. A `Function` is a list of `Operation`s, input parameters, and output parameters. The proposal adds new operations `CreateFunctionOp`, `ExecuteFunctionOp`, `DeleteFunctionOp`. Functions are referenced by their hash. The operations within a function may operate on a parameterized source account, or on a literal source account.

The proposal defines a new transaction envelope that extends the existing transaction envelope to include a list of `Function`s that may be referenced by operations in the transaction.

The proposal defines a new signer key type `SIGNER_KEY_TYPE_FUNCTION` for accounts that references a specific function is authorized.

Transactions can be built containing logical programs by using including the control structure and arithmetic operations directly inside the operation list of a transaction.

Transactions can be built containing functions. Transactions containing functions may execute them, or store them in the ledger for later execution.

Transactions can be built referencing functions stored in the ledger. Transactions referencing functions may execute them, or delete them.

The execution of a function requires signing weight that meets any requirements of the operations within the function, and the accounts - either literal or parameterized - referenced by those operations.

An account may create a function for other accounts to use. An account may permit other accounts to execute functions that operate on their account by making the function a signer of their account.

## Specification

### XDR changes

This patch of XDR changes is based on the XDR files in tag/commit `v15.1.0` (`90b2780584c6390207bf09291212d606896ce9f8`) of [stellar-core].

```diff
```

### Semantics


## Design Rationale

## Protocol Upgrade Transition

### Backwards Incompatibilities

 
### Resource Utilization


## Test Cases

None yet.

## Implementation

None yet.

[stellar-core]: https://github.com/stellar/stellar-core
