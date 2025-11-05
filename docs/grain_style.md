# grain style

zig development guidelines for the grain network

## what is grain style?

grain style is a philosophy of writing code that teaches. every line should
help the next generation understand not just how something works, but why it
works. we write code that lasts, code that teaches, code that grows
sustainably like grain in a field.

when you follow grain style, you're not just solving a problem - you're
creating a teaching moment. future developers (including yourself) will
read your code and understand the choices you made. they'll learn from your
decisions, not just execute them.

## design goals

style serves design. our design goals are safety, performance, and
developer experience. in that order. all three are important. good style
advances these goals. does the code make for more or less safety,
performance, or developer experience? that is why we need style.

put this way, style is more than readability. readability is table stakes,
a means to an end rather than an end in itself. style is necessary only
where understanding is missing. when understanding is present, style
emerges naturally.

## core principles

### simplicity through hard work

simplicity is not a free pass. it is not in conflict with our design goals.
it need not be a concession or a compromise.

simplicity is how we bring our design goals together. it is how we
identify the "super idea" that solves multiple axes simultaneously, to
achieve something elegant.

simplicity is not the first attempt. it is the hardest revision. it's easy
to say "let's do something simple", but to do that in practice takes
thought, multiple passes, many sketches. sometimes we must throw one away
and start fresh.

the hardest part is how much thought goes into everything. we spend this
mental energy upfront, proactively rather than reactively, because we know
that when the thinking is done, what is spent on the design will be dwarfed
by the implementation and testing, and then again by the costs of
operation and maintenance.

an hour or day of design is worth weeks or months in production. the
simple and elegant systems tend to be easier and faster to design and get
right, more efficient in execution, and much more reliable.

### zero technical debt

what could go wrong? what's wrong? which question would we rather ask? the
former, because code, like steel, is less expensive to change while it's
hot. a problem solved in production is many times more expensive than a
problem solved in implementation, or a problem solved in design.

since it's hard enough to discover showstoppers, when we do find them, we
solve them. we don't allow potential latency spikes or exponential
complexity algorithms to slip through.

we do it right the first time. this is important because the second time
may not transpire, and because doing good work, that we can be proud of,
builds momentum.

we know that what we ship is solid. we may lack crucial features, but what
we have meets our design goals. this is the only way to make steady
incremental progress, knowing that the progress we have made is indeed
progress.

### patient discipline

code is written once, read many times. take the time to write it right the
first time. this doesn't mean perfection - it means intention. every
decision should be made consciously, with awareness of the consequences.

when you're about to write a quick hack, pause. ask yourself: "will i
remember why i did this in six months? will someone else understand this?"
if the answer is no, take a moment to write it more clearly.

### explicit limits

zig gives us the power to be explicit. use it. don't hide complexity behind
abstractions - make it visible and understandable.

- use explicit error types, not generic `anyerror`
- set bounds explicitly in your types
- document your assumptions in comments
- make your allocators explicit
- put a limit on everything

when you see a function that takes `[]const u8`, you know exactly what it
expects. when you see `!void`, you know it can fail. this explicitness
makes code easier to understand and maintain.

everything has a limit. in reality, this is what we expect. all loops
must have a fixed upper bound to prevent infinite loops. all queues must
have a fixed maximum size to prevent unbounded growth. all messages must
have a fixed maximum length. this follows the fail-fast principle:
violations are detected immediately, not after they cause problems.

when a loop cannot terminate (for example, an event loop), this must be
asserted. when a queue is full, the system must decide what to do. when a
message is too large, the system must reject it. this creates systems
that are bounded by construction.

use explicitly-sized types like `u32` for everything, avoid
architecture-specific `usize` when the size matters for correctness or
portability.

### static memory allocation

all memory is allocated statically at system startup. no memory is
dynamically allocated or freed after initialization. this eliminates
unpredictable allocation behavior that can affect performance. it
eliminates use-after-free errors. it eliminates memory fragmentation. it
creates predictable memory layouts.

this constraint forces explicit design decisions. every memory allocation
must be planned before the system starts. every data structure must have a
fixed maximum size. every queue must have a fixed upper bound. this
creates systems that are bounded by construction.

