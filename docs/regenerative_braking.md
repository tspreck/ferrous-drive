# Regenerative Braking

> **The rider initiates braking. Ferrous Drive manages only the regenerative contribution.**

## Status

| Area | Status |
|---|---|
| Architecture | Proposed |
| Binary brake intent | Selected concept |
| Speed-scheduled regen | Proposed |
| IMU and wheel-speed feedback | Proposed |
| Tre Pulse integration | Proposed |
| Bench validation | Not started |
| Road validation | Not started |

> [!WARNING]
> Regenerative braking is not a substitute for the bicycle's hydraulic brakes.
> Mechanical brakes remain independent, immediately available, and authoritative.

## 1. Purpose

Recover routine braking energy while preserving predictable road-bike braking
and complete rider authority.

```text
Light lever movement
    Brake intent active
    Positive torque removed
    Regen begins smoothly

Further lever movement
    Hydraulic braking adds naturally

Strong or emergency pull
    Hydraulic brakes dominate
```

## 2. Core Safety Principles

- The rider initiates every braking event
- Stopping pedalling means coasting, not braking
- Brake intent overrides every positive-assistance and reward request
- Regen remains bounded by battery, motor, controller, speed, and confidence
- Hydraulic braking remains functional without Ferrous Drive
- Loss of safe regen must never delay mechanical braking
- The Tre Pulse engine cannot initiate braking

## 3. Relationship to the Three Modes

Regen remains available in:

```text
Neutral
Recovery
Training
```

Mode changes may alter presentation or validated comfort settings, but they do
not change braking priority or protection limits.

### Neutral

- Cancel direct-drive compensation on brake intent
- Apply normal bounded regen
- Preserve natural coasting when no braking is requested

### Recovery

- Cancel baseline or HRV-adapted support immediately
- Do not count safety braking as Recovery failure
- Pause Tre Pulse progress during legitimate road interruption where possible

### Training

- Cancel work-phase compensation or active reward torque immediately
- Pause or cancel active reward according to the profile
- Preserve Golden Streak during legitimate safety braking where possible
- Never incentivize avoiding the brakes to protect progress

## 4. Tre Pulse Integration

Tre Pulse may display:

```text
Regen available
Regen active
Regen constrained
Mechanical-only braking
Recovered energy
Reward paused by braking
```

Tre Pulse does not command regenerative torque.

### Reward interruption

```text
Normal or Golden reward active
        +
Brake request
        =
Positive reward torque cancelled immediately
Regen controller takes priority
```

After brake release:

- Regen fades to zero
- Coasting resumes if the rider is not pedalling
- Positive assistance returns only after valid pedalling and arbitration
- Reward continuation depends on the profile and remaining authorized duration

## 5. Brake-Intent Sensing

The first prototype should use a binary Hall sensor on each brake lever.

```text
RELEASED
ACTIVE
INVALID
```

The combined brake request is active when either valid sensor is active.

A sensor fault may inhibit propulsion, but it must not automatically command
continuous regen.

## 6. Speed-Scheduled Regenerative Torque

Binary brake input establishes intent. Vehicle speed shapes the nominal regen
response.

```text
Brake intent
        ↓
Validated wheel speed
        ↓
Speed-dependent regen target
        ↓
Soft engagement ramp
        ↓
Constraint manager
        ↓
Bounded wheel-speed and IMU trim
        ↓
Final negative-torque request
```

### Regions

```text
Mechanical-only
    Regen zero at very low speed

Low-speed taper
    Regen rises or falls smoothly

Normal-speed region
    Predictable routine contribution

High-speed bounded region
    Stronger contribution permitted within limits
```

The map is not a simple linear rule. Exact thresholds and torque values remain
open until bench and controlled ride validation.

## 7. Feedback

### Required

- Wheel speed
- Motor speed
- Battery voltage
- Bidirectional battery current
- Motor temperature
- Controller temperature
- Controller health

### IMU

The IMU measures physical response and supports plausibility checking. It does
not decide whether braking begins.

```text
Brake sensors
    Rider intent

Wheel speed
    Longer-term deceleration

IMU
    Fast physical response

Battery current
    Recovered electrical energy
```

## 8. State Machine

```text
PEDALLING
COASTING
BRAKE_ENTRY
REGEN_BRAKING
MECHANICAL_ONLY
LOW_SPEED_HANDOFF
BRAKE_RELEASE
STOPPED
FAULT
```

### Key transitions

```text
Pedalling or coasting + brake intent
    → Brake entry

Brake entry + regen permitted
    → Regen braking

Brake entry + regen unavailable
    → Mechanical only

Regen braking + low speed
    → Low-speed handoff

Brake released
    → Brake release

Brake release + valid pedalling
    → Pedalling

Brake release + no pedalling
    → Coasting
```

## 9. Deterministic First-Generation Profile

