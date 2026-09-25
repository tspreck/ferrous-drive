> 📚 [README](../README.md) · 📐 [Architecture](architecture.md) · 🔋 [Battery Design](battery_design.md) · 🛑 [Regenerative Braking](regenerative_braking.md) · 🗺️ [Roadmap](roadmap.md)

# Decision Log

This log records why major Ferrous Drive design choices exist.

It is intentionally lightweight. Detailed engineering belongs in the owning
technical document, while this file preserves the decision, rationale, status,
consequences, and conditions for revisiting it.

## Status Definitions

| Status | Meaning |
|---|---|
| `ACCEPTED` | Current project direction and safe to build further design work upon |
| `PROPOSED` | Preferred direction, but key evidence or implementation detail remains open |
| `SUPERSEDED` | Replaced by a later decision |
| `DEFERRED` | Valid idea intentionally moved outside the current scope |
| `REJECTED` | Considered and not selected |

---

## FD-001: Use Rust for the Ferrous Drive Control Platform

**Status:** `ACCEPTED`

### Decision

Use Rust for controller-independent control logic, simulation components,
hardware adapters, and embedded prototypes.

### Rationale

- Strong type system
- Explicit error handling
- Suitable embedded ecosystem
- Good fit for deterministic state machines
- Memory safety without a garbage collector
- Reusable logic between host simulation and embedded targets

### Consequences

- Hardware integrations require Rust support or a clearly bounded foreign
  interface.
- No-heap or bounded-memory patterns are preferred in safety-relevant paths.

---

## FD-002: Develop Simulation Before Moving Hardware

**Status:** `ACCEPTED`

### Decision

Make replayable simulation and explainable decision traces the primary
development path before road testing.

### Rationale

- Reduces risk
- Makes assumptions explicit
- Allows repeatable comparison of controllers and motors
- Supports regression testing
- Separates algorithm development from hardware availability

### Consequences

- Recorded and synthetic inputs should drive the same control logic used by
  hardware builds wherever practical.
- A feature is not trusted merely because it compiles or appears plausible.

---

## FD-003: Keep the Core Controller Independent

**Status:** `ACCEPTED`

### Decision

Separate rider intent, state management, constraints, torque decisions, and
telemetry from motor-controller protocol details.

### Rationale

Ferrous Drive should support different wheel and controller combinations without
rewriting the rider-level control policy.

### Consequences

- Controller drivers implement a common drive-system interface.
- Unsupported capabilities are explicit.
- Simulation uses a simulated adapter rather than bypassing the abstraction.

---

## FD-004: Use Capability-Based Drive Integration

**Status:** `ACCEPTED`

### Decision

Represent each drive system through explicit capabilities, including positive
torque, negative torque, regeneration, rider sensing, motor speed, temperature,
and battery-current telemetry.

### Rationale

A lightweight geared hub and a direct-drive Grin wheel have materially different
capabilities. One shared core must not assume that every motor can regenerate or
provide the same telemetry.

### Consequences

- A conventional freewheeling geared hub exposes no regenerative capability.
- The Grin direct-drive path may expose positive and negative torque.
- Unsupported functions are disabled by construction.

---

## FD-005: Retain Two Motor Paths During Early Design

**Status:** `PROPOSED`

### Decision

Continue theoretical and simulation comparison of:

1. A lightweight 36 V, 12 × 142 mm freewheeling geared rear hub
2. A Grin V3 Rear All-Axle 6T direct-drive rear hub with a compatible controller

### Rationale

The geared path prioritizes low mass, low drag, and conventional bicycle feel.
The Grin path prioritizes regeneration, integrated sensing, telemetry, and
bidirectional torque.

### Consequences

- No final motor selection is frozen.
- Core architecture must support both paths.
- Hardware-specific claims remain provisional until interfaces are verified.

### Revisit When

- Controller access is confirmed
- Real efficiency maps are available
- The battery and BMS are selected
- Route simulations include measured rider power and battery current

---

## FD-006: Use Rider-Intent Modes Instead of Conventional Assist Levels

**Status:** `ACCEPTED`

### Decision

Use four cyclist-oriented modes:

- Neutral Ride
- Active Recovery
- Commute
- Tempo

Remove High Assist from the current project scope.

### Rationale

The system is intended to preserve training and journey intent rather than act
as a conventional Eco, Tour, Sport, or Turbo power selector.

### Consequences

- Modes express a rider objective, not a fixed battery-power command.
- The actual motor request depends on rider contribution, physiology, route
  demand, drive capability, battery reserve, and constraints.

