# xzyvsh-12025-11-05--1237--pst--learning-zig-through-tigerbeetle

grainorder: xzyvsh  
timestamp: 12025-11-05--1237--pst  
team: teamtravel12 (Pisces ♓ / XII. Upside-Down)

---

> **note:** we do not condone real-life tigerbeetles eating other insects,
> nor do we condone carnivorous tigers, big cats, or cats eating other
> animals. this document is about learning zig by reading the tigerbeetle
> codebase, a database and distributed systems project written in zig. the
> name "tigerbeetle" refers to the software project, not the insect species
> or any animal species.
>
> for more information on the vegan animal rights movement, please visit:
> https://www.youtube.com/watch?v=Zl_7mD4WZ5Q

---

## the intuition

learning a programming language requires reading code. reading code
requires understanding patterns. understanding patterns requires seeing
them in context. seeing them in context requires real examples.

this is not about reading documentation alone. it is about reading code
that works. code that teaches. code that demonstrates best practices.
code that shows how concepts fit together.

the tigerbeetle codebase is written in zig. it follows tiger style
principles. it demonstrates distributed systems concepts. it shows how
zig's features enable correctness, performance, and developer experience.
it teaches through its implementation.

## the problem space

you want to learn zig. you have no prerequisites. you have no external
dependencies. you need a path forward. you need examples that teach.

learning zig requires understanding:
- types and memory management
- error handling and explicit control flow
- comptime and compile-time computation
- structs, enums, and unions
- allocators and resource management
- testing and assertions
- standard library patterns

traditional learning paths require setup. they require dependencies. they
require context. they assume prior knowledge. they don't show how concepts
fit together in real systems.

the question becomes: what if we learned zig by reading tigerbeetle? what
if we started with simple concepts and built understanding incrementally?
what if we read code that demonstrates best practices? what if we learned
by seeing how experts write code?

## what is tigerbeetle

tigerbeetle is a distributed financial accounting database. it is written
in zig. it uses viewstamped replication for consensus. it uses static
memory allocation for performance. it uses explicit error handling for
correctness. it follows tiger style principles for developer experience.

tigerbeetle demonstrates:
- distributed systems: consensus, replication, fault tolerance
- performance: static memory allocation, zero-cost abstractions
- safety: explicit error handling, bounds checking, assertions
- style: tiger style principles, decomplected design, code that teaches

tiger style is tigerbeetle's coding style guide. it emphasizes safety,
performance, and developer experience. it describes explicit limits,
assertion-based verification, and code organization. it teaches through its
principles.

## the learning path

the learning path is incremental. start with documentation. understand the
architecture. then read code. build understanding gradually. read code
that demonstrates concepts. ask questions. explore. experiment.

### step one: read the documentation

start with tigerbeetle's internal documentation. the documentation explains
the architecture. it explains design decisions. it explains how components
fit together. it provides context for reading code.

read in this order:
1. `docs/internals/README.md` - overview of internal documentation
2. `docs/internals/ARCHITECTURE.md` - one-page technical intro (start here)
3. `docs/TIGER_STYLE.md` - coding style guide and philosophy
4. `docs/internals/HACKING.md` - building and testing the codebase
5. `docs/internals/vsr.md` - viewstamped replication protocol
6. `docs/internals/data_file.md` - data storage organization

these documents provide context. they explain why code is structured this
way. they explain design decisions. they explain how components interact.
they prepare you for reading code.

### step two: understand the structure

start by understanding the codebase structure. the tigerbeetle codebase is
organized into modules. each module has one clear responsibility. modules
are decomplected. dependencies flow in one direction.

key directories:
- `src/` - main source code
- `src/vsr/` - viewstamped replication protocol implementation
- `src/lsm/` - log-structured merge tree implementation
- `src/state_machine/` - state machine logic
- `src/stdx/` - standard library extensions
- `docs/` - documentation, including `TIGER_STYLE.md`

key files to start with:
- `src/tigerbeetle.zig` - data structures (account, transfer)
- `src/constants.zig` - configuration constants
- `src/stdx/stdx.zig` - utility functions and types
- `docs/TIGER_STYLE.md` - coding style guide

these files demonstrate basic zig concepts: structs, enums, constants,
modules. they show how code is organized. they show how types are
defined. they show how modules are structured.

### step two: understand types

read `src/tigerbeetle.zig`. this file defines the core data structures:
account, transfer, account flags, transfer flags. these structures
demonstrate zig's type system.

