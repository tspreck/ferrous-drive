> 📚 [README](../README.md)admap](roadmap.md) · 📐 [Architecture](architecture.md) · 📜 project_origin.md · 🧠 assumptions.md · ✅ validation_matrix.md

# Decision Log

This page records the decisions that shaped Ferrous Drive.

The goal is not to prove that every decision was correct. The goal is to preserve:

- What was decided
- Why it made sense at the time
- What changed later
- What evidence is still missing
- Which decisions have been superseded

This prevents the project from quietly rewriting its own history as the design evolves.

## Decision Status

| Status | Meaning |
|---|---|
| `ACCEPTED` | Current project direction |
| `PROVISIONAL` | Current preference, but validation is still required |
| `EXPLORATORY` | Interesting idea that requires investigation |
| `SUPERSEDED` | Replaced by a newer decision |
| `REJECTED` | Considered and intentionally not pursued |
| `ON HOLD` | Useful, but not part of the current work |

---

## Summary

| ID | Decision | Status |
|---|---|---|
| FD-001 | Use Rust for the control core | ACCEPTED |
| FD-002 | Separate domain logic from hardware adapters | ACCEPTED |
| FD-003 | Develop through simulation and deterministic replay | ACCEPTED |
| FD-004 | Preserve assumptions, decisions, and validation evidence in the repository | ACCEPTED |
| FD-005 | Model motion states separately from operating constraints | ACCEPTED |
| FD-006 | Return explainable drive decisions instead of only target watts | ACCEPTED |
| FD-007 | Represent telemetry freshness and quality explicitly | ACCEPTED |
| FD-008 | Use time-normalized slew rates | ACCEPTED |
| FD-009 | Use Cycle Analyst V3 as a mandatory control intermediary | SUPERSEDED |
| FD-010 | Use direct digital communication with a headless motor controller | PROVISIONAL |
| FD-011 | Use ESP32-S3 as the initial prototype computer | SUPERSEDED |
| FD-012 | Use nRF54L15 DK as the initial prototype computer | ACCEPTED |
| FD-013 | Use a conventional RTOS for the embedded runtime | SUPERSEDED |
| FD-014 | Investigate RTIC as the embedded concurrency model | PROVISIONAL |
| FD-015 | Prefer heapless and fixed-capacity data structures | PROVISIONAL |
| FD-016 | Use Bafang G310 as the initial motor target | SUPERSEDED |
| FD-017 | Use Grin V3 Rear All-Axle 6T as the target motor | ACCEPTED |
| FD-018 | Treat energy flow as bidirectional | ACCEPTED |
| FD-019 | Use the integrated freehub torque and PAS sensors for rider input | ACCEPTED |
| FD-020 | Calculate hub-side rider power from freehub torque and rotation | PROVISIONAL |
| FD-021 | Broadcast rider-power data to cycling head units over BLE | PROVISIONAL |
| FD-022 | Treat ANT+ as a future investigation | ON HOLD |
| FD-023 | Investigate controlled negative torque for outdoor training resistance | EXPLORATORY |
| FD-024 | Preserve independent brake, controller, and battery protections | ACCEPTED |

---

## FD-001: Use Rust for the Control Core

**Status:** `ACCEPTED`

### Decision

Ferrous Drive will use Rust for its portable control core and intended embedded implementation.

### Why

Rust supports the project’s goals around:

- Explicit data modelling
- Strong type safety
- Exhaustive state handling
- Embedded development
- Hardware-independent logic
- Deterministic testing
- Clear handling of missing or invalid data

The project is also intended to provide a practical and enjoyable environment for learning embedded Rust.

### Consequences

- The core should be designed to compile separately from platform-specific hardware code.
- Public types should be explicit and readable.
- Unsafe code should be avoided unless it has a clear hardware-related justification.
- Rust tooling, formatting, linting, and tests should become part of the contribution workflow.

---

## FD-002: Separate Domain Logic from Hardware Adapters

**Status:** `ACCEPTED`

### Decision

The control core will remain independent of:

- Microcontroller peripheral registers
- Wireless stack implementations
- Controller-specific frame formats
- Desktop replay file formats
- Logging transports
- Hardware-specific timers

### Why

The same control behaviour should be testable in:

```text
Desktop Replay
Embedded Prototype
Future Hardware Platforms
```

without rewriting the rider-assistance and energy-flow logic.

### Consequences

The architecture is divided conceptually into:

```text
Platform and Protocol Adapters
            ↓
Trusted Domain Inputs
            ↓
Ferrous Drive Core
            ↓
Controller-Independent Request
            ↓
Hardware-Specific Driver
```