---

## FD-007: Define Neutral Ride as Installed-System Compensation

**Status:** `PROPOSED`

### Decision

Neutral Ride compensates only for penalties introduced by Ferrous Drive:

- Direct-drive magnetic drag
- Rolling resistance from added system mass
- Climbing energy for added system mass

Neutral Ride does not compensate aerodynamic drag, headwind, rider mass, or the
original bicycle mass.

### Rationale

The intended experience is an electrified bicycle that behaves approximately
like the base bicycle rather than an assisted mode at minimum power.

### Consequences

- A freewheeling geared hub normally remains inactive on flat terrain.
- A direct-drive hub may use virtual freewheeling or active compensation.
- Deliberate regenerative braking remains available.

---

## FD-008: Make Active Recovery HRV-Led and Multimodal

**Status:** `PROPOSED`

### Decision

Use personal HRV baseline and valid real-time HRV response as the leading
physiological inputs for Active Recovery, supported by:

- Heart rate
- Heart-rate drift
- Rider power
- Cadence
- Route demand
- Temperature
- Signal quality
- Recent physiological response

### Rationale

The mode should regulate internal load rather than road speed.

### Consequences

- The initial 50–100 W rider band is provisional.
- HRV cannot be interpreted without personal baseline, exercise context, and
  signal-quality validation.
- Invalid HRV falls back to conservative deterministic behaviour.

---

## FD-009: Make Tempo Reward Sustained Rider Effort

**Status:** `PROPOSED`

### Decision

Tempo should encourage the rider to remain near an adaptive sweet spot based on
estimated or measured power and physiological response.

### Rationale

The mode should feel more rewarding when the rider commits to a productive
workload, while avoiding a simple more-rider-power-equals-more-motor-power loop.

### Consequences

- Reward sustained effort rather than isolated torque spikes.
- Assistance remains bounded and may taper above the target band.
- Physiological strain can lower the adaptive target.
- The initial 200–300 W rider band is provisional.

---

## FD-010: Use Rider-Triggered Regenerative Braking

**Status:** `PROPOSED`

### Decision

Initiate ordinary regenerative braking only from explicit rider brake-lever
intent.

Stopping pedalling means coasting, not braking.

### Rationale

The rider must remain in control of when braking begins. Deceleration,
non-pedalling time, headwind, gradient, or IMU data alone do not reliably prove
braking intent.

### Consequences

- A binary Hall sensor is proposed for each road brake lever.
- Valid brake intent immediately prohibits positive torque.
- Hydraulic braking remains mechanically independent.
- IMU data is feedback, not the primary brake trigger.

---

## FD-011: Use Binary Brake Intent in Generation One

**Status:** `PROPOSED`

### Decision

Use binary brake indication rather than proportional lever or hydraulic-pressure
measurement for the first generation.

### Rationale

A small lever movement can initiate routine regen, while further lever movement
naturally adds hydraulic braking. This preserves rider authority and reduces
integration into the safety-critical hydraulic circuit.

### Consequences

- The binary input establishes brake state, not requested braking force.
- Regen follows a deterministic map.
- Mechanical brake contribution is inferred only for telemetry.
- A later linear Hall implementation remains possible.

---

## FD-012: Schedule Regenerative Torque by Speed

**Status:** `PROPOSED`

### Decision

Use validated wheel speed as a primary input to the nominal regenerative-torque
map.

The proposed map contains:

- Mechanical-only region
- Low-speed taper
- Normal-speed region
- High-speed bounded region

### Rationale

The same binary request should not produce identical braking behaviour at every
speed. Regeneration becomes less effective near zero motor speed, and available
recovery power changes with speed.

### Consequences

- Speed selects the nominal regen envelope.
- Time since brake activation shapes soft engagement.
- Wheel speed and IMU provide bounded trim.
- Battery, thermal, controller, and rear-wheel limits remain authoritative.
- A simplistic linear speed-to-torque relation is rejected.

---

## FD-013: Keep Live Braking Deterministic

**Status:** `ACCEPTED`

### Decision

Use deterministic, validated live braking control in generation one. Restrict
learning to observation-only shadow mode.

### Rationale

Braking is safety relevant. Learning may improve future calibration but should
not define live safety behaviour before deterministic control is validated.

### Consequences

- Learning may log events and recommend map changes offline.
- Suggested changes require review, simulation, bench validation, and controlled
  ride validation before promotion.
- Learning can never alter immutable battery, thermal, fault, or rider-authority
  limits.

---