when memory is allocated statically, the memory layout is known at compile
time. when memory is allocated statically, there are no allocation failures
at runtime. when memory is allocated statically, there are no deallocation
races. this creates systems that are simpler by construction.

static allocation enables performance optimization. the compiler can
optimize memory access patterns when layouts are fixed. the runtime can
avoid allocation overhead when allocations are known. the system can
guarantee memory locality when layouts are predictable.

for libraries and applications that must allocate dynamically, prefer
explicit allocators passed as parameters. document lifetime expectations.
does the caller need to keep the allocator alive? does the function own it?
make this clear.

### sustainable practice

code that works today but breaks tomorrow isn't sustainable. code that
works for one case but breaks for another isn't sustainable. write code
that can grow without breaking.

think about the boundaries of your code:
- what are the valid inputs?
- what are the edge cases?
- what happens when memory is exhausted?
- what happens when the system is under load?

write code that handles these cases gracefully, not code that crashes when
they occur.

### assertion-based verification

assertions detect programmer errors. unlike operating errors, which are
expected and must be handled, assertion failures are unexpected. the only
correct way to handle corrupt code is to crash. assertions downgrade
catastrophic correctness bugs into liveness bugs. assertions are a force
multiplier for discovering bugs by fuzzing.

assert all function arguments and return values, preconditions and
postconditions, and invariants. a function must not operate blindly on data
it has not checked. the purpose of a function is to increase the
probability that a program is correct. assertions within a function are
part of how functions serve this purpose. the assertion density of the
code must average a minimum of two assertions per function.

pair assertions: for every property you want to enforce, try to find at
least two different code paths where an assertion can be added. for
example, assert validity of data right before writing it, and also
immediately after reading it. this creates redundant verification that
catches bugs that slip through one path.

split compound assertions: prefer `assert(a); assert(b);` over `assert(a
and b);`. the former is simpler to read, and provides more precise
information if the condition fails.

use single-line `if` to assert an implication: `if (a) assert(b);`.

assert the relationships of compile-time constants as a sanity check, and
also to document and enforce subtle invariants. compile-time assertions
are extremely powerful because they are able to check a program's design
integrity before the program even executes.

the golden rule of assertions is to assert the positive space that you do
expect and to assert the negative space that you do not expect. where data
moves across the valid/invalid boundary between these spaces is where
interesting bugs are often found. this is also why tests must test
exhaustively, not only with valid data but also with invalid data, and as
valid data becomes invalid.

assertions are a safety net, not a substitute for human understanding.
build a precise mental model of the code first, encode your understanding
in the form of assertions, write the code and comments to explain and
justify the mental model to your reviewer.

### code that teaches

comments should explain why, not what. if you need a comment to explain what
the code does, the code should be clearer. if you need a comment to explain
why you made a choice, that's valuable - write it.

good comments answer questions like:
- "why did we choose this algorithm?"
- "what edge case does this handle?"
- "what assumption does this code rely on?"
- "what would break if we changed this?"

always motivate, always say why. if you explain the rationale for a
decision, it not only increases the hearer's understanding, and makes them
more likely to adhere or comply, but it also shares criteria with them
with which to evaluate the decision and its importance.

don't forget to say how. when writing a test, think of writing a
description at the top to explain the goal and methodology of the test, to
help your reader get up to speed, or to skip over sections, without
forcing them to dive in.

comments are sentences, with a space after the slash, with a capital letter
and a full stop, or a colon if they relate to something that follows.
comments are well-written prose describing the code, not just scribblings
in the margin. comments after the end of a line can be phrases, with no
punctuation.

## graincard constraints

graincards are 75×100 monospace teaching cards used throughout the grain
network. all zig code should be written to fit within these constraints.

### dimensions

- **total size**: 75 characters wide × 100 lines tall
- **content area**: 73 characters wide × 98 lines tall (after borders)
- **borders**: 1 character on each side (left, right, top, bottom)

this means:
- **zig code lines**: max 73 characters per line (hard wrap)
- **zig functions**: max 70 lines (leaves 28 lines for title/metadata)
- **borders included**: the 1-char borders are part of the 75×100 total

### why these constraints?

graincards are designed to be:
- **portable**: viewable in any terminal
- **consistent**: all cards same size
- **focused**: forces concise, clear code
- **beautiful**: ASCII art borders create visual structure

