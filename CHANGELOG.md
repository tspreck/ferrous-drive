# Changelog

All notable changes to Ferrous Drive are documented in this file.

The project is in early development and does not yet publish stable releases.
Entries therefore track architectural and design milestones rather than released
production functionality.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and the project intends to use semantic versioning once versioned releases
begin.

## [Unreleased]

### Added

- Added a rider-first operating model with four intent-based modes:
  - **Neutral Ride** compensates only for penalties introduced by the installed
    electric system.
  - **Active Recovery** aims to preserve an easy physiological load using
    HRV-led, multimodal adaptation.
  - **Commute** aims to maintain a sustainable and repeatable journey while
    protecting arrival reserve.
  - **Tempo** aims to reward sustained rider effort near an adaptive sweet spot.
- Added a controller-independent torque model supporting positive, zero, and
  negative torque requests.
- Added a drive-capability abstraction so unsupported features, including
  regeneration, can be disabled explicitly.
- Added rider-triggered regenerative-braking architecture for compatible
  direct-drive systems.
- Added binary brake-lever intent sensing as the proposed first-generation
  braking trigger.
- Added a speed-dependent regenerative-torque relationship with:
  - A mechanical-only region
  - A low-speed taper region
  - A normal-speed regen region
  - A high-speed bounded region
- Added bounded wheel-speed and IMU feedback for regenerative deceleration.
- Added deterministic first-generation braking control with observation-only
  shadow learning.
- Added separate descent and junction regenerative-energy accounting.
- Added a shared 36 V battery concept based on a 10S2P Molicel P50B pack.
- Added a provisional `7 + 6 + 7` cell arrangement for bottle-style packaging.
- Added bidirectional battery-current, BMS charge-permission, voltage-rollback,
  and charge-temperature requirements.
- Added route-informed commute modelling based on recorded GPX traces from the
  current Vulpine 36c and aluminium-wheel commuter setup.
- Added a dual drive-system strategy covering:
  - A lightweight freewheeling geared-hub configuration
  - A Grin V3 Rear All-Axle direct-drive configuration
- Added dedicated regenerative-braking design documentation.
- Added updated architecture and battery-design documentation.
- Added an updated repository README reflecting the rider-first PAS training
  direction.

### Changed

- Reframed Ferrous Drive from deterministic propulsion control into a
  rider-intent and energy-management platform.
- Replaced conventional assistance-level terminology with cyclist-oriented
  training and journey intent.
- Renamed **Acoustic** mode to **Neutral Ride**.
- Renamed **Recovery** mode to **Active Recovery**.
- Renamed **Sport** mode to **Tempo**.
- Removed **High Assist** from the current project scope.
- Changed the top-level control path from a positive-only motor request to a
  torque arbiter capable of positive, zero, or negative torque.
- Changed regen estimation from a blanket route percentage to separate descent
  and junction opportunities derived from recorded ride traces.
- Changed Neutral Ride so regeneration remains available during explicit rider
  braking.
- Changed physiological adaptation to an HRV-led, multimodal model using HR,
  rider power, cadence, signal quality, route demand, and personal baselines.
- Changed self-learning scope so safety-relevant live control remains
  deterministic and learning begins in shadow mode.
- Changed the battery design baseline so regenerative charge capability is a
  first-class requirement even when the installed motor cannot regenerate.

### Documentation

- Added `docs/regenerative_braking.md`.
- Updated `docs/architecture.md` for bidirectional torque, braking priority,
  drive capabilities, physiological modes, and degraded operation.
- Updated `docs/battery_design.md` for bidirectional current, regenerative
  charging, BMS requirements, route recovery, and battery validation.
- Updated `README.md` with the current architecture, modes, motor strategy,
  battery platform, braking direction, and simulation-first workflow.
- Updated `docs/decision_log.md` with the current proposed and accepted design
  direction.
- Updated `docs/roadmap.md` with evidence-based validation phases and explicit
  gates before moving hardware.

### Safety

- Clarified that hydraulic brakes remain mechanically independent and
  authoritative.
- Clarified that stopping pedalling means coasting, not braking.
- Clarified that explicit brake intent overrides positive motor torque.
- Clarified that sensor faults may inhibit propulsion but must not automatically
  command continuous regen.
- Clarified that battery recovery never takes priority over predictable braking.
- Clarified that invalid or stale telemetry disables affected adaptation rather
  than being silently reused.

## Historical Note

Earlier project work established the simulation-first, controller-independent,
explainable-control foundation. Detailed pre-release history remains available
through the Git commit history until formal versioned releases begin.
