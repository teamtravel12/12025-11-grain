# 995-unified-mobile-os-plan

experimental: unified zig monolith kernel mobile derivative

## goal

build a tiger style mobile platform that subverts ios/android limitations by extending the unified zig monolith kernel OS (see docs 997, 998, 999) to phones/tablets/other devices, targeting open, repairable hardware modeled after framework/fairphone. start on amenable firmware (amd x86_64 or mature arm SOCs), converge to risc-v when ecosystem stabilises.

## reference documents

- `998-full-os-stack-in-zig.md`: zig kernel, wayland/river, s6 supervision, distributed stack vision
- `999-s6-in-zig.md`: process supervision suite in zig
- `997-unified-system-vision.md`: 30-year unified system roadmap (risc-v, tigerbeetle, nostr, MMT)

## high-level phases

### phase 0: foundations (desktop-first)

1. **zig kernel baseline (998)**
   - aero-inspired monolith in zig
   - explicit memory allocators, static schemas
   - s6-zig for service supervision (999)

2. **unified stack components (997)**
   - tigerbeetle for distributed state (core financial layer)
   - nostr comms layer (decentralised messaging)
   - wayland/river UI pipeline

3. **desktop hardware bring-up**
   - target repairable x86_64 laptops (framework) w/ open-ish firmware
   - port kernel to run under custom firmware -> minimal boot flow
   - design modular device drivers (graphics, input, storage, networking)

### phase 1: mobile hardware adaptation

1. **select hardware**
   - repairable x86_64 handheld (e.g. future framework phone) OR
   - fairphone-level openness (ARM) w/ accessible bootloaders
   - optional: x86 tablets with detachable keyboards

2. **firmware strategy**
   - x86: coreboot/seabios path for custom firmware, leveraging AMD open-source components
   - ARM: u-boot + vendor sources; ensure ability to flash and unlock
   - design neutral boot interface allowing future RISC-V swap

3. **kernel mobile extensions**
   - power management (DVFS, thermal budgets, battery drivers)
   - telephony stack (modem integration via minimal drivers)
   - secure enclave replacement (zig state machines + hardware key storage)
   - sensor subsystem (accelerometer, camera, fingerprint)

4. **UI/UX pipeline**
   - continue using wayland/river for coherent UI (tiling for phone/tablet? design mobile shells)
   - integrate zig-native GUI components (from 996 strategy) for performance-critical rendering

5. **middleware**
   - port s6-zig supervision for mobile daemons (modem, sensor processes)
   - adopt nostr for messaging/notifications (decentralised push)
   - embed tigerbeetle for wallet/ledger features (optional)

### phase 2: ios/android parity bypass (emulation/compat)

1. **application story**
   - encourage wasm+zig native apps (unified runtime from 997)
   - design bridging layer for limited iOS/Android API compatibility (if needed), via sandboxed environment or compatibility layer (e.g. Android runtime inside container) – kept optional, non-core

2. **platform services**
   - push notifications via nostr relays (decentralised)
   - app signing/public identity using MMT-inspired sovereign principles
   - curated repositories for tiger style apps

3. **security**
   - adopt capability-based security (stacked with s6 supervision)
   - incorporate pluggable secure element APIs; aim for open hardware

### phase 3: expansion to other devices

1. **tablets**
   - adjust UI to support larger displays, multi-window tiling

2. **TVs / set-top boxes**
   - adapt GPU pipeline; remote-friendly interface

3. **watches / wearables**
   - design minimal zig microkernel variant w/ same supervision principles

4. **automotive / IoT**
   - evaluate deterministic scheduling, hard real-time options in zig kernel

### phase 4: risc-v convergence

1. **hardware readiness**
   - monitor libre risc-v SOC projects
   - pilot boards (HiFive, StarFive, future mobile-grade SOCs)
   - port boot chain to risc-v once performance/battery acceptable

2. **software adjustments**
   - verify zig kernel portable (endian/l2 caches)
   - re-tune power management for risc-v

