# Intergalactic Flight Log Database (IFLD)

This is an idea repo for now.  

## 1. Purpose & Vision

Imagine you could make all OSS flight control systems better, improve your own drones capabilities, and unlock a ton of unrealized fun.

The process would be trivial - an app that can take your flight logs, upload them to a collaborative database, and, optionally, add any data you want to explain them (links to videos, etc.).  

The **Intergalactic Flight Log Database (IFLD)** is a permissionless, survivable, and volunteer-driven data commons for unmanned aerial vehicle (UAV) flight logs. Its long-term objective is to help **open-source flight control software reach parity with, and eventually exceed, proprietary flight control systems** by addressing two core constraints:

1. **Data availability at scale** – large, diverse, real-world flight logs across vehicles, environments, and configurations.
2. **Systems integration feedback** – the ability to analyze, replay, simulate, and learn from those logs in ways that directly improve open-source control stacks.

IFLD treats these challenges not as a single technical problem, but as a combined **UX + incentive + infrastructure** problem. Success depends on making contribution easy, valuable to the contributor, and resilient over time.  This project is not technologically challenging, but it is an engineering problem that relies on enabling collaboration at multiple points.

---

## 2. Core Design Principles

1. **Low friction beats perfect security**\
   The system must be usable by non-cryptographers. If the UX requires ongoing key management, most contributors will disengage.  

2. **Personal value first, public value second**\
   Contributors  are creating *their own* persistent flight records; the commons emerges as a byproduct.

3. **Append-only, auditable data**\
   Flight logs are immutable once uploaded, but may be enriched later through signed metadata and annotations.

4. **Permissionless and durable storage**\
   Data should be independently mirrorable, verifiable, and reusable without requiring ongoing approval or centralized custody.

5. **Attribution matters**\
   Improvement of OSS control systems is a system-wide goal.  However, persistent identity and visible credit are individual incentives for participation.

---

## 3. Strategy Overview

### 3.1 Focus on the Solvable Problem: Data

Matching proprietary flight control systems requires massive real-world datasets accumulated over long periods. While full parity also depends on algorithmic sophistication and hardware integration, **data collection is the most immediately tractable bottleneck** for open-source ecosystems.

IFLD prioritizes:

- Large volumes of real flight data
- Diversity of vehicles, pilots, environments, and failure modes
- High-integrity provenance (signed uploads)
- Resilience of the database

### 3.2 Friction Reduction for Volunteers

Key tactics:

- One-click upload after initial setup
- Minimal required metadata at upload time
- Optional, delayed enrichment
- No requirement to understand cryptography, IPFS, or identity systems

### 3.3 Incentive Alignment

Contribution should benefit the contributor directly, immediately, and visibly. Altruism is welcome but not assumed.  The industrial ecosystem around drones benefits, and can incentivize users as well.

### 3.4 Durable, Permissionless Data

Flight logs and metadata are stored as content-addressed objects on IPFS. This ensures:

- Long-term availability independent of any single organization
- Verifiable integrity
- Broad reuse across research, simulation, and tooling ecosystems

This durability is important for long-lived research efforts and for maintaining continuity across changing institutional and technical landscapes.

---

## 4. Incentive Structure

The incentive model combines **recognition, utility, access, and optional status**, without coercion.

### 4.1 Personal Flight Portfolio

Each contributor identity maintains its own signed collection of flight logs:

- A personal, portable flight history
- Downloadable and mirrorable by the contributor
- Reusable for analysis, training, or demonstration (YOUR drone might become almost immediately better using the tools available to the community)

This portfolio framing turns contribution into asset-building rather than just  donation.

### 4.2 Attribution & Recognition

- Public contributor profiles (callsign / username)
- Metrics such as:
  - Number of flights
  - Total flight time
  - Vehicle categories
  - Environmental diversity
- Attribution in downstream analyses, tools, and research outputs

### 4.3 Contribution-Weighted Benefits

Possible additional benefits for contributors - these are ideas I have mostly for industry initiatives that integrate with IFLD:

- Access to enhanced analysis and visualization tools
- Early access to new open-source control algorithms
- Ability to compare personal flights against global baselines
- AIs or humans could provide actionable ways to improve your drone.

### 4.4 Soft Reciprocity

Possible restrictions for contributors - if necessary:

- Full dataset access may be prioritized for contributors
- Non-contributors may experience rate limits, delays, or partial access

This maintains fairness without punishing curiosity.

### 4.5 Community & Future Extensions

FUN STUFF HERE: Potential future incentives and use cases - imagine asking an AI to create a VR flythrough of an insane drone competition from the perspective of the winner - or a game in which you can try to replay the competition and fly against the other drones with your own added to the competition.  This and way more can happen with great datasets:

- Drone flying clubs sharing collective flight datasets
- Competitions uploading logs to recreate events in simulation or VR
- Educational replays of complex or interesting flights from arbitrary viewpoints
- Collaborative debugging and post-mortem analysis of failures

## 5. Identity & Authentication UX

### 5.1 Identity Model

- One passkey corresponds to one **Flight Identity**
- Flight Identity is defined by a public key hash
- Human-readable callsign/username maps to this identity
- Optional contact fields (email, links) may be added later

Key characteristics:

- No passwords
- No seed phrases
- No expectation of multi-device setup

Loss of a device implies loss of identity (if a backup passkey was not created on a second device); this is acceptable at early stages and can be revisited if adoption warrants more complex recovery.  It might be like losing an email address, it sucks, but you move on.  Unlike losing an email address, since users can upload metadata, they can upload and sign any other keys they want to, providing ways to validate and join Flight Identities by those keys at any time in the future, possibly this might be done by a third party front-end.

### 5.2 Signing Model

All actions are signed by the user’s passkey:

- Initial flight log upload
- Metadata additions
- Annotations or corrections

This provides provenance without requiring trust in a central authority.

---

## 6. Data Model (Conceptual)

### 6.1 Objects in the System

**Flight Log (Immutable)**

- Raw log artifact (IPFS CID)
- Source log type (e.g., PX4 ULog, ArduPilot DataFlash, DJI DAT, DJI Fly TXT)
- Uploader public key
- Upload signature
- Timestamp
- Basic extraction fingerprints (optional): duration, sample rate estimate, sensor set present, etc.

**Metadata Claims (Append-only)**

- References the raw log CID
- Signed by the same identity
- May include:
  - Vehicle and firmware configuration
  - Environment notes
  - Pilot commentary
  - Links to media (video, photos)
  - Post-flight analysis

**Derived Artifacts (Deterministic, Regenerable)**

- Parsed / indexed representations (e.g., extracted message indexes)
- Normalized log representations (IFLD standard)
- Simulation exports (e.g., formats compatible with replay tools)

Derived artifacts can be regenerated from the raw log + metadata and therefore do not need to be treated as the system of record.

---

## 7. Log Ingestion, Metadata, and Standardization

### 7.1 Problem Statement

To serve both the near-term goal (low-friction volunteer uploads) and the long-term goal (analysis + simulation + control-system improvement), IFLD must support:

1. **Accepting logs “as-is”** from both open-source and proprietary ecosystems, without requiring users to convert formats.
2. **Layering metadata on top** immediately and over time.
3. **Normalizing logs later** into a common, analysis- and simulation-friendly standard.

The app therefore implements a two-stage approach:

- **Stage A: Preserve & Prove** (upload raw logs + signed provenance + minimal metadata)
- **Stage B: Normalize & Use** (parse + map + export into a standard representation)

### 7.2 Common Log Types (Initial Target Set)

**Open-source / broadly documented ecosystems**

- **PX4 ULog** (binary, self-describing message/topic schemas)
- **ArduPilot DataFlash** (`.bin` onboard logs; structured messages; includes format definitions early in the log)
- **ArduPilot Telemetry Logs** (`.tlog` MAVLink message streams recorded by GCS)
- **Betaflight / Cleanflight Blackbox** (high-rate controller-state logging intended for offline analysis)

**Proprietary ecosystems (best-effort support, format evolves)**

- **DJI aircraft logs** (commonly `.DAT` extracted from the aircraft / device)
- **DJI mobile-app logs** (commonly `.TXT` from the app; sometimes encrypted/obfuscated depending on app/version)

Notes:

- Proprietary formats may require community-maintained parsers and careful versioning.
- The system should be designed so new parsers can be added without changing the upload UX.

### 7.3 “Consume As-Is” Upload Strategy

The desktop/mobile app should accept any supported raw log file directly:

- User selects a file (or a folder, where the app can detect logs)
- The app detects log type by signature/headers and common filename conventions
- The app generates a **Raw Log Record**:
  - content CID
  - detected log type + version guess
  - minimal extracted summary (duration, approximate start time, sample rate ranges)
- User optionally adds minimal metadata (vehicle type, flight purpose, location roughness, etc.)
- User signs and uploads

Crucially: conversion is never required from the user.

### 7.4 Metadata Layering (Now and Later)

Metadata is not embedded into raw logs. Instead, it is appended as signed claims that reference the raw log CID.

This supports:

- “Upload now, annotate later” workflows
- Multiple layers of metadata (pilot notes, club/competition tags, independent analyst tags)
- Provenance-preserving enrichment without mutating original data

### 7.5 Normalization Strategy: IFLD Flight Log Standard

