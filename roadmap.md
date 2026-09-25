> 📚 [README](../README.md) · 📐 [Architecture](architecture.md) · 🔋 [Battery Design](battery_design.md) · 🛑 [Regenerative Braking](regenerative_braking.md) · 📝 [Decision Log](decision_log.md)

# Roadmap

Ferrous Drive follows an evidence-driven roadmap rather than a date-driven
feature schedule.

A feature progresses from idea to trusted behaviour only when it has passed the
appropriate design, simulation, bench, controlled-motion, and route-validation
gates.

```text
IDEA
    ↓
DESIGNED
    ↓
SIMULATED
    ↓
BENCH TESTED
    ↓
CONTROLLED MOTION
    ↓
ROUTE TESTED
```

> [!WARNING]
> Hardware road testing is out of scope until the relevant sensing, torque,
> battery, fault, and mechanical-independence gates have passed.

---

## Roadmap Principles

- Safety before feature completeness
- Simulation before moving hardware
- Deterministic live control before adaptive control
- Capability-based support rather than hidden assumptions
- Rider authority over motor authority
- Independent mechanical braking
- Explainable torque decisions
- Measured evidence before accepted calibration
- Small reversible steps
- Public documentation without exposing private route locations

---

## Current Position

Ferrous Drive is currently between **Designed** and **Simulated**.

### Established direction

- Rust-based controller-independent core
- Simulation-first workflow
- Positive, zero, and negative torque model
- Four rider-intent operating modes
- Dual geared-hub and direct-drive comparison
- 10S2P P50B battery baseline
- Rider-triggered regenerative-braking architecture
- Speed-dependent regen relationship
- Independent hydraulic braking
- Recorded commute route baseline

### Still provisional

- Controller protocol and update rate
- Geared-hub candidate
- BMS selection
- Brake Hall sensor implementation
- IMU selection and placement
- Regen speed map and current limits
- HRV sensor, metric, and confidence framework
- Rider-power estimation accuracy
- Battery enclosure and mounting

---

# Phase 0: Documentation Baseline

**Goal:** Align the project around one coherent rider-first architecture.

## Completed or drafted

- [x] Reframe Ferrous Drive as a rider-intent and energy-management platform
- [x] Define Neutral Ride, Active Recovery, Commute, and Tempo
- [x] Remove High Assist from current scope
- [x] Define geared and direct-drive capability paths
- [x] Define bidirectional torque arbitration
- [x] Draft regenerative-braking architecture
- [x] Add speed-dependent regen relationship
- [x] Update architecture documentation
- [x] Update battery-design documentation
- [x] Update main README
- [x] Update changelog
- [x] Update decision log
- [x] Update roadmap

## Remaining

- [ ] Update assumptions register
- [ ] Update validation matrix
- [ ] Update documentation navigation consistently
- [ ] Check Markdown and Mermaid rendering on GitHub
- [ ] Remove duplicate or superseded terminology

## Exit criteria

- All top-level documents describe the same modes, motor paths, battery, and
  braking boundaries.
- Provisional values are clearly identified.
- No document presents GPX-inferred recovery as measured performance.

---

# Phase 1: Route Replay and Plant Model

**Goal:** Convert the current commute data into reproducible simulation inputs.

## Route work

- [ ] Sanitize precise start and end coordinates
- [ ] Preserve outbound and return as separate scenarios
- [ ] Convert GPX into a distance, elevation, speed, temperature, and event trace
- [ ] Reject GPS speed outliers deterministically
- [ ] Define elevation-smoothing method
- [ ] Classify provisional descent, junction, mixed, and unclassified events

## Bicycle plant

- [ ] Model aerodynamic drag
- [ ] Model rolling resistance
- [ ] Model gradient energy
- [ ] Model acceleration energy
- [ ] Model configured system mass
- [ ] Model direct-drive magnetic drag
- [ ] Model rider power as an input trace or scenario band

## Battery plant

- [ ] Model 10S2P voltage and usable-energy envelope
- [ ] Model charge and discharge current limits
- [ ] Model voltage rollback near full charge
- [ ] Model temperature-dependent capability
- [ ] Model bidirectional current and net energy