when you write zig code for graincards, you're writing for a specific
display format. this constraint breeds creativity - it forces you to
think carefully about every line.

### hard wrapping

zig code should be hard-wrapped to 73 characters per line. this ensures
code fits perfectly in graincard content areas without breaking the
visual structure.

use `zig fmt` but also use `grainwrap` to automatically wrap lines that
exceed 73 characters. if a line is too long, break it across multiple
lines in a way that maintains readability.

```zig
// good: fits in 73-char width
const result = try std.fmt.allocPrint(
    allocator,
    "branch: {s}",
    .{branch_name},
);

// also acceptable: if breaking would hurt readability
const short_msg = "this fits";
```

see the "grain network tooling" section for details on using `grainwrap` to
enforce line length limits automatically. `grainwrap` can wrap code
intelligently while preserving readability.

### function size

functions should be max 70 lines. this leaves 28 lines in a graincard
for:
- title and metadata
- function signature documentation
- brief usage examples
- related functions or notes

if a function exceeds 70 lines, consider:
- breaking it into smaller functions
- extracting helper functions
- moving complex logic to separate modules

use `grainvalidate` to check function length automatically. see the "grain
network tooling" section for details.

remember: code that fits in a graincard is easier to understand,
teach, and maintain.

### source code vs graincard display

it's important to understand the distinction:

- **source code files** (`.zig` files): no borders, but still respect
  73-char limit for graincard compatibility. this ensures code can be
  displayed in graincards without modification.

- **graincard display format**: borders are part of the 75×100 total.
  when code is displayed in a graincard, the borders consume 2 characters
  horizontally (left + right) and 2 lines vertically (top + bottom).

this means:
- zig source code files should be hard-wrapped to 73 characters
- the 73-char limit applies even though source files don't have borders
- this ensures code fits perfectly when displayed in graincards later

why this approach? it's better to write code that fits the constraint
from the start, rather than having to reformat it later when you want
to display it in a graincard. the constraint is a feature, not a
limitation - it forces clear, concise code.

## zig-specific guidelines

### memory management

zig gives us explicit control over memory. use it wisely.

#### allocators

always make allocators explicit. pass them as parameters, don't use global
allocators unless absolutely necessary.

```zig
// good: explicit allocator
fn create_buffer(allocator: std.mem.Allocator, size: usize) ![]u8 {
    return try allocator.alloc(u8, size);
}

// bad: implicit global allocator
fn create_buffer(size: usize) ![]u8 {
    return try std.heap.page_allocator.alloc(u8, size);
}
```

when you take an allocator, document its lifetime expectations. does the
caller need to keep it alive? does the function own it? make this clear.

for systems that require static allocation, all memory must be allocated at
startup. no memory may be dynamically allocated or freed after
initialization. this avoids unpredictable behavior and creates systems
that are bounded by construction.

construct larger structs in-place by passing an out pointer during
initialization. in-place initializations can assume pointer stability and
immovable types while eliminating intermediate copy-move allocations, which
can lead to undesirable stack growth.

prefer:
```zig
fn init(target: *LargeStruct) !void {
    target.* = .{
        // in-place initialization
    };
}

fn main() !void {
    var target: LargeStruct = undefined;
    try target.init();
}
```

over:
```zig
fn init() !LargeStruct {
    return LargeStruct{
        // moving the initialized object
    };
}
```

#### error handling

zig's error handling is explicit and powerful. use it.

```zig
// good: explicit error handling
fn parse_number(str: []const u8) !u32 {
    return std.fmt.parseInt(u32, str, 10) catch |err| {
        std.log.err("failed to parse '{s}': {s}", .{ str, @errorName(err) });
        return err;
    };
}

// acceptable: simple error propagation
fn parse_number(str: []const u8) !u32 {
    return std.fmt.parseInt(u32, str, 10);
}
```

don't swallow errors. if something can fail, handle it or propagate it.
silent failures make debugging impossible.

all errors must be handled. an analysis of production failures in
distributed data-intensive systems found that the majority of catastrophic
failures could have been prevented by simple testing of error handling
code. almost all catastrophic system failures are the result of incorrect
handling of non-fatal errors explicitly signaled in software.