key concepts to learn:
- `extern struct` - structures with explicit layout (128 bytes for Account)
- `packed struct` - bit-packed structures for flags (AccountFlags, TransferFlags)
- `comptime` blocks - compile-time assertions verifying size and alignment
- `@sizeOf` and `@alignOf` - memory layout queries
- `pub fn` - public functions
- method syntax - functions on types (`Account.debits_exceed_credits()`)

example: the `Account` struct uses `extern struct` to ensure specific
memory layout (128 bytes, 16-byte alignment). it uses `comptime` blocks to
verify size and alignment at compile time. it uses method syntax for
operations like `debits_exceed_credits()`. the `AccountFlags` uses `packed
struct(u16)` to pack multiple boolean flags into a single 16-bit integer.

read the `CreateAccountResult` enum to see how error codes are organized.
notice how the enum values are ordered by precedence. notice how `comptime`
blocks verify enum properties.

### step three: understand error handling

read functions that return errors. zig uses error unions: `!Type` means
"either an error or Type". errors must be handled explicitly. errors
cannot be ignored.

key concepts to learn:
- error unions: `!Type` syntax
- error handling: `try`, `catch`, `if` statements
- error types: custom error types (like `CreateAccountResult`)
- `try` keyword - propagate errors
- `catch` keyword - handle errors
- `if` statements - check for errors

example: read `src/state_machine.zig` functions that apply operations. they
return error unions. they use `try` to propagate errors. they use explicit
error types like `CreateAccountResult` to indicate specific failure modes.
errors are values, not exceptions. errors are explicit, not implicit.

read `src/vsr/replica.zig` functions that handle messages. they use error
unions extensively. they handle errors explicitly. they never ignore errors.
this demonstrates tiger style principles: explicit error handling, no
silent failures.

### step four: understand static memory allocation

read how tigerbeetle uses static memory allocation. tigerbeetle allocates
all memory at startup. no memory is dynamically allocated after
initialization. this enables performance optimization and correctness.

key concepts to learn:
- static allocation - memory allocated at startup
- fixed-size buffers - bounded memory usage
- `StaticAllocator` - allocator that disables after initialization
- explicit bounds - fixed maximum sizes for all data structures
- no dynamic allocation - all memory pre-allocated

example: read `src/vsr/replica.zig`. notice the `static_allocator` field.
notice how it's used during initialization and then disabled. notice how
all buffers are fixed-size. notice how this enables performance
optimization and prevents use-after-free errors.

read `src/static_allocator.zig` to understand how static allocation works.
this demonstrates tiger style principles: static memory allocation,
explicit bounds, bounded by construction.

### step five: understand comptime

read code that uses comptime. zig has powerful compile-time computation.
types are first-class values. code can be generated at compile time.
abstractions can be zero-cost.

key concepts to learn:
- `comptime` keyword - compile-time execution
- `comptime` parameters - compile-time values
- type parameters - types as values
- `@TypeOf` - get type of value
- `@typeInfo` - inspect type information
- compile-time evaluation - `comptime` blocks
- generic types - functions that return types

example: read `src/vsr/replica.zig`. notice `ReplicaType` is a generic
function that takes compile-time parameters (`comptime StateMachine: type`,
`comptime MessageBus: type`, `comptime Storage: type`). it returns a type
customized for these parameters. this demonstrates zig's powerful type
system.

read `src/constants.zig`. notice `comptime` blocks that verify constants
at compile time. notice derived values computed at compile time. notice
compile-time assertions that catch bugs before execution.

### step six: understand testing

read test files. zig has built-in testing. tests are written alongside
code. tests use `std.testing`. assertions verify behavior.

key concepts to learn:
- `test` blocks - test functions
- `std.testing` - testing utilities
- `expectEqual` - value equality
- `expectEqualStrings` - string equality
- `expectError` - error checking
- assertions - `assert` statements
- fuzz tests - testing with random inputs

example: read `src/tigerbeetle.zig` tests. tests are written in `test`
blocks. tests use `try testing.expectEqual()` to verify values. tests use
`try testing.expectError()` to verify errors. tests are placed near the
code they test.

read `src/fuzz_tests.zig` to see fuzz testing patterns. read `src/vsr/`
fuzz test files (like `free_set_fuzz.zig`) to see how fuzz tests are
structured. this demonstrates tiger style principles: comprehensive
testing, property-based testing, deterministic testing.

### step seven: understand vsr (viewstamped replication)

read `src/vsr/replica.zig`. this file implements the viewstamped
replication protocol. it demonstrates distributed systems concepts. it
shows how zig enables correctness and performance.

first, read `docs/internals/vsr.md` to understand the protocol. then read
the code. this provides context for understanding the implementation.

