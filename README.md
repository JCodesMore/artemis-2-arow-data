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
| Wayback Machine | 13 | 2026-04-01 22:44:33 | 2026-04-03 11:59:56 | T+9m to T+37h25m |
| AROW GCS | 5,255 | 2026-04-02 00:24:22 | 2026-04-03 22:56:27 | T+1h49m to T+48h21m |
| JPL Horizons | 8 | 2026-04-02 06:34:38 | 2026-04-03 02:22:34 | T+7h59m to T+27h47m |

**Total: 5,276 files, ~102 MB**

### Wayback Machine Recovery

The Internet Archive crawled the AROW GCS bucket (`p-2-cen1`) during the launch window, capturing 13 snapshots of the live telemetry file at irregular intervals. Two of these snapshots are from **just 9-10 minutes after launch** -- the earliest known public AROW telemetry for the Artemis II mission.

| Wayback Capture (UTC) | Telemetry Time (UTC) | MET | Alt (km) | Full State Vector? |
|------------------------|---------------------|-----|----------|-------------------|
| Apr 1 22:45:39 | Apr 1 22:44:33 | **T+9m21s** | **205** | **Yes** |
| Apr 1 22:46:05 | Apr 1 22:45:33 | **T+10m21s** | **246** | **Yes** |
| Apr 2 00:34:33 | Apr 2 00:34:17 | T+1h59m | 7,146 | No (alt/lat/lon only) |
| Apr 2 02:30:14 | Apr 2 02:30:14 | T+3h55m | ~115,000 | Yes |
| Apr 2 11:56:41 | Apr 2 11:56:41 | T+13h21m | -- | Yes |
| Apr 2 15:04:59 | Apr 2 15:04:59 | T+16h30m | -- | No (timestamps only) |
| Apr 2 22:45:31 | Apr 2 22:45:31 | T+24h10m | -- | Yes |
| Apr 3 02:44:26 | Apr 3 02:44:26 | T+28h9m | -- | Yes |
| Apr 3 06:31:06 | Apr 3 06:31:06 | T+31h56m | -- | Yes |
| Apr 3 07:03:53 | Apr 3 07:03:53 | T+32h29m | -- | Yes |
| Apr 3 08:36:51 | Apr 3 08:36:51 | T+34h2m | -- | Yes |
| Apr 3 10:27:22 | Apr 3 10:27:22 | T+35h52m | -- | Yes |
| Apr 3 11:59:56 | Apr 3 11:59:56 | T+37h25m | -- | Yes |

The first two snapshots confirm Orion was in its initial parking orbit (185 x 2,253 km) at ~8.2 km/s, consistent with the post-MECO insertion orbit.

### Remaining Gap

```
LAUNCH    WAYBACK     WAYBACK        WAYBACK (alt only)    GCS ARCHIVE
  |       #1  #2           |              |                     |
  v       v   v            |              v                     v
  T+0 ··· T+9 T+10 ======= T+1h49m ····· T+1h59m ············ T+1h49m
  22:35   22:44 22:45      00:24          00:34                00:24

  ^^^^^^^^                  ^^^^^^^^^^^^^^^^
  T+0 to T+9m (no data)    T+10m to T+1h49m (no data)
```

**Gap 1 (T+0 to T+9m):** Ascent phase through MECO. No public telemetry exists for this period.

**Gap 2 (T+10m to T+1h49m):** Parking orbit coast through PRM and ARB burns. The T+1h59m Wayback snapshot (altitude only) provides one calibration point within this gap, but no continuous data exists.

**Events within the gaps:**