use simpler function signatures and return types to reduce dimensionality
at the call site, the number of branches that need to be handled at the
call site, because this dimensionality can also be viral, propagating
through the call chain. as a return type, `void` trumps `bool`, `bool`
trumps `u64`, `u64` trumps `?u64`, and `?u64` trumps `!u64`.

ensure that functions run to completion without suspending, so that
precondition assertions are true throughout the lifetime of the function.
these assertions are useful documentation without a suspend, but may be
misleading otherwise.

explicitly pass options to library functions at the call site, instead of
relying on the defaults. this improves readability but most of all avoids
latent, potentially catastrophic bugs in case the library ever changes its
defaults.

zig has named arguments through the `options: struct` pattern. use it when
arguments can be mixed up. a function taking two `u64` must use an options
struct. if an argument can be `null`, it should be named so that the meaning
of `null` literal at the call site is clear.

because dependencies like an allocator or a tracer are singletons with
unique types, they should be threaded through constructors positionally,
from the most general to the most specific.

### type safety

zig's type system is powerful. use it to make your code safer.

#### structs over primitives

if you're passing around multiple related values, use a struct. it's
self-documenting and type-safe.

```zig
// good: explicit structure
const Point = struct {
    x: f32,
    y: f32,
};

fn distance(p1: Point, p2: Point) f32 {
    const dx = p1.x - p2.x;
    const dy = p1.y - p2.y;
    return std.math.sqrt(dx * dx + dy * dy);
}

// bad: magic numbers
fn distance(x1: f32, y1: f32, x2: f32, y2: f32) f32 {
    const dx = x1 - x2;
    const dy = y1 - y2;
    return std.math.sqrt(dx * dx + dy * dy);
}
```

#### enums for state

use enums to represent states, not magic numbers or strings.

```zig
// good: explicit state machine
const ConnectionState = enum {
    disconnected,
    connecting,
    connected,
    error,
};

// bad: magic numbers
// 0 = disconnected, 1 = connecting, 2 = connected, 3 = error
```

### decomplection

separate concerns. each module should have one clear responsibility. if you
find yourself saying "this module does X and also Y", consider splitting it.

distinguish between the control plane and data plane. a clear delineation
between control plane and data plane through the use of batching enables a
high level of assertion safety without losing performance. the control plane
handles coordination, consensus, and replication. the data plane handles
state machine execution and application logic.

this separation enables high assertion density in the control plane without
losing performance in the data plane. it enables batching in the control
plane while maintaining low latency in the data plane.

#### module organization

organize your code into focused modules:

```
src/
├── types.zig          # data structures and constants
├── format.zig         # formatting logic
├── parse.zig          # parsing logic
├── graintime.zig      # public API and re-exports
└── cli.zig            # command line interface
```

each module should be importable on its own. if `format.zig` needs
something from `types.zig`, that's fine. if it needs something from `cli.zig`,
that's a code smell - you might have your dependencies backwards.

#### function size

functions should do one thing. we enforce a hard limit of 70 lines per
function. this reduces the probability of poorly structured code. use
`grainvalidate` to check function length automatically.

splitting code into functions requires taste. there are many ways to cut a
wall of code into chunks of 70 lines, but only a few splits will feel
right. some rules of thumb:

- good function shape is often the inverse of an hourglass: a few
  parameters, a simple return type, and a lot of meaty logic between the
  braces.
- centralize control flow. when splitting a large function, try to keep all
  switch/if statements in the "parent" function, and move non-branchy
  logic fragments to helper functions. divide responsibility. all control
  flow should be handled by one function, the rest shouldn't care about
  control flow at all. in other words, push `if`s up and `for`s down.
- similarly, centralize state manipulation. let the parent function keep
  all relevant state in local variables, and use helpers to compute what
  needs to change, rather than applying the change directly. keep leaf
  functions pure.

this doesn't mean every function should be tiny - sometimes you need 70
lines to do one thing well. but if you find yourself scrolling through a
function to understand it, it's probably doing too much.

see the "grain network tooling" section for details on using `grainvalidate`
to enforce function length limits automatically.

### naming conventions

names should be clear and descriptive. prefer clarity over brevity.

#### variables

use `snake_case` for variables and functions. be descriptive - `user_data`
is better than `ud`, `buffer_size` is better than `bufsz`.