---

## FD-003: Develop Through Simulation and Deterministic Replay

**Status:** `ACCEPTED`

### Decision

The desktop replay simulator will be the primary environment for developing and validating control behaviour.

### Why

A moving bicycle should not be the first place where new control logic is debugged.

Replay provides:

- Repeatability
- Regression testing
- Fault injection
- Decision inspection
- Profile comparison
- Safer iteration
- A bridge between recorded rides and implementation changes

### Consequences

The simulator must eventually support:

- Recorded ride data
- Synthetic scenarios
- Sensor dropouts
- Stale measurements
- Brake events
- Controller communication faults
- Thermal rollback
- Assist-to-regeneration transitions
- Decision-trace export

---

## FD-004: Preserve Project Reasoning in the Repository

**Status:** `ACCEPTED`

### Decision

Important engineering context must live in the repository rather than only in AI conversations, personal notes, or memory.

### Why

Ferrous Drive began through AI-assisted exploration and iterative review. Without explicit documentation, future contributors would see the finished code but not understand:

- Why an architecture was selected
- Which alternatives were rejected
- Which values are placeholders
- Which capabilities are proven
- Which assumptions remain open
- Why earlier decisions were changed

### Consequences

The repository maintains:

- `project_origin.md`
- `decision_log.md`
- `assumptions.md`
- `validation_matrix.md`
- `architecture.md`
- `CHANGELOG.md`
- GitHub issues and pull requests

Superseded decisions should remain visible rather than being silently deleted.

---

## FD-005: Separate Motion State from Operating Constraints

**Status:** `ACCEPTED`

### Decision

The motion state machine will describe movement, while safety and operating restrictions will be represented separately.

Current conceptual motion states are:

```text
Stationary
Launching
Cruising
Coasting
```

Braking is observed as rider intent and acts as a high-priority override.

### Why

The following are constraints, not motion states:

- Brake input
- Thermal rollback
- Battery limits
- Speed limits
- Controller faults
- Communications health
- Regeneration availability
- Ride-profile limits

Combining all these concerns inside one finite-state machine would create unnecessary state combinations.

### Consequences

The architecture should support combinations such as:

```text
Cruising + Assist
Cruising + Neutral
Coasting + Regenerate
Braking + Regenerate
Braking + Inhibit
```

without creating a separate FSM state for every combination.

---

## FD-006: Produce Explainable Drive Decisions

**Status:** `ACCEPTED`

### Decision

The domain core should return a structured decision rather than only a scalar target-watt value.

### Why

A single number cannot explain:

- Which rider-input source was used
- Which motion state was active
- Which constraints were applied
- Why assistance was reduced
- Why regeneration was inhibited
- Whether telemetry confidence affected the result
- Whether communications were healthy

### Consequences

A future drive decision may include:

```text
Motion State
Energy-Flow Mode
Rider Input Source
Measured Hub-Side Rider Power
Requested Energy Flow
Active Constraints
Final Controller Request
Decision Reason
Telemetry Confidence
Controller Health
```

The exact Rust API remains to be designed.

---

## FD-007: Represent Telemetry Quality Explicitly

**Status:** `ACCEPTED`

### Decision

Sensor presence and sensor trust will be represented separately.

Potential quality states are:

```text
VALID
AGING
STALE
INVALID
```

### Why

A measurement can be present while still being:

- Old
- Frozen
- Implausible
- Delayed
- Inconsistent
- Unsafe for control

Plain `Option<T>` is useful for representing absence but is insufficient as the complete telemetry trust model.

### Consequences

Trusted measurements should eventually include:

```text
Value
Capture Time
Age
Quality
Source
Calibration Revision
```

Derived values such as rider power must carry their own confidence.

---

## FD-008: Use Time-Normalized Slew Rates

**Status:** `ACCEPTED`

### Decision

Power and torque ramp limits will be expressed per unit of time rather than per control-loop tick.

### Why

A value such as:

```text
5 watts per tick
```

changes physical behaviour when scheduler frequency changes.

A value such as:

```text
100 watts per second
```

can be applied consistently in both desktop replay and embedded execution.

### Consequences

Control calculations must use measured elapsed time and reject invalid timing intervals.

---

## FD-009: Use Cycle Analyst V3 as a Mandatory Intermediary

**Status:** `SUPERSEDED`

### Original Decision

The first architecture routed ESP32-generated PWM through analog conditioning into a Cycle Analyst V3 auxiliary input.

