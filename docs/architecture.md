> 📚 [Repository README](../README.md) · 🗺️ [Roadmap](roadmap.md) · 🧠 [Assumptions](x.md · 📜 decision_log.md

# Architecture

> Measure effort. Preserve momentum. Learn from every ride.

Ferrous Drive is a simulation-first, controller-independent Rust platform for e-bike propulsion control.

The architecture separates telemetry acquisition, trust evaluation, assist calculation, constraint handling, simulation, and hardware integration so that control logic can be developed and validated independently of any specific motor controller.

---

## Table of Contents

#Overview
- [design-principles](#
- [system-overview](#
- [runtime-control-flow](#
- [motion-state-machine](#
- [telemetry-trust-model](#
- [constraint-architecture](#
- [simulation-architecture](#
- [controller-abstraction](#
- [target-hardware-architecture](#
- [engineering-feedback-loop](#
- [known-unknowns](#
- [non-goals](#
- [long-term-vision](#
- [project-mantra](#

---

## Overview

Ferrous Drive sits between telemetry and motor controllers.

Its job is not to directly control hardware.

Its job is to make the best possible assist decision using the information available at the time.

```text
Rider Effort
      +
Vehicle Telemetry
      ↓
Ferrous Drive
      ↓
Motor Request
      ↓
Controller Driver
      ↓
Motor Controller
```

---

## Design Principles

### Safety First

Predictable behaviour is preferred over aggressive behaviour.

### Simulation Before Hardware

Validation should happen in simulation before running on a moving bike.

### Explainable Decisions

Assist decisions should be observable and traceable.

### Controller Independence

The control core should not depend on a specific controller implementation.

### Graceful Degradation

The system should react predictably when telemetry becomes stale, invalid, or unavailable.

---

## System Overview

```mermaid
flowchart LR

    Telemetry[Telemetry Sources]
    Trust[Telemetry Trust Model]
    State[Motion State Machine]
    Constraints[Constraint Engine]
    Decision[Assist Decision]
    Request[Motor Request]
    Driver[Controller Driver]
    Controller[Motor Controller]

    Telemetry --> Trust
    Trust --> State
    State --> Constraints
    Constraints --> Decision
    Decision --> Request
    Request --> Driver
    Driver --> Controller
```

---

## Runtime Control Flow

The runtime path executed during each control update.

```mermaid
flowchart LR

    Telemetry[Telemetry]
    Trust[Telemetry Trust]
    State[Motion State Machine]
    Constraints[Constraint Engine]
    Decision[Assist Decision]
    Request[Motor Request]
    Driver[Controller Driver]

    Telemetry --> Trust
    Trust --> State
    State --> Constraints
    Constraints --> Decision
    Decision --> Request
    Request --> Driver
```

---

## Motion State Machine

The state machine models rider movement only.

It deliberately does **not** represent:

- Thermal limits
- Battery limits
- Speed limits
- Communication failures

Those belong in the constraint layer.

```mermaid
stateDiagram-v2

    [*] --> Stationary

    Stationary --> Launching : Pedalling Begins
    Launching --> Cruising : Stable Motion

    Cruising --> Coasting : Pedalling Stops

    Coasting --> Cruising : Pedalling Resumes
    Coasting --> Stationary : Speed Approaches Zero

    Launching --> Stationary : Rider Stops
```

---

## Telemetry Trust Model

Raw telemetry is not automatically trusted.

Every measurement must pass quality and freshness checks before it can influence assist decisions.

```mermaid
flowchart LR

    Raw[Raw Measurement]
    Range[Range Check]
    Freshness[Freshness Check]
    Trust[Trust State]

    Raw --> Range
    Range --> Freshness
    Freshness --> Trust

    Trust --> Valid[VALID]
    Trust --> Aging[AGING]
    Trust --> Stale[STALE]
    Trust --> Invalid[INVALID]
```

Expected trust states:

```text
VALID
AGING
STALE
INVALID
```

---

## Constraint Architecture

One of the key architectural decisions in Ferrous Drive is that constraints are not states.

```mermaid
flowchart TD

    StateMachine[Motion State Machine]

    Speed[Speed Limit]
    Thermal[Thermal Limit]
    Battery[Battery Limit]
    Profile[Ride Profile Limit]

    StateMachine --> Decision

    Speed --> Decision
    Thermal --> Decision
    Battery --> Decision
    Profile --> Decision

    Decision[Final Assist Decision]
```

This separation avoids state explosion and keeps the system understandable.

---

## Simulation Architecture

Simulation is a first-class citizen.

Most development should happen here before involving hardware.

```mermaid
flowchart LR

    RideData[Ride Data]
    Replay[Replay Engine]
    Core[Control Core]
    Trace[Decision Trace]
    Analysis[Analysis]

    RideData --> Replay
    Replay --> Core
    Core --> Trace
    Trace --> Analysis
```

Expected outcomes:

- Deterministic replay
- Regression testing
- Decision inspection
- Validation before riding

---

## Controller Abstraction

The control core should not know which controller is attached.

```text
Control Core
      ↓
Motor Request
      ↓
Controller Driver
      ↓
Hardware Protocol
```

Potential future targets:

- Baserunner
- VESC
- Future controller platforms

Controller-specific logic belongs in drivers, not in the control core.

---

## Target Hardware Architecture