key concepts to learn:
- generic types - `ReplicaType(comptime StateMachine, ...)`
- state machines - deterministic execution
- consensus protocols - quorum-based consensus
- message passing - explicit message types
- static allocation - fixed-size buffers
- view changes - handling primary failures
- quorums - majority voting

example: `ReplicaType` is a generic function that returns a type. it takes
compile-time parameters (`comptime StateMachine: type`, `comptime
MessageBus: type`, `comptime Storage: type`). it creates a replica type
customized for specific state machine and storage types. this demonstrates
zig's powerful type system.

read the `Status` enum to understand replica states (normal, view_change,
recovering, recovering_head). read the `ReplicaEvent` union to understand
events. read the `CommitStage` union to understand commit stages. this
shows how state is represented explicitly.

### step eight: understand state machine

read `src/state_machine.zig`. this file implements the state machine
logic. it demonstrates deterministic execution. it shows how operations
are applied to state.

key concepts to learn:
- deterministic execution - same inputs, same outputs
- operation application - applying operations to state
- state transitions - moving from one state to another
- validation - checking operation validity
- error handling - handling invalid operations
- lsm trees - log-structured merge trees for storage

example: `StateMachineType` is a generic function that returns a state
machine type. it takes compile-time configuration. it uses lsm trees for
storage. it applies operations deterministically. given the same initial
state and the same sequence of operations, it produces the same final state.
this enables replication and verification.

read the `tree_ids` struct to understand how data is indexed. read the
groove types (`AccountsGroove`, `TransfersGroove`) to understand how data
is stored. this demonstrates decomplection: each tree has one clear
responsibility.

### step nine: understand lsm (log-structured merge tree)

read `docs/internals/lsm.md` first to understand the storage system. then
read `src/lsm/tree.zig`. this file implements the log-structured merge
tree. it demonstrates storage organization. it shows how data is
structured on disk.

key concepts to learn:
- data structures - trees, levels, compaction
- storage organization - how data is stored
- performance optimization - batching, merging
- memory management - static allocation patterns
- functional data structures - immutable tree nodes

example: read `src/lsm/tree.zig` to see how trees are structured. read
`src/lsm/compaction.zig` to see how compaction works. read `src/lsm/forest.zig`
to see how multiple trees are organized. the lsm tree organizes data in
levels. data is merged during compaction. this creates efficient storage
and retrieval patterns.

notice how all memory is statically allocated. notice how bounds are
explicit. notice how assertions verify correctness. this demonstrates tiger
style principles: static memory allocation, explicit bounds, assertion-based
verification.

### step ten: understand testing patterns

read test files throughout the codebase. tests demonstrate how code is
verified. tests show expected behavior. tests teach through examples.

read `docs/internals/testing.md` to understand tigerbeetle's testing
approach. then read test files.

key concepts to learn:
- unit tests - testing individual functions
- integration tests - testing component interactions
- fuzz tests - testing with random inputs
- property tests - testing invariants
- deterministic tests - comparing outputs
- vopr - deterministic simulator for distributed systems testing

example: read `src/integration_tests.zig` to see integration tests. read
fuzz test files in `src/vsr/` and `src/lsm/` to see fuzz testing patterns.
read `src/vopr.zig` to understand the deterministic simulator. tests are
comprehensive. they test normal cases, edge cases, and error cases. they
verify invariants. they demonstrate expected behavior.

notice how tests use assertions extensively. notice how tests verify both
positive and negative cases. notice how tests are deterministic. this
demonstrates tiger style principles: comprehensive testing, assertion-based
verification, deterministic execution.

## reading strategies

reading code requires strategy. start with documentation. understand the
architecture. then read simple files. build understanding gradually. ask
questions. explore. experiment.

### strategy one: read documentation first

read documentation before reading code. `docs/internals/ARCHITECTURE.md`
explains the system. `docs/internals/vsr.md` explains the protocol.
`docs/internals/lsm.md` explains storage. `docs/TIGER_STYLE.md` explains
principles.

this documentation provides context. it explains why code is structured
this way. it explains design decisions. it explains how components interact.
it prepares you for reading code.

### strategy two: start simple

start with simple files. `src/tigerbeetle.zig` defines data structures.
`src/constants.zig` defines constants. `src/stdx/stdx.zig` defines
utilities. these files demonstrate basic concepts without complex logic.

read these files first. understand the types. understand the constants.
understand the utilities. build a foundation before reading complex code.

### strategy three: follow dependencies

when you read a file, follow its dependencies. if it imports `vsr`, read
`src/vsr.zig`. if it imports `state_machine`, read `src/state_machine.zig`.
if it imports `stdx`, read `src/stdx/stdx.zig`.

following dependencies builds understanding incrementally. you see how
modules fit together. you see how concepts build on each other. you see
the system as a whole.

