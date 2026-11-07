# Project Scope

**Status:** Early development. This document records scope and design decisions and will change as the project evolves.

## Goal

A natural-language fishing and camping trip planner for the Eastern Sierra, built on a PostGIS database of reconciled public land, water, and conditions data. An LLM agent translates multi-constraint requests into queries against the spatial database and external APIs.

## Study area

Initial slice: the Highway 395 corridor in Inyo County, starting with:

- Bishop Creek drainage
- Rock Creek drainage
- June Lake area

The area is defined by drainage and bounding box, not by forest boundary. Land management (Inyo NF, BLM, Humboldt-Toiyabe NF, etc.) is stored as an attribute of each feature. Expansion to other drainages comes only after the first slice works end to end.

## Coordinate reference systems

| Purpose | CRS |
|---|---|
| Analysis and storage (distance, buffers) | EPSG:26911 (NAD83 / UTM Zone 11N, meters) |
| Incoming web API data (weather, GPS) | EPSG:4326 (WGS84, degrees) |
| Web map display only | EPSG:3857 |

Rules:
- All distance and buffer calculations are done in meters in EPSG:26911.
- Data is transformed on ingest. The source CRS of every dataset is recorded.
- Coordinates in raw sources are normalized to decimal degrees (lon/lat order for PostGIS) and the original value is kept in a raw column for auditing.

## First question the system must answer

> "Find campgrounds within 5 km of a fishable stream in my study area."

Version 1 is built to answer this question reliably. Later versions add conditions (current streamflow, forecast, road access) as additional constraints.

## Candidate data sources

To be evaluated; none ingested yet.

- USFS campgrounds and trails (FSGeodata Clearinghouse, Recreation.gov RIDB)
- CDFW fish stocking and fishing regulations
- USGS NHD hydrography and NWIS stream gauges
- NWS weather (api.weather.gov)

## Out of scope for v1

- Wilderness permits and reservation quotas
- Regions beyond the initial drainages
- Front-end polish
- Booking or reservation actions
- Authoritative regulations advice. Fishing rules will link to official CDFW sources and ask users to verify.

## Approach

Requirements, schema, CRS decisions, and test queries are written by me. Implementation is assisted by Claude Code, and I review and test all output, including visual checks on a map. Design decisions are recorded in `docs/decisions/`.

## Success criteria for v1

- At least three sources ingested and reconciled into one working CRS
- The first question answered correctly for the initial drainages, verified against a map
- A small set of test queries with expected results
- Documentation of the projection issues encountered and how they were resolved