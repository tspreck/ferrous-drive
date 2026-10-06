# Training Model

> Sports-science-informed. Rider-first. Experimental until validated.

## Status

| Area | Status |
|---|---|
| Physiological foundations | Established practices selected |
| Ferrous Drive interpretation | Proposed |
| Recovery model | Proposed |
| Training profiles | Proposed |
| Golden Streak | Proposed |
| Simulation | Not started |
| Rider validation | Not started |

> [!IMPORTANT]
> Ferrous Drive is not a coach, laboratory assessment, or medical device.
> Its Recovery and Training concepts have been cross-checked and iteratively
> refined against established endurance-sport practices. Specific scoring,
> assistance, reward, and game parameters remain experimental until validated.

## 1. Purpose

This document separates three things that must not be mixed together:

1. Established endurance-training concepts
2. Ferrous Drive's interpretation of those concepts
3. Experimental parameters that require simulation and rider testing

The training model supports two of the three Ferrous Drive modes:

```text
Recovery
    Regulate a personalized low-intensity aerobic workload

Training
    Complete structured work and earn bounded assistance
```

Neutral does not prescribe a physiological target.

## 2. Sports-Science Foundations

The current design draws on:

- Relative power zones based on FTP or Critical Power
- Heart-rate zones based on a named rider-specific model
- First-threshold concepts such as LT1 or VT1 when available
- Time-in-zone accumulation
- Sweet-spot training
- High-intensity interval structures
- Critical Power and W-prime concepts
- Autoregulatory training
- HRV-informed readiness and fatigue adaptation
- Progressive overload and recovery

### Power, heart rate, and HRV

```text
Power
    External mechanical workload

Heart rate
    Internal cardiovascular response

HRV
    Readiness and fatigue context
```

No single signal is sufficient in every condition.

HRV adjusts the permitted assistance envelope. It does not directly command
motor torque.

## 3. Three-Mode Relationship

```text
Neutral
    Preserve

Recovery
    Regulate

Training
    Earn
```

### Neutral

Neutral records physiological and mechanical data but does not regulate the
rider's training load.

### Recovery

Recovery targets a personalized Zone 2 envelope and adapts support according
to trusted power, heart-rate, HRV, and fatigue indicators.

### Training

Training contains structured profiles that separate qualifying work from the
assistance reward earned afterward.

## 4. Recovery Model

### Relative target

Recovery must name its zone model explicitly.

Possible anchors include:

- Power Zone 2 relative to FTP
- Power Zone 2 relative to Critical Power
- Heart-rate Zone 2 relative to threshold HR
- A measured or estimated first threshold

Fixed watt values may appear in examples, but they are not universal Recovery
targets.

### Assistance envelope

```text
Normal readiness
    1.0:1 rider-to-motor assistance

Moderate validated fatigue
    Progress toward 1.1:1

Persistent validated fatigue
    Progress toward 1.2:1

HRV unavailable or unreliable
    Fall back toward 1.0:1
```

The final request is always limited by:

- Remaining road demand
- Valid pedalling
- Battery arrival reserve
- Brake state
- Motor, controller, and battery limits
- Signal quality

### Physiological arbitration

```text
Power in target and HR response appropriate
    Continue progress

Power in target but HR drifts above target
    Permit more support or allow speed to fall

Power above target
    Pause, decay, or reset progress according to profile rules

Power low while HR remains elevated
    Permit bounded support if signals are trusted

Signals unreliable
    Pause progress and return assistance toward baseline
```

### Recovery reward

The first implementation should not introduce a Recovery ceiling above 1.2:1.
Completing a Recovery cycle grants sustained access to the validated upper
Recovery envelope for a bounded duration.

## 5. Training Model

Training separates work from reward.

```text
Qualifying work
        ↓
Visible Tre Pulse progress
        ↓
Milestone completion
        ↓
Earned bounded assistance
```

### Work phase

- The rider performs the configured workload
- Earned reward assistance remains inactive
- Required system-penalty compensation may remain active
- Valid road interruptions use grace or pause rules
- Progress is based on trusted telemetry

### Reward phase

- Valid pedalling remains required
- Assistance ramps in smoothly
- Brake intent cancels positive torque immediately
- Battery reserve may shorten or defer the reward
- Assistance fades out predictably