but be careful: some files have many dependencies. start with files that
have few dependencies. build understanding gradually. don't try to understand
everything at once.

### strategy four: read with questions

read code with questions. what does this function do? why does it work this
way? what problem does it solve? what assumptions does it make? what
errors can occur? what invariants does it maintain?

asking questions deepens understanding. it reveals design decisions. it
shows trade-offs. it teaches through inquiry.

look for comments that explain why. tiger style emphasizes "always
motivate, always say why." comments explain rationale. they share criteria
for evaluation. they help you understand decisions.

### strategy five: read tests

read tests alongside code. tests show expected behavior. tests demonstrate
usage. tests verify correctness. tests teach through examples.

tests are often easier to understand than implementation. they show how
code is used. they show what code does. they show expected behavior.

read tests before reading implementation. tests describe what code should
do. implementation shows how it does it. understanding the "what" before
the "how" builds better understanding.

### strategy six: experiment

experiment with code. copy examples. modify them. run them. see what
happens. break things. fix them. learn through doing.

experimentation builds understanding. it reveals behavior. it shows
consequences. it teaches through practice.

start with simple experiments. modify constants. add assertions. change
behavior. see what breaks. understand why. this builds intuition.

## key zig concepts in tigerbeetle

tigerbeetle demonstrates many zig concepts. understanding these concepts
deepens understanding of zig. seeing them in context shows how they fit
together.

### explicit memory management

tigerbeetle uses explicit allocators. every allocation requires an
allocator. allocators are passed explicitly. memory management is visible.

this teaches explicit resource management. it shows how memory is
allocated and freed. it demonstrates zig's philosophy: explicit is better
than implicit.

### static memory allocation

tigerbeetle uses static memory allocation for performance. buffers are
fixed-size. memory is allocated at startup. no dynamic allocation during
operation.

this teaches performance optimization. it shows how explicit bounds enable
optimization. it demonstrates grain style principles: explicit limits,
bounded by construction.

### explicit error handling

tigerbeetle uses explicit error handling. errors are values, not
exceptions. errors must be handled explicitly. errors cannot be ignored.

this teaches explicit error handling. it shows how errors are represented
and handled. it demonstrates zig's philosophy: errors are part of the type
system.

### comptime computation

tigerbeetle uses comptime computation extensively. types are first-class
values. code is generated at compile time. abstractions are zero-cost.

this teaches compile-time computation. it shows how types can be
manipulated at compile time. it demonstrates zig's powerful type system.

### assertions

tigerbeetle uses assertions extensively. preconditions are asserted.
postconditions are asserted. invariants are asserted. assertions verify
correctness.

this teaches verification. it shows how assertions catch bugs. it
demonstrates tiger style principles: assertion-based verification, fail-fast.

### decomplection

tigerbeetle is decomplected. each module has one clear responsibility.
modules are independent. dependencies flow in one direction.

this teaches code organization. it shows how complex systems are built from
simple components. it demonstrates tiger style principles: decomplection,
separation of concerns.

## common patterns

tigerbeetle demonstrates common patterns. understanding these patterns
helps read code. recognizing patterns speeds understanding.

### pattern one: generic types

tigerbeetle uses generic types extensively. `ReplicaType` is a generic
function that returns a type. it takes compile-time parameters. it creates
customized types.

pattern: `pub fn TypeName(comptime Param: type) type { ... }`

this pattern creates reusable types. it enables type-safe code
generation. it demonstrates zig's powerful type system.

### pattern two: explicit allocators

tigerbeetle passes allocators explicitly. functions take `allocator:
Allocator` as a parameter. memory is allocated with `try
allocator.alloc()`. memory is freed with `defer` or explicit `free()`.

pattern: `pub fn function(allocator: Allocator, ...) !ReturnType { ... }`

this pattern makes memory management explicit. it enables custom allocators.
it demonstrates zig's philosophy: explicit is better than implicit.

### pattern three: error unions

tigerbeetle uses error unions extensively. functions return `!Type` to
indicate possible errors. errors are handled with `try` or `catch`.
errors are values, not exceptions.

pattern: `pub fn function(...) !ReturnType { ... }`

this pattern makes error handling explicit. it prevents silent failures.
it demonstrates zig's philosophy: errors are part of the type system.

### pattern four: assertions

tigerbeetle uses assertions extensively. preconditions are asserted.
postconditions are asserted. invariants are asserted. assertions verify
correctness.

pattern: `assert(condition);` or `comptime { assert(condition); }`

this pattern catches bugs early. it documents assumptions. it
demonstrates tiger style principles: assertion-based verification.

### pattern five: comptime blocks