## FD-014: Use Independent Hydraulic Braking Authority

**Status:** `ACCEPTED`

### Decision

Keep the bicycle's hydraulic brakes mechanically independent of Ferrous Drive.

### Rationale

The bicycle must remain stoppable if the battery, controller, software,
communication, sensing, or regen capability is unavailable.

### Consequences

- Regen is always a bounded contribution.
- The rider adds mechanical braking directly through normal lever travel.
- Ferrous Drive does not command hydraulic pressure.
- Mechanical braking completes low-speed and emergency stops.

---

## FD-015: Treat the Battery as Bidirectional

**Status:** `PROPOSED`

### Decision

Design the shared 10S2P battery, BMS, telemetry, interconnects, and controller
interface for bounded regenerative charge current.

### Rationale

The same battery platform should support both non-regenerative and regenerative
drive configurations.

### Consequences

- BMS charge and discharge permissions are separate inputs.
- Pack voltage, current, and temperature are required telemetry.
- Regen is reduced near the voltage ceiling or outside the validated charge-
  temperature range.
- The controller should act before BMS protection opens the current path.

---

## FD-016: Use Molicel P50B in a 10S2P Prototype Pack

**Status:** `PROPOSED`

### Decision

Use twenty Molicel INR-21700-P50B cells in a 10S2P configuration as the current
battery baseline.

### Current Targets

```text
Nominal voltage
    36 V

Full-charge voltage
    42 V

Nominal capacity
    10 Ah

Nominal energy
    360 Wh

Planning usable energy
    320 Wh
```

### Consequences

- The provisional physical layout is `7 + 6 + 7`.
- Working finished-mass target is 1.75 kg or less.
- Pack-level limits remain below published cell limits until validated.

---

## FD-017: Use Recorded Commute Data as the Primary Route Baseline

**Status:** `ACCEPTED`

### Decision

Use the current commuter-bike GPX traces as the primary route model.

### Rationale

The Vulpine 36c tyres and aluminium wheels better represent the intended
commuter system than historical faster rides on the broken Trek Domane with
deep-section carbon wheels and 28 mm race tyres.

### Consequences

- Outbound and return are modeled separately.
- Rider power remains assumed until measured data is available.
- Exact GPS coordinates should be sanitized before public inclusion.
- Historical Domane rides remain a lower-drag comparison case.

---

## FD-018: Separate Descent and Junction Regeneration

**Status:** `PROPOSED`

### Decision

Track descent-related and junction-related regenerative energy separately.

### Current Planning Values

```text
Outbound
    Descent: approximately 18 Wh
    Junctions: approximately 7 Wh

Return
    Descent: approximately 23 Wh
    Junctions: approximately 8 Wh

Round trip
    Total: approximately 55–56 Wh
```

### Consequences

- Values are simulation inputs inferred from GPX traces.
- They are not measured electrical recovery.
- Physical validation requires battery voltage and bidirectional current.

---

## FD-019: Protect Arrival Reserve

**Status:** `PROPOSED`

### Decision

Make configurable arrival-energy reserve a core constraint for assisted modes.

### Rationale

The project prioritizes reliability and repeatable commuting over extracting all
available battery energy.

### Consequences

- Commute assistance may reduce when predicted reserve is at risk.
- Active Recovery remains subordinate to safe journey completion.
- Tempo cannot consume reserve without an explicit future policy.

---

## FD-020: Keep Safety Limits Outside Ride Modes

**Status:** `ACCEPTED`

### Decision

Battery, motor, controller, communications, braking, and sensor-validity limits
remain independent of the selected ride mode.

### Consequences

- No mode can override a fault or hardware limit.
- All modes use the same immutable safety envelope.
- Mode changes affect intent and comfort, not protection.

---

## Superseded Terminology

| Previous term | Current term | Reason |
|---|---|---|
| Acoustic | Neutral Ride | Better communicates system-penalty compensation |
| Recovery | Active Recovery | Uses established cyclist training language |
| Sport | Tempo | Emphasizes sustained productive effort |
| High Assist | Removed | Outside the current rider-first project scope |

---

## Next Decisions Requiring Evidence

- Exact geared-hub motor and controller
- Exact BMS and communication interface
- Brake Hall sensor and fail-safe wiring
- IMU device, placement, and filter architecture
- Regen speed thresholds and torque map
- Maximum validated pack regen current
- Battery voltage-rollback strategy
- HRV metric, sensor, baseline, and confidence model
- Rider-power estimation and Garmin broadcast method
- Public route-data sanitization strategy