```text
Control Computer
      ↓
PWM
      ↓
RC Filter and Level Conversion
      ↓
Cycle Analyst V3
      ↓
Motor Controller
```

### Why It Originally Made Sense

The Cycle Analyst offered an established place for:

- Brake handling
- Thermal behaviour
- Controller interfacing
- Display and configuration

### Why It Was Superseded

The architecture introduced:

- Analog conversion
- Filter tuning
- Additional wiring
- Noise sensitivity
- An extra device dependency
- Split configuration ownership
- A display even though the intended build is headless

### Replacement

See FD-010.

---

## FD-010: Use Direct Digital Controller Communication

**Status:** `PROVISIONAL`

### Decision

Ferrous Drive will target a headless Grin controller through a hardware-specific digital driver.

```text
Ferrous Drive Core
      ↓
Controller-Independent Request
      ↓
Grin Controller Driver
      ↓
Digital Transport
      ↓
Headless Grin Controller
```

### Why

This better matches the intended clean, display-free bicycle installation and preserves the controller-independent domain boundary.

### Remaining Questions

- Exact controller model
- Exact firmware
- Writable command interface
- Telemetry format
- Command acknowledgements
- Watchdog behaviour
- Communications timeout
- Assist command semantics
- Regeneration command semantics
- Safe-state behaviour

### Consequences

Direct digital control must not be described as supported until these questions are resolved through documentation or bench evidence.

---

## FD-011: Use ESP32-S3 as the Initial Prototype Computer

**Status:** `SUPERSEDED`

### Original Decision

ESP32-S3 was selected as the intended control computer because the project required wireless fitness-sensor integration.

### Why It Was Superseded

An nRF54L15 development kit became available, and the Nordic platform aligns more naturally with the project’s wireless and deterministic embedded direction.

### Replacement

See FD-012.

---

## FD-012: Use nRF54L15 DK as the Initial Prototype Computer

**Status:** `ACCEPTED`

### Decision

The Nordic nRF54L15 DK will be the initial embedded prototype platform.

### Why

The available development kit provides a practical platform for:

- Bluetooth Low Energy
- Embedded Rust experimentation
- Fitness-sensor integration
- Event-driven control
- Low-power wireless development
- Hardware bring-up without designing a custom PCB

### Consequences

- The architecture must remain portable rather than embedding nRF54L15 assumptions into the domain core.
- Peripheral and BLE support must be validated.
- Memory, timing, and runtime capabilities must be measured.
- The development kit is a prototype platform, not yet the final bike-mounted hardware.

---

## FD-013: Use a Conventional RTOS

**Status:** `SUPERSEDED`

### Original Decision

Early plans assumed a conventional RTOS-style architecture with independent tasks for:

- Control updates
- Controller telemetry
- Wireless sensors
- Diagnostics
- Logging

### Why It Was Superseded

Peer review suggested that Ferrous Drive is better described as a bounded, event-driven real-time system than as a collection of long-running threads.

### Replacement

See FD-014.

---

## FD-014: Investigate RTIC as the Embedded Concurrency Model

**Status:** `PROVISIONAL`

### Decision

Ferrous Drive will prototype an RTIC-based runtime before adopting a conventional RTOS.

### Why

The project naturally reacts to events such as:

- Torque samples
- PAS edges
- Brake inputs
- Controller frames
- Control timers
- Communications timeouts
- Wireless updates

RTIC may provide a clearer and more deterministic mapping between those events and the required work.

### Remaining Questions

- nRF54L15 support
- HAL compatibility
- BLE-stack integration
- Task priorities
- Worst-case execution time
- Interrupt load
- Resource sharing
- Logging behaviour
- Debugging workflow

### Consequences

RTIC should remain outside the portable domain core.

This decision becomes fully accepted only after a working prototype demonstrates that the required peripherals and wireless stack can coexist reliably.

---

## FD-015: Prefer Heapless and Fixed-Capacity Data

**Status:** `PROVISIONAL`

### Decision

The embedded prototype will prefer fixed-capacity data structures and avoid requiring a dynamic allocator.

### Why

This supports:

- Known memory bounds
- Explicit overflow handling
- Reduced allocator complexity
- More predictable embedded behaviour
- Easier resource budgeting

### Consequences

Potential embedded structures include bounded:

- Queues
- Vectors
- Strings
- Telemetry buffers
- Decision traces
- Controller frame buffers

Capacity choices must be documented and validated rather than chosen arbitrarily.

Desktop tooling may still use standard dynamically allocated collections where appropriate.

---

## FD-016: Use Bafang G310 as the Initial Motor

