# Ferrous Drive Decision Log

This document records project-level decisions that materially affect Ferrous Drive architecture, behaviour, validation, hardware direction, or contributor workflow.

The log preserves both the decision and its rationale so future work does not depend on chat history or undocumented assumptions.

---

## Decision status

| Status | Meaning |
|---|---|
| Proposed | Under active review on an idea or feature branch |
| Accepted | Current project direction |
| Superseded | Replaced by a later decision |
| Deferred | Intentionally postponed |
| Rejected | Considered but not adopted |

---

## Architectural principles

The following principles apply across all current decisions:

1. Safety constraints override rider experience, rewards, and visual behaviour.
2. Core logic should remain deterministic, portable, and testable away from the bicycle.
3. Board-specific drivers must remain behind explicit interfaces.
4. Experimental assumptions must be labelled and validated before becoming trusted.
5. Major architecture changes should preserve their rationale and migration path.
6. Detailed ride metrics belong on the ride computer and in post-ride analysis.
7. Ferrous Drive should reduce friction in both development and riding.

---

## 2026-10-06: Centre Ferrous Drive around Ferrous Hub

**Status:** Proposed  
**Branch:** `idea/ferrous-hub`

### Context

Ferrous Drive initially treated battery development as the central enabling project. Lighting, power conversion, monitoring, and future drive integration consequently depended on completion of an experimental battery pack.

That dependency placed too much uncertainty on the critical path:

- reclaimed ES2 cell condition was unknown;
- winter lighting depended on battery validation;
- boost conversion, charging, protection, and enclosure work all had to succeed first;
- Tre Pulse and local interaction lacked a stable hardware home.

The P2600 loaded kit now provides a complete, immediately usable winter-lighting solution. This removes safe winter lighting from the experimental battery critical path while preserving battery-module development as a separate learning track.

### Decision

Ferrous Drive will adopt a hub-centred architecture.

```text
Interchangeable Energy Modules
              ↓
         Ferrous Hub
           nRF54
              ↓
 Trinity Ring + Touch Interface
              ↓
 Lighting, Telemetry and Peripherals
              ↓
   Future Ferrous Drive System
```

The Ferrous Hub becomes the architectural centre of the platform.

### Ferrous Hub responsibilities

The Hub owns:

- system and ride-mode orchestration;
- Tre Pulse accumulation and reward state;
- local state machines;
- Trinity Ring semantic output;
- capacitive-touch interpretation;
- lighting coordination;
- telemetry aggregation and diagnostics;
- power-state coordination;
- peripheral health monitoring;
- future UART and CAN integration boundaries;
- future drive-controller command boundaries.

The Hub does not own:

- battery chemistry;
- cell selection;
- series or parallel cell configuration;
- cell balancing;
- cell-level protection;
- final motor commutation;
- route navigation;
- detailed ride recording;
- post-ride analysis.

### Consequences

- Ferrous Hub V0.1 becomes the next primary development track.
- Battery experiments continue in parallel rather than blocking Hub development.
- Tre Pulse gains a stable hardware and software home.
- Lighting, UI, telemetry, and interaction can evolve before the final custom PCB.
- Energy modules can change without rewriting behavioural logic.
- Hub power management and interface ownership become first-class design concerns.

### Architectural invariants

1. Battery chemistry must not leak into ride-mode or Tre Pulse logic.
2. Core behaviour must not depend on one microcontroller board.
3. Trinity Ring semantics must remain independent of the LED driver.
4. Lighting must remain usable when optional telemetry is unavailable.
5. P2600 must remain a functional standalone lighting solution.
6. Energy modules must be replaceable without rewriting behavioural logic.
7. Brake, electrical-fault, and power-safety inputs override rewards and visuals.
8. Detailed metrics remain the responsibility of the ride computer and post-ride tools.
9. Trinity Ring owns glanceable behaviour, progress, state, and rewards.
10. Experimental hardware must not become an undocumented source-of-truth dependency.

### Deferred decisions

- production Ferrous Hub PCB;
- final energy-module connector;
- production CAN protocol;
- final ANT+ representation;
- complete touch-gesture vocabulary;
- production enclosure;
- final main drive pack;
- final motor-controller interface.

---

## 2026-10-06: Use P2600 as the immediate winter-lighting solution

**Status:** Accepted

### Context

Reliable lighting is required for dark winter commutes. Existing backup lighting is insufficient or unreliable, and the Exposure light must be sent for repair.

The alternative E2000 route required a separate battery, converter, charging solution, enclosure, and validation before it could solve the immediate safety problem.

### Decision

Purchase and use the P2600 loaded kit as the immediate standalone lighting solution.

The kit includes the lamp, battery, charger, mount, and remote, allowing safe commuting without waiting for experimental Ferrous Drive battery work.

### Consequences

