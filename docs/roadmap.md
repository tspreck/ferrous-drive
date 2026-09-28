# Roadmap

> Build the smallest validated layer, then move outward.

Ferrous Drive uses validation gates rather than calendar promises.

```text
Idea
    ↓
Designed
    ↓
Simulated
    ↓
Bench tested
    ↓
Controlled motion
    ↓
Route tested
    ↓
Validated
```

## Guiding Priorities

```text
1. Safety and braking
2. Trusted telemetry
3. Battery and hardware constraints
4. Destination arrival reserve
5. Physiological intent
6. Earned reward
7. Journey speed
```

---

## M1: First Green Rust Build

### Goal

Create the first executable Ferrous Drive foundation.

### Deliverables

- [ ] Root Cargo workspace
- [ ] `ferrous-drive-core`
- [ ] `ferrous-drive-sim`
- [ ] `no_std` portable core
- [ ] `heapless` bounded signal history
- [ ] Signal-quality model
- [ ] Built-in Rust unit tests
- [ ] Desktop command-line example
- [ ] GitHub Actions build, test, formatting, and Clippy checks

### Exit Criteria

```text
cargo fmt --all --check
cargo build --workspace --all-targets
cargo test --workspace --all-targets
cargo clippy --workspace --all-targets -- -D warnings
```

All checks pass locally and in GitHub Actions.

### Out of Scope

- Hardware bring-up
- HRV
- Modes
- Motor communication
- Braking
- Battery plant modelling

---

## M2: Trusted Telemetry Foundation

### Goal

Create the common trust model used by every later subsystem.

### Deliverables

- [ ] Generic timestamped measurement
- [ ] `Valid`, `Aging`, `Stale`, and `Invalid`
- [ ] Plausibility checks
- [ ] Fixed-capacity histories
- [ ] Explicit overflow policies
- [ ] Authoritative state for safety-critical signals
- [ ] Decision trace for rejected inputs

### Exit Criteria

- Threshold and boundary tests pass
- Stale and invalid values cannot authorize control
- Critical state cannot be silently lost

---

## M3: Three-Mode Domain Model

### Goal

Represent Neutral, Recovery, and Training without motor hardware.

### Deliverables

- [ ] `RideMode` domain type
- [ ] Journey context separated from mode
- [ ] Neutral intent
- [ ] Recovery intent
- [ ] Training profile identity
- [ ] Mode transition rules
- [ ] Explainable mode state

### Exit Criteria

- Commute exists only as journey context
- Mode transitions are deterministic
- Unsupported states fail conservatively

---

## M4: Tre Pulse State Engine

### Goal

Implement the common three-segment behavioural state machine in simulation.

### Deliverables

- [ ] Accumulation
- [ ] Grace
- [ ] Decay
- [ ] Milestone locking
- [ ] Reward readiness
- [ ] Reward countdown
- [ ] Pause and constraint states
- [ ] Tre Pulse presentation state
- [ ] Explainable transitions

### Exit Criteria

- Identical input gives identical output
- Locked milestones behave predictably
- Road interruptions do not create unsafe incentives

---

## M5: Battery and Reward Budget Simulation

### Goal

Authorize optional rewards without compromising arrival reserve.

### Deliverables

- [ ] Journey budget
- [ ] Reserve budget
- [ ] Contingency budget
- [ ] Normal reward budget
- [ ] Golden reward budget
- [ ] Full, shortened, deferred, and unavailable outcomes
- [ ] Tre Pulse range state

### Exit Criteria

- Optional reward cannot breach the configured reserve
- Every constrained reward has an explanation
- Mild, cold, and adverse route scenarios are replayable

---

## M6: Recovery Simulation

### Goal

Simulate relative Zone 2 Recovery before adding physiological hardware.

### Deliverables

- [ ] Configurable Zone 2 model
- [ ] `1.0:1` baseline assistance
- [ ] Bounded adaptation toward `1.2:1`
- [ ] Synthetic HR, HRV, and power signals
- [ ] HR-drift scenario
- [ ] Signal-loss fallback
- [ ] Shallow-descent PAS behaviour
- [ ] Arrival-reserve intervention
- [ ] Recovery Tre Pulse progress

### Exit Criteria

- HRV never commands torque directly
- Invalid HRV returns support toward baseline
- Speed yields before recovery or reserve is violated

---

## M7: Training Profile Simulation

### Goal

Implement the shared work-and-reward model.

### Initial Profiles

```text
Tempo Tailwind
Anaerobic Shield
```

### Deliverables

- [ ] Profile configuration schema
- [ ] Work-phase qualification
- [ ] Grace and decay
- [ ] Commitment guard
- [ ] Milestone floors
- [ ] Reward request
- [ ] Smooth ramp-in and ramp-out
- [ ] PAS minimum-input enforcement
- [ ] Brake cancellation

### Exit Criteria

- Work remains authentic during accumulation
- Rewards begin only after valid completion
- Profile parameters are classified as experimental

---

## M8: Golden Streak Simulation

### Goal

Validate the three-cycle consistency mechanic before hardware or rider use.

### Deliverables

- [ ] Consecutive-cycle counting
- [ ] Golden third-cycle presentation
- [ ] Double-duration Super Reward
- [ ] Battery constraint handling
- [ ] Safety interruption handling
- [ ] Streak reset rules
- [ ] Accessibility state independent of colour