tigerbeetle uses comptime blocks extensively. compile-time assertions
verify constants. compile-time computation generates code. compile-time
evaluation optimizes code.

pattern: `comptime { ... }`

this pattern enables compile-time verification. it enables code
generation. it demonstrates zig's powerful comptime system.

## reading specific files

some files are particularly educational. reading these files teaches
specific concepts. understanding these files builds understanding of zig.

### src/tigerbeetle.zig

this file defines core data structures: account, transfer, flags. it
demonstrates:
- `extern struct` - explicit memory layout (128 bytes, 16-byte alignment)
- `packed struct` - bit-packed structures for flags
- `comptime` blocks - compile-time assertions verifying size and alignment
- method syntax - functions on types (`Account.debits_exceed_credits()`)
- type safety - compile-time verification
- enum ordering - error codes ordered by precedence

read this file first. it's the simplest. it demonstrates basic zig
concepts. it shows how types are defined. it shows how memory layout is
controlled. it shows how compile-time verification works.

notice the `comptime` blocks that verify structure sizes. notice how
`AccountFlags` uses `packed struct(u16)` to pack multiple boolean flags.
notice how `CreateAccountResult` enum values are ordered by precedence.
notice how method syntax is used for operations.

### src/constants.zig

this file defines configuration constants. it demonstrates:
- constants - compile-time values
- `comptime` blocks - compile-time assertions verifying relationships
- derived values - values computed at compile time
- type system - compile-time type checking
- explicit bounds - fixed maximum sizes

read this file to understand compile-time constants and assertions.
notice how constants are verified at compile time. notice how derived
values are computed. notice how bounds are explicit and verified.

read the comments explaining why constants have specific values. notice
how `comptime` blocks verify invariants. notice how bounds are calculated
from other bounds. this demonstrates tiger style principles: explicit
limits, compile-time verification.

### src/stdx/stdx.zig

this file defines standard library extensions. it demonstrates:
- utility functions - helper functions (like `div_ceil`)
- type extensions - extending standard library
- `comptime` functions - compile-time functions
- testing - test-driven development
- `inline` functions - functions inlined at call site

read this file to understand utility patterns and testing. notice how
functions are simple and focused. notice how `comptime` is used for
compile-time verification. notice how tests are comprehensive.

read `src/stdx/` subdirectories to see specialized utilities:
- `bit_set.zig` - bit set data structure
- `bounded_array.zig` - fixed-size array type
- `ring_buffer.zig` - ring buffer implementation
- `prng.zig` - pseudo-random number generator

these show how data structures are implemented in zig. they demonstrate
static allocation, explicit bounds, and type safety.

### src/vsr/replica.zig

this file implements viewstamped replication. it demonstrates:
- generic types - type parameters (`ReplicaType(comptime StateMachine, ...)`)
- state machines - deterministic execution
- message passing - explicit message types
- error handling - explicit error types
- static allocation - fixed-size buffers
- assertions - extensive use of assertions
- explicit bounds - fixed maximum sizes for all data structures

read `docs/internals/vsr.md` first to understand the protocol. then read
this file. it's large (over 12,000 lines), so read it in sections.

start with the type definition. notice how `ReplicaType` is a generic
function that returns a type. notice how it takes compile-time parameters.
notice how the returned type has fields for all components (journal, state
machine, superblock, etc.).

read the status enum and replica event union. these show how state is
represented. read commit stage union to understand commit process. read
individual functions to see how operations are implemented.

this file demonstrates tiger style principles: static memory allocation,
explicit bounds, assertion-based verification, decomplection.

### src/state_machine.zig

this file implements state machine logic. it demonstrates:
- deterministic execution - same inputs, same outputs
- operation application - applying operations to state
- validation - checking operation validity
- error handling - handling invalid operations
- lsm trees - log-structured merge trees for storage
- generic types - `StateMachineType(comptime Storage, comptime config)`
- testing - comprehensive test coverage

read `docs/internals/data_file.md` first to understand data storage. then
read this file. it's large (over 5,000 lines), so read it in sections.

start with the type definition. notice how `StateMachineType` is a generic
function. notice how it uses lsm trees for storage. notice how operations
are applied deterministically.

read the `tree_ids` struct to understand indexing. read the groove types
to understand storage organization. read operation application functions
to see how operations are processed.

this file demonstrates tiger style principles: deterministic execution,
explicit error handling, static memory allocation, decomplection.

## learning exercises

learning requires practice. these exercises help build understanding. they
guide exploration. they reinforce concepts.

### exercise one: read and understand

read `src/tigerbeetle.zig`. understand the `Account` struct. understand the
`Transfer` struct. understand the flags. understand the methods.

