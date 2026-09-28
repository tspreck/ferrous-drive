# Assumptions

This register separates known design decisions from claims that still require
evidence.

## Status Values

| Status | Meaning |
|---|---|
| Open | Not yet supported by evidence |
| Provisional | Used for design or simulation |
| Partially supported | Some evidence exists |
| Validated | Verified against defined acceptance criteria |
| Superseded | Replaced but preserved historically |

## A-001: Prototype battery usable energy

**Assumption:** The 360 Wh nominal battery provides 320 Wh of mild-weather
planning energy and 288 Wh in the provisional cold scenario.

**Status:** Provisional

**Validation:** Instrumented charge and discharge tests across temperature and
state of health.

## A-002: P50B pack architecture

**Assumption:** A 10S2P P50B pack in the `7 + 6 + 7` physical layout can meet
electrical, thermal, environmental, and mass requirements.

**Status:** Open

**Validation:** CAD study, weighed mechanical prototypes, electrical review,
thermal testing, vibration, water, and mounting tests.

## A-003: Bidirectional BMS compatibility

**Assumption:** A suitable 10S BMS can support controller regeneration and
expose reliable charge permission.

**Status:** Open

**Validation:** Bench testing with the selected controller under full, cold,
and faulted battery conditions.

## A-004: Journey-reserve prediction

**Assumption:** Route, rider, battery, and weather inputs can predict arrival
energy accurately enough to authorize optional rewards.

**Status:** Open

**Validation:** Compare predicted and measured arrival energy over repeated
outbound and return rides.

## A-005: Tre Pulse range comprehension

**Assumption:** Journey, Reserve, and Contingency are understandable through
three segments without requiring raw state-of-charge interpretation.

**Status:** Open

**Validation:** Rider comprehension and usability testing.

## A-006: Recovery zone model

**Assumption:** A configured relative Zone 2 model can represent the intended
Recovery workload.

**Status:** Provisional

**Validation:** Explicitly compare power, HR, and first-threshold approaches
using measured rider data.

## A-007: HRV-led assistance adaptation

**Assumption:** Trusted HRV, HR, and power trends can safely adjust Recovery
support from `1.0:1` toward `1.2:1` without unstable or misleading behaviour.

**Status:** Open

**Validation:** Offline analysis, simulation, then controlled rider studies.

## A-008: Recovery upper ratio

**Assumption:** `1.2:1` is a useful and sufficient first-generation upper
Recovery ratio.

**Status:** Provisional

**Validation:** Route replay, energy analysis, comfort testing, and
physiological-response comparison.

## A-009: Training work remains authentic

**Assumption:** Removing earned reward assistance during accumulation preserves
the intended training stimulus while system compensation remains active.

**Status:** Open

**Validation:** Compare rider power and physiological response with and without
the work/reward split.

## A-010: Tre Pulse milestones improve motivation

**Assumption:** Three visible milestones improve engagement without distracting
the rider or encouraging unsafe behaviour.

**Status:** Open

**Validation:** Human-factors and rider-feedback studies.

## A-011: Golden Streak improves consistency

**Assumption:** A Super Reward after three consecutive completions improves
motivation and repeatability.

**Status:** Open

**Validation:** Compare completion and repeat behaviour with the feature enabled
and disabled.

## A-012: Double-duration Golden reward

**Assumption:** Doubling duration creates a meaningful reward without excessive
battery or training distortion.

**Status:** Provisional

**Validation:** Simulation, energy budgeting, and rider validation.

## A-013: Reward constraint communication

**Assumption:** Riders will understand full, shortened, deferred, and unavailable
reward states through Tre Pulse.

**Status:** Open

**Validation:** UI comprehension testing in static and moving scenarios.

## A-014: Binary brake intent

**Assumption:** Binary Hall sensors provide sufficiently early and reliable
brake intent for first-generation regen.

**Status:** Open

**Validation:** Lever-specific mechanical prototypes and environmental tests.

## A-015: Speed-scheduled regen

**Assumption:** A deterministic speed map with bounded IMU and wheel-speed trim
can provide predictable routine braking.

**Status:** Open

**Validation:** Bench, lifted-wheel, rolling, and closed-course tests.

## A-016: Safety braking can preserve streak state

**Assumption:** Progress can pause during legitimate braking without making the
behaviour engine exploitable or confusing.

**Status:** Open

**Validation:** Simulation of traffic, junction, descent, and interrupted
training scenarios.

## A-017: Mechanical-braking inference

**Assumption:** Mechanical braking contribution can be inferred sufficiently for
analysis from deceleration and commanded regen.

**Status:** Open

**Validation:** Compare inference against additional lever-position or pressure
instrumentation.

## A-018: Route-derived regen

**Assumption:** Existing GPX traces identify useful descent and junction regen
opportunity.

**Status:** Partially supported

**Limitation:** Brake intent and battery current were not recorded.

**Validation:** Measure bidirectional battery current and brake events on the
physical route.

## A-019: Grin direct-drive platform

**Assumption:** The Grin V3 and selected controller expose sufficient command,
telemetry, thermal, and watchdog behaviour for Ferrous Drive.

**Status:** Open

**Validation:** Obtain protocol information and complete controller bench tests.

## A-020: First-generation learning remains shadow-only

**Assumption:** Observation-only learning provides useful calibration insight
without altering live safety behaviour.

**Status:** Provisional

**Validation:** Compare offline recommendations against deterministic-map
performance.
