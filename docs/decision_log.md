# Decision Log

This log preserves the reasoning behind significant Ferrous Drive decisions.
It records both accepted direction and proposed choices that still require
simulation, bench work, or rider validation.

## Status Definitions

| Status | Meaning |
|---|---|
| `ACCEPTED` | Current architectural direction |
| `PROPOSED` | Preferred direction, not yet sufficiently validated |
| `SUPERSEDED` | Replaced but retained for historical context |
| `REJECTED` | Considered and deliberately not pursued |

---

## FD-001: Use Rust for the Ferrous Drive Platform

**Status:** `ACCEPTED`

### Decision

Implement Ferrous Drive in Rust.

### Why

- Strong type and memory-safety model
- Suitable for shared desktop and embedded development
- Supports deterministic and allocation-free control logic
- Encourages explicit error and state handling
- Aligns with the project's open-source and reusable-platform goals

### Consequences

- The portable core should support `no_std`.
- Hardware, runtime, and protocol concerns remain outside the core.
- Desktop simulation and embedded adapters share domain logic.

---

## FD-002: Develop Simulation First

**Status:** `ACCEPTED`

### Decision

Use the desktop simulator as the primary development environment before moving
control logic onto a ridden bicycle.

### Why

Ferrous Drive contains safety-relevant, physiological, energy, and behavioural
logic that benefits from repeatability and explanation before hardware testing.

### Consequences

- Identical input and configuration should produce identical output.
- Route replay, synthetic signals, and fault injection are first-class tools.
- Physical testing progresses through explicit validation gates.

---

## FD-003: Keep the Domain Core Controller Independent

**Status:** `ACCEPTED`

### Decision

Represent rider state, behaviour, battery capability, and torque intent through
portable domain types rather than controller-specific commands.

### Why

Ferrous Drive should remain reusable and testable without a particular motor
controller or MCU.

### Consequences

- Controller protocols live in adapters.
- The core produces bounded intent rather than wire-level commands.
- Unsupported drive capabilities are disabled explicitly.

---

## FD-004: Use the Nordic nRF54L15 DK as the Initial Embedded Platform

**Status:** `ACCEPTED`

### Decision

Use the Nordic nRF54L15 DK for the first embedded prototype.

### Why

The project requires wireless sensor integration, deterministic control,
controller communication, and sufficient development visibility.

### Consequences

- Board-specific code remains outside the portable core.
- Embedded support follows the first desktop Rust milestone.
- Runtime and HAL compatibility require a focused technical spike.

---

## FD-005: Prefer RTIC and Fixed-Capacity Data

**Status:** `PROPOSED`

### Decision

Use RTIC as the preferred embedded scheduling model and `heapless` for bounded
collections where capacity is meaningful.

### Why

- Explicit priorities and shared resources
- Deterministic task structure
- Bounded memory use
- Explicit overflow behaviour

### Consequences

- RTIC support on the selected platform must be proven.
- Safety-critical state must not depend on a lossy general-purpose queue.
- Capacity values become part of system design and validation.

---

## FD-006: Use the Grin V3 Rear All-Axle 6T as the Active Drive Baseline

**Status:** `ACCEPTED`

### Decision

Use the Grin V3 Rear All-Axle 6T direct-drive motor and a compatible headless
Grin controller as the active reference implementation.

### Why

The platform supports the project's current goals:

- Regenerative braking
- Integrated rider torque and pedal sensing
- Motor-temperature telemetry
- Positive and negative torque control
- Rich controller instrumentation

### Consequences

- Direct-drive drag must be managed.
- Rear-wheel-only regen requires conservative limits.
- Controller command, telemetry, and watchdog behaviour must be verified.
- Earlier geared-hub investigation becomes historical exploration.

---

## FD-007: Retain the 10S2P Molicel P50B Battery Baseline

**Status:** `ACCEPTED`

### Decision

Retain the 20-cell Molicel P50B battery concept:

```text
10S2P
36 V nominal
42 V full charge
10 Ah
360 Wh nominal
7 + 6 + 7 physical layers
```