- winter lighting is removed from the prototype critical path;
- the P2600 becomes the first serious Ferrous Drive peripheral;
- Ferrous Hub can later coordinate lighting intent and feedback without making the lamp dependent on Hub firmware;
- P60B module development remains useful but is no longer urgent;
- the commercial battery and charger must not be modified for Hub V0.1.

### Boundary

For V0.1, Ferrous Drive may:

- represent lighting state;
- demonstrate lighting-control intent;
- map touch input to a prototype lighting state machine;
- log lighting-state transitions;
- investigate a safe future control interface.

For V0.1, Ferrous Drive must not:

- open or modify the commercial battery;
- replace or bypass its BMS;
- bypass the supplied charger;
- rely on undocumented connector pinouts;
- make safe lighting dependent on experimental firmware.

---

## 2026-10-06: Treat batteries as interchangeable energy modules

**Status:** Proposed  
**Branch:** `idea/ferrous-hub`

### Context

A battery-centred architecture coupled platform behaviour to one chemistry, voltage, configuration, and development path. Ferrous Drive needs to support experimentation without allowing cell choices to define system behaviour.

### Decision

Battery packs will be treated as interchangeable energy modules behind a stable electrical and telemetry boundary.

Current and future examples include:

- P2600 Power Pack XL;
- P60B 2S1P experimental module;
- P60B 2S2P experimental module;
- future main drive pack.

Reclaimed ES2 cells are deprioritized because their condition, capacity, and degradation are uncertain.

### Module ownership

Each module owns:

- chemistry and cell configuration;
- BMS behaviour;
- cell balancing;
- over-voltage and under-voltage protection;
- short-circuit and over-current protection;
- module thermal limits;
- safe charging requirements.

Ferrous Hub owns:

- platform power-state decisions;
- load coordination;
- optional telemetry consumption;
- subsystem enable and disable requests;
- user-visible power state;
- future destination and operating reserve policy.

### Consequences

- P60B experimentation remains relevant.
- P60B work is no longer a prerequisite for Ferrous Hub V0.1.
- Hub V0.1 must operate with power-only or minimal module-presence information.
- Rich BMS telemetry is optional rather than foundational.
- Production voltage, connector, charging ownership, and hot-plug behaviour remain open.

---

## 2026-10-06: Make Trinity Ring the local behavioural interface

**Status:** Proposed  
**Branch:** `idea/ferrous-hub`

### Context

The rider normally uses a Garmin or Karoo mounted in front of the bars and does not want a phone living near the stem. Detailed numbers are useful for navigation, occasional checks, and post-ride analysis, but constant numerical feedback can distract from riding on feel.

Ferrous Drive needs a small, glanceable interface for behaviour and motivation rather than another dashboard.

### Decision

Use a 24-LED addressable Trinity Ring as the primary local behavioural display.

Use separate dedicated indicators for:

- system power state;
- selected ride mode.

Place a capacitive-touch surface in the centre of the ring.

### Trinity Ring responsibilities

The ring communicates:

- progress;
- secured Tre Pulse milestones;
- consistency;
- reward readiness;
- active rewards;
- Golden rewards;
- lighting state;
- constrained operation;
- warnings and faults.

The ring does not primarily communicate:

- numeric power;
- battery percentages;
- detailed statistics;
- navigation;
- post-ride analysis.

### Interaction direction

Initial prototype use:

```text
Tap
    Cycle lighting state

Long press
    Guarded system action
```

Future candidate gestures:

```text
Double tap
    Change ride mode

Triple tap
    Request a guarded function such as regen enable

Long press
    Context-dependent system function
```

Future mappings remain proposals until gesture reliability and safety are validated.

### Consequences

- 24 LEDs are preferred over a nine-LED ring for progress granularity and animation quality.
- core logic should emit semantic display intent rather than raw LED frames;
- brightness must support dark adaptation;
- warning and fault states must override decorative animations;
- colour cannot be the only state differentiator;
- loss of the ring must not block safe lighting or shutdown behaviour.

---

## 2026-10-06: Keep nRF54L15 DK as the primary Ferrous Hub development platform

**Status:** Accepted

### Context

The nRF54L15 DK is already available and supports continued development of the long-term platform. The Adafruit Feather nRF52832 offers an attractive compact form factor for prototype packaging, but changing boards should not require rewriting Ferrous Drive behaviour.

### Decision

Continue primary Ferrous Hub development on the nRF54L15 DK.

Keep the Feather nRF52832 as an optional compact prototype target rather than the architectural centre.

### Software boundary

Portable core:

- ride modes;
- Tre Pulse;
- rewards;
- state machines;
- lighting policy;
- battery and power policy;
- telemetry model;
- fault policy.

Board-specific adapters:

- LED transport;
- touch input;
- timers and monotonic clock;
- BLE and future ANT+ transport;
- UART;
- future CAN;
- voltage and current sensing;
- board power control.