**Status:** `SUPERSEDED`

### Original Decision

The Bafang G310 geared hub motor was selected as a lightweight commuter motor.

### Why It Originally Made Sense

The G310 offered:

- Low mass
- Compact packaging
- Efficient freewheeling
- Suitable commuting assistance
- Compatibility with the original Baserunner concept

### Why It Was Superseded

The planned bicycle and project direction increasingly valued:

- Regenerative braking
- Bidirectional energy flow
- Integrated rider-input sensing
- Hub-side torque measurement
- Fitness-data generation
- Controlled negative torque
- Expanded experimentation

These capabilities better match a direct-drive motor.

### Replacement

See FD-017.

---

## FD-017: Use Grin V3 Rear All-Axle 6T

**Status:** `ACCEPTED`

### Decision

The current target motor is the Grin V3 rear All-Axle with the 6T standard winding and torque-sensing freehub configuration.

### Why

The motor provides a stronger match for the emerging project direction:

- Direct-drive operation
- Regenerative capability
- Integrated motor-temperature sensing
- Integrated freehub torque sensing
- Quadrature PAS sensing
- Digital-controller integration
- Bidirectional energy experiments
- Hub-side rider-power calculation

### Consequences

The project must account for:

- Direct-drive drag
- Regeneration behaviour
- Battery charge acceptance
- Motor thermal behaviour
- Motor-controller thermal behaviour
- Negative torque
- Freehub torque calibration
- PAS timing
- Different ride feel from a geared hub

The exact installation and controller pairing must be documented separately.

---

## FD-018: Treat Energy Flow as Bidirectional

**Status:** `ACCEPTED`

### Decision

Ferrous Drive will model energy flow in both directions.

```text
ASSIST
NEUTRAL
REGENERATE
INHIBIT
```

### Why

The direct-drive motor and controller system can potentially provide both propulsion and regenerative resistance.

The previous one-way model:

```text
Battery → Motor → Wheel
```

is no longer sufficient.

The architecture must support:

```text
Battery ↔ Controller ↔ Motor ↔ Wheel
```

### Consequences

The decision model must represent:

- Positive torque
- Neutral torque
- Negative torque
- Inhibited output

Regeneration also introduces new constraints:

- Battery-full behaviour
- Charge-current limits
- Low-speed effectiveness
- Regen thermal limits
- Controller availability
- Safe brake release
- Communications faults

---

## FD-019: Use Integrated Freehub Torque and PAS for Rider Input

**Status:** `ACCEPTED`

### Decision

The integrated freehub torque sensor and quadrature PAS signal will be the primary rider-input source for the target Grin motor configuration.

### Why

This removes the requirement for a separate crank, pedal, or spider power meter and gives the motor system a second purpose as part of the fitness-sensing platform.

Torque and rotation are both required.

A torque value alone must not trigger assistance without valid pedalling rotation.

### Consequences

The telemetry model must include:

```text
Freehub Torque
Freehub Rotational Speed
Pedalling Direction
Signal Quality
Capture Time
Calibration
```

The freehub measurement location must remain visible in naming and documentation.

---

## FD-020: Calculate Hub-Side Rider Power

**Status:** `PROVISIONAL`

### Decision

Ferrous Drive will investigate calculating rider power at the rear hub from measured freehub torque and angular velocity.

```text
Power = Torque × Angular Velocity
```

### Why

The target hardware provides dedicated torque and PAS signals at the freehub.

This is preferable to estimating rider power indirectly from:

- Vehicle acceleration
- Motor contribution
- Aerodynamic drag
- Road gradient
- Rolling resistance

### Important Limitation

The direct calculated quantity is:

```text
Hub-Side Rider Power
```

It is not automatically equivalent to:

```text
Crank Power
Pedal Power
```

because drivetrain losses occur before the measurement point.

### Validation Required

- Zero-offset calibration
- Scale validation
- Temperature drift
- Dynamic response
- Gear-range testing
- Cadence-range testing
- Comparison with a reference power meter
- Seated and standing efforts
- Repeatability between rides

### Consequences

The project must not claim fitness-grade accuracy until comparative testing supports that claim.

---

## FD-021: Broadcast Rider Power over BLE

**Status:** `PROVISIONAL`

### Decision

Ferrous Drive will target BLE cycling-power broadcasting to compatible cycling head units.

### Why

This increases the utility of the motor and torque-sensor investment by allowing the bicycle to act as a virtual power source for:

- Training display
- Ride recording
- Power zones
- Work totals
- Post-ride analysis

