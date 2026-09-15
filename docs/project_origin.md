> 📚 [README](../README.md) · 🗺️ [Roadmap](roadmap.md) · 📐 [Architecture](architecture.md) · 🧠 [Assumptions]dation_matrix.md · 📜 decision_log.md

# Project Origin

> Measure effort. Preserve momentum. Learn from every ride.

**Estimated reading time:** 10 minutes

## TL;DR

Ferrous Drive started as a personal Rust learning project and an experiment in designing an e-bike motor controller who's assistance is more rider-aware, transparent, and testable. I am a passionate daily sports commuter and want to DIY design and build a system that matches my needs.

The original idea was to run a Rust control core on an ESP32, using a Cycle Analyst V3 to pass assistance requests to a Baserunner controller. Early reviews showed that the harder problems were not PWM generation or hardware integration, but deciding when telemetry could be trusted, explaining why assistance decisions were made, and validating behaviour without testing every change on a moving bike.

That led to three important changes:

1. The Cycle Analyst is no longer a required part of the architecture. Ferrous Drive now aims to produce controller-independent motor requests that can be translated by hardware-specific drivers. Direct Baserunner communication is still an unverified possibility, not a supported feature.
2. Sensor data will carry freshness and quality information rather than being treated as simply present or missing.
3. Development will begin with deterministic ride replay and simulation before moving to controller integration or physical hardware.

The current direction focuses on:

- Rider-effort-aware assistance
- Predictable and explainable control decisions
- Graceful handling of stale or missing sensors
- Separation between motion states and safety constraints
- Controller-independent architecture
- Replayable simulation and regression testing
- Evidence-driven hardware integration

Ferrous Drive is still experimental. Important details such as the Baserunner protocol, thermal behaviour, sensor timing, battery modelling, and real-world ride quality remain to be validated.

The project workflow is intentionally simple:

```text
Understand It
    ↓
Simulate It
    ↓
Validate It
    ↓
Ride It
```

Ferrous Drive exists both to build a useful open-source e-bike control platform and to create a practical, enjoyable way to learn Rust, embedded systems, simulation, and control-system design.

---

## Why Ferrous Drive Exists