### Why

The pack balances range, winter performance, voltage stability, transient
capability, and regenerative-charge headroom.

### Consequences

- The pack remains provisional until physically validated.
- The battery is not down-sized from simulation alone.
- Destination charging is considered acceptable for the commute mission.
- Finished mass is expected around the current 1.70 to 1.85 kg target region.

---

## FD-008: Use Three Rider Modes

**Status:** `ACCEPTED`

### Decision

Use three top-level rider modes:

```text
Neutral
Recovery
Training
```

### Why

The three-mode model is simpler to understand and better represents rider
intent than conventional e-bike assist levels.

### Consequences

- Neutral preserves natural cycling.
- Recovery regulates a personalized low-intensity workload.
- Training hosts structured earn-and-reward profiles.
- Commute becomes a journey and energy context rather than a mode.
- Tempo becomes a Training profile rather than a top-level mode.

### Supersedes

The previous Neutral Ride, Active Recovery, Commute, and Tempo mode set.

---

## FD-009: Adopt Tre Pulse as the Shared Interaction Language

**Status:** `ACCEPTED`

### Decision

Use Tre Pulse as Ferrous Drive's shared visual, behavioural, range, and reward
language.

### Why

Tre Pulse creates one simple mental model across:

- Three rider modes
- Three interaction segments
- Three battery-range layers
- Three physical battery layers

### Consequences

- Tre Pulse becomes a first-class subsystem.
- Detailed telemetry remains available outside the three-segment summary.
- Colour must not be the only information channel.
- The interaction model requires rider-comprehension testing.

---

## FD-010: Introduce a Shared Tre Pulse Behaviour Engine

**Status:** `ACCEPTED`

### Decision

Use one configurable engine for Recovery and Training accumulation, milestones,
streaks, and rewards.

### Why

Recovery and Training share common mechanics:

- Qualifying conditions
- Grace and decay
- Locked milestones
- Completion detection
- Reward authorization
- Energy constraints

### Consequences

- Profiles become configuration and scoring rules rather than independent
  control architectures.
- The engine produces reward requests, not final torque commands.
- Braking, battery protection, telemetry trust, and torque arbitration remain
  independent.

---

## FD-011: Define Recovery Relative to Rider Physiology

**Status:** `ACCEPTED`

### Decision

Define Recovery through a named relative Zone 2 model rather than one universal
wattage range.

Possible anchors include:

- FTP-relative power
- Critical-Power-relative power
- Heart-rate zones
- LT1 or VT1 when available

### Why

Recovery intent depends on the rider's current physiology and the chosen zone
model.

### Consequences

- Fixed power values remain examples or rider configuration.
- Power describes external work.
- Heart rate describes part of the internal response.
- HRV provides readiness and fatigue context.

---

## FD-012: Start Recovery at 1.0:1 and Adapt to 1.2:1

**Status:** `PROPOSED`

### Decision

Use a `1.0:1` rider-to-motor baseline in Recovery. Trusted HRV, heart-rate,
power, and drift trends may progressively permit support up to `1.2:1`.

### Why

A conservative baseline preserves rider ownership while giving the system room
to respond to fatigue.

### Consequences

- HRV adjusts the permitted envelope rather than commanding torque directly.
- Invalid HRV returns the system toward baseline.
- Arrival reserve and safety limits may reduce the permitted ratio.
- The values require simulation and rider validation.

---

## FD-013: Treat Training Assistance as Earned

**Status:** `ACCEPTED`

### Decision

Separate Training into qualifying work and a bounded assistance reward.

### Why

The motor becomes an immediate physical reward for productive work rather than
a passive way to avoid the training stimulus.

### Consequences

- Earned reward assistance remains inactive during the work phase.
- Required system-penalty compensation may remain available.
- Valid pedalling remains required during the reward.
- Braking cancels positive reward torque immediately.

---

## FD-014: Begin with Tempo Tailwind and Anaerobic Shield Profiles