questions to ask:
- what is the memory layout of `Account`? (128 bytes, 16-byte alignment)
- why is `extern struct` used? (explicit layout for disk storage and network)
- what do the flags represent? (account behavior constraints)
- how do the methods work? (check flags and compare values)
- why are error codes ordered by precedence? (deterministic error selection)

verify your understanding by reading the tests. see how accounts are
created and used. see how flags affect behavior. see how errors are
handled.

### exercise two: trace execution

read a simple function. trace its execution. understand each step. see how
it works. see what it does.

start with `Account.debits_exceed_credits()`. trace through the logic.
understand the condition. see how flags are checked. see how values are
compared.

read the test for this function. see what cases are tested. see what
edge cases are covered. understand why each test case exists.

then read a more complex function. trace through it step by step. see how
assertions verify correctness. see how errors are handled. see how state
is managed.

### exercise three: read tests comprehensively

read tests for a function. understand what they test. see how they verify
behavior. see what edge cases are covered. see how both positive and
negative cases are tested.

start with tests for `Account`. see how accounts are created. see how
flags are tested. see how methods are verified. see how error cases are
handled.

notice how tests verify both valid and invalid inputs. notice how tests
verify edge cases. notice how tests verify error conditions. this
demonstrates tiger style principles: comprehensive testing, positive and
negative space verification.

### exercise four: modify code

modify a simple function. add a method. change behavior. see what breaks.
fix it. learn through experimentation.

start with `Account`. add a method. see how it compiles. see how it works.
see how tests verify it.

but be careful: tigerbeetle uses static memory allocation. structures have
fixed sizes. modifying structures may break layout. always verify with
`comptime` blocks that sizes and alignments are correct.

experiment with assertions. add assertions to verify your understanding.
see when they fail. understand why. this builds intuition about invariants.

### exercise five: read documentation comprehensively

read `docs/TIGER_STYLE.md` thoroughly. understand the principles. see how
they apply to code. see how they guide design.

understand:
- safety, performance, developer experience (design goals)
- explicit limits and bounds (put a limit on everything)
- assertion-based verification (pair assertions, positive and negative space)
- decomplection and code organization (control plane and data plane)
- static memory allocation (no dynamic allocation after initialization)
- function size limits (70 lines per function)
- naming conventions (snake_case, descriptive names, units last)

then read `docs/internals/ARCHITECTURE.md` to understand the system design.
read `docs/internals/vsr.md` to understand the protocol. read
`docs/internals/lsm.md` to understand storage. these documents provide
context for understanding code.

## common questions

learning zig through tigerbeetle raises questions. these questions help
build understanding. answering them deepens knowledge.

### why extern struct?

`extern struct` ensures explicit memory layout. it guarantees structure
size and alignment. it enables interoperability. it ensures predictable
layout.

tigerbeetle uses `extern struct` for data structures that are stored on
disk or sent over the network. the `Account` struct is exactly 128 bytes
with 16-byte alignment. this ensures compatibility when reading from disk
or sending over the network. the `comptime` blocks verify this at compile
time.

read `src/tigerbeetle.zig` to see how `comptime` blocks verify structure
sizes and alignments. this catches layout bugs at compile time, not
runtime.

### why packed struct?

`packed struct` packs fields into bits. it saves memory. it enables
bit-level manipulation. it creates efficient flag storage.

tigerbeetle uses `packed struct` for flags. `AccountFlags` uses `packed
struct(u16)` to pack 6 boolean flags and 10 padding bits into a single
16-bit integer. `TransferFlags` uses `packed struct(u16)` to pack 9
boolean flags and 7 padding bits. this saves memory and enables efficient
flag manipulation.

read `src/tigerbeetle.zig` to see how flags are defined. notice how
`comptime` blocks verify the size matches the underlying integer type.
notice how flags can be set and checked individually.

### why comptime blocks?

`comptime` blocks are evaluated at compile time. they verify constants.
they generate code. they enable compile-time optimization.

tigerbeetle uses `comptime` blocks extensively. they verify structure
sizes and alignment. they verify enum properties. they verify constant
relationships. they generate code based on types. they enable compile-time
optimization.

read `src/constants.zig` to see how `comptime` blocks verify constant
relationships. read `src/tigerbeetle.zig` to see how `comptime` blocks
verify structure layouts. read `src/vsr/replica.zig` to see how `comptime`
blocks verify configuration.

this catches bugs at compile time, not runtime. it documents invariants.
it enables the compiler to optimize based on known values.

### why static memory allocation?

tigerbeetle uses static memory allocation. all memory is allocated at
startup. no memory is dynamically allocated after initialization. this
enables performance optimization and correctness.