```zig
// good: clear and descriptive
const user_name = "alice";
const buffer_size: usize = 1024;

// acceptable: when context is clear
const name = "alice";
const size: usize = 1024;

// bad: cryptic abbreviations
const un = "alice";
const bufsz: usize = 1024;
```

add units or qualifiers to variable names, and put the units or qualifiers
last, sorted by descending significance, so that the variable starts with
the most significant word, and ends with the least significant word. for
example, `latency_ms_max` rather than `max_latency_ms`. this will then
line up nicely when `latency_ms_min` is added, as well as group all
variables that relate to latency.

when choosing related names, try hard to find names with the same number
of characters so that related variables all line up in the source. for
example, as arguments to a memcpy function, `source` and `target` are
better than `src` and `dest` because they have the second-order effect
that any related variables such as `source_offset` and `target_offset` will
all line up in calculations and slices. this makes the code symmetrical,
with clean blocks that are easier for the eye to parse and for the reader
to check.

infuse names with meaning. for example, `allocator: Allocator` is a good,
if boring name, but `gpa: Allocator` and `arena: Allocator` are excellent.
they inform the reader whether `deinit` should be called explicitly.

declare variables at the smallest possible scope, and minimize the number
of variables in scope, to reduce the probability that variables are
misused. calculate or check variables close to where/when they are used.
don't introduce variables before they are needed. don't leave them around
where they are not. this will reduce the probability of bugs caused by a
semantic gap, where code is not contained along the dimensions of time or
space.

the usual suspects for off-by-one errors are casual interactions between
an `index`, a `count`, or a `size`. these are all primitive integer types,
but should be seen as distinct types, with clear rules to cast between
them. to go from an `index` to a `count` you need to add one, since
indexes are 0-based but counts are 1-based. to go from a `count` to a `size`
you need to multiply by the unit. again, this is why including units and
qualifiers in variable names is important.

#### types

use `PascalCase` for types (structs, enums, unions). the name should
describe what the type represents, not how it's implemented.

```zig
// good: describes what it is
const User = struct {
    name: []const u8,
    age: u32,
};

// bad: describes implementation
const UserStruct = struct {
    name: []const u8,
    age: u32,
};
```

#### constants

use `SCREAMING_SNAKE_CASE` for compile-time constants. make them const when
possible, and use `comptime` when the value is known at compile time.

```zig
// good: compile-time constant
const MAX_BUFFER_SIZE: usize = 4096;
const DEFAULT_TIMEOUT_MS: u64 = 5000;

// good: computed at compile time
const MAX_PATH_LEN = comptime std.fs.MAX_PATH_BYTES;
```

### formatting

consistency makes code easier to read. use `zig fmt` to format your code.
it's the standard, and it removes the need for style debates.

```bash
zig fmt src/
```

if you disagree with a formatting decision, that's okay - but use `zig fmt`
anyway. consistency is more valuable than personal preference.

## grain network tooling

the grain network provides tools to enforce style constraints automatically.
these tools ensure your code follows grain style while maintaining
readability and clarity.

### grainwrap: line wrapping

grainwrap enforces 73-character hard wrapping for zig code, ensuring perfect
fit within graincard constraints. every line is measured, validated, and
wrapped with precision.

**why use grainwrap?**

graincards are 75×100 monospace teaching cards. with 1-character borders on
each side, the content area is 73 characters wide. this constraint ensures
code fits in graincard displays, forces concise clear code, creates visual
consistency across the grain network, and makes code easier to read and
understand.

**installation and usage:**

grainwrap is available as `teamprecision06/grainwrap`. after running `zig
fmt`, use grainwrap to enforce 73-character line length:

```bash
# format with zig fmt
zig fmt src/

# then wrap to 73 chars with grainwrap
grainwrap wrap src/

# validate line length without wrapping
grainwrap validate src/main.zig
```

**as a library:**

```zig
const std = @import("std");
const grainwrap = @import("grainwrap");

pub fn main() !void {
    var gpa = std.heap.GeneralPurposeAllocator(.{}){};
    defer _ = gpa.deinit();
    const allocator = gpa.allocator();

    const code = "const very_long_line = true;";
    const result = try grainwrap.validate(allocator, code, 73);
    defer grainwrap.free_result(allocator, result);

    if (!result.compliant) {
        for (result.violations) |violation| {
            std.debug.print(
                "Line {d}: {d} chars\n",
                .{ violation.line_number, violation.length },
            );
        }
    }

    const config = grainwrap.default_config;
    const wrapped = try grainwrap.wrap(allocator, code, config);
    defer allocator.free(wrapped);
}
```