### Consequences

- portability becomes an explicit acceptance criterion;
- nRF54-specific APIs must not leak into core behaviour;
- host-side tests remain the preferred validation path for state logic;
- a future custom Hub PCB can reuse the same core;
- the estimated portability target remains high, but must be demonstrated rather than assumed.

---

## 2026-10-06: Defer INA226 from essential hardware to optional instrumentation

**Status:** Accepted

### Context

INA226 can measure voltage, current, and power and remains useful for rail monitoring, battery experiments, and subsystem validation.

The P2600 purchase removes the need for Ferrous Hub V0.1 to depend on immediate custom lighting-pack instrumentation.

### Decision

Treat INA226 as useful optional instrumentation rather than required V0.1 hardware.

### Consequences

- Hub V0.1 must boot and demonstrate its core interaction model without INA226;
- INA226 may later monitor the lighting rail, energy modules, or prototype subsystem loads;
- absence or failure of the monitor must not block basic operation;
- detailed power measurements remain valuable for bench validation.

---

## 2026-10-06: Separate detailed ride metrics from local behavioural feedback

**Status:** Accepted

### Context

The rider prefers to ride primarily on feel, often keeping the ride computer display dark or in power-save mode. Detailed numbers remain valuable for navigation, occasional form checks, selected segments, and post-ride review.

### Decision

The ride computer owns:

- navigation;
- ride recording;
- detailed metrics;
- post-ride analysis.

Ferrous Hub and Trinity Ring own:

- mode and system state;
- behavioural cues;
- Tre Pulse progress;
- reward feedback;
- lighting-state feedback;
- warnings and faults.

### Consequences

- Ferrous Drive must not require a phone mounted near the stem;
- Trinity Ring designs should avoid becoming a miniature numeric dashboard;
- ANT+ or BLE may later broadcast compact events and state, but local operation must remain independent;
- detailed ride data remains available without dominating the riding experience.

---

## 2026-09-28: Use idea branches and early draft pull requests for major architecture changes

**Status:** Accepted

### Context

Major changes can affect architecture, roadmap, assumptions, documentation, simulation behaviour, and future contributors. Editing the main branch directly risks incomplete migrations and loss of decision context.

### Decision

Project-wide or architectural changes will use:

```text
Idea or feature branch
        ↓
Early draft pull request
        ↓
Decision and migration review
        ↓
Coherent merge into main
```

### Consequences

- the Ferrous Hub migration is developed on `idea/ferrous-hub`;
- the decision log is updated before or alongside implementation documents;
- draft pull requests carry unresolved questions and migration checklists;
- implementation work should follow the accepted architecture in focused pull requests;
- main should not contain a half-migrated architectural story.

---

## 2026-09-28: Name the shared interaction language Tre Pulse

**Status:** Accepted

### Context

Ferrous Drive needs one consistent term for the shared visual and behavioural language used across progress, rewards, state, and future interaction.

### Decision

Use **Tre Pulse** as the project term.

Tre Pulse replaces the earlier **Triad Pulse** wording.

### Consequences

- new documentation and code should use `Tre Pulse`;
- historical references may remain when explicitly described as superseded;
- Trinity Ring becomes the primary local expression of Tre Pulse;
- terminology checks should prevent accidental reintroduction of the obsolete name.

---

## Open decisions

The following items remain intentionally unresolved:

- exact 24-LED ring model and voltage;
- capacitive-touch device for the first prototype;
- prototype power-rail design;
- logic-level conversion requirements;
- exact P2600 control interface, if any;
- production energy-module connector;
- module-identification strategy;
- custom Hub PCB architecture;
- enclosure and mounting system;
- ANT+ event representation;
- CAN transport and message ownership;
- final gesture mapping;
- final main drive pack;
- production commute, recovery, and training parameters.

---

## Superseded directions

### Battery-centred architecture

**Status:** Superseded on 2026-10-06

The battery is no longer treated as the product or the centre of the platform.

Battery development remains important, but now proceeds as interchangeable energy-module work behind the Ferrous Hub boundary.

### Reclaimed ES2 pack as the winter-lighting critical path

**Status:** Superseded on 2026-10-06

The P2600 loaded kit now solves immediate winter lighting. Reclaimed ES2 cells may still support isolated learning, but their uncertain condition means they are no longer a required project step.

---

## Decision review checklist

Before accepting a proposed decision, confirm:

- [ ] The problem and context are clear.
- [ ] The chosen direction and ownership boundaries are explicit.
- [ ] Safety implications are recorded.
- [ ] Positive and negative consequences are included.
- [ ] Deferred questions are visible.
- [ ] Experimental values are labelled as assumptions.
- [ ] Related architecture and roadmap documents are updated.
- [ ] Historical rationale is preserved.
- [ ] The repository tells one coherent story.