read `src/vsr/replica.zig` to see the `static_allocator` field. notice how
it's used during initialization and then disabled. notice how all buffers
are fixed-size. notice how this prevents use-after-free errors. notice how
this enables performance optimization.

read `src/static_allocator.zig` to understand how static allocation works.
this demonstrates tiger style principles: static memory allocation,
explicit bounds, bounded by construction.

this is different from explicit allocators. static allocation means no
dynamic allocation after initialization. all memory is pre-allocated. all
buffers are fixed-size. this enables predictable performance and prevents
memory-related bugs.

### why error unions?

error unions make error handling explicit. errors are values, not
exceptions. errors must be handled. errors cannot be ignored.

tigerbeetle uses error unions extensively. every function that can fail
returns an error union. this prevents silent failures and enables
explicit error handling.

## batch processing: ~8,000 transactions per batch

tigerbeetle processes approximately 8,000 transactions per batch. this
design choice maximizes throughput and efficiency by optimizing compute
into byte organization and single-threaded execution.

### the mathematics of batching

the batch size is calculated from the maximum message body size and the
size of each transaction. each `Transfer` struct is exactly 128 bytes
with 16-byte alignment. the maximum message body size determines how many
transfers can fit in a single batch.

read `src/state_machine.zig` to see how `batch_max.create_transfers` is
calculated:

```zig
pub const batch_max = struct {
    pub const create_transfers: u32 = @max(
        operation_event_max(.create_transfers, message_body_size_max),
        operation_event_max(
            .deprecated_create_transfers_unbatched,
            message_body_size_max,
        ),
    );
};
```

the `operation_event_max` function divides `message_body_size_max` by the
size of each transfer (128 bytes). this yields approximately 8,000
transfers per batch, depending on the configured message size.

### single-threaded computing optimization

tigerbeetle is single-threaded by design. this eliminates thread
synchronization overhead, reduces context switching, and enables
predictable performance. batching amplifies this benefit: processing 8,000
transfers in one batch amortizes the cost of consensus, network
communication, and disk I/O across many operations.

read `docs/TIGER_STYLE.md` to understand the performance philosophy:
"let the CPU be a sprinter doing the 100m. be predictable. don't force the
CPU to zig zag and change lanes. give the CPU large enough chunks of work.
this comes back to batching."

the batch size is chosen to balance:
- **latency:** batches must be small enough to commit quickly
- **throughput:** batches must be large enough to amortize overhead
- **memory:** batches must fit within static memory allocation limits
- **network:** batches must fit within message size constraints

### byte organization and cache efficiency

transfers are organized as a contiguous array of 128-byte structs. this
enables:
- **cache efficiency:** sequential access patterns leverage CPU cache
- **vectorization:** the compiler can optimize loops over batches
- **zero-copy:** batches can be sent over the network without conversion

read `docs/internals/ARCHITECTURE.md` to see how batches flow through the
system: "each prepare is a batch of 8 thousand individual transfer objects."

this demonstrates tiger style principles: performance from design,
explicit bounds, and maximizing compute into byte organization.

## zero-copy: casting raw bytes to zig types

tigerbeetle uses zero-copy techniques to cast raw bytes over the wire
directly to zig type definitions, without relying on JSON parsers or
serialization libraries.

### the zero-copy technique

when messages arrive over the network, they are raw byte arrays. instead
of parsing JSON or deserializing with a library, tigerbeetle uses zig's
type system to cast bytes directly to struct types.

read `src/stdx/stdx.zig` to see the `bytes_as_slice` function:

```zig
pub fn bytes_as_slice(
    comptime precision: SizePrecision,
    comptime T: type,
    bytes: anytype,
) []T {
    switch (precision) {
        .exact => {
            assert(bytes.len % @sizeOf(T) == 0);
            return @alignCast(std.mem.bytesAsSlice(T, bytes));
        },
        .inexact => {
            const size = @divFloor(bytes.len, @sizeOf(T)) * @sizeOf(T);
            return @alignCast(std.mem.bytesAsSlice(T, bytes[0..size]));
        },
    }
}
```

this function uses `@alignCast` and `std.mem.bytesAsSlice` to safely cast
raw bytes to a slice of zig structs. the `@alignCast` ensures proper memory
alignment, which is required because zig structs have explicit alignment
requirements (like the 16-byte alignment for `Account` and `Transfer`).

### why zero-copy matters

zero-copy eliminates parsing overhead. there is no JSON parsing, no
deserialization library, no intermediate data structures. bytes arrive from
the network and are immediately usable as zig structs.

read `src/tigerbeetle/benchmark_load.zig` to see zero-copy in action:

