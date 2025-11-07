# 996-tiger-style-mobile

experimental: tiger style mobile strategies (ios + android)

## intent

can we build mobile apps that honor tiger style / grain style constraints — explicit control, minimal dependencies, static allocation strategy — on platforms that expect heap-heavy, garbage-collected languages?

## tiger style recap

- explicit control: no hidden allocations, deterministic lifetimes, thin abstraction boundaries
- minimal dependencies: avoid frameworks that conceal cost; keep glue layers tiny
- static mindset: prefer stack/arena allocation, bounded buffers, reuse
- intentional interfaces: low-level cores + minimal platform-specific UI glue

## ios strategy (objective-c / swift / zig)

### constraints (ios 26, xcode 26.1)

- objective-c objects must live on heap (isa pointers, ARC, autorelease pools expect heap semantics)
- stack-allocated `alloca` tricks are unsupported; app review / runtime hardening make them fragile
- swift value types can stay on stack if simple, but arrays/strings/classes hit the heap
- ui must be built with uikit/swiftui; no alternative runtimes allowed

### tiger style compliant plan

1. **core logic in zig (or c)**
   - compile to `aarch64-ios` static library (no bitcode since ios 14)
   - use zig allocators (arena, fixed buffer) for deterministic memory
   - expose `extern "C"` functions returning POD structs or pointers to caller-managed buffers

2. **bridging layer in objective-c or swift**
   - tiny façade code: collect inputs from ui, call zig functions, translate outputs
   - keep allocations explicit (use `CFDataCreateWithBytesNoCopy`, reuse mutable buffers, avoid autorelease churn)
   - adopt `@autoreleasepool {}` and `objc_precise_lifetime` to bound object lifetimes explicitly

3. **ui discipline**
   - manual layout (avoid auto layout churn), reuse cells/views
   - maintain object pools for view models to limit heap pressure
   - restrict dynamic dispatch by sealing classes/using final where possible

4. **memory policy**
   - preallocate working sets (e.g. ring buffers) in zig core
   - pass raw pointers to bridge; convert to `NSData`/`NSString` only at the edge
   - run `objc_memtime`/instruments to police hidden allocations

5. **testing**
   - unit test zig core on mac/linux with same allocators
   - integration test on device for alignment/abi issues
   - ensure app store compliance (no private api, no jit)

## android strategy (kotlin/java + ndk + zig/c)

### constraints

- dalvik/art uses gc; java/kotlin objects always heap-allocated
- no stack allocation for reference types; primitives may live on stack but are limited
- ui must use android sdk/jetpack compose/views

### tiger style compliant plan

1. **core logic in zig/c++/rust via ndk**
   - build shared library (`.so`) targeting `arm64-v8a`, `armeabi-v7a`
   - manage memory with arenas/fixed buffers, avoid malloc where possible
   - expose `extern "C"` functions returning plain structs / pointer-to-buffer

2. **minimal kotlin/java glue**
   - use `JNI` thin wrappers: convert between primitive arrays and native buffers explicitly
   - leverage `DirectByteBuffer` / `ByteBuffer.allocateDirect` for zero-copy transfer to native layer
   - keep lifecycle management explicit; avoid heavy frameworks (coroutine builders, reflection)

3. **ui discipline**
   - prefer view recycling (recyclerview), avoid overdraw, manual layout where necessary
   - contain compose usage; if used, wrap deterministic pipelines around recomposition

4. **memory policy**
   - native side owns long-lived buffers; kotlin layer holds lightweight handles
   - enforce strict boundary: no native code allocating gc-managed objects
   - use `StrictMode`/`Android Studio Profiler` to catch accidental allocations

5. **build & deployment**
   - gradle + cmake or cargo/zig build integrated via ndk build script
   - sign with app bundles; ensure abi splits (arm64) to reduce size
   - test with `hwaddress`/asan on native side for determinism

## comparison & notes

| aspect | ios | android |
| --- | --- | --- |
| core tiger logic | zig static lib | zig/c++ ndk lib |
| glue language | objective-c/swift minimal | kotlin/java minimal |
| ui layer | uikit/swiftui (disciplined) | android views/compose (disciplined) |
| memory control | zig allocators + explicit bridging | native allocators + explicit jni |
| unavoidable heap | ui objects, foundation types | ui objects, jvm objects |
| tooling | xcode, instruments | android studio, profilers |

## best-effort tiger style checklist

- isolate platform-managed heaps to ui layer only
- preallocate buffers + reuse them across calls
- keep glue thin: macros, generics, reflection avoided
- treat zig native core as authoritative source of truth/state
- automate performance audits (ci running instruments/profiler scripts)

## open questions

- can we ship zig-first ui toolkit with metal/vulkan while satisfying app store / play policies? (currently risky)
- explore `metal-cpp` + zig for custom render pipeline? (needs objc bridging)
- investigate llvm stack allocations via swift `unsafe` apis for micro-optimizations (subject to compiler changes)
- maintain tooling for deterministic builds (zig build, reproducible archives) to align with tiger style

---

grainorder: 996-tiger-style-mobile  
timestamp: 12025-11-06  
team: teamtravel12 (Pisces ♓ / XII. Upside-Down)

*now == next + 1* 🌾

