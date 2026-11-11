# 001: Working CRS for storage and analysis

**Status:** Accepted

## Context

The project combines data from several sources that arrive in different coordinate systems (for example, WGS84 lat/lon from web APIs and GPS, and projected systems in meters or feet from agency datasets). Distance, buffer, and proximity queries give wrong answers in degrees, so the data needs one working CRS with linear units.

The study area is the Eastern Sierra along the Highway 395 corridor, which falls entirely within UTM Zone 11N.

## Decision

Store and analyze all spatial data in **EPSG:26911** (NAD83 / UTM Zone 11N, meters).

- Incoming data is transformed to 26911 on ingest, and the source CRS of each dataset is recorded.
- Distance and buffer calculations are done in meters in 26911.
- EPSG:4326 is used only for incoming web API data, and EPSG:3857 only for web map display.

## Consequences

**Benefits:**
- Distances and buffers are in meters, so `ST_DWithin(a, b, 5000)` means 5 km.
- Distortion is very small within the zone, which suits this study area.

**Tradeoffs:**
- UTM is only accurate within its zone. Expanding beyond Zone 11N (for example, to the western Sierra or other states) means revisiting this decision.
- Data must be transformed on ingest, and a missed or wrong source CRS will silently misplace features. Each ingest step should be checked visually on a map.
- NAD83 and WGS84 differ slightly, and the difference can be about a meter or more. This is acceptable for campground and trail-level work.

## Verified

A test point near Bishop (-118.3951, 37.3636) transformed from EPSG:4326 to 26911 gives approximately (376460, 4136122), consistent with the expected easting and northing for the zone. Swapping the axis order raises a PROJ error (-14, latitude or longitude exceeded limits).