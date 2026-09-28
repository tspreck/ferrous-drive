# Validation Matrix

This matrix records what Ferrous Drive intends to prove. A design description or
simulation output is not equivalent to validation.

## Evidence Levels

| Level | Meaning |
|---|---|
| Designed | Behaviour documented |
| Simulated | Repeatable desktop evidence |
| Bench | Physical hardware tested without normal riding |
| Controlled motion | Lifted wheel or closed-course evidence |
| Route | Measured on the intended route |
| Validated | Acceptance criteria met with retained evidence |

## 1. Battery

| Item | Target evidence | Current state |
|---|---|---|
| Authentic P50B sourcing | Traceable purchase and batch record | Open |
| Cell capacity and resistance | Incoming measurement record | Open |
| 10S2P topology | Electrical review | Designed |
| `7 + 6 + 7` fit | CAD and inert prototype | Open |
| Finished mass | Weighed complete prototype | Open |
| Carbon isolation | Electrical isolation after vibration | Open |
| BMS thresholds | Bench fault tests | Open |
| Bidirectional current | Measured discharge and regen | Open |
| Mild usable energy | Controlled discharge | Open |
| Cold usable energy | Cold-soak discharge | Open |
| Battery-full regen rollback | Controller and BMS bench test | Open |
| Cold regen restriction | Temperature-controlled bench test | Open |

## 2. Tre Pulse Energy and Rewards

| Item | Acceptance intent | Current state |
|---|---|---|
| Journey prediction | Arrival estimate within defined error | Designed |
| Reserve protection | Optional torque cannot breach minimum reserve | Designed |
| Contingency state | Responds to adverse model changes | Designed |
| Normal reward estimate | Predicted versus measured energy | Open |
| Golden reward estimate | Duration cost predicted correctly | Open |
| Full reward | Delivered only when authorized | Open |
| Shortened reward | Duration reduction explainable | Open |
| Deferred reward | Earned state preserved correctly | Open |
| Unavailable reward | No torque and clear reason | Open |
| Three-segment comprehension | Rider correctly interprets states | Open |

## 3. Recovery

| Item | Acceptance intent | Current state |
|---|---|---|
| Zone model | Explicit relative definition | Designed |
| Baseline support | Starts at `1.0:1` | Designed |
| HRV adaptation | Bounded at `1.2:1` | Designed |
| HRV invalid fallback | Returns toward baseline | Designed |
| HR drift handling | Gradual support or speed reduction | Open |
| Valid pedalling | Required for positive assistance | Designed |
| Arrival reserve | Overrides support and reward | Designed |
| Road interruption | Progress pauses safely | Open |
| Shallow-descent support | PAS and reserve constraints respected | Simulated only |

## 4. Training Profiles

| Item | Acceptance intent | Current state |
|---|---|---|
| Work phase | No earned reward assistance | Designed |
| System compensation | Remains independent from reward | Designed |
| Grace period | Deterministic interruption handling | Open |
| Decay | Deterministic and profile-specific | Open |
| Locked milestone | Does not regress unexpectedly | Open |
| Reward ramp-in | No abrupt torque step | Open |
| Reward ramp-out | Smooth return to normal control | Open |
| Minimum rider input | PAS requirement enforced | Designed |
| Brake cancellation | Positive reward torque removed immediately | Designed |

## 5. Golden Streak

| Item | Acceptance intent | Current state |
|---|---|---|
| Cycle count | Exactly three valid completions | Designed |
| Gold accumulation | Begins during third cycle | Designed |
| Super Reward | Doubles duration, not peak power | Designed |
| Triple duration | Remains disabled initially | Designed |
| Safety braking | Does not unfairly break streak | Open |
| Mode change | Ends or stores streak per policy | Open |
| Battery constraint | Shortens or defers safely | Open |
| Accessibility | State readable without colour alone | Open |
| Motivation | Improves repeat engagement | Open |

## 6. Brake Intent