## Exit criteria

- A replay is deterministic.
- Outbound and return results are independently reproducible.
- Energy is separated into aerodynamic, rolling, climbing, acceleration,
  magnetic drag, propulsion, and regen terms.
- All assumptions are emitted in the simulation output.

---

# Phase 2: Drive Capability and Adapter Model

**Goal:** Prove that one core supports materially different drive systems.

## Common interface

- [ ] Define drive capability structure
- [ ] Define positive, zero, and negative torque request types
- [ ] Define controller status and fault types
- [ ] Define motor speed and temperature telemetry
- [ ] Define battery-current telemetry
- [ ] Define stale and invalid communication behaviour

## Geared-hub simulation adapter

- [ ] Forward assistance only
- [ ] Freewheel when inactive
- [ ] No regen capability
- [ ] Candidate efficiency map
- [ ] Candidate torque and thermal limits

## Direct-drive simulation adapter

- [ ] Positive and negative torque
- [ ] Published magnetic-drag model
- [ ] Motor-speed and thermal feedback
- [ ] Regen current and voltage limits
- [ ] Virtual freewheeling capability

## Exit criteria

- The same route and mode scenario runs with both adapters.
- Unsupported regen cannot be requested through the geared adapter.
- Every final request reports the active capability and limiting condition.

---

# Phase 3: Operating-Mode Simulation

**Goal:** Make rider intent explicit and testable.

## Neutral Ride

- [ ] Compensate direct-drive drag
- [ ] Compensate added-system rolling cost
- [ ] Compensate added-system climbing cost
- [ ] Do not compensate aerodynamic drag or headwind
- [ ] Preserve explicit regen braking

## Active Recovery

- [ ] Implement provisional 50–100 W rider range
- [ ] Define personal HRV baseline interface
- [ ] Define HRV confidence and fallback
- [ ] Add HR and cardiac-drift guardrails
- [ ] Keep internal load primary and speed secondary

## Commute

- [ ] Implement provisional 100–200 W rider range
- [ ] Protect configurable arrival reserve
- [ ] Respond to gradient, wind, and acceleration
- [ ] Avoid fixed-speed climbing behaviour

## Tempo

- [ ] Implement provisional 200–300 W rider range
- [ ] Define adaptive sweet-spot band
- [ ] Reward sustained rather than instantaneous effort
- [ ] Taper assistance above the target band
- [ ] Add physiological-strain protection

## Exit criteria

- Each mode produces a distinct, explainable policy.
- Modes cannot bypass battery, thermal, braking, or communication limits.
- Invalid physiological data produces a deterministic fallback.

---

# Phase 4: Regenerative-Braking Simulation

**Goal:** Validate the first-generation braking state machine and speed map.

## Brake intent

- [ ] Model independent left and right binary sensors
- [ ] Model activation, release, debounce, invalid, and disconnected states
- [ ] Ensure coasting never becomes braking without explicit intent
- [ ] Ensure brake intent immediately prohibits positive torque

## State machine

- [ ] Pedalling
- [ ] Coasting
- [ ] Brake entry
- [ ] Regen braking
- [ ] Mechanical-only fallback
- [ ] Low-speed handoff
- [ ] Brake release
- [ ] Stopped
- [ ] Fault

## Speed-dependent map

- [ ] Define provisional `v_stop`
- [ ] Define provisional `v_taper_end`
- [ ] Define provisional `v_high`
- [ ] Implement continuous interpolation
- [ ] Add engagement ramp
- [ ] Add high-speed ceiling
- [ ] Add bounded feedback trim
- [ ] Add threshold hysteresis

## Constraints

- [ ] Near-full battery
- [ ] Cold battery
- [ ] BMS charge prohibited
- [ ] Motor temperature
- [ ] Controller temperature
- [ ] Wheel-speed invalid
- [ ] IMU invalid
- [ ] Communication stale
- [ ] Rear-wheel response implausible

## Exit criteria

