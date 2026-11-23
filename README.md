# osm-rt Specification

## Summary

`osm-rt`[^1] is a lightweight exchange format for dynamic mobility and transportation data to enrich routing networks with dynamic, operational, temporary and real-time information.

The specification enables routing systems to consume traffic and mobility data from heterogeneous sources and apply them to OpenStreetMap-based routing graphs using either OpenStreetMap identifiers or geometry-based references.

The specification is not finished and nothing is carved in stone. We welcome questions, discussions or suggestions which add to the useability of the format. Please open a [Discussion](https://github.com/Mobidrom/osm-rt/discussions) if you think something is missing or could be improved.

## Purpose

Routing engines increasingly depend on dynamic information such as:

- Road works
- Detours
- Traffic congestion
- Temporary speed restrictions
- Incidents and closures
- Other events affecting network accessibility or travel times

However, source systems use different location referencing mechanisms, including:

- OpenStreetMap node and way IDs
- DATEX II geometries (GML Position Lists)
- OpenLR references
- GeoJSON geometries (`LineString`, `MultiLineString`, `FeatureCollection`)
- Other linear or area-based geospatial references

`osm-rt` provides a common representation layer for translating such information into routing engine updates.

While the current focus is road traffic and routing, the specification is intentionally generic and may be used to augment OpenStreetMap-based networks with additional temporary or operational information outside the traditional OSM scope, including:

- Events and conferences
- Exhibitions
- Temporary access restrictions
- Mobility service information
- Other location-dependent, time-limited data

## Design Goals

- Lightweight and implementation-friendly
- Compatible with OSM-based routing engines
- Support both OSM entity references and geometry-based references
- Reuse established OSM key/value semantics where possible
- Enable efficient exchange of dynamic network information

The format extends concepts known from [**OsmChange**](https://wiki.openstreetmap.org/wiki/OsmChange) by allowing changes to be defined through geometries rather than requiring pre-mapped OSM entities.

## Dynamic Attributes

Permanent infrastructure properties should continue to use standard OpenStreetMap tags. `osm-rt` might introduce additional attributes for operational and time-dependent data. Examples of information that is currently not covered by existing OSM tags are

- Current observed speed derived from traffic data or Floating Car Data (FCD)
- Historical average speed by weekday and hour or day
- Predicted speed
- Estimated traversal time
- Normalized traffic congestion indicator / "Level of Service"
- Traffic condition (e.g. `free_flow`, `dense`, `stop_and_go`, `blocked`)

## Temporal Validity

Dynamic attributes may include validity periods using the OSM [`opening_hours syntax`](https://wiki.openstreetmap.org/wiki/Key:opening_hours) or compatible temporal constraints.

Consumers should treat `osm-rt` attributes as transient observations and operational conditions, not as persistent network properties.

## Known implementations

|                  Producer                   |                    Consumer                    |             Type of Data             |
| :-----------------------------------------: | :--------------------------------------------: | :----------------------------------: |
| [**MOBIDROM**](https://github.com/Mobidrom) | [**MOTIS**](https://github.com/motis-project/) | Construction sites and road closures |
| [**MOBIDROM**](https://github.com/Mobidrom) | [**MOTIS**](https://github.com/motis-project/) |  INRIX Floating Car Data (planned)   |

[^1]: The name is derived from the popular formats for public transport timetable and realtime information `GTFS` and `GTFS-RT`