```zig
const transfers = stdx.bytes_as_slice(
    .exact,
    tb.Transfer,
    &b.client_requests[client_index],
)[0..transfer_count];
```

this casts a raw byte buffer directly to a slice of `Transfer` structs.
the operation is safe because:
- structs use `extern struct` for explicit memory layout
- struct sizes are verified at compile time with `comptime` blocks
- alignment requirements are enforced with `@alignCast`

### the wire format

tigerbeetle's wire format is the in-memory representation of zig structs.
this means:
- **no parsing overhead:** bytes are directly usable
- **no serialization bugs:** the wire format matches the in-memory format
- **type safety:** zig's type system verifies correctness at compile time
- **performance:** zero-copy enables maximum throughput

read `src/tigerbeetle.zig` to see how `Account` and `Transfer` are defined
with `extern struct` to ensure explicit memory layout. this layout is
identical on disk, in memory, and over the wire.

this demonstrates tiger style principles: performance from design,
explicit memory layout, and zero-cost abstractions.

## minimal dependencies: zig standard library only

tigerbeetle adheres to a "zero dependencies" policy, relying solely on the
zig standard library. this design choice enhances security, simplifies
maintenance, and ensures predictable behavior.

### why minimal dependencies

tigerbeetle uses only the zig standard library. there are no external
libraries, no package managers, no dependency resolution. this creates:

- **security:** reduced attack surface, no supply chain vulnerabilities
- **simplicity:** no dependency management, no version conflicts
- **predictability:** behavior depends only on zig and the codebase
- **portability:** code compiles on any platform zig supports

read `build.zig` to see how tigerbeetle builds. notice there are no
external dependencies listed. the only requirement is the zig compiler and
standard library.

### what tigerbeetle provides itself

instead of external dependencies, tigerbeetle implements what it needs in
the `src/stdx/` directory. this includes:
- `bit_set.zig` - bit set data structure
- `bounded_array.zig` - fixed-size array type
- `ring_buffer.zig` - ring buffer implementation
- `prng.zig` - pseudo-random number generator
- `bytes_as_slice` - zero-copy casting utilities

these are not external dependencies. they are part of the tigerbeetle
codebase, written in zig, following tiger style principles.

read `src/stdx/stdx.zig` to see how these utilities are organized. notice
how they are simple, focused, and designed for tigerbeetle's specific
needs. they demonstrate zig patterns without external complexity.

### the trade-off

this approach requires implementing more code, but the benefits outweigh
the costs:
- **control:** tigerbeetle controls all code paths
- **optimization:** code can be optimized for tigerbeetle's specific needs
- **understanding:** all code is visible and understandable
- **reliability:** no dependency on external library maintenance

this demonstrates tiger style principles: simplicity through hard work,
zero technical debt, and code that teaches.

## the path forward

the path forward is patient. learning zig takes time. learning
distributed systems takes time. learning tigerbeetle takes time. the work
is worth doing because it teaches through practice.

the path forward is incremental. start with simple concepts. build
understanding gradually. read code that demonstrates concepts. ask
questions. explore. experiment.

the path forward is explicit. understand each concept. understand how
concepts fit together. understand why choices were made. this creates
understanding that lasts.

the path forward is practice. read code. understand code. modify code.
experiment. learn through doing. this creates understanding through
experience.

## the dream

the dream is understanding zig through practice. reading code that works.
seeing patterns in context. learning through examples. understanding
through inquiry.

the dream is understanding distributed systems through code. seeing
consensus protocols in implementation. understanding replication through
practice. learning through reading.

the dream is understanding tiger style through examples. seeing principles
in practice. understanding design through code. learning through
observation.

this is the intuition. this is the problem space. this is the path
forward. the work is worth doing. the work takes time. the work continues.

---

## references

- [grain style](../grain_style.md) — zig development guidelines for the grain
  network. this document describes development principles that complement
  tiger style and guide our own zig development.

- [tigerbeetle codebase](https://github.com/tigerbeetle/tigerbeetle) — the
  tigerbeetle database implementation. this codebase demonstrates advanced
  zig patterns, distributed systems concepts, and tiger style principles.

- [tiger style documentation](https://github.com/tigerbeetle/tigerbeetle/blob/main/docs/TIGER_STYLE.md) —
  tigerbeetle's coding style guide. this document describes the principles
  that guide tigerbeetle's implementation and can guide your own zig
  learning.

---

grainorder: xzyvsh  
timestamp: 12025-11-05--1237--pst  
author: kae3g (@risc.love, kj3x39)  
team: teamtravel12 (Pisces ♓ / XII. Upside-Down)

*now == next + 1* 🌾