grainwrap is decomplected into focused modules: `types.zig` for data
structures, `wrap.zig` for line wrapping logic, `validate.zig` for line
length validation, and `grainwrap.zig` for the public API. each module has
one clear responsibility.

### grainvalidate: style validation

grainvalidate enforces all grain style constraints on zig code. it checks
function length, line width, naming conventions, and other grain_style
rules. every violation is reported with precision.

**what does grainvalidate check?**

- function length: max 70 lines per function
- line width: max 73 characters per line (uses grainwrap internally)
- naming conventions: snake_case for functions, PascalCase for types
- explicit error handling: no generic `anyerror`
- module organization: decomplected, focused modules

**installation and usage:**

grainvalidate is available as `teamprecision06/grainvalidate`. integrate it
into your workflow:

```bash
# validate a zig file
grainvalidate check src/main.zig

# validate all files in directory
grainvalidate check src/

# check only function length
grainvalidate check --function-length src/main.zig

# check only line width (uses grainwrap)
grainvalidate check --line-width src/main.zig
```

**as a library:**

```zig
const std = @import("std");
const grainvalidate = @import("grainvalidate");

pub fn main() !void {
    var gpa = std.heap.GeneralPurposeAllocator(.{}){};
    defer _ = gpa.deinit();
    const allocator = gpa.allocator();

    const code = @embedFile("src/main.zig");
    const config = grainvalidate.default_config;
    const result = try grainvalidate.validate(allocator, code, config);
    defer grainvalidate.free_result(allocator, result);

    if (!result.compliant) {
        std.debug.print("Found {d} violations:\n", .{result.violations.len});
        for (result.violations) |violation| {
            std.debug.print(
                "  {s} at line {d}: {s}\n",
                .{
                    @tagName(violation.violation_type),
                    violation.line,
                    violation.message,
                },
            );
        }
    }
}
```

grainvalidate is decomplected into focused modules: `types.zig` for data
structures, `function_length.zig` for function length validation,
`style.zig` for naming and error type validation, and `grainvalidate.zig`
for the public API.

### complete workflow

integrate both tools into your development workflow:

```bash
# 1. write your code normally
# 2. format with zig fmt
zig fmt src/

# 3. wrap to 73 chars with grainwrap
grainwrap wrap src/

# 4. validate all style rules with grainvalidate
grainvalidate check src/
```

this ensures your code follows grain style completely: it fits in
graincards, respects function length limits, follows naming conventions, and
maintains explicit error handling.

both tools are maintained by **teamprecision06** (Virgo ♍ / VI. The
Lovers), the precision-makers who measure, validate, and enforce boundaries.
virgo's analytical nature meets the lovers' conscious choice. we make
constraints visible, measurable, and beautiful.

### comments

comments should explain why, not what. if the code needs a comment to
explain what it does, make the code clearer instead.

#### good comments

```zig
// we use a linear search here because the array is small (< 10 elements)
// and sorted. binary search would be overkill and add complexity.
for (items) |item| {
    if (item.id == target_id) return item;
}
```

```zig
// this function can fail if the system is out of memory. we don't
// retry here because the caller should handle retries with backoff.
const buffer = try allocator.alloc(u8, size);
```

#### bad comments

```zig
// loop through items
for (items) |item| {
    // check if id matches
    if (item.id == target_id) {
        // return the item
        return item;
    }
}
```

if you need a comment to explain what the code does, the code should be
clearer. comments should add value, not restate the obvious.

### testing

write tests. they're how you verify your code works, and they're how you
document how your code should be used.

#### test organization

put tests near the code they test. use `test` blocks in the same file, or
create a `test.zig` file in the same directory.

```zig
// src/format.zig
pub fn format_branch(branch: GrainBranch, allocator: std.mem.Allocator) ![]u8 {
    // ... implementation
}

// test in same file
test "format_branch creates valid grainbranch name" {
    // ... test code
}
```

#### test names

test names should describe what they're testing. "test format" is not
helpful. "test format_branch_with_all_fields" is better.

