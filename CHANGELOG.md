## [Unreleased]

### Changed

#### Prototype hardware platform

- Replaced the planned ESP32-S3 prototype platform with the Nordic nRF54L15 DK.
- Selected the nRF54L15 DK as the initial platform for wireless fitness-sensor integration, control-system prototyping, and controller communication experiments.
- Kept the Ferrous Drive control core platform-independent so the project does not become permanently tied to one microcontroller family.

#### Embedded runtime direction

- Replaced the planned general-purpose RTOS approach with a native embedded Rust runtime based on RTIC.
- Selected `heapless` collections and fixed-capacity data structures as the preferred starting point for embedded data handling.
- Prioritized deterministic execution, bounded memory use, explicit task priorities, and minimal runtime dependencies.
- Kept the desktop replay simulator as the primary development environment, with the embedded runtime treated as an adapter around the shared control core.

#### Motor and smart-wheel baseline

- Replaced the Bafang G310 geared hub motor concept with the Grin V3 Rear All-Axle direct-drive hub motor.
- Selected the standard 6T winding as the current design baseline.
- Selected the THG torque-sensing Shimano HG freehub as the current rider-input sensing baseline.
- Updated the reference bicycle to the Fairlight Strael 4.0.
- Defined the current reference wheel as a removable 700C rear smart wheel with a nominal 35-622 tyre.
- Reframed the smart wheel as a reference hardware implementation for Ferrous Drive rather than the definition of the portable platform.

#### Controller architecture

- Removed the Cycle Analyst V3 from the required control path.
- Replaced the proposed PWM, RC-filter, level-shifter, and analog `AuxIn` architecture with a digital controller-driver architecture.
- Adopted a headless Grin motor controller as the current controller direction.
- Kept controller-specific protocol handling outside the Ferrous Drive control core.
- Retained controller-independent `MotorRequest` and telemetry abstractions as architectural goals.

#### Assistance and energy-flow model

- Expanded the architecture from one-way propulsion assistance to bidirectional energy and torque control.
- Added regenerative braking as a core design consideration enabled by the direct-drive hub motor.
- Added virtual freewheeling or drag-compensation behaviour as a requirement for preserving normal road-bike feel.
- Added integrated freehub torque and pedal-rotation sensing as potential primary rider-input sources.
- Expanded rider modes to include the intended sensations of Neutral, Support, Commute, and Recovery.
- Defined Neutral mode as assistance intended to offset direct-drive magnetic drag and added system mass rather than provide obvious propulsion.
- Updated the project direction from a simple assist controller toward a rider-oriented propulsion and energy-management platform.

#### Battery-pack direction

- Retained `10S2P`, 36 V nominal and 42 V maximum, as the current practical battery baseline.
- Added a lightweight fourteen-cell battery study based on a physical arrangement of two layers of seven cells.
- Identified `14S1P` as the primary fourteen-cell topology worth investigating for higher-voltage, low-cell-count packaging.
- Identified `7S2P` as physically possible but likely poorly matched to the current 6T, 700C, and approximately 20–35 km/h assistance target.
- Added regenerative charge acceptance, cold charging, per-cell current, voltage sag, BMS behaviour, and full-pack voltage as required battery-selection criteria.
- Clarified that physical cell layout and electrical series-parallel topology are separate design decisions.

### Added

#### Smart-wheel design baseline

Added a hardware reference design with the following initial targets:

```text
Reference bicycle:
Fairlight Strael 4.0

Primary use:
Year-round 35 km commute

Typical unassisted speed:
Approximately 27–33 km/h

Target assistance window:
Approximately 20–35 km/h

Prototype control computer:
Nordic nRF54L15 DK

Embedded runtime:
RTIC

Embedded memory strategy:
heapless

Motor:
Grin V3 Rear All-Axle

Winding:
Standard 6T

Freehub:
THG torque-sensing Shimano HG

Controller:
Headless Grin motor controller with digital communication

Reference battery:
10S2P
36 V nominal
42 V maximum

Reference wheel:
700C
Nominal 35-622 tyre
