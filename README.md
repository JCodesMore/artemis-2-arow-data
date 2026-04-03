# Artemis II AROW Telemetry Archive

Real-time telemetry data captured from NASA's AROW (Artemis Real-time Orbit Website) GCS data stream during the Artemis II mission, plus supplementary JPL Horizons ephemeris snapshots.

## Mission Reference

- **Launch:** April 1, 2026 at 22:35:12 UTC (6:35:12 PM EDT), KSC LC-39B
- **Vehicle:** Orion MPCV (Multi-Purpose Crew Vehicle)
- **Crew:** Wiseman, Glover, Koch, Hansen
- **Mission:** ~10-day crewed free-return lunar flyby

## Data Coverage

### What We Have

| Source | Files | From (UTC) | To (UTC) | Post-Launch Offset |
|--------|------:|------------|----------|-------------------|
| AROW GCS | 5,255 | 2026-04-02 00:24:22 | 2026-04-03 22:56:27 | T+1h49m to T+48h21m |
| JPL Horizons | 8 | 2026-04-02 06:34:38 | 2026-04-03 02:22:34 | T+7h59m to T+27h47m |

**Total: 5,263 files, ~102 MB**

### What We're Missing

```
LAUNCH                    FIRST DATA POINT
  |                            |
  v                            v
  T+0 ======================== T+1h49m
  Apr 1 22:35:12 UTC          Apr 2 00:24:22 UTC

  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  GAP: ~1 hour 49 minutes
```

**The gap covers these mission events:**

| Time (UTC) | T+ | Event |
|------------|-----|-------|
| Apr 1 22:35:12 | T+0 | Liftoff |
| ~22:37 | ~T+2m | SRB separation |
| ~22:43 | ~T+8m | MECO / Core stage separation |
| ~22:55 | ~T+20m | Orion solar array deployment |
| ~23:24 | ~T+49m | ICPS perigee raise burn |
| ~23:26 | ~T+51m | Brief comms dropout (NSN handover) |
| Apr 2 00:23 | ~T+1h48m | ICPS apogee raise burn (15 min) |
| **00:24:22** | **T+1h49m** | **Our data begins here** |

### Visual Timeline (first 50 hours)

```
Hour:  0    3    6    9   12   15   18   21   24   27   30   33   36   39   42   45   48
       |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |
GCS:   --████████████████████████████████████████████████████████████████████████████████
HOR:   ---------████████████████████████---
       ^^
       GAP
```

## Directory Structure

```
artemis-2-arow-data/
├── README.md
├── gcs/                          # 5,255 AROW GCS telemetry files
│   ├── arow_gcs_20260402T002422Z.txt   (earliest)
│   ├── ...
│   └── arow_gcs_20260403T225627Z.txt   (latest)
└── horizons/                     # 8 JPL Horizons ephemeris snapshots
    ├── horizons_20260402T063438Z.txt   (earliest)
    ├── ...
    └── horizons_20260403T022234Z.txt   (latest)
```

## Data Format

### AROW GCS Files (`gcs/`)

JSON files polled from NASA's AROW GCS (Google Cloud Storage) data stream at ~7-second intervals. Each file is a snapshot of Orion's telemetry at a single point in time.

**Filename format:** `arow_gcs_YYYYMMDDTHHMMSSZ.txt` (UTC timestamp of when the data was fetched)

**Structure:**

```json
{
  "File": {
    "Date": "2026/04/02 03:43:35",
    "Activity": "MIS",
    "Type": 4
  },
  "Parameter_2003": {
    "Number": "2003",
    "Length": "8",
    "Status": "Good",
    "Time": "2026:092:08:43:33.769",
    "Type": "2",
    "Value": "-87140777.99858"
  }
}
```

**Key fields:**

| Field | Description |
|-------|-------------|
| `File.Date` | Wall-clock timestamp (EDT) |
| `File.Activity` | `MIS` = mission, `SIM` = simulation, `TST` = test |
| `File.Type` | `4` = active mission data |
| `Parameter_NNNN.Time` | DOY timestamp: `YYYY:DDD:HH:MM:SS.mmm` |
| `Parameter_NNNN.Value` | Numeric telemetry value |
| `Parameter_NNNN.Status` | Data quality (`Good` / etc.) |

**Key parameters for spacecraft state vector:**

| Parameter | Description | Unit |
|-----------|-------------|------|
| 2003 | Position X (Earth-centered J2000) | feet |
| 2004 | Position Y | feet |
| 2005 | Position Z | feet |
| 2009 | Velocity X | feet/s |
| 2010 | Velocity Y | feet/s |
| 2011 | Velocity Z | feet/s |
| 2012 | Attitude quaternion q0 (w) | unitless |
| 2013 | Attitude quaternion q1 (x) | unitless |
| 2014 | Attitude quaternion q2 (y) | unitless |
| 2015 | Attitude quaternion q3 (z) | unitless |

**Unit conversions:** Multiply feet by `0.3048` for meters. Multiply ft/s by `0.3048` for m/s.

Each file contains 105+ parameters including additional health/status telemetry beyond the state vector.

### Horizons Files (`horizons/`)

Ephemeris snapshots from JPL's Horizons API for object `-1024` (Artemis II Orion). These provide independent position/velocity data in km and km/s (J2000 frame).

## How This Data Was Collected

A Python poller running locally fetched the AROW GCS data stream every ~7 seconds and archived each raw JSON response. The poller was started approximately 1 hour 49 minutes after launch, which is why we're missing the initial post-launch window.

JPL Horizons data was fetched periodically as a backup/cross-reference source via their public API.

## Usage

This data is useful for:
- Reconstructing Orion's trajectory during the Artemis II mission
- 3D visualization (e.g., OpenSpace, Cesium, or similar tools)
- Validating against official NASA ephemeris products
- Educational/research purposes

## License

This data originates from NASA public data streams and JPL's publicly accessible Horizons API. NASA data is generally not copyrighted (per NASA media usage guidelines). This archive is provided as-is for educational and research use.
