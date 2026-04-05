# Artemis II Trajectory Archive

Real-time telemetry data captured from NASA's AROW (Artemis Real-time Orbit Website) GCS data stream during the Artemis II mission, plus supplementary JPL Horizons ephemeris data.

## Mission Reference

- **Launch:** April 1, 2026 at 22:35:12 UTC (6:35:12 PM EDT), KSC LC-39B
- **Vehicle:** Orion MPCV (Multi-Purpose Crew Vehicle)
- **Crew:** Wiseman, Glover, Koch, Hansen
- **Mission:** ~10-day crewed free-return lunar flyby

| Time (UTC)     | T+       | Event                                                       |
|----------------|----------|-------------------------------------------------------------|
| Apr 1 22:35:12 | T+0      | Liftoff                                                     |
| ~22:37         | ~T+2m    | SRB separation                                              |
| ~22:43         | ~T+8m    | MECO / Core stage separation (orbit: 185 x 2,253 km)        |
| ~22:55         | ~T+20m   | Orion solar array deployment                                |
| ~23:24         | ~T+49m   | ICPS perigee raise burn (orbit: 185 x 2,222 km)             |
| Apr 2 00:23    | ~T+1h48m | ICPS apogee raise burn (15 min, orbit: ~2,414 x ~70,400 km) |

## Data Coverage

### What We Have

| Source       | Files | From (UTC)          | To (UTC)            | Post-Launch Offset     |
|--------------|-------|---------------------|---------------------|------------------------|
| AROW GCS     | 503   | 2026-04-01 22:30:32 | 2026-04-02 08:43:34 | T-0h05m to T+10h09m    |
| JPL Horizons | 1     | 2026-04-02 01:57:37 | 2026-04-10 23:53:25 | T+3h22m to T+9d-01h18m |

**Total: 505 files, ~10 MB**

### Gaps > 1 minute

| From (UTC)         | To (UTC)            | Gap Duration (s) |
|--------------------|---------------------|------------------|
|2026-04-01 23:13:55 | 2026-04-01 23:38:03 | 1448             |
|2026-04-01 23:38:03 | 2026-04-02 00:24:14 | 2771             |
|2026-04-02 00:25:15 | 2026-04-02 00:27:16 | 121              |
|2026-04-02 00:29:18 | 2026-04-02 00:35:18 | 361              |
|2026-04-02 00:36:19 | 2026-04-02 00:41:22 | 303              |
|2026-04-02 00:59:26 | 2026-04-02 01:06:32 | 426              |
|2026-04-02 01:30:56 | 2026-04-02 01:33:00 | 124              |
|2026-04-02 02:01:28 | 2026-04-02 02:03:30 | 122              |
|2026-04-02 03:39:21 | 2026-04-02 03:41:20 | 120              |
|2026-04-02 03:41:20 | 2026-04-02 03:43:20 | 120              |
|2026-04-02 03:59:22 | 2026-04-02 04:01:22 | 120              |
|2026-04-02 04:22:22 | 2026-04-02 04:24:23 | 121              |
|2026-04-02 04:33:23 | 2026-04-02 04:35:23 | 120              |
|2026-04-02 05:10:29 | 2026-04-02 05:13:32 | 183              |
|2026-04-02 05:15:33 | 2026-04-02 05:17:35 | 122              |
|2026-04-02 05:28:46 | 2026-04-02 05:30:48 | 122              |
|2026-04-02 05:50:06 | 2026-04-02 05:53:24 | 198              |
|2026-04-02 05:53:24 | 2026-04-02 05:55:25 | 121              |

### Visual Timeline (first 12 hours)

```
Hour:      0     3     6     9    12
           |     |     |     |     |
GCS:       █-*-█████████████-------
HOR:       -------█████████████████
Estimated:  █*█--------------------
```

## Directory Structure

```
artemis-2-arow-data/
├── README.md
├── Artemis2_Trajectory_J2000.csv             # reconstructed full trajectory from launch to Earth re-entry
├── gcs/                                      # 503 AROW GCS telemetry files (~1min intervals)
│   ├── arow_gcs_20260401T223031Z.txt         # (earliest)
│   ├── ...
│   └── arow_gcs_20260402T084333Z.txt         # (latest)
└── horizons/                                 # JPL Horizons Artemis II ephemeris data
    └── Artemis_II_OEM_2026_04_03_to_EI.asc   # Upper stage separation (~T+3h22m) to Earth re-entry (T+9d-01h18m)
```

## Data Format

### Reconstructed Continuous Dataset

A derived file (Artemis2_Trajectory_J2000.csv) provides a continuous spacecraft state history by filling gaps in the raw telemetry using physics-based methods.  

**Reconstruction Summary**  
- All gaps > ~150 seconds filled  
- 60-second cadence interpolation  
- 33 synthetic state vectors added  
- Original data preserved unchanged  
  
**Methods Used**
  1. Coast Phases (Two-Body Propagation)
      For all non-thrust periods: r̈ = −μ/r³ · r (numerical RK4 integration)
  2. Perigee Raise Burn (Impulsive Model)
      The perigee raise burn at 2026-04-01 23:24:00 UTC is modeled as an instantaneous impulse with estimated Δv ≈ 44.67 m/s.  
  3. Apogee Raise Burn (Finite Burn Model)
  
      The interval:

      - Start: 2026-04-02 00:23:00 UTC
      - End: 2026-04-02 00:38:00 UTC  
        
      is modeled as a finite-duration burn, not a ballistic coast.  
  
      Method:  
      - Boundary conditions derived from observed pre- and post-burn states
      - Cubic Hermite interpolation between endpoints:
      - Matches position and velocity at both ends
      - Produces smooth trajectory and velocity evolution

**Data Flags**
Inserted rows are labeled in the Source field:  
| Value              | Meaning                        |
|--------------------|--------------------------------|
| extrapolated_coast | Two-body propagated state      |
| extrapolated_burn  | Finite-burn interpolated state |
  
Attitude (q0–q3) is not reconstructed and remains blank for synthetic points.  
  
**Accuracy Notes**
- Coast segments are accurate to <~100–200 m over short gaps
- Burn segment is a physically consistent interpolation, not a thrust-resolved solution
- Long-term propagation does not include perturbations:
  - J2 oblateness
  - Third-body gravity (Moon/Sun)
  - Drag (negligible after LEO)

### AROW GCS Files (`gcs/`)

JSON files polled from NASA's AROW GCS (Google Cloud Storage) data stream. Each file is a snapshot of Orion's telemetry at a single point in time.  

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

### Horizons Data (`horizons/`)

Ephemeris data from JPL's Horizons API for object `-1024` (Artemis II Orion). This provides independent position/velocity data in km and km/s (J2000 frame).  

## How This Data Was Collected

**GCS archive:** A Python poller running locally fetched the AROW GCS data stream every ~7 seconds and archived each raw JSON response. Duplicate files were deleted, resulting in ~1 min intervals.  

**JPL Horizons:** Downloaded from NASA.gov (https://www.nasa.gov/wp-content/uploads/2026/03/artemis-ii-oem-2026-04-04-to-ei.zip?emrc=69d1829f7a009).

## Usage

This data is useful for:
- Reconstructing Orion's trajectory during the Artemis II mission
- 3D visualization (e.g., OpenSpace, Cesium, or similar tools)
- Validating against official NASA ephemeris products
- Educational/research purposes

## License

This data originates from NASA public data streams and JPL's publicly accessible Horizons API. NASA data is generally not copyrighted (per NASA media usage guidelines). This archive is provided as-is for educational and research use.