Normalization is performed after upload (either locally on the contributor’s device or by any third-party toolchain), producing a standard representation suitable for analysis and simulation.

**Design goals for the standard**

- Represent diverse vehicles and log formats without forcing a single vendor schema
- Preserve original semantics as much as possible
- Support high-rate time series, events, and configuration snapshots
- Be easy to store, query, and stream

**Proposed structure: IFLD-Log v0 (conceptual)**

1. **Header / Provenance**

   - Raw log CID(s) referenced
   - Parser version used
   - Normalization version
   - Uploader identity pubkey (and any secondary identity links)

2. **Clock & Timeline Model**

   - A canonical monotonic timeline (microseconds)
   - Mapping(s) from source timestamps to canonical timeline
   - Optional wall-clock time if available/reliable

3. **Frames & Coordinate Conventions**

   - Explicit frame definitions (body, world/NED/ENU, sensor frames)
   - Transformation metadata if known

4. **Configuration Snapshots**

   - Firmware version, flight controller type
   - Parameters/tuning values (where available)
   - Sensor inventory and calibration info (where available)

5. **Signals (Time Series)**

   - Named signals with units, sample rates, and provenance
   - Grouped into common categories:
     - IMU / gyro / accel
     - magnetometer / barometer
     - GPS / position / velocity
     - attitude estimates
     - RC inputs / pilot commands
     - controller internals (PIDs, setpoints)
     - actuator outputs (motor commands)
     - power / battery / current

6. **Events**

   - Discrete markers (arm/disarm, failsafe, mode changes, EKF resets, GPS lock changes, collisions)

7. **Media Linkage (Optional)**

   - References to video/audio files (CIDs)
   - Time alignment claims (manual or computed)

**Implementation note**

For practical analysis and large-scale processing, the normalized form should have a “data-science friendly” serialization (e.g., Arrow/Parquet-like columnar structure) while remaining content-addressable and verifiable.

### 7.6 Parser Adapters and Mapping Tables

Each supported log type has a dedicated adapter that converts source fields into:

- Canonical time base
- Canonical units (with explicit conversion steps)
- Canonical signal names (with a mapping table maintained per source type/version)

The mapping should be versioned and transparent so that:

- improvements do not invalidate old uploads
- third parties can reproduce normalization deterministically

### 7.7 Handling Incomplete / Partial Proprietary Logs

Proprietary logs may:

- omit controller-internal details
- encrypt or obfuscate fields
- change schema across firmware/app versions

IFLD should treat these logs as still valuable:

- for trajectory, performance, and environment-derived analysis
- for club/competition replay and visualization
- for cross-validation against open-source logs

Normalization should be best-effort:

- parse what is available
- label unknown fields as unknown
- preserve raw bytes and reference the original CID

### 7.8 Simulation & Replay Outputs

Once normalized, logs can be exported into replay-friendly forms, enabling:

- Flight-path reconstruction
- VR flythroughs (competition replays from arbitrary viewpoints)
- Controller “bench simulation” using recorded inputs and sensor traces

Simulation exports should be derived artifacts built from IFLD-Log v0 and kept regenerable.

---

## 8. Authentication & Interaction Flows

### 7.1 First-Time User

1. Install desktop or mobile app
2. “Create Flight Identity”
3. Passkey created
4. Choose callsign
5. Ready to upload

### 7.2 Upload Flow

1. Select log file
2. (Optional) Enter minimal metadata
3. Confirm upload via biometric / device auth
4. Log is signed and uploaded to IPFS
5. User receives CID and attribution confirmation

### 7.3 Later Metadata Edit

1. Browse personal flight history
2. Select flight
3. Add or edit metadata
4. Sign metadata claim
5. Metadata appended without altering original log

### 7.4 Download / Mirror

1. Browse public or personal datasets
2. Download raw logs or aggregates
3. Verify signatures locally
4. Optionally pin or mirror data

### 7.5 Community Use

- Researchers and developers pull datasets
- Tools verify provenance via signatures
- Results credit contributing identities

---

## 8. Summary

IFLD aims to close the data gap that currently prevents open-source flight control systems from matching proprietary alternatives. By treating identity, UX, incentives, and storage durability as first-class design concerns, it seeks to build a data commons that is:

- Easy to contribute to
- Valuable to contributors
- Technically trustworthy
- Long-lived and broadly reusable

Notes:

The project’s success depends on quiet competence: reducing friction, respecting contributors, and letting individual self-interest align naturally with collective progress.

I've noticed that projects that are narrowly focused and developed in a decentralized and independent way seem to last a bit longer.  But validating data is a hard problem.

IPFS collaborative clusters are here: https://collab.ipfscluster.io/