| Item | Acceptance intent | Current state |
|---|---|---|
| Left sensor | Reliable activation and release | Open |
| Right sensor | Reliable activation and release | Open |
| Debounce | No chatter or missed intent | Open |
| Broken wire | Conservative known state | Open |
| Lever return | No mechanical interference | Open |
| Shifter operation | No interference | Open |
| Environmental exposure | Maintains behaviour after water and cold | Open |

## 7. Regen State and Torque Transitions

| Item | Acceptance intent | Current state |
|---|---|---|
| Brake priority | Positive torque prohibited | Designed |
| Positive-to-zero | Immediate and controlled | Open |
| Zero-to-negative | Soft engagement | Open |
| Normal plateau | Repeatable deceleration | Open |
| Low-speed taper | Smooth handoff | Open |
| Brake release | No positive-torque surge | Open |
| No-brake coasting | No unintended regen | Designed |
| Normal reward active | Cancelled on brake intent | Designed |
| Golden reward active | Cancelled on brake intent | Designed |

## 8. Speed-Scheduled Regen

| Item | Acceptance intent | Current state |
|---|---|---|
| Mechanical-only region | Regen zero below threshold | Open |
| Low-speed taper | Continuous interpolation | Open |
| Normal-speed region | Predictable routine contribution | Open |
| High-speed region | Bounded within validated limit | Open |
| Threshold hysteresis | No state chatter | Open |
| Wheel-speed loss | Regen reduced or disabled | Designed |
| IMU loss | Fixed conservative map only | Designed |

## 9. Regen Constraints

| Item | Acceptance intent | Current state |
|---|---|---|
| Pack voltage | Rollback before ceiling | Open |
| BMS charge permission | Regen inhibited when denied | Open |
| Battery current | Enforced below validated limit | Open |
| Battery temperature | Cold and hot restrictions | Open |
| Motor temperature | Thermal rollback | Open |
| Controller temperature | Thermal rollback | Open |
| Communication loss | Safe removal of adaptive request | Open |
| Mechanical-only fallback | Full braking remains available | Designed |

## 10. Route Energy

| Item | Target evidence | Current state |
|---|---|---|
| Outbound route replay | Deterministic simulation | Partially simulated |
| Return route replay | Deterministic simulation | Partially simulated |
| Rider power | Measured reference ride | Open |
| Battery voltage/current | Instrumented route ride | Open |
| Descent regen | Measured watt-hours | Open |
| Junction regen | Measured watt-hours | Open |
| Arrival reserve | Predicted versus measured | Open |
| Mild weather | Repeated route evidence | Open |
| Cold weather | Repeated route evidence | Open |
| Headwind | Route and weather evidence | Open |

## 11. Telemetry Trust

| Item | Acceptance intent | Current state |
|---|---|---|
| Valid | Fresh and plausible data accepted | Planned first code |
| Aging | Degraded confidence represented | Planned first code |
| Stale | Timed-out data rejected for control | Planned first code |
| Invalid | Implausible data rejected | Planned first code |
| Bounded history | Deterministic capacity | Planned first code |
| Critical state | Cannot be silently dropped | Designed |

## 12. Human Factors

| Item | Acceptance intent | Current state |
|---|---|---|
| Tre Pulse clarity | Correct interpretation | Open |
| Daylight readability | Understandable while riding | Open |
| Night readability | Understandable without glare | Open |
| Colour accessibility | Redundant non-colour cues | Designed |
| Cognitive load | Minimal attention required | Open |
| Safety incentives | No reward for unsafe behaviour | Designed |
| Constrained reward | Rider understands reason | Open |

## 13. Validation Rules

- Every test records configuration, firmware revision, environment, and result.
- Simulation values remain assumptions until measured.
- Road tests require independent mechanical braking and prior lower-level gates.
- Failed tests remain visible.
- Reward validation never weakens safety validation.
- Human motivation results do not redefine hardware safety limits.

## 14. Immediate Validation Sequence

```text
Telemetry trust unit tests
        ↓
Tre Pulse state simulation
        ↓
Battery reward-budget simulation
        ↓
Brake-sensor bench tests
        ↓
Controller and regen bench tests
        ↓
Lifted-wheel transitions
        ↓
Closed-course braking
        ↓
Instrumented route validation
```