### Exit Criteria

- Exactly three valid cycles are required
- Peak assistance does not increase
- Safety braking does not encourage unsafe streak preservation
- Triple duration remains disabled

---

## M9: nRF54L15 DK Bring-Up

### Goal

Prove the embedded development path without adding application complexity.

### Deliverables

- [ ] Cross-compilation
- [ ] Flash and debug
- [ ] Debug output
- [ ] Basic timer and I/O
- [ ] Fixed-capacity event flow
- [ ] Runtime compatibility result
- [ ] Embedded CI compile check

### Exit Criteria

- Board build remains isolated from the portable core
- Runtime and HAL limitations are documented

---

## M10: Brake-Sensor Bench Prototype

### Goal

Prove non-invasive brake intent on the road levers.

### Deliverables

- [ ] Lever-specific Hall sensor carrier
- [ ] Left and right independent channels
- [ ] Debounce
- [ ] Broken-wire behaviour
- [ ] Full lever return
- [ ] No shifting interference
- [ ] Water and cold exposure test

### Exit Criteria

- Sensor hardware cannot obstruct mechanical braking
- Invalid state inhibits positive torque without commanding continuous regen

---

## M11: Battery and Controller Bench Integration

### Goal

Verify bidirectional energy flow and the Grin command path.

### Deliverables

- [ ] Controller protocol and watchdog verification
- [ ] Positive torque request
- [ ] Zero torque request
- [ ] Negative torque request
- [ ] Bidirectional current measurement
- [ ] BMS charge permission
- [ ] Voltage rollback
- [ ] Thermal rollback
- [ ] Communication-loss behaviour

### Exit Criteria

- Battery and controller limits are independently enforced
- Loss of communication produces a defined safe state

---

## M12: Lifted-Wheel Regen Integration

### Goal

Validate state transitions without normal road loading.

### Deliverables

- [ ] Brake entry
- [ ] Positive-to-zero transition
- [ ] Soft negative-torque engagement
- [ ] Speed-scheduled map
- [ ] Low-speed taper
- [ ] Brake release
- [ ] Assistance restart
- [ ] Normal and Golden reward interruption

### Exit Criteria

- No regen without brake intent
- No positive torque while braking
- No abrupt positive-torque restart

---

## M13: Battery Mechanical Prototype

### Goal

Validate the `7 + 6 + 7` package before building an energized pack.

### Deliverables

- [ ] Dimensioned CAD
- [ ] Dummy-cell fit
- [ ] Printed cell carrier
- [ ] Carbon-isolation coupons
- [ ] Mounting spine
- [ ] Retention mechanism
- [ ] Removal path
- [ ] Weighed mass budget

### Exit Criteria

- Cells are not structural members
- Carbon remains electrically isolated
- Mounting and removal are practical

---

## M14: Controlled Rolling and Closed-Course Tests

### Goal

Validate braking, compensation, assistance, and fallback at low risk.

### Deliverables

- [ ] Straight-line dry braking
- [ ] Multiple entry speeds
- [ ] Regen-unavailable scenario
- [ ] Mechanical-only fallback
- [ ] Neutral compensation
- [ ] Recovery baseline support
- [ ] Reward interruption
- [ ] Wet-surface conservative tests

### Exit Criteria

- Mechanical braking remains authoritative
- Rider behaviour is predictable
- Fault outcomes match documentation

---

## M15: Instrumented Commute Validation

### Goal

Replace route assumptions with measured data.

### Required Signals

- Rider power
- Cadence
- Brake intent
- Battery voltage
- Bidirectional battery current
- Motor speed
- Motor and battery temperature
- Mode and Tre Pulse state

### Deliverables

- [ ] Outbound validation
- [ ] Return validation
- [ ] Mild-weather validation
- [ ] Cold-weather validation
- [ ] Headwind scenario
- [ ] Descent regen measurement
- [ ] Junction regen measurement
- [ ] Arrival prediction accuracy
- [ ] Recovery response

### Exit Criteria

- Predicted and measured energy difference is characterized
- Simulation inputs are recalibrated
- Battery capacity remains or is revised from measured evidence

---

## M16: Human Factors and Sports-Science Validation

### Goal

Assess whether Tre Pulse supports the intended rider behaviour.

### Deliverables

- [ ] Tre Pulse comprehension
- [ ] Day and night readability
- [ ] Colour-independent cues
- [ ] Recovery adherence
- [ ] Training-profile usability
- [ ] Golden Streak motivation
- [ ] Cognitive-load assessment
- [ ] Review of physiological interpretation

### Exit Criteria

- The interaction model is understandable
- Reward mechanics do not create unsafe riding incentives
- Sports-science claims remain appropriately bounded

---

## Deferred

The following remain outside the current roadmap until earlier gates are met:

- Triple-duration Golden reward
- Live machine-learned control
- Cloud competition and leaderboards
- Automatic workout generation
- Autonomous route-predictive braking
- Clinical or medical claims
- Production certification

## Current Focus

```text
M1: First Green Rust Build
```

The project should not implement Tre Pulse, Recovery, Training, or hardware
control before the telemetry foundation is clean, tested, and automatically
verified.
