# Changelog

All notable changes to Ferrous Drive are recorded here.

The project is currently in early development. Until the first tagged release, changes are listed under **Unreleased**.

For the reasoning behind major architectural changes, see docs/decision_log.md.

## [Unreleased]

### Added

- Created the public Ferrous Drive repository.
- Added the initial project vision, architecture, roadmap, and project-origin documentation.
- Added an assumptions register and validation-matrix work stream.
- Added a telemetry trust model covering valid, aging, stale, and invalid measurements.
- Added simulation-first development and deterministic ride replay as core project principles.
- Added explainable drive decisions and controller-independent motor requests to the architecture.
- Added regenerative braking and bidirectional energy flow to the system model.
- Added hub-side rider-power calculation using freehub torque and rotation.
- Added planned BLE cycling-power broadcasting to compatible head units.
- Added outdoor ERG-like regenerative resistance as an exploratory research idea.
- Added GitHub issue and contribution workflows to preserve project context and decision history.

### Changed

- Renamed the project from `ebike-os-core` to **Ferrous Drive**.
- Replaced the ESP32-S3 prototype direction with the **Nordic nRF54L15 DK**.
- Replaced the planned conventional RTOS architecture with an investigation of **RTIC**.
- Adopted **heapless** and fixed-capacity data structures as the preferred embedded-memory direction.
- Replaced the **Bafang G310 geared hub motor** with the **Grin V3 Rear All-Axle 6T direct-drive motor**.
- Replaced the mandatory Cycle Analyst V3 analog-control path with a controller-independent digital-driver architecture.
- Changed the system model from assist-only propulsion to assist, neutral, regeneration, and inhibited energy-flow modes.
- Changed the primary rider-input direction from an external power meter to the Grin motor’s integrated freehub torque and PAS sensors.
- Changed rider-power terminology to distinguish measured hub-side power from estimated crank-equivalent power.
- Changed slew-rate definitions from watts per control tick to watts per second.
- Separated motion-state modelling from thermal, battery, speed, braking, communication, and regeneration constraints.
- Changed control output from a single watt value to an explainable drive-decision model.
- Changed braking from a simple motor cut-off concept to a high-priority override that may permit bounded regenerative torque when conditions allow.

### Removed

- Removed the Cycle Analyst V3 as a mandatory system component.
- Removed the proposed PWM, RC-filter, and 3.3 V to 5 V analog-command architecture.
- Removed the ESP32-S3 as the current prototype target.
- Removed the Bafang G310 as the target motor.
- Removed the conventional RTOS as the preferred runtime direction.
- Removed the requirement for a separate external rider power meter from the target architecture.
- Removed the assumption that telemetry is either present or absent without freshness or quality information.

### Under Investigation

- Exact headless Grin motor-controller model and firmware.
- Digital command and telemetry protocol for the Grin controller.
- Controller acknowledgements, watchdogs, and communication timeouts.
- RTIC and HAL support for the nRF54L15.
- BLE-stack integration with the RTIC runtime.
- Torque-sensor calibration, drift, filtering, and dynamic response.
- Accuracy of hub-side rider power compared with a reference crank or pedal power meter.
- BLE Cycling Power Service compatibility with cycling head units.
- ANT+ stack availability, licensing, and nRF54L15 integration.
- Battery charge acceptance and regeneration limits.
- Safe regenerative-braking behaviour.
- Outdoor ERG-like resistance, including control stability and rider override.
- Simulation and bench-validation methods for assist-to-regeneration transitions.

### Safety Notes

- Ferrous Drive remains experimental and is not ready to control a ridden bicycle.
- Mechanical brakes remain independent and primary.
- Regenerative braking must not be treated as the only means of slowing the bicycle.
- Battery-management, motor-controller, overcurrent, and thermal protections must remain active.
- Direct digital controller operation must not be considered supported until communication and safe-state behaviour have been verified.
- Outdoor ERG-like resistance requires simulation, bench testing, wheel-off-ground testing, and controlled-environment validation before ridden experimentation.
