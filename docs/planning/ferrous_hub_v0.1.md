# Ferrous Hub V0.1 Plan

## Goal

Demonstrate a portable, deterministic Ferrous Hub core on the nRF54L15 DK with a Trinity Ring, capacitive touch, dedicated indicators, and a lighting-control state model.

## Milestone outcome

```text
Touch input
    ↓
Board adapter
    ↓
Ferrous Hub core state machine
    ↓
Semantic UI intent
    ↓
Trinity Ring + dedicated indicators
```

## Work packages

### 1. Architecture baseline

- [ ] Merge the Ferrous Hub ADR.
- [ ] Define Hub responsibilities and exclusions.
- [ ] Define energy-module boundary.
- [ ] Define Trinity Ring semantics.
- [ ] Define P2600 peripheral boundary.

### 2. Hardware bring-up

- [ ] Select the exact 24-LED ring variant.
- [ ] Select the capacitive-touch breakout.
- [ ] Define prototype power rails.
- [ ] Confirm logic-level compatibility.
- [ ] Establish current-limited bench wiring.
- [ ] Add dedicated power and mode indicators.

### 3. Board-independent core

- [ ] Add lifecycle state machine.
- [ ] Add normalized touch gestures.
- [ ] Add semantic ring intents.
- [ ] Add prototype lighting states.
- [ ] Add warning and fault priorities.
- [ ] Add host-side state-transition tests.

### 4. nRF54L15 adapters

- [ ] Monotonic clock adapter.
- [ ] Addressable LED driver.
- [ ] Touch input adapter.
- [ ] Dedicated indicator adapter.
- [ ] Logging or telemetry adapter.

### 5. Demonstration

- [ ] Boot and self-test animation.
- [ ] Touch acknowledgment.
- [ ] Lighting-state cycle.
- [ ] Three-stage Tre Pulse demonstration.
- [ ] Reward-ready and active states.
- [ ] Golden reward demonstration.
- [ ] Fault override demonstration.

### 6. Validation

- [ ] Host tests pass.
- [ ] Repeated power-cycle test passes.
- [ ] Ring current is measured.
- [ ] Night brightness is evaluated.
- [ ] Touch false-trigger behaviour is recorded.
- [ ] Failure modes are documented.
- [ ] Hardware-specific code remains isolated.

## Explicit non-goals

- final custom PCB;
- production enclosure;
- final battery module;
- production lighting electrical integration;
- motor control;
- finished ANT+ profile;
- production CAN protocol.

## Completion definition

Ferrous Hub V0.1 is complete when a contributor can run host tests, flash the nRF54L15 DK, interact through touch, observe deterministic Trinity Ring behaviour, and understand the remaining production gaps from repository documentation alone.