```zig
test "format_branch creates valid grainbranch name" {
    // ...
}

test "format_branch handles missing optional fields" {
    // ...
}

test "format_branch fails on invalid input" {
    // ...
}
```

### error messages

when your code fails, it should tell the user why. error messages should be
actionable and specific.

```zig
// good: specific and actionable
return error.InvalidTemperature;
// user sees: "error: InvalidTemperature"
// they know: temperature was invalid

// better: with context
std.log.err("invalid temperature: {d}K (must be 1700-4700K)", .{temp});
return error.InvalidTemperature;
```

don't use generic errors when you can be specific. `error.InvalidInput` is
less helpful than `error.InvalidTemperature` or `error.InvalidFormat`.

## project structure

### directory layout

organize your project clearly. here's a suggested structure:

```
project/
├── src/
│   ├── types.zig          # data structures
│   ├── api.zig            # public API
│   ├── internal/          # internal modules
│   │   ├── format.zig
│   │   └── parse.zig
│   └── cli.zig            # CLI entry point
├── tests/                 # integration tests
├── build.zig              # build configuration
├── readme.md              # project documentation
└── license                # license information
```

### build.zig

keep your build configuration clear and minimal. document any non-obvious
choices.

```zig
const std = @import("std");

pub fn build(b: *std.Build) void {
    const target = b.standardTargetOptions(.{});
    const optimize = b.standardOptimizeOption(.{});

    // create the library module
    const lib_mod = b.addModule("project", .{
        .root_source_file = b.path("src/api.zig"),
    });

    // create executable
    const exe = b.addExecutable(.{
        .name = "project",
        .root_source_file = b.path("src/cli.zig"),
        .target = target,
        .optimize = optimize,
    });
    exe.root_module.addImport("project", lib_mod);

    b.installArtifact(exe);
}
```

### performance from design

think about performance from the outset, from the beginning. the best time
to solve performance, to get the huge wins, is in the design phase, which is
precisely when we can't measure or profile. it's also typically harder to
fix a system after implementation and profiling, and the gains are less. so
you have to have mechanical sympathy. like a carpenter, work with the grain.

perform back-of-envelope sketches with respect to the four resources
(network, disk, memory, cpu) and their two main characteristics (bandwidth,
latency). sketches are cheap. use sketches to be "roughly right" and land
within 90% of the global maximum.

optimize for the slowest resources first (network, disk, memory, cpu) in
that order, after compensating for the frequency of usage, because faster
resources may be used many times more. for example, a memory cache miss may
be as expensive as a disk fsync, if it happens many times more.

amortize network, disk, memory, and cpu costs by batching accesses. let the
cpu be a sprinter doing the 100m. be predictable. don't force the cpu to zig
zag and change lanes. give the cpu large enough chunks of work. this comes
back to batching.

whenever your program has to interact with external entities, don't do
things directly in reaction to external events. instead, your program should
run at its own pace. not only does this make your program safer by keeping
the control flow of your program under your control, it also improves
performance for the same reason (you get to batch, instead of context
switching on every event). additionally, this makes it easier to maintain
bounds on work done per time period.

be explicit. minimize dependence on the compiler to do the right thing for
you. in particular, extract hot loops into stand-alone functions with
primitive arguments without `self`. that way, the compiler doesn't need to
prove that it can cache struct's fields in registers, and a human reader
can spot redundant computations easier.

### code organization