**Status:** `PROPOSED`

### Decision

Use Tempo Tailwind and Anaerobic Shield as the first Training profile concepts.

### Why

They demonstrate two distinct use cases:

- Cumulative sub-threshold time in zone
- Committed higher-intensity effort with bounded recovery reward

### Consequences

- Target zones, point curves, grace, decay, and reward values remain
  experimental.
- Sports-science concepts and Ferrous Drive game parameters must remain
  explicitly separated.

---

## FD-015: Add the Three-Cycle Golden Streak

**Status:** `PROPOSED`

### Decision

Unlock a Golden Super Reward after three consecutive valid Recovery or Training
cycles.

### First-Generation Rule

```text
Normal reward
    1× duration

Golden Super Reward
    2× duration
```

### Why

The mechanic rewards consistency rather than only one completed effort.

### Consequences

- The third accumulation cycle progressively turns Tre Pulse gold.
- Peak assistance does not increase beyond the validated profile ceiling.
- Triple duration remains deferred.
- Safety interruptions should pause rather than unfairly break the streak.
- The motivational and energy effects require validation.

---

## FD-016: Protect Arrival Reserve Above Rewards

**Status:** `ACCEPTED`

### Decision

Authorize rewards only after predicting their impact on destination arrival
energy.

### Reward Outcomes

```text
Full
Shortened
Deferred
Unavailable
```

### Consequences

- Journey and reserve energy have priority over reward energy.
- Tre Pulse explains constrained delivery.
- A reward may be earned without being immediately deliverable.

---

## FD-017: Keep Regenerative Braking Independent from Reward Logic

**Status:** `ACCEPTED`

### Decision

Keep rider-triggered regenerative braking outside the Tre Pulse Behaviour Engine.

### Why

Braking authority must remain independent from training games, progress, and
rewards.

### Consequences

- Brake intent always cancels positive torque.
- Regen remains available in all compatible modes.
- Legitimate safety braking should preserve streak state where possible.
- Tre Pulse may present braking status but cannot initiate braking.

---

## FD-018: Use Binary Brake Intent with Speed-Scheduled Regen

**Status:** `PROPOSED`

### Decision

Use lever-mounted binary Hall sensors to identify rider brake intent. Use speed
to shape a deterministic regen map, with bounded wheel-speed and IMU feedback.

### Consequences

- Coasting remains distinct from braking.
- The first generation uses deterministic control.
- Learning remains observation-only.
- Hydraulic brakes remain mechanically independent and authoritative.

---

## FD-019: Use Recorded Commute Data as the Primary Route Baseline

**Status:** `ACCEPTED`

### Decision

Use the recorded current-bike GPX routes as the primary route baseline.

### Current Baseline

```text
Recorded moving mass
    95 kg

Tyres
    WTB Vulpine 36c

Wheels
    Aluminium
```

### Consequences

- Outbound and return remain separate scenarios.
- Rider power, wind, battery current, and regen remain assumptions until
  measured.
- Older faster road-bike rides remain a comparative lower-drag reference.

---

## FD-020: Start Coding with Telemetry Trust

**Status:** `ACCEPTED`

### Decision

Make telemetry trust the first implemented Ferrous Drive concept.

### First Milestone

- Portable `no_std` core
- Desktop simulator
- `heapless` bounded history
- `Valid`, `Aging`, `Stale`, and `Invalid` signal states
- Unit tests
- GitHub Actions verification

### Why

Every later physiological, braking, battery, and reward feature depends on
trusted data.

---

## Superseded Summary

The following earlier directions remain part of project history but are no
longer the active source of truth:

- ESP32-S3 as the initial platform
- Bafang G310 geared-hub baseline
- Lightweight geared-hub alternative as an active path
- Four top-level modes
- Commute as a ride mode
- Tempo as a top-level mode
- Fixed Recovery wattage as a universal definition
- `1.2:1` Recovery baseline
- Unvalidated `1.4:1` Recovery reward
- Triad UI Framework and Triad Pulse naming
- High Assist mode
