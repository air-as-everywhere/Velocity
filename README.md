# Velocity

### A premium GPS speedometer and journey intelligence application for Android.

**Velocity** is an offline-first Android application designed to measure speed, record journeys, visualize routes and analyze movement.

Built around a minimalist interface, Velocity combines reliable GNSS speed measurement with advanced recording and journey analysis.

The goal is simple:

**Measure accurately. Record reliably. Analyze intelligently.**

> **Development status:** Velocity 0.5.0 is the planned feature release. Features described below represent the development specification and should not be considered implemented until verified in the source code and tested.

---

## Overview

Velocity is designed for:

- 🚴 **Cycling:** Real-time speed, distance and route recording.
- 🚗 **Driving:** Journey statistics, speed history and route visualization.
- 🚄 **Railways:** Speed profiles, acceleration, cruising and braking analysis.
- 📊 **Analysis:** Detailed examination and replay of recorded journeys.

The application prioritizes measurement reliability, efficient operation, user privacy and a clean interface.

---

## Core Features

### 1. GPS Speedometer

Velocity uses Android's location system to obtain GNSS-based speed measurements.

**Capabilities**

- Real-time speed display.
- km/h and mph support.
- Native GNSS speed measurement.
- Coordinate-derived speed fallback.
- Measurement-quality assessment.
- Moving and stopped time.
- Distance tracking.
- Trip statistics.
- Background recording through an Android foreground service.

**Measurement philosophy**

GNSS is the primary speed source.

Complex inertial navigation is not required for normal speed display.

When a reliable speed measurement is unavailable, Velocity must clearly distinguish unavailable or stale data from a valid zero-speed measurement.

The application must never fabricate speed values.

---

### 2. Black Box Mode

Black Box Mode acts as a personal journey recorder.

It captures speed measurements and significant events throughout a recorded trip.

**Planned capabilities**

- Speed-over-time graphs.
- Movement and stop detection.
- Acceleration and deceleration events.
- GNSS signal interruptions.
- Tracking-service events.
- Timestamped event markers.
- Interactive journey timeline.
- Optional rolling recording buffer.

**Rolling buffer options**

- 30 seconds.
- 60 seconds.
- 120 seconds.

Users can inspect recorded events and save relevant portions of a journey.

---

### 3. Speed Accuracy Laboratory

The Speed Accuracy Laboratory compares multiple methods of estimating speed.

| Channel | Measurement |
|---|---|
| A | Native GNSS speed |
| B | Coordinate-derived speed |
| C | Filtered GNSS speed |

**Planned capabilities**

- Live speed comparison.
- Synchronized historical graphs.
- Measurement disagreement analysis.
- Speed-source identification.
- Measurement freshness indicators.
- Available uncertainty estimates.

The laboratory is designed to evaluate measurement consistency.

Agreement between methods does not independently establish absolute accuracy.

---

### 4. Railway Performance Analyzer

Velocity includes a dedicated analysis system for railway journeys.

**Performance metrics**

- Maximum verified speed.
- Maximum observed speed.
- Average moving speed.
- Time spent within speed bands.
- Acceleration milestones.
- Deceleration trends.
- Cruising duration.
- Longest sustained cruising period.
- Journey duration and distance.
- GNSS measurement coverage.

**Default speed bands**

| Speed range | Category |
|---|---|
| 0–40 km/h | Very low speed |
| 40–80 km/h | Low speed |
| 80–120 km/h | Moderate speed |
| 120–160 km/h | Higher speed |
| 160–200 km/h | High speed |
| 200–250 km/h | Very high speed |
| 250–300 km/h | High-speed railway |
| 300–350 km/h | High-speed cruising |
| Above 350 km/h | Extended high-speed range |

Speed bands are configurable.

Calculations use recorded measurements and timestamps.

Intervals with insufficient data must not be treated as verified measurements.

---

### 5. Motion Replay Studio

Motion Replay Studio allows users to revisit recorded journeys.

**Planned capabilities**

- Interactive journey timeline.
- Play and pause.
- Timeline seeking.
- 1×, 2×, 5× and 10× playback.
- Synchronized speed graphs.
- Recorded position playback.
- Event markers.
- Speed-source and measurement-quality information.

Playback must use recorded measurements.

Missing data must remain clearly identifiable.

---

## Maps and Routes

### OpenStreetMap Integration

Velocity uses OpenStreetMap-derived geographic data with a compatible Android mapping engine such as MapLibre Native.

**Planned mapping features**

- Live location display.
- Real-time route visualization.
- Historical journey maps.
- Start and finish markers.
- Route replay.
- Speed-coloured route segments.
- GNSS interruption markers.
- Black Box event markers.
- Railway performance visualization.
- GPX import and export.

### Speed-Coloured Routes

Routes can be coloured according to recorded speed.

**Cycling**

| Speed | Colour |
|---|---|
| 0–10 km/h | Green |
| 10–20 km/h | Yellow |
| 20–30 km/h | Orange |
| Above 30 km/h | Red |

**Railway**

| Speed | Colour |
|---|---|
| 0–80 km/h | Green |
| 80–160 km/h | Yellow |
| 160–250 km/h | Orange |
| 250–300 km/h | Red |
| Above 300 km/h | Purple |

Colours and thresholds may be customized.

Segments with insufficient measurement data must be distinguished from verified speed segments.

### Offline Maps

Velocity is designed to support offline map packages.

Planned capabilities include:

- Importing compatible offline map packages.
- Viewing installed map regions.
- Managing map storage.
- Removing downloaded regions.
- Selecting a compatible map source.