- No positive and negative torque overlap.
- Regen is zero below the configured useful-speed region.
- The map is continuous across region boundaries.
- All fault cases preserve mechanical braking.
- Shadow learning cannot change live commands.

---

# Phase 5: Electronics and Sensor Bench

**Goal:** Validate sensing and command paths without a moving bicycle.

## Embedded platform

- [ ] Bring up nRF54L15 development hardware
- [ ] Establish bounded task and message architecture
- [ ] Implement watchdog and fault reporting
- [ ] Validate monotonic timing and signal freshness

## Brake sensors

- [ ] Select Hall sensor and magnet
- [ ] Design lever-specific, non-invasive carrier
- [ ] Confirm no interference with braking or shifting
- [ ] Test activation, release, debounce, and disconnect
- [ ] Test rain, cold, vibration, and magnet displacement

## IMU and wheel speed

- [ ] Select IMU
- [ ] Define mounting location and orientation
- [ ] Validate wheel-speed input and update rate
- [ ] Validate acceleration and pitch filtering
- [ ] Define signal-quality metrics

## Controller path

- [ ] Verify deterministic zero-torque command
- [ ] Verify bounded positive request
- [ ] Verify bounded negative request on a compatible bench setup
- [ ] Verify communication timeout and watchdog behaviour

## Exit criteria

- A brake event is detected reliably.
- Invalid sensing produces the defined conservative state.
- No sensor hardware obstructs mechanical lever travel.
- The controller command path is bounded and observable.

---

# Phase 6: Battery Mechanical Prototype

**Goal:** Validate packaging before assembling an energized pack.

## CAD and inert pack

- [ ] Model maximum P50B dimensions
- [ ] Validate `7 + 6 + 7` arrangement
- [ ] Allocate BMS, fuse, sensing, and routing cavity
- [ ] Check frame clearance and removal path
- [ ] Produce safe dummy-cell prototype
- [ ] Measure structural mass

## Enclosure and mounting

- [ ] Design printed polymer cell carrier
- [ ] Define terminal and inter-layer barriers
- [ ] Define continuous isolation from carbon shell
- [ ] Define reinforced mounting spine
- [ ] Define positive and secondary retention
- [ ] Check vibration and impact load paths

## Exit criteria

- Cells are not structural members.
- Carbon cannot contact live conductors or cell cans.
- Mounting and removal are deliberate and secure.
- Protection is not removed to meet mass targets.

---

# Phase 7: Instrumented Battery Bench

**Goal:** Establish real bidirectional pack limits.

## Battery hardware

- [ ] Select and review BMS
- [ ] Confirm common-port or separate-port topology
- [ ] Confirm fuse, connector, interconnect, and current sensor
- [ ] Validate group-voltage monitoring
- [ ] Validate charge and discharge permission
- [ ] Validate at least two temperature channels

## Discharge testing

- [ ] Controlled low-current discharge
- [ ] Assistance-level transient testing
- [ ] Voltage sag and thermal characterization
- [ ] Controller rollback before BMS protection

## Regen testing

- [ ] Controlled charge-current injection
- [ ] Pack-voltage rise
- [ ] Cell-group overvoltage margin
- [ ] Cold and hot battery limits
- [ ] Near-full rollback
- [ ] Abrupt charge-permission loss
- [ ] Connector and interconnect temperature
- [ ] Bidirectional energy accounting

## Exit criteria

- Validated pack-level discharge limit
- Validated provisional regen-current limit
- Validated voltage rollback and cutoff
- Defined full and cold battery fallback
- No reliance on abrupt BMS disconnect during normal control

---

# Phase 8: Lifted-Wheel Integration

**Goal:** Validate complete electrical state transitions without road load.

- [ ] Positive torque to zero torque
- [ ] Zero torque to soft negative torque
- [ ] Speed-dependent regen request
- [ ] Low-speed taper
- [ ] Brake release
- [ ] Pedalling restart
- [ ] Regen unavailable fallback
- [ ] Communication timeout
- [ ] Sensor failure
- [ ] Energy and event logging