```text
Brake detected
    ↓
Positive torque cancelled
    ↓
Soft engagement
    ↓
Controlled ramp
    ↓
Bounded plateau
    ↓
Low-speed taper or release
```

The first generation uses a validated deterministic map. Learning operates only
in observation mode.

## 10. Constraint Manager

Regen is limited by the minimum of:

- Battery charge current
- Pack and group voltage
- BMS charge permission
- Battery temperature
- Motor temperature
- Controller temperature
- Wheel and motor speed
- Rear-wheel torque limit
- Signal confidence
- Communication health

If safe regen cannot be maintained:

```text
Positive torque remains inhibited while braking is requested
Regen is reduced or removed
Hydraulic brakes remain available
```

## 11. Mechanical Brake Blending

Ferrous Drive does not actuate hydraulic pressure.

```text
Regen
    Bounded electrical contribution

Hydraulic brakes
    Rider-controlled additional contribution
```

Expected operation:

| Situation | Regen | Mechanical braking |
|---|---|---|
| Light slowing | Primary | Minimal |
| Normal stop | Early and middle contribution | Increases near stop |
| Strong stop | Bounded | Dominant |
| Emergency stop | Secondary | Authoritative |
| Regen unavailable | None | Complete stopping force |

## 12. Low-Speed Handoff

- Taper regen smoothly as speed falls
- Keep positive torque inhibited while braking is active
- Let hydraulic brakes complete the stop
- Avoid sudden assistance restart after release

## 13. Streak and Progress Fairness

Safety behaviour always has priority over behavioural progress.

A legitimate braking event should, where possible:

- Pause accumulation
- Preserve secured milestones
- Preserve Golden Streak
- Pause reward duration rather than wasting it

A profile may still declare a cycle incomplete if the qualifying work is truly
abandoned, but road-safety braking must never be treated as misconduct.

## 14. Energy Accounting

Record separately:

```text
Descent regen
Junction regen
Unclassified regen
Constraint-lost recovery
Gross recovered energy
Net battery energy
```

Current route-derived values remain provisional inputs, not measured recovery.
They must be replaced by battery current and voltage measurements.

## 15. Shadow Learning

Observation-only learning may record:

- Entry speed
- Duration
- Commanded negative torque
- Achieved deceleration
- Recovered energy
- Constraint activation
- Possible mechanical-brake contribution
- Rider release timing

It may suggest future map changes, but it cannot change live braking or safety
limits in generation one.

## 16. Failure Behaviour

### Sensor stuck active

- Positive torque prohibited according to fault policy
- Regen must not remain active indefinitely
- Fault reported
- Hydraulic brakes unaffected

### Sensor unavailable

- Regen unavailable
- Positive-assistance policy becomes conservative
- Hydraulic brakes remain fully functional

### IMU unavailable

- Fixed map may remain available
- Adaptive trim and learning disabled

### Wheel speed unavailable

- Positive torque still cancelled by brake intent
- Regen reduced or inhibited

### Battery cannot accept charge

- Regen reduced or removed
- Positive torque remains inhibited while braking
- Hydraulic brakes provide stopping force

## 17. Validation Gates

### Brake sensing

- [ ] Activation and release
- [ ] Debounce
- [ ] Broken-wire behaviour
- [ ] Lever return and shifter clearance
- [ ] Independent left and right channels

### Torque transitions

- [ ] Positive to zero torque
- [ ] Zero to negative torque
- [ ] Smooth release
- [ ] No regen during ordinary coasting
- [ ] No positive torque during braking

### Speed relationship

- [ ] Mechanical-only region
- [ ] Low-speed taper
- [ ] Normal-speed region
- [ ] High-speed ceiling
- [ ] Continuous interpolation and hysteresis

### Tre Pulse and reward

- [ ] Normal reward cancels immediately on braking
- [ ] Golden reward cancels immediately on braking
- [ ] Safety braking preserves streak where appropriate
- [ ] Reward does not restart with a torque step
- [ ] Constraint state is communicated clearly

### Constraints and fallback

- [ ] Full battery
- [ ] Cold battery
- [ ] BMS charge prohibition
- [ ] Thermal rollback
- [ ] Communication loss
- [ ] Wheel-speed loss
- [ ] IMU loss
- [ ] Mechanical-only fallback

## 18. Open Questions

1. Exact Hall sensor and placement
2. Sensor electrical topology
3. Speed-map thresholds
4. Maximum negative torque
5. Feedback-trim authority
6. Low-speed handoff thresholds
7. Reward pause versus cancellation rules
8. Rear-wheel instability detection
9. Rider indication when regen is unavailable
10. Bench and closed-course acceptance values

## 19. Core Principle

> Tre Pulse can reward effort. Only the rider can request braking. Safety always
> wins over progress, streaks, rewards, and energy recovery.