**Important:** OpenStreetMap's public tile servers must not be used for unauthorized bulk downloads or offline prefetching.

Offline maps must come from appropriately licensed sources that permit their intended use.

Map rendering and GPS recording operate independently.

A map-loading failure must never interrupt journey recording.

---

## Architecture

Velocity uses a shared measurement and recording pipeline.

```text
                  VELOCITY
                     |
              Android Location
                     |
                Speed Engine
                     |
               Journey Recorder
                     |
                Room Database
                     |
        +------------+------------+
        |            |            |
     Black Box    Analysis      Replay
        |            |            |
      Events     Speed Lab      Timeline
                 Railway       Route Map
                 Performance
```

### Design Principles

**Single location pipeline**

All features consume measurements from the same tracking engine.

**Shared recording system**

Black Box, Speed Accuracy, Railway Performance and Replay use common journey data.

**Measurement transparency**

Recorded values distinguish observed measurements, estimates and unavailable information.

**Offline-first**

Journey recording does not require an internet connection.

**Efficient operation**

The application avoids unnecessary sensor sampling, duplicated GPS listeners and expensive background processing.

---

## Technology

The intended Android technology stack includes:

| Component | Technology |
|---|---|
| Language | Kotlin |
| Platform | Android |
| Location | Android Location APIs / existing compatible provider |
| Database | Room |
| Mapping | MapLibre Native |
| Geographic data | OpenStreetMap-derived data |
| Background tracking | Android Foreground Service |
| Route exchange | GPX |
| Measurement export | CSV |
| User interface | Existing Velocity UI framework |

Actual dependencies and SDK versions must be verified against the project source.

---

## Privacy

Velocity is designed around local data ownership.

**Privacy principles**

- No mandatory account.
- No mandatory cloud synchronization.
- No advertising SDK.
- No unnecessary analytics collection.
- No automatic uploading of journey history.
- Local recording by default.
- User-controlled exports.
- User-controlled journey deletion.

Map tile requests may contact the configured map provider.

Offline map packages can reduce network dependency.

---

## Reliability

Velocity prioritizes reliable speed measurement over unnecessary algorithmic complexity.

### Speed Measurement

1. Prefer usable native GNSS speed.
2. Use coordinate-derived speed when appropriate.
3. Evaluate measurement quality and freshness.
4. Distinguish displayed estimates from verified statistics.
5. Never allow optional analysis systems to suppress valid speed measurements.

### Known Engineering Priority

Earlier development builds experienced intermittent missing speed readings during cycling.

The GNSS measurement and UI publication pipeline must be audited, corrected and tested before the new features are considered complete.

### Testing Targets

Testing should cover:

- Stationary operation.
- Low-speed cycling.
- Normal road speeds.
- High-speed railway movement.
- GNSS interruptions.
- Background tracking.
- Long recording sessions.
- Database migrations.
- Route playback.
- Offline operation.

---

## Development Roadmap

### Phase 1: Core Reliability

- Simplify experimental navigation systems.
- Remove obsolete diagnostics.
- Repair missing speed readings.
- Validate GNSS-first speed measurement.
- Preserve existing trip history.

### Phase 2: Journey Recording

- Extend the shared recorder.
- Implement Black Box events.
- Implement rolling recording buffers.
- Add measurement metadata.

### Phase 3: Speed Analysis

- Implement Speed Accuracy Laboratory.
- Add synchronized comparison graphs.
- Implement Railway Performance Analyzer.

### Phase 4: Maps and Routes

- Integrate MapLibre.
- Display live and historical routes.
- Add speed-coloured segments.
- Implement compliant offline map support.
- Add GPX import/export.

### Phase 5: Motion Replay

- Implement playback controls.
- Synchronize maps and graphs.
- Integrate Black Box event markers.
- Support historical journey inspection.

### Phase 6: Optimization

- Performance improvements.
- Battery optimization.
- Database testing.
- Android lifecycle testing.
- UI refinement.
- Release validation.

---

## Building Velocity

Velocity is intended to be built using Android Studio and Gradle.

### Requirements

- Android Studio compatible with the project.
- Android SDK matching the project configuration.
- Supported JDK and Gradle versions.
- Complete Velocity source project.

### Build

Open the project in Android Studio and allow Gradle synchronization to complete.

For a typical Gradle-based Android project, a debug APK can be built using:

```bash
./gradlew assembleDebug
```

The exact build commands, module names and toolchain requirements must be verified against the source project.

A successful compilation does not replace physical-device testing.

---

## Project Goals

Velocity is not intended to become a general-purpose navigation application.

Its primary purpose is to provide:

1. Reliable speed measurement.
2. Accurate journey recording.
3. Useful performance analysis.
4. Interactive route visualization.
5. Historical journey replay.

The application should remain visually minimal despite its analytical capabilities.

---

## OpenStreetMap Attribution

Map data © OpenStreetMap contributors.

OpenStreetMap data is available under the Open Database License (ODbL).

Map styles, fonts, icons and tile packages may have separate licensing requirements.

All applicable attribution and redistribution requirements must be respected.

---

## Version

**Project:** Velocity  
**Target release:** 0.5.0  
**Platform:** Android  
**Development status:** Planned / In Development  
**Primary focus:** Speed Measurement, Journey Intelligence and Offline Route Mapping

---

## Philosophy

> **Velocity should be simple when measuring and powerful when analyzing.**

A speedometer should display speed reliably.

A recorder should preserve what actually happened.

An analysis system should distinguish measurements from assumptions.

And a map should make the journey easier to understand, not make the application more complicated.

**Velocity: Every journey tells a story.**
