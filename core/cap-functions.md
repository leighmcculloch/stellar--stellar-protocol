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
be developed outside the Stellar network, or if it is to occur on network,
significant effort is required to validate new first-class functionality,
implement and test it, and once released is locked in indefinitely due to
backwards compatibility guarantees. Adding support for logical programs
creates space for experimentation on the Stellar network. Successful and
widely used experiments can still be adopted as first-class features.

### Goals Alignment
This CAP is aligned with the following Stellar network Goals:

- The Stellar network should support the development of new use cases.

## Abstract

The proposal adds a fixed sized stack to the transient state of the
transaction application process that may be pushed, popped, and randomly
read.

The proposal adds new operation `JumpIfOp` for skipping or repeating
operations.

The proposal adds new operation `SetRegisterOp` for setting a register with a
literal value, or a referenced value from a register, the current
transaction, or current ledger.

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

#### Registers

During execution of a transaction's operations a set of 16 64-bit registers
are available for setting.

#### Jump Operation (`JumpOp`)

The `BeginIfOp` and `EndIfOp` operations wrap a set of operations and cause
them only to be applied if the expression tree defined in `BeginIfOp`
resolves to true. All operations in the set are still validated regardless of
whether they are applied.

Each leaf in the expression tree is a right-hand operand, a left-hand side
operand, and an operator. An operand may be a literal value, may reference a
register, may reference a transaction field, or may reference a ledger entry
field in the current ledger. An operator may be a `==`, `!=`, `>`, `<`, `>=`,
`<=`, `contains`, or `notcontains`.

TODO: How are ledger entry and transaction fields referenced.
TODO: How are ledger entry and transaction fields that are arrays indexed.

#### While Loop Operations (`BeginWhileOp`, `EndWhileOp`)

The `BeginWhileOp` and `EndWhileOp` operations wrap a set of operations and
cause them to be applied repeatedly while the expression tree defined in
`BeginWhileOp` resolves to true. All operations in the set are still
validated regardless of whether they are applied.

The expression tree functions the same as `BeginIfOp`.

#### Set Register Operation (`SetRegisterOp`)

The `SetRegisterOp` operation sets one of 16 64-bit registers.

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
by the greater of, the number of operations executed by the function, or the
number of operations in the function.

#### Delete Function Operation (`DeleteFunctionOp`)

## Design Rationale

### Fees

Fees for `CreateFunctionOp` are based on the number of operations in the
function because the size of functions may be wildly different and a single
base fee for the operation is not equivalent to the effort required to
validate the function is valid.

Fees for `ExecuteFunctionOp` are based on the greater of operations executed
to account for while loops, or the number of operations in the function, to
account for the overhead of iterating over large functions that apply only a
small number of operations.

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

## Unknowns Requiring Further Research

- This proposal may change when signature verification of a transaction must
occur. If a fork in a function or logical program executed by a transaction
includes operations that do not have a signature authorizing, but that fork
is not executed, the validator must know to skip the requirement for it to be
authorized.

## Known Limitations
- This proposal makes defining expressions arduous, especially referencing
ledger entries, reading and copying values. This would be addressed by a
scripting language built atop.

## Test Cases

None yet.

## Implementation

None yet.

[stellar-core]: https://github.com/stellar/stellar-core
