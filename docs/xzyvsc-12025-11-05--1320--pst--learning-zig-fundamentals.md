# xzyvsc-12025-11-05--1320--pst--learning-zig-fundamentals

grainorder: xzyvsc  
timestamp: 12025-11-05--1320--pst  
team: teamtravel12 (Pisces ♓ / XII. Upside-Down)

---

> **note:** we do not condone real-life tigerbeetles eating other insects,
> nor do we condone carnivorous tigers, big cats, or cats eating other
> animals. this document is about learning zig fundamentals through
> understanding the tigerbeetle project, a database and distributed systems
> project written in zig. the name "tigerbeetle" refers to the software
> project, not the insect species or any animal species.
>
> for more information on the vegan animal rights movement, please visit:
> https://www.youtube.com/watch?v=Zl_7mD4WZ5Q

---

## the intuition

before diving into tigerbeetle's codebase, before understanding how zig
enables distributed systems, before seeing how experts write zig code,
there are fundamentals. what is zig? why does it exist? what problems
does it solve? how does it think about systems programming?

this is not about syntax alone. it is about philosophy. it is about
design decisions. it is about understanding why zig makes the choices it
makes. it is about seeing how zig's constraints create simplicity.

zig is more than a language. it is a philosophy of systems programming.
it is a return to explicitness. it is a rejection of hidden complexity.
it is a belief that simplicity comes from subtractive discipline, not
from adding features.

## the problem space

systems programming languages face fundamental tensions. safety versus
control. expressiveness versus simplicity. performance versus guarantees.
each language makes different trade-offs.

traditional systems languages hide complexity. they allocate memory
implicitly. they hide control flow. they provide abstractions that leak.
they make assumptions about what you want. they optimize for the common
case, sometimes at the expense of the edge case.

the question becomes: what if we made everything explicit? what if we
removed hidden behavior? what if we treated safety as a spectrum, not as
a binary? what if we designed for simplicity through subtraction?

## why zig: a better c

zig positions itself as a better c. it fixes c's problems while
preserving c's power. it adds safety without removing control. it
enables modern practices without hiding complexity.

zig fixes c's small cuts: explicit alignment in the type system for
direct I/O, safer casts, better error handling. but it also fixes c's
showstoppers: spatial memory safety through bounds checking, and temporal
memory safety through debug allocator.