## subverting ios/android constraints

### ios constraints and answer

| ios limitation | tiger style mobile approach |
| --- | --- |
| heap-only obj-c/Swift runtime | run Zig OS: no reliance on objc runtime; use zig-managed UI stack |
| locked bootloaders | target open hardware (framework phone) permitting alt OS; not deploying to Apple hardware |
| app store restrictions | provide alternative app ecosystem (nostr/tigerbeetle based identity + repository) |
| closed firmware | utilise coreboot/u-boot, open firmware stack |

### android limitation | approach

| android limitation | tiger style mobile approach |
| --- | --- |
| GC-managed runtime (ART) | Zig kernel + native runtime -> no JVM |
| vendor lock-in / proprietary drivers | select hardware w/ upstreamed drivers or community support; design for driver openness |
| security model (keystore, gms) | replace w/ nostr identity + tigerbeetle ledger; capability-based access |
| Play Store dependency | build curated open repository, MMT-aligned funding |

### bridging needs

- optional compatibility layer to run existing apps (e.g. Anbox-like container) without compromising core Tiger Style; isolate via s6-managed sandbox
- emphasise Zig/WASM native apps for new ecosystem

## technical pillars

1. **zig kernel stack (998)**
   - scheduler, memory manager, drivers, file systems
   - modular to support mobile hardware specifics

2. **s6 in zig (999)**
   - service supervision, logging, dependency graph
   - manage daemons (modem, radio, sensors)

3. **unified system components (997)**
   - nostr: communications + push
   - tigerbeetle: financial ledgers / caches
   - wasm runtime: cross-platform apps
   - MMT principles: funding/infrastructure model

4. **hardware strategy**
   - initial focus: AMD x86_64 handheld (framework-level openness)
   - short-term alternative: ARM SOC with unlockable bootloader (fairphone)
   - long-term: RISC-V once mobile SoCs mature

5. **UI/UX**
   - leverage wayland/river; design mobile shell with tiling + gestures
   - zig-native rendering pipeline for performance-critical pieces

## roadmap sketch

1. **Year 1-2**: desktop prototype (zig kernel, river UI, nostr integration). Boot on framework laptop; deliver developer kit.
2. **Year 3-4**: mobile hardware alpha on x86 handheld; port drivers, power management; deliver developer reference phone/tablet.
3. **Year 5**: launch Tiger Style app ecosystem (zig+wasm), secure messaging via nostr, economic layer via tigerbeetle. Evaluate compatibility container for legacy apps.
4. **Year 6-8**: expand to TV/watch, refine hardware manufacturing partnerships (repairable design). Begin RISC-V pilot hardware.
5. **Year 10**: stable RISC-V mobile hardware, fully open supply chain, replicate across device categories.

## open research questions

- designing a mobile-friendly UI atop river/wayland (touch interactions, gesture navigation)
- modem firmware openness (seek hardware with open AT command interfaces)
- secure element replacement strategies (open-source hardware tokens?)
- power management policies for zig kernel (scheduling, suspend/resume) across x86/ARM/RISC-V
- WASM sandbox performance on mobile hardware; offline-first capabilities aligned w/ nostr
- funding model (MMT-inspired public investment) to support hardware manufacturing + ecosystem

## summary

this plan extends the unified zig monolith kernel (doc 998) with s6 supervision (doc 999) and the broader 30-year vision (doc 997) to craft a tiger style mobile platform. start with open, repairable hardware; leverage zig + wasm for application stack; embed nostr/tigerbeetle for communications/economics. eventually aim for risc-v hardware when ready, enabling fully sovereign, repairable, tiger-aligned devices across phones, tablets, TVs, watches, and beyond.

---

grainorder: 995-unified-mobile-os-plan  
timestamp: 12025-11-06  
team: teamtravel12 (Pisces ♓ / XII. Upside-Down)

*now == next + 1* 🌾