## Exit criteria

- State transitions are deterministic and logged.
- Brake intent always overrides positive torque.
- Assistance cannot restart until braking is released and restart conditions are
  valid.

---

# Phase 9: Controlled Rolling Test

**Goal:** Validate low-energy physical response in a controlled environment.

- [ ] Straight dry surface
- [ ] Conservative speed and torque limits
- [ ] Multiple entry speeds
- [ ] IMU and wheel-speed agreement
- [ ] Hydraulic braking unaffected
- [ ] Low-speed mechanical handoff
- [ ] Regen unavailable scenario
- [ ] High state-of-charge scenario
- [ ] Cold-battery scenario
- [ ] Sensor and communication fault scenarios

## Exit criteria

- Regen engagement and release are smooth.
- The rider can always add mechanical braking directly.
- Unexpected negative torque is not observed.
- Measured response remains within the validated envelope.

---

# Phase 10: Closed-Course Validation

**Goal:** Validate repeatability, blending, and conservative environmental cases.

- [ ] Repeated junction stops
- [ ] Sustained descents
- [ ] Different gradients
- [ ] Wet-surface conservative testing
- [ ] Rough-road and vibration testing
- [ ] Strong front-brake use
- [ ] Rear-wheel plausibility response
- [ ] Battery and thermal rollback
- [ ] Mode-independent braking consistency

## Exit criteria

- Mechanical braking remains authoritative.
- Regen reduction is predictable when constraints activate.
- No ride mode changes immutable braking limits.
- Braking events can be reconstructed from logs.

---

# Phase 11: Route Validation

**Goal:** Compare commute predictions with measured physical performance.

## Energy

- [ ] Outbound propulsion energy
- [ ] Return propulsion energy
- [ ] Descent regen
- [ ] Junction regen
- [ ] Gross and net battery energy
- [ ] Neutral Ride compensation
- [ ] Arrival reserve

## Modes

- [ ] Active Recovery internal-load response
- [ ] Commute reserve protection
- [ ] Tempo sweet-spot support
- [ ] Neutral Ride base-bicycle feel

## Environmental

- [ ] Calm conditions
- [ ] Headwind
- [ ] Cold weather
- [ ] Wet conditions
- [ ] Different rider fatigue states

## Exit criteria

- Prediction error is quantified.
- GPX-inferred regen values are replaced by measured electrical data.
- Outbound and return remain separately calibrated.
- The model explains significant deviations.

---

# Phase 12: Adaptive Features

**Goal:** Promote learning only after deterministic control is trusted.

## Shadow analysis

- [ ] Rider-specific regen comfort recommendations
- [ ] HRV readiness baseline
- [ ] Active Recovery multiplier recommendations
- [ ] Tempo sweet-spot recommendations
- [ ] Motor and route calibration

## Promotion process

Every learned suggestion requires:

1. Sufficient valid data
2. Offline review
3. Replay simulation
4. Regression testing
5. Bench validation where applicable
6. Controlled ride validation
7. Explicit promotion into deterministic configuration

## Deferred live adaptation

- Online braking-map changes
- Learned safety limits
- Autonomous braking
- Learned BMS limits
- Learned fault handling

---

# Long-Term Direction

Potential future work, subject to evidence:

- Proportional brake-lever sensing
- Descent Hold with explicit rider arming
- Controller-agnostic Garmin rider-power broadcast
- Improved real-time rider-power inference
- Wider controller support
- Alternative battery sizes
- Open route and telemetry datasets with privacy protection
- Contributor hardware-in-the-loop fixtures
- Formal safety and regulatory assessment

---

# Current Next Steps

1. Align assumptions and validation documents with the updated architecture.
2. Turn the commute GPX data into a sanitized deterministic replay format.
3. Define the controller-independent torque and capability interfaces in Rust.
4. Implement simulated geared and direct-drive adapters.
5. Implement the regen state machine and provisional continuous speed map.
6. Select the BMS, brake Hall sensor, IMU, and controller communication path.
7. Keep all moving-hardware work behind documented validation gates.