read [why zig](https://tigerbeetle.com/blog/2025-10-25-synadia-and-tigerbeetle-pledge-512k-to-the-zig-software-foundation/)
to understand tigerbeetle's choice. zig was chosen over rust and c
because it provides explicit control with safety checks, not guarantees.
it treats safety as a spectrum, not as an on/off decision.

zig's philosophy resonates with systems programming fundamentals. it
enables writing code that is correct by design, not just safe by
language. it forces explicit decisions about memory, errors, and control
flow.

---

## part one: zig language fundamentals

### explicitness

zig makes everything explicit. memory allocation is explicit. control
flow is explicit. error handling is explicit. there are no hidden
behaviors. there are no implicit assumptions.

#### no hidden allocations

zig makes memory allocation explicit. there are no hidden allocations.
every allocation requires an allocator. allocators are passed explicitly.
memory management is visible.

this explicitness creates understanding. when you see a function that
takes an allocator, you know it allocates memory. when you see a function
that doesn't, you know it doesn't. this makes code easier to reason
about.

modern systems languages can provide safety without hiding complexity.
zig's explicit allocators are one example of this.

zig has no blessed global allocator. this leads to two flavors of
containers: "managed" containers accept an allocator at construction and
store it as a field. "unmanaged" containers require an allocator to be
passed to every method call. this call-site dependency injection pattern
allows for greater flexibility and frugality in resource usage. a single
allocator reference can be shared across multiple containers, reducing
memory overhead and enabling explicit resource management.

zig's `std.heap.DebugAllocator` can be initialized with default values and
used to manage memory safely. this enables debugging memory issues while
maintaining explicit allocation boundaries. only the operations that
require allocation need an allocator parameter, making allocation sites
immediately obvious in the code.

the question is not whether to allocate memory. the question is where
and when. explicit allocators force us to think about memory lifetime.
they force us to plan memory usage. they create systems that are
predictable.

#### no hidden control flow

zig makes control flow explicit. there are no exceptions. there are no
hidden returns. there is no preprocessor. there are no macros that hide
code generation.

this explicitness creates clarity. when you read code, you see what it
does. there are no surprises. there are no hidden paths. everything is
visible.

watch this [boundaries](https://www.destroyallsoftware.com/talks/boundaries) talk
to understand how explicit control flow creates clear boundaries. zig's
explicit error handling is one example of this.

the question is not whether to handle errors. the question is how to
handle them explicitly. zig's error unions make error handling visible.
they force us to think about failure modes. they create systems that fail
gracefully.

#### explicit error handling

zig uses error unions: `!Type` means "either an error or Type". errors
are values, not exceptions. errors must be handled explicitly. errors
cannot be ignored.

this explicitness creates safety. when you see `!void`, you know the
function can fail. when you see `try`, you know an error is being
propagated. when you see `catch`, you know an error is being handled.

read [the error model](http://joeduffyblog.com/2016/02/07/the-error-model/)
to understand different approaches to error handling. zig's error unions
are explicit and intentional. they make failure modes visible.

the question is not whether errors will occur. the question is how we
handle them explicitly. zig's error unions force us to think about
errors. they force us to decide how to handle each failure mode. they
create systems that fail gracefully.

### language invariants

zig maintains key invariants that simplify the language. these invariants
reduce complexity by eliminating special cases and unifying concepts.

#### everything is an expression

zig maintains a key invariant: everything is an expression. there are no
separate syntactic categories for values, types, and patterns. this
simplifies the grammar and avoids combinatorial explosion. the language
does not need distinct syntax for different categories of constructs.

this uniformity creates mechanical simplicity. the same syntax works for
values, types, and patterns. there is no need to remember different rules
for different contexts. the language is smaller because it does not
duplicate concepts across categories.

zig eliminates namespaces. only one kind of identifier can exist in scope
at a time. this constraint prevents ambiguity. it forces explicit naming.
it creates code that is easier to search and understand. field writes are
easily searchable because the syntax is uniform.

the question is not whether to have separate categories. the question is
how to unify them. zig's expression-based design eliminates syntactic
duplication. it creates a smaller, more uniform language.

### compile-time computation

zig's `comptime` enables compile-time code execution. types are
first-class values. code can be generated at compile time. abstractions
can be zero-cost.

`comptime` reduces language complexity while multiplying power. it
enables generic programming without type parameters. it enables code
generation without macros. it enables optimizations without runtime
overhead.

read [what is the minimal set of optimizations needed for zero-cost
abstraction?](https://robert.ocallahan.org/2020/08/what-is-minimal-set-of-optimizations.html)
to understand how compile-time computation enables zero-cost
abstractions. zig's `comptime` is one example of this.

the question is not whether to use generics. the question is how to
implement them. `comptime` enables generics without language complexity.
it enables code generation without runtime cost. it enables abstractions
that are truly zero-cost.

`comptime` capabilities allow for powerful compile-time computations.
database operations can be generated based on type information. only the
operations that require allocation need an allocator parameter, making
allocation boundaries clear. this is supported by zig's `std.heap.DebugAllocator`,
which can be initialized with default values and used to manage memory safely.

### safety and correctness

zig treats safety as a spectrum, not as a binary. it provides safety
checks by default, with explicit ways to opt out when needed. it enables
correctness through design, not just through language features.

#### safety as a spectrum

zig treats safety as a spectrum, not as a binary. it doesn't aim for
100% guarantees across 1 or 2 categories. it aims for 90% across more
categories. it doesn't eliminate classes of bugs. it downgrades their
probability.

this approach preserves zig's power-to-weight ratio. it keeps the
language simple. it enables writing code that is safe enough for most
cases, with explicit escape hatches when needed.

read [composition of unsafe](https://smallcultfollowing.com/babysteps/blog/2016/10/02/observational-equivalence-and-unsafe-code/)
to understand how safety can be a spectrum. zig's approach is similar:
safety checks by default, with explicit ways to opt out when needed.

the question is not whether to be safe. the question is how to be safe
enough. zig's spectrum approach enables writing safe code most of the
time, with explicit control when needed.

#### checked arithmetic

zig enables checked arithmetic by default in safe builds. integer
overflow is detected and handled. this prevents a common class of bugs
that can cause security vulnerabilities and correctness failures.

this is remarkable. most languages disable checked arithmetic by default.
rust disables it in safe builds. c never had it. zig makes it the
default because correctness matters.

read [what every c programmer should know about undefined
behavior](https://blog.llvm.org/2011/05/what-every-c-programmer-should-know.html)
to understand how undefined behavior causes bugs. zig's checked arithmetic
prevents integer overflow bugs.

the question is not whether to check arithmetic. the question is when
to check it. zig checks by default in safe builds, enabling explicit
opt-out when performance is critical. this creates systems that are
correct by default.

### memory and types

zig gives explicit control over memory layout and allocation. this
enables zero-copy protocols, predictable memory layouts, and performance
optimization from design.

#### static memory allocation

zig enables static memory allocation. all memory can be allocated at
startup. no memory needs to be dynamically allocated after
initialization. this creates predictable memory layouts and eliminates
allocation overhead.

this constraint forces explicit design decisions. every memory
allocation must be planned. every data structure must have a fixed
maximum size. every queue must have a fixed upper bound.

read [tigerbeetle's choice of zig](https://tigerbeetle.com/blog/2025-10-25-synadia-and-tigerbeetle-pledge-512k-to-the-zig-software-foundation/)
to understand how static allocation simplifies memory management. by
centralizing resource allocation in time and space, zig enables
designing away the need for a borrow checker.

the question is not whether to allocate memory. the question is when to
allocate it. static allocation enables predictable memory layouts. it
enables performance optimization. it enables systems that are bounded by
construction.

#### explicit types and memory layout

zig gives explicit control over memory layout. `extern struct` ensures
specific memory layout for interoperability. `packed struct` enables
bit-level control. alignment is explicit in the type system.

this explicitness enables zero-copy wire protocols. data structures can
be sent over the network without serialization. they can be written to
disk without conversion. the in-memory representation is the wire
format.

read [your abi is probably wrong](https://outerproduct.net/boring/2021-05-07_abi-wrong.html)
to understand how explicit memory layout matters. zig's explicit types
enable correct abis and wire protocols.

the question is not whether to control memory layout. the question is
how to control it explicitly. zig's type system enables explicit
control over layout, alignment, and size. this creates systems that are
correct by construction.

#### fixed-size, cache-aligned data structures

zig's explicit memory layout enables fixed-size, cache-aligned data
structures. every field has a known size. every structure has a known
alignment. the compiler can optimize memory access patterns.

this explicitness enables performance from design. data structures can
be designed to fit cache lines. they can be aligned to avoid false
sharing. they can be laid out to minimize cache misses.

read [jepsen analysis of tigerbeetle](https://jepsen.io/analyses/tigerbeetle-0.16.11)
to understand how fixed-size, cache-aligned data structures enable
high-performance distributed systems. tigerbeetle's fixed schema provides
efficient encoding and decoding, zero-copy transfer, prefetcher-friendly
layouts, and cache line alignment.

the question is not whether to optimize memory layout. the question is
how to optimize it explicitly. zig's type system enables designing data
structures for performance, not just correctness.

### simplicity

zig's simplicity comes from subtractive discipline. it removes features
that hide complexity. it rejects abstractions that leak. it enables
choosing the right architecture for each problem.

#### simplicity through subtraction

zig's simplicity is unusual. it comes from subtractive discipline, not
from deferred complexity. it minimizes surface area. it removes features
that hide complexity. it rejects abstractions that leak.

many languages start simple and grow complex. zig starts with a
clear vision and removes what doesn't fit. it has no private fields. it
has no exceptions. it has no preprocessor. it has no macros.

read [limits to growth](https://graydon2.dreamwidth.org/263429.html)
to understand how languages grow complex. zig's subtractive discipline
prevents this growth. it keeps the language simple by removing features,
not by adding them.

the question is not whether to add features. the question is whether to
remove them. zig's simplicity comes from saying no. it comes from
rejecting complexity. it comes from subtractive discipline.

#### single-threaded by design

zig doesn't force multithreading. it doesn't hide concurrency. it
enables single-threaded architectures when they're appropriate. it
enables explicit queue interfaces when needed.

this flexibility enables choosing the right architecture. for
transaction processing with inherent contention, single-threaded
architectures can be more efficient. zig doesn't force a multithreaded
model.

read [jepsen analysis of tigerbeetle](https://jepsen.io/analyses/tigerbeetle-0.16.11)
to understand why single-threaded architectures make sense for
high-contention workloads. tigerbeetle funnels all writes through a
single core to avoid per-object concurrency control overhead, which can
be the limiting factor in throughput.

read [threads cannot be implemented as a library](https://courses.cs.washington.edu/courses/cse590p/05au/HPL-2004-209.pdf)
to understand the fundamental challenges of concurrency. zig's
flexibility enables choosing when concurrency is needed, not forcing it
everywhere.

the question is not whether to use threads. the question is when to
use them. zig enables single-threaded architectures when appropriate,
with explicit interfaces for coordination when needed.

---

## part two: distributed systems concerns

### design philosophy

zig recognizes that correctness is a design problem, not a language
problem. language features enable correct design, but they do not
guarantee correctness. total correctness requires explicit design
decisions and rigorous testing.

#### correctness is a design problem

zig recognizes that correctness is a design problem, not a language
problem. local memory safety is no guarantee of local correctness, let
alone distributed system correctness. total correctness requires design,
not just language features.

this recognition enables focusing on design. it enables using language
features to enable design, not to guarantee correctness. it enables
thinking about correctness at the system level, not just at the language
level.

read [simple testing can prevent most critical failures](https://www.usenix.org/system/files/conference/osdi14/osdi14-paper-yuan.pdf)
to understand how testing catches bugs that language features miss. zig
enables writing testable code, but testing is still necessary.

the question is not whether language features guarantee correctness. the
question is how to design for correctness. zig enables correct design
through explicitness, but correctness still requires thought.

### fault tolerance

distributed systems must handle faults explicitly. memory can corrupt.
processes can crash. clocks can drift. storage can fail. networks can
partition. these faults are not exceptional. they are expected.

#### explicit fault models

distributed systems must handle faults. memory can corrupt. processes
can crash. clocks can drift. storage can fail. networks can partition.
these faults are not exceptional. they are expected.

zig enables explicit fault models. every fault mode can be handled
explicitly. every fault tolerance mechanism can be designed explicitly.
every failure path can be tested explicitly.

read [jepsen analysis of tigerbeetle](https://jepsen.io/analyses/tigerbeetle-0.16.11)
to understand how tigerbeetle defines explicit models for memory,
process, clock, storage, and network faults. tigerbeetle assumes ECC
RAM is correct. processes may pause or crash. clocks may jump forward
and backward. disks may fail completely or corrupt individual writes.
networks may delay, drop, duplicate, misdirect, and corrupt messages.

the question is not whether faults will occur. the question is how we
handle them explicitly. zig enables designing fault tolerance from the
beginning, not as an afterthought.

#### runtime correctness assertions

zig enables extensive runtime correctness assertions. every invariant
can be asserted. every precondition can be checked. every postcondition
can be verified. assertions detect programmer errors and limit damage
from faults.

this explicitness creates safety. when an assertion fails, the system
knows there is a bug. when an assertion passes, the system knows an
invariant holds. assertions document assumptions and catch violations
early.

read [jepsen analysis of tigerbeetle](https://jepsen.io/analyses/tigerbeetle-0.16.11)
to understand how tigerbeetle makes extensive use of runtime correctness
assertions to identify and limit damage from faults and bugs alike.
assertions are paired: for every property enforced, there are at least
two different code paths where an assertion can be added.

the question is not whether to assert invariants. the question is how to
assert them extensively. zig enables writing assertions that catch bugs
early and document assumptions clearly.

#### checksums and corruption tolerance

distributed systems must handle corruption. memory can corrupt. disks
can corrupt. networks can corrupt. corruption can cause correctness
failures.

zig enables explicit checksum validation. every piece of data can carry
a checksum. checksums can be verified on read. corruption can be
detected and repaired.

read [jepsen analysis of tigerbeetle](https://jepsen.io/analyses/tigerbeetle-0.16.11)
to understand how tigerbeetle handles corruption. tigerbeetle uses
extensive checksums stored separately from data blocks. for critical
data, it writes and reads multiple copies. it offers exceptional
resilience to disk corruption, including damage to every replica's
files.

the question is not whether corruption will occur. the question is how
to detect and repair it. zig enables writing checksum-aware code, but
corruption tolerance requires careful protocol design and extensive
testing.

### testing

distributed systems are hard to test. they are non-deterministic.
failures are rare. bugs are subtle. traditional testing approaches miss
many bugs. comprehensive testing requires multiple approaches and
multiple levels.

#### deterministic simulation testing

distributed systems are hard to test. they are non-deterministic.
failures are rare. bugs are subtle. traditional testing approaches miss
many bugs.

deterministic simulation testing makes non-deterministic systems
deterministic. clocks, disk state, scheduler, network delivery, and
external services are controlled to ensure reproducibility. tests can
explore failure modes systematically.

read [jepsen analysis of tigerbeetle](https://jepsen.io/analyses/tigerbeetle-0.16.11)
to understand how tigerbeetle employs deterministic simulation testing.
the viewstamped operation replicator (VOPR) test simulates an entire
tigerbeetle cluster, including clock, disk, and network interfaces. it
simulates clock skew, corrupts reads and writes, loses and reorders
network messages, and so on.

tigerbeetle also uses non-deterministic fault-injection harnesses like
Vörtex. these complement deterministic simulation by exploring different
failure modes. together, deterministic and non-deterministic testing
provide comprehensive coverage.

the question is not whether to test. the question is how to test
deterministically. zig enables writing testable code, but deterministic
simulation testing is still necessary for distributed systems.

#### testing at every level

distributed systems require testing at multiple levels. unit tests verify
individual components. integration tests verify component interactions.
system tests verify end-to-end behavior. property tests verify
invariants. deterministic tests compare output across multiple machines.

read [jepsen analysis of tigerbeetle](https://jepsen.io/analyses/tigerbeetle-0.16.11)
to understand how tigerbeetle tests at every level. tigerbeetle has
unit tests, integration tests, system tests, property tests, and
deterministic simulation tests. each level catches different classes of
bugs.

the question is not whether to test. the question is how to test at
every level. zig enables writing testable code, but comprehensive
testing requires multiple approaches and multiple levels.

### performance

high-performance systems require explicit design decisions. operations
must be batched. IO must be parallelized. memory layouts must be
optimized. these decisions must be made explicitly, not hidden in
abstractions.

#### batching and IO parallelization

high-performance systems require batching and IO parallelization.
operations must be batched to amortize network and CPU costs. IO must be
parallelized to maximize throughput.

zig enables explicit batching. operations can be collected into batches.
batches can be processed efficiently. batching can be designed
explicitly, not hidden in abstractions.

read [jepsen analysis of tigerbeetle](https://jepsen.io/analyses/tigerbeetle-0.16.11)
to understand how tigerbeetle uses batching extensively. tigerbeetle
funnels all writes through a single core, using batching and IO
parallelization to maximize throughput. operations are batched to
amortize network and CPU costs across multiple operations.

the question is not whether to batch. the question is how to batch
explicitly. zig enables writing code that batches efficiently, but
batching requires explicit design.

### consistency

distributed systems must provide consistency guarantees. operations must
be ordered. state must remain consistent. these guarantees require
careful protocol design and extensive testing.

#### strong serializability and consistency

distributed systems must provide consistency guarantees. strong
serializability ensures that all operations appear to execute in some
order, and that order is consistent with the real-time order of
operations. this is the strongest consistency guarantee possible.

read [jepsen analysis of tigerbeetle](https://jepsen.io/analyses/tigerbeetle-0.16.11)
to understand how tigerbeetle offers strong serializability through
viewstamped replication. as of version 0.16.30, tigerbeetle appeared to
meet its promise of strong serializability. this is not guaranteed by
language features. it is achieved through careful design and rigorous
testing.

the question is not whether to provide consistency. the question is how
to provide it explicitly. zig enables writing correct code, but
consistency requires careful protocol design and extensive testing.

#### physical time and logical time

distributed systems must handle time. operations must be ordered. time
must be consistent. clocks must be synchronized.

tigerbeetle defines an explicit model of time. viewstamped replication
forms a totally ordered sequence of state transitions. view and operation
numbers can be used as a totally ordered logical clock. financial
systems usually prefer wall clocks, so most tigerbeetle timestamps are in
"physical time," which approximates POSIX time with stronger ordering
guarantees.

read [jepsen analysis of tigerbeetle](https://jepsen.io/analyses/tigerbeetle-0.16.11)
to understand how tigerbeetle handles time. tigerbeetle leaders collect
POSIX timestamps from all replicas and try to find a time which falls
within a reasonable margin of error across a quorum of nodes. those
timestamps are incorporated into the replicated state machine and
constrained to be strictly monotonic.

the question is not whether to use time. the question is how to use it
explicitly. zig enables writing time-aware code, but time handling
requires careful protocol design and explicit clock synchronization.

#### flexible quorums and consensus

distributed systems must handle consensus. nodes must agree. operations
must be ordered. consistency must be maintained.

tigerbeetle uses flexible quorums, not just majority quorums. as heidi
howard showed in 2016, consensus systems can use different quorums for
leader election and replication phases. these two quorums must
intersect, but one may be less than a majority.

read [jepsen analysis of tigerbeetle](https://jepsen.io/analyses/tigerbeetle-0.16.11)
to understand how tigerbeetle applies flexible quorum approach to
viewstamped replication. it requires only half, not a majority, of
clocks to agree. this enables more flexible deployments and better
performance characteristics.

the question is not whether to use quorums. the question is how to use
them flexibly. zig enables writing quorum-aware code, but flexible
quorums require careful protocol design and explicit safety properties.

#### establishing quantitative bounds

distributed systems must establish quantitative bounds. every operation
must have an explicit upper bound. every queue must have a fixed
maximum size. every loop must have a fixed iteration limit.

read [jepsen analysis of tigerbeetle](https://jepsen.io/analyses/tigerbeetle-0.16.11)
to understand how tigerbeetle establishes quantitative bounds. every
operation has explicit bounds. these bounds are part of the design, not
afterthoughts. they enable predictable behavior and enable performance
optimization.

the question is not whether to establish bounds. the question is how to
establish them explicitly from the beginning. zig enables writing
bounded code, but quantitative bounds require explicit design decisions
and careful verification.

### operations

distributed systems must handle operational concerns. upgrades must be
coordinated. membership must change. retries must be handled correctly.
these concerns require careful protocol design and clear semantics.

#### multi-version upgrade support

distributed systems must handle upgrades. machines run different
versions. operations must remain correct across versions. state must not
diverge.

zig enables multi-version upgrade support. each binary can include code
for multiple versions. upgrades can be coordinated automatically. state
divergence can be prevented.

read [jepsen analysis of tigerbeetle](https://jepsen.io/analyses/tigerbeetle-0.16.11)
to understand how tigerbeetle handles upgrades. each binary includes
code for several previous versions. upgrades are coordinated across the
cluster automatically. operations committed on one version never commit
on another version, preventing state divergence.

multi-version systems are devilishly hard to pull off. while tigerbeetle
had excellent test coverage for single versions, they lacked fuzz tests
for cross-version upgrades. jepsen found several issues in the upgrade
process. this demonstrates the difficulty of safe upgrades.

the question is not whether to support upgrades. the question is how to
support them safely. zig enables writing version-aware code, but safe
upgrades require careful protocol design and extensive testing, including
cross-version fuzz testing.

#### membership changes and protocol-aware recovery

distributed systems must handle membership changes. nodes are added and
removed. the system must adapt to changing membership. state must remain
consistent.

membership changes in distributed systems are notoriously challenging.
currently unimplemented in tigerbeetle, they represent a rich
opportunity for further testing and design. protocol-aware recovery
techniques go beyond basic consensus to handle complex failure modes.

read [jepsen analysis of tigerbeetle](https://jepsen.io/analyses/tigerbeetle-0.16.11)
to understand how tigerbeetle combines viewstamped replication with
protocol-aware recovery techniques. these techniques enable recovery from
complex failure modes that basic consensus cannot handle.

the question is not whether to support membership changes. the question
is how to support them safely. zig enables writing membership-aware
code, but membership changes require careful protocol design and extensive
testing.

#### retry behavior and client error representation

distributed systems must handle retries. operations fail. clients must
retry. retry behavior must be correct. error representation must be
robust.

read [jepsen analysis of tigerbeetle](https://jepsen.io/analyses/tigerbeetle-0.16.11)
to understand how retry behavior has been the subject of ongoing
discussion. tigerbeetle's approach to retries has been redesigned
over time. requests were intentionally retried forever, which complicates
error handling. robust client representation of errors requires careful
design.

the question is not whether to retry. the question is how to retry
correctly. zig enables writing retry-aware code, but retry behavior
requires careful protocol design and clear error semantics.

### verification

language features alone cannot guarantee distributed system correctness.
total correctness requires external verification. it requires explicit
safety properties. it requires rigorous testing by independent parties.

#### external verification and safety properties

language features alone cannot guarantee distributed system correctness.
total correctness requires external verification. it requires explicit
safety properties. it requires rigorous testing by independent parties.

read [jepsen analysis of tigerbeetle](https://jepsen.io/analyses/tigerbeetle-0.16.11)
to understand the rigor of external verification. jepsen found seven
client and server crashes, two safety issues, and several liveness issues.
tigerbeetle addressed every issue found, demonstrating the value of
external verification.

the question is not whether to verify. the question is how to verify
rigorously. zig enables writing verifiable code, but external
verification is still necessary for distributed systems.

---

## part three: synthesis

### what we should care about

when building distributed systems in zig, we should care about explicit
fault models. we should care about runtime correctness assertions. we
should care about deterministic simulation testing. we should care about
testing at every level. we should care about batching and IO
parallelization. we should care about external verification. we should
care about strong serializability. we should care about multi-version
upgrade support. we should care about membership changes and
protocol-aware recovery. we should care about retry behavior and client
error representation. we should care about physical time and logical
time. we should care about flexible quorums and consensus. we should
care about establishing quantitative bounds. we should care about
checksums and corruption tolerance.

we should care about these things because they are not guaranteed by
language features. they require explicit design. they require rigorous
testing. they require external verification. they require patient
discipline.

zig enables writing code that is correct by design, but correctness
still requires thought. zig enables writing testable code, but testing
still requires rigor. zig enables writing verifiable code, but
verification still requires external analysis.

the question is not whether zig guarantees correctness. the question is
how zig enables correct design. zig provides the tools. we must use
them wisely.

### the learning path

the learning path is incremental. start with zig's philosophy.
understand why zig makes the choices it makes. understand how zig's
constraints create simplicity.

then learn zig's features. understand explicit allocators. understand
error unions. understand `comptime`. understand explicit types. each
feature enables a specific way of thinking about systems.

then learn what to care about. understand explicit fault models.
understand runtime correctness assertions. understand deterministic
simulation testing. understand testing at every level. understand
batching and IO parallelization. understand external verification.
understand strong serializability. understand multi-version upgrade
support. understand membership changes and protocol-aware recovery.
understand retry behavior and client error representation. understand
physical time and logical time. understand flexible quorums and
consensus. understand establishing quantitative bounds. understand
checksums and corruption tolerance.

then read code. read code that demonstrates zig's philosophy. read code
that shows how experts use zig's features. read code that reveals design
decisions. read code that shows what to care about.

finally, read tigerbeetle. tigerbeetle demonstrates zig's philosophy
in practice. it shows how explicit allocators enable static allocation.
it shows how error unions enable explicit error handling. it shows how
`comptime` enables zero-cost abstractions. it shows how explicit fault
models, runtime assertions, deterministic testing, testing at every
level, batching, external verification, flexible quorums, quantitative
bounds, and other techniques enable correct distributed systems.

### the path forward

the path forward is patient. learning zig takes time. understanding
zig's philosophy takes time. seeing how zig's features enable design
takes time. understanding what to care about takes time. the work is
worth doing because zig enables writing systems that are correct by
design.

the path forward is explicit. understand why before how. understand
philosophy before syntax. understand design before implementation.
understand what to care about before writing code. this creates
understanding that transfers.

the path forward is practice. read zig code. understand zig code. write
zig code. experiment with zig's features. learn through doing. this
creates understanding through experience.

### the dream

the dream is understanding zig through philosophy. seeing how zig's
constraints create simplicity. understanding why zig makes the choices it
makes. learning through principles.

the dream is understanding systems through zig. seeing how explicit
allocators enable static allocation. understanding how error unions
enable explicit error handling. learning through design.

the dream is understanding what to care about. seeing how explicit fault
models enable fault tolerance. understanding how runtime assertions
enable correctness. understanding how testing at every level enables
comprehensive verification. understanding how batching and flexible
quorums enable performance. understanding how quantitative bounds enable
predictability. learning through verification.

the dream is understanding code through reading. seeing how experts use
zig's features. understanding how systems are built with zig. learning
through observation.

this is the intuition. this is the problem space. this is the path
forward. the work is worth doing. the work takes time. the work continues.

---

## references

- [why zig for tigerbeetle](https://tigerbeetle.com/blog/2025-10-25-synadia-and-tigerbeetle-pledge-512k-to-the-zig-software-foundation/) —
  tigerbeetle's explanation of choosing zig over rust and c, and how zig
  enables their design philosophy.

- [jepsen analysis of tigerbeetle](https://jepsen.io/analyses/tigerbeetle-0.16.11) —
  rigorous external verification of tigerbeetle's safety and liveness
  properties, demonstrating what to care about when building distributed
  systems.

- [matklad's links](https://matklad.github.io/links.html) — a curated
  collection of programming resources that shape understanding of systems,
  languages, and practices.

- [systems programmers can have nice things](https://robert.ocallahan.org/2016/08/random-thoughts-on-rust-cratesio-and.html) —
  how modern systems languages can provide safety without hiding complexity.

- [the error model](http://joeduffyblog.com/2016/02/07/the-error-model/) —
  different approaches to error handling in programming languages.

- [boundaries](https://www.destroyallsoftware.com/talks/boundaries) —
  how explicit control flow creates clear boundaries in systems design.

- [simple testing can prevent most critical failures](https://www.usenix.org/system/files/conference/osdi14/osdi14-paper-yuan.pdf) —
  how testing catches bugs that language features miss.

- [what is the minimal set of optimizations needed for zero-cost abstraction?](https://robert.ocallahan.org/2020/08/what-is-minimal-set-of-optimizations.html) —
  how compile-time computation enables zero-cost abstractions.

- [what every c programmer should know about undefined behavior](https://blog.llvm.org/2011/05/what-every-c-programmer-should-know.html) —
  how undefined behavior causes bugs and how to avoid it.

- [threads cannot be implemented as a library](https://courses.cs.washington.edu/courses/cse590p/05au/HPL-2004-209.pdf) —
  the fundamental challenges of concurrency and when to use it.

- [limits to growth](https://graydon2.dreamwidth.org/263429.html) —
  how languages grow complex and how zig's subtractive discipline prevents
  this.

- [learning zig through tigerbeetle](./xzyvsh-12025-11-05--1237--pst--learning-zig-through-tigerbeetle.md) —
  the next step in the learning path, applying these zig fundamentals to
  understanding a real codebase.

---

grainorder: xzyvsc  
timestamp: 12025-11-05--1320--pst  
author: kae3g (@risc.love, kj3x39)  
team: teamtravel12 (Pisces ♓ / XII. Upside-Down)

*now == next + 1* 🌾