### Remaining Questions

- BLE Cycling Power Service mapping
- Update rate
- Power smoothing
- Cadence mapping
- Head-unit compatibility
- Calibration metadata
- Dropout recovery
- Zero-power reporting
- Quality or confidence diagnostics

### Consequences

The broadcast value should initially be described as hub-side or estimated rider power rather than as certified crank power.

---

## FD-022: Treat ANT+ as a Future Investigation

**Status:** `ON HOLD`

### Decision

BLE is the initial fitness-broadcast target.

ANT+ remains a future investigation rather than a current commitment.

### Why

The project has not yet established:

- A suitable ANT+ software stack
- Licensing requirements
- nRF54L15 integration details
- BLE and ANT+ coexistence
- Head-unit interoperability

### Consequences

Documentation should not claim ANT+ support.

The architecture should avoid preventing future ANT+ support.

---

## FD-023: Investigate Outdoor ERG-Like Resistance

**Status:** `EXPLORATORY`

### Decision

Ferrous Drive will preserve the possibility of using controlled regenerative torque to create an ERG-like outdoor training mode.

### Concept

```text
Target Rider Power
        -
Measured Hub-Side Power
        ↓
Power Error
        ↓
Bounded Negative Torque
```

Potential uses include:

- Low-speed intervals
- Virtual climbing resistance
- Cadence drills
- Increased training load without increased road speed

### Why It Is Only Exploratory

This feature introduces significant safety and control questions:

- Loop stability
- Sudden resistance changes
- Rider surprise
- Loss of communications
- Battery charge acceptance
- Low-speed behaviour
- Cornering
- Loose or wet surfaces
- Traffic interaction
- Mechanical-brake interaction
- Safe disengagement
- Legal implications

### Consequences

Outdoor ERG-like control must pass:

```text
Simulation
    ↓
Bench Test
    ↓
Wheel-Off-Ground Test
    ↓
Controlled Closed-Course Test
```

before any normal road use is considered.

No ridden implementation should begin from this decision alone.

---

## FD-024: Preserve Independent Hardware Protections

**Status:** `ACCEPTED`

### Decision

Ferrous Drive must not replace or bypass independent safety protections.

These include:

- Mechanical brakes
- Brake cut-off paths
- Battery-management protection
- Controller overcurrent protection
- Controller thermal protection
- Motor thermal protection
- Hardware watchdogs where available

### Why

Software running on the prototype computer is not an acceptable single point of protection for all propulsion hazards.

### Consequences

- Brake intent must immediately suppress positive torque.
- Regen must never be treated as the only braking mechanism.
- Controller faults must result in zero or safe output.
- Communications loss must have a defined controller-side response.
- Hardware protections must remain active during development and testing.

---

## Current Architecture Direction

The accepted and provisional decisions currently produce this stack:

```text
Recorded Ride and Synthetic Data
              ↓
Desktop Replay Simulator
              ↓
Portable Ferrous Drive Core
              ↓
nRF54L15 Prototype
              ↓
RTIC Runtime Investigation
              ↓
Heapless Data Strategy
              ↓
Digital Grin Controller Driver
              ↓
Headless Grin Controller
              ↓
Grin V3 Rear All-Axle 6T
```

The associated sensing and feedback loop is:

```text
Freehub Torque and PAS
              ↓
Telemetry Trust
              ↓
Hub-Side Rider Power
              ↓
Assist, Neutral, or Regen Decision
              ↓
Motor Controller
              ↓
Wheel Response
              ↓
Decision Trace and Fitness Broadcast
```

---

## Next Decisions Expected

The following decisions are likely to be needed next:

- Exact Grin controller model
- Digital controller protocol strategy
- Torque-sensor electrical interface
- PAS capture method
- ADC conditioning and calibration
- RTIC task and priority model
- BLE stack integration
- BLE cycling-power data model
- Signed versus explicit assist/regen request types
- Regen safety limits
- Battery charge-acceptance model
- Rider-power validation method
- Outdoor ERG research boundaries
- Final prototype wiring architecture

Each should be added as a new entry rather than silently changing an earlier decision.

---

## Decision-Logging Rule

When a significant change is accepted:

1. Add a new decision entry.
2. Mark any replaced decision as `SUPERSEDED`.
3. Explain why the earlier decision made sense.
4. Link the replacement decision.
5. Update architecture and assumptions.
6. Update the validation matrix.
7. Update the changelog.
8. Add tests or validation work when implementation begins.

The purpose of this log is not to avoid changing direction.

The purpose is to make changing direction understandable.