## 6. Initial Training Profiles

### Tempo Tailwind

Purpose:

- Accumulate time in a configured sweet-spot band
- Bank progress through three Tre Pulse milestones
- Receive a temporary tailwind after completing the work

Parameters requiring validation:

- Target power range
- Accumulation duration
- Grace period
- Decay rate
- Locked milestone floors
- Reward assistance
- Reward duration
- Ramp-in and ramp-out

### Anaerobic Shield

Purpose:

- Recognize committed higher-intensity efforts
- Accumulate progress faster for stronger valid work
- Reject accidental spikes
- Provide a bounded recovery runway after completion

Parameters requiring validation:

- Entry threshold
- Commitment duration
- Point curve
- Completion target
- Reward power
- Reward duration
- W-prime interpretation
- Maximum clamps

## 7. Shared Behaviour Engine

Recovery and Training should use one configurable Tre Pulse engine.

### Accumulation configuration

```text
Qualifying condition
Entry commitment
Accumulation rate
Grace duration
Decay rate
Milestone thresholds
Locked floors
Completion threshold
Reset rules
```

### Reward configuration

```text
Assistance rule
Minimum rider contribution
Ramp-in duration
Reward duration
Ramp-out duration
Cancellation conditions
Energy budget
Golden duration multiplier
```

### Shared states

```text
Idle
Accumulating
Grace
Decaying
MilestoneLocked
RewardReady
Rewarding
Paused
Constrained
Resetting
Faulted
```

## 8. Golden Streak

Three consecutive valid cycles unlock a Golden Super Reward.

```text
Cycle 1 complete
    Streak = 1

Cycle 2 complete
    Streak = 2

Cycle 3 accumulating
    Tre Pulse progressively turns gold

Cycle 3 complete
    Golden Super Reward unlocked
```

### First-generation rule

```text
Normal reward
    1× duration

Golden Super Reward
    2× duration

Future validated option
    Up to 3× duration
```

Golden rewards extend duration, not peak assistance.

Safety braking, red lights, and legitimate road interruptions should pause
progress where possible rather than encouraging unsafe behaviour.

## 9. Energy and Safety Governance

Priority order:

```text
1. Braking and fault handling
2. Battery, motor, and controller protection
3. Valid PAS and trusted telemetry
4. Destination arrival reserve
5. Physiological intent
6. Earned reward
7. Journey speed
```

A reward may be:

- Delivered fully
- Shortened
- Deferred
- Unavailable

The rider-facing state must explain the outcome.

## 10. Classification of Values

Every numerical value should be labelled as one of:

```text
Rider configuration
Published model input
Ferrous Drive interpretation
Game-balancing parameter
Physics estimate
Hardware safety limit
Validated measurement
```

Example FTP values, point curves, reward durations, and estimated speeds must
not silently become hardware or physiological requirements.

## 11. Validation Requirements

### Recovery

- [ ] Zone model is explicit
- [ ] 1.0:1 baseline behaves predictably
- [ ] HRV adaptation never exceeds 1.2:1
- [ ] Invalid HRV falls back conservatively
- [ ] Heart-rate drift changes support gradually
- [ ] Arrival reserve overrides support

### Training

- [ ] Work remains authentic during accumulation
- [ ] Grace rules handle road interruptions
- [ ] Progress and decay are deterministic
- [ ] Rewards require valid pedalling
- [ ] Reward ramps are smooth
- [ ] Brake intent cancels reward torque

### Golden Streak

- [ ] Exactly three valid cycles are required
- [ ] Duration increases without raising peak assistance
- [ ] Safety interruptions do not create unsafe incentives
- [ ] Battery constraints shorten or defer transparently
- [ ] The mechanic improves motivation without distorting training intent

## 12. Open Questions

1. Initial Recovery zone model
2. HRV metric and sampling method
3. Confidence and fallback rules
4. Recovery cycle duration
5. Tempo Tailwind starting parameters
6. Anaerobic Shield scoring model
7. Reward energy budgets
8. Golden Streak persistence rules
9. Whether deferred rewards can carry across activities
10. External head-unit presentation

## 13. Core Principle

> Established training practice defines the intent. Tre Pulse makes progress
> visible. Ferrous Drive delivers a bounded physical reward.
