# Trinity Ring

## Purpose

Trinity Ring is the glanceable behavioural interface of Ferrous Drive.

It communicates progress, state, rewards, consistency, and momentum without turning the bicycle into a numeric dashboard.

```text
Garmin or ride computer
    Navigation
    Recording
    Detailed metrics
    Post-ride analysis

Trinity Ring
    Behaviour
    Motivation
    State
    Progress
    Rewards
```

## Hardware direction

```text
24 addressable LEDs
Dedicated power indicator
Dedicated ride-mode indicator
Central capacitive-touch surface
```

A 24-LED ring provides sufficient granularity for smooth progress and reward animations while allowing deliberate dark spacing between semantic groups.

## Design principles

1. Ride on feel.
2. Review detailed metrics afterwards.
3. Show behaviour, not data overload.
4. Make every state distinguishable without reading text.
5. Keep critical warnings visually distinct from rewards.
6. Avoid using colour as the only differentiator.
7. Dim aggressively for night riding.
8. Retain predictable interaction across prototypes.

## Semantic ownership

The core emits intent:

```text
Boot
Ready
Mode state
Progress
Milestone secured
Reward ready
Reward active
Golden reward
Lighting state
Power constraint
Warning
Fault
```

The hardware adapter maps intent into LED frames and timing.

## Progress model

Trinity Ring should support three secured Tre Pulse regions:

```text
Pulse 1
    Commitment established

Pulse 2
    Meaningful work secured

Pulse 3
    Objective completed
```

The 24 LEDs permit eight LEDs per pulse region while still allowing dark separators or animation boundaries.

The exact mapping remains experimental.

## Dedicated indicators

### Power indicator

Represents coarse system-power state:

- off;
- starting;
- ready;
- constrained;
- fault.

### Mode indicator

Represents the selected ride mode independently from progress animation.

The dedicated indicators prevent mode and power state from consuming Trinity Ring capacity.

## Capacitive touch

The centre touch surface provides one interaction model across project stages.

### Prototype mapping

```text
Tap
    Cycle lighting state

Long press
    Power or system action where safe
```

### Future candidate mapping

```text
Double tap
    Change ride mode

Triple tap
    Request regen enable or another guarded function

Long press
    Context-dependent system function
```

These future mappings are proposals, not locked behaviour.

Safety-relevant actions must require clear validation and must not rely on ambiguous gesture detection.

## Accessibility and night behaviour

- brightness must be limited for dark adaptation;
- animations must avoid distracting high-frequency flashing;
- state must remain understandable with reduced colour discrimination;
- touch acknowledgment must be immediate and unambiguous;
- fault presentation must supersede decorative animation;
- loss of the ring must not prevent safe lighting or shutdown behaviour.

## Initial V0.1 states

- [ ] off;
- [ ] boot sweep;
- [ ] ready confirmation;
- [ ] lighting mode indication;
- [ ] touch acknowledgment;
- [ ] three-stage progress demonstration;
- [ ] reward-ready state;
- [ ] reward-active state;
- [ ] golden reward demonstration;
- [ ] warning;
- [ ] fault override.

## Test strategy

```text
Host test
    Semantic state transitions

Bench test
    LED mapping and timing

Dark-room test
    Minimum brightness and glare

Glance test
    Recognition without sustained attention

Ride test
    Visibility, distraction and vibration tolerance
```
