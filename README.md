# OSM-RT Specification

The OSM-RT specification is an open standard which enables layers of additional data on top of OpenStreetMap data.

There is relevant data which cannot or should not be added to static OSM data for various reasons:

- The data is too dynamic like floating car data (FCD), temporary construction sites, dynamic traffic management, etc.
- The data is dealing with things too temporary for static OSM like meetings, events or exhibitions
- The data is coming from another data source that is not directly compatible with OSM (e.g. coming from another system)

## Key Features

- Similar to other data standards like CIFS (by Waze) or DATEX-II, OSM-RT does not rely on OSM-IDs but allows the use of a geometry to reference entities. Thus producers do not have to match their data to OSM entities and do not need prior knowledge about the specific OSM snapshot the consuming system is using. It's the task of OSM-RT consumers to match the geometry to OSM data.

- OSM-RT **is not a delta update stream.** An OSM-RT feed update will overwrite all preceding realtime information for the feed. Thus each update is expected to provide a full snapshot of all known realtime information.

- OSM-RT **is additive to the baseline OSM file.** Every tag previously attached to the matched OSM entity will stay valid unless it is explicitely overwritten by a tag in the update message. This avoids that data producing systems need to know all existing tags in the specific OSM snapshot the consuming system uses.

A data consumer (like a routing engine) can subscribe to and consume multiple OSM-RT endpoints and internally combine the consumed updates.

## Design Goals

- Lightweight and implementation-friendly
- Support both OSM entity references and geometry-based references
- Reuse established OSM key/value semantics where possible
- Enable efficient exchange of dynamic network information

The format extends concepts known from [**OsmChange**](https://wiki.openstreetmap.org/wiki/OsmChange) by allowing changes to be defined through geometries rather than requiring pre-mapped OSM entities.

## Dynamic Attributes

Permanent infrastructure properties (like road closures, construction-sites, opening_hours for time information) should continue to use standard (static) OpenStreetMap tags. OSM-RT might introduce additional attributes for operational and time-dependent data. Examples of information that is currently not covered by existing OSM tags are

- Current observed speed derived from traffic data or Floating Car Data (FCD)
- Historical average speed by weekday and hour or day
- Predicted speed
- Estimated traversal time
- Normalized traffic congestion indicator / "Level of Service"
- Traffic condition (e.g. `free_flow`, `dense`, `stop_and_go`, `blocked`)

## Temporal Validity

While consumers should generally treat OSM-RT attributes as transient observations and operational conditions there might be cases in which an OSM-RT feed augments static information that is not yet included in static OpenStreetMap data.

## Technical Best Practices

- The server hosting OSM-RT data SHOULD be configured to correctly report the file modification date which allows consumers to leverage the [If-Modified-Since HTTP header](https://www.rfc-editor.org/info/rfc7232/#section-3.3). This allows producers and consumers to avoid transferring feed contents that haven't changed and saves bandwith and ressurces and is especially important for feeds with a short `update_interval`
- Feeds SHOULD provide protocol buffer-encoded feed content by default when queried via an HTTP request at the given URL - consumers should not need to define special HTTP accept headers to receive protocol-buffer encoded content.
- Feeds SHOULD use HTTPS instead of HTTP (without encryption) to ensure feed integrity.
- Feeds MUST set the `last_updated` and `update_interval` fields in order to increase resource efficiency for transmission of data and avoid unnecessary high polling rates

## Contributing

The specification is not finished and nothing is carved in stone. We welcome questions, discussions or suggestions which add to the useability of the format. Please open an [Issue](https://github.com/Mobidrom/osm-rt/issues) if you think something is missing or could be improved.

## Known Implementations

|                  Producer                   |                    Consumer                    |             Type of Data             |
| :-----------------------------------------: | :--------------------------------------------: | :----------------------------------: |
| [**MOBIDROM**](https://github.com/Mobidrom) | [**MOTIS**](https://github.com/motis-project/) | Construction sites and road closures |
| [**MOBIDROM**](https://github.com/Mobidrom) | [**MOTIS**](https://github.com/motis-project/) |  INRIX Floating Car Data (planned)   |
