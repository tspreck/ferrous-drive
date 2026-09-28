# Changelog

All notable changes to Ferrous Drive are documented in this file.

The format is inspired by Keep a Changelog. The project is in early development,
so the Unreleased section may contain architecture and documentation changes
before corresponding firmware exists.

## [Unreleased]

### Added

#### Tre Pulse

- Added Tre Pulse as Ferrous Drive's shared visual, behavioural, range, and
  reward language.
- Added a three-segment interaction model for state, progress, reward, and
  battery range.
- Added Journey, Reserve, and Contingency energy presentation.
- Added a shared accumulation and reward engine for Recovery and Training.
- Added grace, decay, locked milestones, reward readiness, and constrained
  reward states.

#### Golden Streak

- Added a three-cycle Golden Streak.
- Added progressive gold presentation during the third valid cycle.
- Added a first-generation Golden Super Reward with double duration.
- Added explicit protection against increasing peak assistance beyond the
  validated profile ceiling.
- Added battery-aware full, shortened, deferred, and unavailable reward states.

#### Training model

- Added a sports-science-informed training model.
- Added explicit separation between established endurance practices, Ferrous
  Drive interpretations, and experimental game parameters.
- Added Tempo Tailwind as an initial Training profile concept.
- Added Anaerobic Shield as an initial Training profile concept.
- Added work-phase and reward-phase separation.

#### Recovery model

- Added relative Zone 2 Recovery targets.
- Added `1.0:1` baseline rider-to-motor assistance.
- Added proposed HRV-, heart-rate-, and power-informed adaptation up to `1.2:1`.
- Added conservative fallback when physiological telemetry is unavailable or
  untrusted.
- Added Recovery compliance progress through Tre Pulse.

#### Battery and energy

- Added Journey, Reserve, Contingency, normal reward, and Golden reward energy
  budgets.
- Added destination-reserve authorization before optional reward delivery.
- Added separate accounting for Recovery assistance, Training rewards, Golden
  extensions, and regenerative energy.
- Added Tre Pulse battery-range presentation.

#### Regenerative braking

- Added Tre Pulse integration for regen availability, active regen, constrained
  regen, and mechanical-only braking.
- Added explicit cancellation of normal and Golden positive reward torque on
  brake intent.
- Added streak and milestone preservation during legitimate safety braking where
  possible.
- Added reward interruption and restart validation requirements.

#### Validation and governance

- Added assumptions for Recovery physiology, reward energy, Golden Streak,
  brake sensing, speed-scheduled regen, and route-derived energy.
- Added validation matrices for Tre Pulse, Recovery, Training profiles, Golden
  Streak, battery rewards, human factors, and braking.
- Added milestone-based development sequencing from telemetry trust through
  instrumented route and human-factors validation.

### Changed

#### Project identity

- Reframed Ferrous Drive as an open-source Rust PAS training and
  energy-management system.
- Reframed assistance as contextual and sometimes earned rather than a
  conventional selectable power level.
- Added the project philosophy: Preserve, Regulate, Earn.

#### Rider modes

- Simplified Ferrous Drive to three top-level modes:
  - Neutral
  - Recovery
  - Training
- Reclassified Commute as a journey and energy context.
- Moved Tempo into the Training profile model.
- Replaced Active Recovery terminology with Recovery.
- Replaced Neutral Ride terminology with Neutral.

#### Recovery

- Replaced fixed Recovery wattage as the primary definition with a relative
  rider-specific Zone 2 model.
- Replaced the proposed `1.2:1` Recovery baseline with a conservative `1.0:1`
  baseline.
- Limited first-generation HRV-led Recovery adaptation to `1.2:1` pending
  validation.
- Reclassified higher Recovery reward ratios as superseded hypotheses.

#### Training

- Replaced separate Tempo and Anaerobic top-level modes with profiles inside
  Training.
- Clarified that earned reward assistance remains inactive during qualifying
  work, while required system compensation may remain active.
- Clarified that valid pedalling remains required for positive reward
  assistance.

#### Battery

- Retained the Molicel P50B `10S2P`, 360 Wh, `7 + 6 + 7` prototype baseline.
- Retained the current capacity pending measured rider power, battery current,
  cold capacity, and real regen evidence.
- Added reward-energy arbitration without changing physical topology.

#### Drive platform

- Retained the Grin V3 Rear All-Axle 6T as the sole active drive baseline.
- Reclassified lightweight geared-hub work as historical exploration.
- Replaced geared-motor protection language with direct-drive, controller,
  battery, axle, thermal, and rear-wheel constraints.

#### Architecture

- Added the Tre Pulse Engine between trusted telemetry and energy and torque
  arbitration.
- Kept regenerative braking independent from reward logic.
- Kept battery protection, braking, and final torque arbitration outside Tre
  Pulse.
- Preserved simulation-first and controller-independent development.

### Deprecated

- Deprecated the Triad UI Framework and Triad Pulse names in favour of Tre
  Pulse.
- Deprecated High Assist as a mode.
- Deprecated Commute as a mode.
- Deprecated fixed, universal Recovery wattage.
- Deprecated zero-effort positive reward assistance.
- Deprecated unvalidated higher Recovery ratios as first-generation behaviour.

### Removed

- Removed the lightweight geared-hub configuration from the active architecture.
- Removed references to nylon motor gears from the active Grin V3 design path.
- Removed the assumption that earned assistance must always be delivered when
  battery reserve is constrained.

### Safety

- Clarified that brake intent overrides every positive assistance and reward
  request.
- Clarified that legitimate safety braking must not be discouraged by streak or
  milestone mechanics.
- Clarified that hydraulic brakes remain mechanically independent and
  authoritative.
- Clarified that optional rewards cannot override arrival reserve, battery,
  motor, controller, thermal, or telemetry constraints.
- Kept first-generation learning in observation-only mode.

### Documentation

- Added `docs/tre_pulse_engine.md`.
- Added `docs/training_model.md`.
- Updated `README.md` for the three-mode Tre Pulse direction.
- Updated `docs/architecture.md` for behavioural, energy, and torque boundaries.
- Updated `docs/battery_design.md` for reward energy and Tre Pulse range.
- Updated `docs/regenerative_braking.md` for reward interruption and streak
  safety.
- Updated `docs/assumptions.md` and `docs/validation_matrix.md`.
- Updated `docs/decision_log.md` and `docs/roadmap.md`.
