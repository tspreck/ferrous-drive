# Battery Design

> **Journey first. Reserve protected. Rewards earned only when energy permits.**

## Status

| Area | Status |
|---|---|
| Cell selection | Accepted for prototype investigation |
| Electrical topology | Selected |
| Physical layout | Selected for CAD investigation |
| BMS selection | Open |
| Reward-energy arbitration | Proposed |
| Tre Pulse range model | Proposed |
| Physical validation | Not started |
| Road readiness | No |

> [!WARNING]
> This document describes a system-level battery concept. It is not a welding,
> assembly, charging, or construction guide. Final electrical and mechanical
> design requires specialist review and staged validation.

## 1. Current Prototype Baseline

| Attribute | Current target |
|---|---|
| Cell | Molicel INR-21700-P50B |
| Topology | 10S2P |
| Cell count | 20 |
| Physical arrangement | `7 + 6 + 7` |
| Nominal voltage | 36 V |
| Full-charge voltage | 42 V |
| Nominal capacity | 10 Ah |
| Nominal energy | 360 Wh |
| Planning usable energy | 320 Wh, provisional |
| Cold planning energy | 288 Wh, provisional |
| Form | Single removable bottle-style module |
| Optimization mass goal | 1.70 kg |
| Working target | 1.75 kg or less |
| Prototype ceiling | 1.85 kg |

The planning-energy values are modelling assumptions. They are not measured
pack capacity and must be replaced through battery validation.

## 2. Physical Architecture

```text
Layer 1
    7 cells

Layer 2
    6 cells plus electronics and routing cavity

Layer 3
    7 cells
```

The three physical layers reinforce the Tre Pulse design language, but Tre Pulse
segments do not correspond electrically to individual cell layers. The battery
remains one managed 10S2P pack.

### Packaging direction

```text
Cross-section
    Mild oval

Initial CAD envelope
    Approximately 76 mm wide
    Approximately 70 mm deep
    Approximately 270 mm long
```

### Structural concept

- Lightweight printed cell carrier
- Dedicated terminal and inter-layer insulation
- Continuous dielectric isolation from the carbon shell
- Electrically isolated carbon structural shell
- Reinforced frame-facing mounting spine
- Positive mechanical retention independent from electrical contacts

Carbon fibre is structural and electrically conductive. It must not be treated
as the primary insulation layer.

## 3. Bidirectional Electrical Architecture

```mermaid
flowchart LR
    Cells[10S2P Cell Assembly]
    BMS[Bidirectional 10S BMS]
    Fuse[Pack Fuse]
    Current[Bidirectional Current Measurement]
    Connector[Main Connector]
    Controller[Grin Controller]

    Cells --> BMS
    BMS --> Fuse
    Fuse --> Current
    Current --> Connector
    Connector --> Controller
    Controller --> Current
```

The current path must support:

```text
Positive assistance
    Battery to controller

Regenerative braking
    Controller to battery
```

## 4. BMS Requirements

### Required

- 10-series lithium-ion monitoring
- Cell-group overvoltage and undervoltage protection
- Charge and discharge overcurrent protection
- Short-circuit protection
- Cell balancing
- Battery-temperature monitoring
- Bidirectional current support
- Regenerative charge handling
- Defined fault recovery
- Low standby consumption

### Strongly desired

- Individual group-voltage telemetry
- Charge and discharge permission
- Fault reporting
- Configurable thresholds
- State-of-charge estimate
- Digital interface available to Ferrous Drive

The motor controller should reduce current before the BMS needs to disconnect.
The BMS remains the independent final protection layer.

## 5. Tre Pulse Energy Model

Tre Pulse describes available energy through three rider-facing layers:

```text
Journey
    Energy required to reach the destination

Reserve
    Preferred arrival energy

Contingency
    Margin for weather, model error, aging, and route change
```

### Presentation states

| Segments | Meaning | Behaviour |
|---:|---|---|
| 3 | Journey, reserve, and contingency protected | Full mode flexibility |
| 2 | Journey and minimum reserve protected | Optional rewards may be constrained |
| 1 | Journey completion or reserve at risk | Destination energy prioritized |
| Final segment pulsing | Current plan is insufficient | Rider or route plan must change |

Tre Pulse range state is based on predicted route energy, not raw state of
charge alone.

## 6. Energy Budgets

The battery manager should distinguish:

```text
Journey budget
    Expected energy needed to reach the destination

Reserve budget
    Minimum and preferred arrival energy

Contingency budget
    Uncertainty and adverse-condition margin

Normal reward budget
    Energy available for earned assistance

Golden reward budget
    Energy available for extended Super Rewards
```

Reward energy is optional. Journey and safety energy are not.

### Priority

```text
1. Battery and hardware protection
2. Reach the destination
3. Preserve minimum arrival reserve
4. Preserve the physiological objective
5. Deliver normal reward
6. Deliver Golden extension
7. Preserve journey speed
```

## 7. Reward Authorization

Before a normal or Golden reward begins:

```text
Estimate reward energy
        ↓
Predict destination energy after reward
        ↓
Apply battery and hardware constraints
        ↓
Authorize reward outcome
```

### Outcomes

```text
Full
    Reward delivered as configured

Shortened
    Duration reduced to protect reserve

Deferred
    Reward remains earned but cannot currently be delivered

Unavailable
    Reward cannot be delivered safely in the current activity
```

The system must explain constrained delivery through Tre Pulse and the decision
trace.