order matters for readability (even if it doesn't affect semantics). on the
first read, a file is read top-down, so put important things near the top.
the `main` function goes first.

the same goes for `structs`, the order is fields then types then methods:

```zig
time: Time,
process_id: ProcessID,

const ProcessID = struct { cluster: u128, replica: u8 };
const Tracer = @This(); // this alias concludes the types section

pub fn init(gpa: std.mem.Allocator, time: Time) !Tracer {
    // ...
}
```

if a nested type is complex, make it a top-level struct.

at the same time, not everything has a single right order. when in doubt,
consider sorting alphabetically, taking advantage of big-endian naming.

when a single function calls out to a helper function or callback, prefix
the name of the helper function with the name of the calling function to
show the call history. for example, `read_sector()` and
`read_sector_callback()`.

callbacks go last in the list of parameters. this mirrors control flow:
callbacks are also invoked last.

don't overload names with multiple meanings that are context-dependent.
think of how names will be used outside the code, in documentation or
communication. a noun is often a better descriptor than an adjective or
present participle, because a noun can be directly used in correspondence
without having to be rephrased.

appreciate, from day one, all compiler warnings at the compiler's strictest
setting.

be on your guard for buffer bleeds. this is a buffer underflow, the
opposite of a buffer overflow, where a buffer is not fully utilized, with
padding not zeroed correctly. this may not only leak sensitive information,
but may cause deterministic guarantees to be violated.

use only very simple, explicit control flow for clarity. do not use
recursion to ensure that all executions that should be bounded are bounded.
use only a minimum of excellent abstractions but only if they make the best
sense of the domain. abstractions are never zero cost. every abstraction
introduces the risk of a leaky abstraction.

## common patterns

### initialization

when initializing structs, use `.{}` syntax for clarity:

```zig
const config = Config{
    .enabled = true,
    .temperature = 3000,
    .schedule_automatic = false,
};
```

### optional values

use optionals explicitly. don't use sentinel values when you can use `?T`.

```zig
// good: explicit optional
fn find_user(id: u32) ?User {
    // ...
}

// bad: sentinel value
const INVALID_USER_ID: u32 = 0;
fn find_user(id: u32) User {
    if (id == INVALID_USER_ID) return User{ .id = 0, .name = "" };
    // ...
}
```

### iteration

prefer explicit iteration over magic. when you iterate, make it clear what
you're iterating over.

```zig
// good: explicit iteration
for (users, 0..) |user, index| {
    std.log.info("user {d}: {s}", .{ index, user.name });
}

// good: simple iteration
for (users) |user| {
    process_user(user);
}
```

compound conditions that evaluate multiple booleans make it difficult for
the reader to verify that all cases are handled. split compound conditions
into simple conditions using nested `if/else` branches. split complex `else
if` chains into `else { if { } }` trees. this makes the branches and cases
clear. again, consider whether a single `if` does not also need a matching
`else` branch, to ensure that the positive and negative spaces are handled
or asserted.

negations are not easy. state invariants positively. when working with
lengths and indexes, this form is easy to get right (and understand):

```zig
if (index < length) {
    // the invariant holds
} else {
    // the invariant doesn't hold
}
```

this form is harder, and also goes against the grain of how `index` would
typically be compared to `length`, for example, in a loop condition:

```zig
if (index >= length) {
    // it's not true that the invariant holds
}
```

show your intent with respect to division. for example, use `@divExact()`,
`@divFloor()`, or `div_ceil()` to show the reader you've thought through all
the interesting scenarios where rounding may be involved.

don't duplicate variables or take aliases to them. this will reduce the
probability that state gets out of sync.

if you don't mean a function argument to be copied when passed by value,
and if the argument type is more than 16 bytes, then pass the argument as
`*const`. this will catch bugs where the caller makes an accidental copy on
the stack before calling the function.

use newlines to group resource allocation and deallocation, i.e. before the
resource allocation and after the corresponding `defer` statement, to make
leaks easier to spot.

## when to break the rules

these guidelines are principles, not laws. sometimes you need to break them.
when you do, document why.

```zig
// we use a global allocator here because this function is called
// from C code and we can't pass allocators through the C API.
// this is a known limitation - see issue #123.
const global_allocator = std.heap.page_allocator;
```

if you're breaking a rule, there should be a good reason. if you can't
articulate the reason, you probably shouldn't break the rule.

## learning resources

- [zig language reference](https://ziglang.org/documentation/)
- [zig standard library documentation](https://ziglang.org/documentation/master/std/)
- [zig learn](https://ziglearn.org/) - excellent tutorial resource
- [zig news](https://zig.news/) - community articles

## questions?

if you're unsure about a style choice, ask yourself:

1. **will this be clear in six months?** if you can't answer yes, make it clearer.

2. **does this teach something?** good code teaches. does yours?

3. **is this sustainable?** will this code work when requirements change?

4. **is this explicit?** zig gives us the power to be explicit. are you using it?

if you're still unsure, ask the team. we're here to help each other write
better code.

---

remember: code is written once, read many times. write it for the reader.
write it to teach. write it to last.

now == next + 1 🌾⚒️