| Time (UTC) | T+ | Event |
|------------|-----|-------|
| Apr 1 22:35:12 | T+0 | Liftoff |
| ~22:37 | ~T+2m | SRB separation |
| ~22:43 | ~T+8m | MECO / Core stage separation (orbit: 185 x 2,253 km) |
| **22:44:33** | **T+9m** | **Wayback snapshot #1 (205 km alt, full state vector)** |
| **22:45:33** | **T+10m** | **Wayback snapshot #2 (246 km alt, full state vector)** |
| ~22:55 | ~T+20m | Orion solar array deployment |
| ~23:24 | ~T+49m | ICPS perigee raise burn (orbit: 185 x 2,222 km) |
| ~23:26 | ~T+51m | Brief comms dropout (NSN handover) |
| Apr 2 00:23 | ~T+1h48m | ICPS apogee raise burn (15 min, orbit: ~2,414 x ~70,400 km) |
| **00:24:22** | **T+1h49m** | **Continuous GCS archive begins** |
| **00:34:17** | **T+1h59m** | **Wayback snapshot #3 (7,146 km alt, partial params)** |

### Visual Timeline (first 50 hours)

```
Hour:  0    3    6    9   12   15   18   21   24   27   30   33   36   39   42   45   48
       |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |
WBK:   **·-·····*··········*··········*·········***·*··········*··········*
GCS:   --████████████████████████████████████████████████████████████████████████████████
HOR:   ---------████████████████████████---
       ^^
       GAP
```

## Directory Structure

```
artemis-2-arow-data/
├── README.md
├── gcs/                          # 5,255 AROW GCS telemetry files (~7s intervals)
│   ├── arow_gcs_20260402T002422Z.txt   (earliest)
│   ├── ...
│   └── arow_gcs_20260403T225627Z.txt   (latest)
├── wayback/                      # 13 Wayback Machine GCS snapshots
│   ├── arow_gcs_20260401T224433Z_wayback.json   (T+9m, earliest known)
│   ├── arow_gcs_20260401T224533Z_wayback.json   (T+10m)
│   ├── arow_gcs_20260402T003417Z_wayback.json   (T+1h59m, alt only)
│   ├── ...
│   └── arow_gcs_20260403T115956Z_wayback.json   (T+37h25m)
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

### Wayback Machine Files (`wayback/`)

GCS telemetry snapshots recovered from the Internet Archive's Wayback Machine. These are captures of the same `October/1/October_105_1.txt` file in the `p-2-cen1` GCS bucket, taken at irregular intervals by the Archive's web crawlers during the mission.

**Filename format:** `arow_gcs_YYYYMMDDTHHMMSSZ_wayback.json` (timestamp from the telemetry data's `Time` field)

The data format is identical to the `gcs/` files -- same JSON structure, same parameter numbering. Some snapshots contain the full 80+ parameter set (including XYZ position/velocity), while others captured a reduced parameter set (altitude/lat/lon or timestamps only). The two earliest snapshots (T+9m and T+10m) contain complete state vectors.

### Horizons Files (`horizons/`)

Ephemeris snapshots from JPL's Horizons API for object `-1024` (Artemis II Orion). These provide independent position/velocity data in km and km/s (J2000 frame).

## How This Data Was Collected

**GCS archive:** A Python poller running locally fetched the AROW GCS data stream every ~7 seconds and archived each raw JSON response. The poller was started approximately 1 hour 49 minutes after launch, which is why the continuous archive begins at T+1h49m.

**Wayback Machine recovery:** The Internet Archive's web crawlers independently captured the GCS telemetry file 13 times between April 1-3, 2026. These snapshots were discovered and recovered on April 3, 2026 by querying the Wayback Machine's CDX API for archived versions of the GCS bucket URL. Each snapshot captured the telemetry file at a single moment in time with a unique GCS `generation` ID, confirming they represent distinct data points.

**JPL Horizons:** Fetched periodically as a backup/cross-reference source via the public API.

## Usage

This data is useful for:
- Reconstructing Orion's trajectory during the Artemis II mission
- 3D visualization (e.g., OpenSpace, Cesium, or similar tools)
- Validating against official NASA ephemeris products
- Educational/research purposes

## License

This data originates from NASA public data streams and JPL's publicly accessible Horizons API. NASA data is generally not copyrighted (per NASA media usage guidelines). This archive is provided as-is for educational and research use.