### Golden Streak rule

The first-generation Golden Super Reward doubles duration rather than increasing
peak assistance. Triple duration remains deferred until energy and training
impact are validated.

## 8. Recovery Mode Energy Behaviour

Recovery begins from a `1.0:1` rider-to-motor ratio and may adapt toward
`1.2:1` when trusted HRV, heart-rate, and power trends indicate fatigue.

Battery management may reduce the permitted ratio when:

- Arrival reserve is threatened
- Temperature reduces usable energy
- The route forecast worsens
- Reward energy is unavailable
- Motor or controller limits are active

Recovery protects the physiological objective before speed, but destination
arrival remains authoritative.

## 9. Training Energy Behaviour

Training separates qualifying work from earned assistance.

During work accumulation:

- Earned reward assistance is inactive
- Required direct-drive compensation may remain active
- Battery state does not create unearned progress

During reward:

- Valid pedalling remains required
- Energy cost is predicted before authorization
- Positive torque stops immediately on brake request
- Battery reserve may shorten or defer the reward

## 10. Regenerative Braking Constraints

Regen is independent from Tre Pulse rewards and may replenish practical energy
margin during a ride.

Regen must remain limited by:

- Pack voltage
- Individual group voltage where available
- Battery temperature
- BMS charge permission
- Maximum battery charge current
- Motor and controller temperature
- Interconnect and connector limits
- Controller communication health

### Near-full battery

- Reduce regen progressively near the configured ceiling
- Inhibit regen if safe charge acceptance is unavailable
- Keep positive torque inhibited while braking is requested
- Let mechanical brakes provide the required stopping force

### Cold battery

- Reduce or inhibit regen outside the validated charging range
- Keep mechanical brakes authoritative
- Record the limiting reason

## 11. Energy Accounting

The system should record separately:

```text
Gross positive-assistance energy
Neutral compensation energy
Recovery-assistance energy
Training reward energy
Golden extension energy
Descent regen energy
Junction regen energy
Auxiliary energy
Net battery energy
```

This enables honest analysis of what Tre Pulse rewards cost and what regen
returns.

## 12. Telemetry

### Required

- Pack voltage
- Bidirectional battery current
- Battery temperature
- BMS charge permission
- BMS discharge permission
- BMS fault state
- Controller battery limits
- Communication health

### Desired

- Individual group voltages
- State of charge
- State of health
- Discharged watt-hours
- Regenerated watt-hours
- Reward-energy totals
- Minimum voltage
- Maximum current
- Maximum temperature
- Cell imbalance
- Fault history

## 13. Provisional Route Baseline

Current route work uses:

```text
Recorded moving mass before assistance hardware
    95 kg

Battery
    360 Wh nominal
    320 Wh mild planning energy
    288 Wh cold planning energy

Mission
    One approximately 35 km commute leg

Destination charging
    Available and acceptable
```

The battery must not be down-sized from simulation alone. The current pack
remains the prototype baseline until measured rider power, pack current, pack
voltage, cold capacity, and real regen are available.

## 14. Mass Budget

| Component | Working allowance |
|---|---:|
| 20 P50B cells | Up to 1,420 g |
| Electrical insulation and barriers | 12 g |
| Interconnects and links | 25 g |
| BMS and balance wiring | 35 g |
| Fuse and holder | 8 g |
| Main wiring and connector | 22 g |
| Temperature sensing | 3 g |
| Printed cell carrier | 45 g |
| Carbon shell and resin | 55 g |
| End protection and seals | 30 g |
| Mounting and retention | 40 g |
| Adhesive and cushioning | 15 g |
| **Working estimate** | **Approximately 1,710 g** |

The mass target must not weaken insulation, protection, sensing, retention, or
mounting integrity.

## 15. Validation Gates

### Cell and electrical

- [ ] Verify authentic cells and traceability
- [ ] Measure cell mass, voltage, resistance, and capacity
- [ ] Match cells before assembly
- [ ] Verify BMS thresholds and balancing
- [ ] Verify discharge and regenerative current measurement
- [ ] Verify fuse and connector temperature
- [ ] Verify voltage sag and rollback

### Bidirectional operation

- [ ] Verify controller-to-battery regen path
- [ ] Verify battery-full rollback
- [ ] Verify cold-battery regen restriction
- [ ] Verify BMS charge prohibition
- [ ] Verify safe removal of regen when charging is unavailable

### Tre Pulse energy

- [ ] Validate Journey, Reserve, and Contingency calculations
- [ ] Validate normal reward energy estimate
- [ ] Validate Golden reward duration cost
- [ ] Validate shortened and deferred reward outcomes
- [ ] Confirm no reward can violate arrival reserve

### Mechanical and environmental

- [ ] Measure finished mass
- [ ] Validate cell retention and carbon isolation
- [ ] Validate mounting and connector strain relief
- [ ] Perform vibration, water, condensation, and cold tests

## 16. Open Questions

1. Exact BMS and telemetry interface
2. Everyday charge ceiling
3. Minimum and preferred arrival reserve
4. Reward-energy reservation policy
5. Whether deferred rewards can carry between activities
6. Cold usable-energy curve
7. Battery aging allowance
8. Regen current limits
9. Physical connector and mounting design
10. Exact Tre Pulse range thresholds

## 17. Core Principle

> The battery enables the journey first, protects reserve second, and funds
> earned rewards only when the system can still arrive safely.
