---
title: GeoChangeset Addendum A - Semantic Tagging Profiles for GeoChangeset (v0.1-Draft)
shortTitle: GeoChangeset Addendum A
date: 2026-07-20
status: Proposed Draft / RCC
domain: Mobility Data Exchange, Intelligent Transport Systems (ITS), and Dynamic Map Updates
---

# GeoChangeset Addendum A - Semantic Tagging Profiles for GeoChangeset (v0.1-Draft)

## Preface

This addendum defines the standardized semantic tagging profiles for the `<geometry>` element within the GeoChangeset specification. To maximize interoperability between consumer navigation engines and corporate Traffic Management Centers (TMCs), these profiles map concepts natively found in **DATEX II (v3.x)** and **ISO 14819 (ALERT-C)** into explicit key/value strings.

## 1. Core Traffic Namespaces

All dynamic metadata tags appended to a `<geometry>` element must utilize one of two operational namespaces:

- `traffic:*`: Used to describe physical conditions, network constraints, traffic incidents, and raw performance metrics.
- `mobility:*`: Used to describe operational instructions, dynamic routing recommendations, and regulatory overrides.

---

## 2. Construction Sites & Lane Configurations

This profile describes temporary roadworks, operational directions, and precise lane-level configurations. Lanes are indexed as a comma-separated array from **left to right** relative to the direction of travel specified by the `traffic:direction` tag.

### Tag Reference Matrix

| Key                    | Value Type  | Example            | Description                                                                |
| ---------------------- | ----------- | ------------------ | -------------------------------------------------------------------------- | ---------------------------------------------------- | ---------------------------------------------------------------------------- |
| `traffic:restriction`  | String      | `construction`     | Defines the root constraint type.                                          |
| `traffic:direction`    | Enumeration | `forward`          | `backward`                                                                 | `both`                                               | The direction affected, relative to the digitization vector of the geometry. |
| `traffic:impact`       | Enumeration | `partial_closure`  | `full_closure`                                                             | General operational impact of the construction zone. |
| `traffic:lanes:total`  | Integer     | `3`                | Total number of physical lanes natively present at the segment.            |
| `traffic:lanes:closed` | Integer     | `1`                | Total count of blocked lanes within the active zone.                       |
| `traffic:lanes:vector` | Comma List  | `open,open,closed` | Left-to-right operational status of each lane in the designated direction. |

### XML Implementation Snippet

```xml
<!-- Example: Partial closure on a 3-lane highway, rightmost lane blocked due to roadworks -->
<geometry id="workzone_5501" version="1" type="geojson">
  <![CDATA[ { "type": "LineString", "coordinates": [[6.81, 51.22], [6.82, 51.23]] } ]]>
  <tag k="traffic:restriction" v="construction"/>
  <tag k="traffic:direction" v="forward"/>
  <tag k="traffic:impact" v="partial_closure"/>
  <tag k="traffic:lanes:total" v="3"/>
  <tag k="traffic:lanes:closed" v="1"/>
  <tag k="traffic:lanes:vector" v="open,open,closed"/>
</geometry>

```

---

## 3. Dynamic Reroute Recommendations

Dynamic reroutes map temporary alternative trajectories suggested by traffic control systems to bypass congestion, blockages, or high-pollution zones.

### Tag Reference Matrix

| Key                         | Value Type  | Example             | Description                                                              |
| --------------------------- | ----------- | ------------------- | ------------------------------------------------------------------------ | --------------------------------------------------------- |
| `mobility:type`             | Enumeration | `detour`            | `diversion`                                                              | Declares the geometry as an alternative trajectory layer. |
| `mobility:reroute:strategy` | Enumeration | `mandatory`         | `recommended`                                                            | Defines the legal enforcement status of the detour.       |
| `mobility:reroute:target`   | String      | `geo_lane_44102`    | The `id` of the constrained geometry or base map element being bypassed. |
| `mobility:reroute:signpost` | String      | `Follow U22 to A52` | Literal string shown on variable message signs (VMS).                    |

### XML Implementation Snippet

```xml
<!-- Example: A recommended alternative path bypassing the blocked workzone_5501 -->
<geometry id="detour_u22" version="1" type="geojson">
  <![CDATA[ { "type": "LineString", "coordinates": [[6.81, 51.22], [6.85, 51.24], [6.82, 51.23]] } ]]>
  <tag k="mobility:type" v="detour"/>
  <tag k="mobility:reroute:strategy" v="recommended"/>
  <tag k="mobility:reroute:target" v="workzone_5501"/>
  <tag k="mobility:reroute:signpost" v="Follow U22 to bypass construction"/>
</geometry>

```

---

## 4. Operational Traffic Constraints & Hazards

This layout defines external temporary structural limits, incidents, or environmental emergencies occurring on the target geometry to ensure precise mapping to DATEX II schemas.

### Extended Value Matrix (`traffic:restriction`)

| Value                     | Core Cause                                                             | Operational Intent                                                  | Typical Secondary Tags                              |
| ------------------------- | ---------------------------------------------------------------------- | ------------------------------------------------------------------- | --------------------------------------------------- |
| `accident`                | Vehicle collisions, jackknifed heavy goods vehicles, or vehicle fires. | Signals unexpected capacity drops or immediate hazardous blockages. | `traffic:lanes:closed`, `traffic:impact`            |
| `special_event`           | Marathons, cycling races, state visits, or public demonstrations.      | Planned or semi-planned short-term structural closures.             | `traffic:event:name`, `traffic:impact`              |
| `weather_hazard`          | Flash flooding, black ice, high winds, or severe snow drifts.          | Variable environmental hazards that restrict safe operating speeds. | `traffic:hazard:type`, `traffic:visibility`         |
| `environmental_emergency` | Peak ozone or particulate matter ($PM_{10}$) threshold breaches.       | Temporary regulatory speed reductions or vehicle-class bans.        | `traffic:restriction:hgv`, `traffic:flow:speed_max` |
| `structural_damage`       | Sinkholes, bridge joint failures, or immediate landslide risks.        | Sudden structural down-rating of infrastructure assets.             | `traffic:restriction:maxweight`                     |
| `security_incident`       | Unexploded ordnance disposal, police operations, or border checks.     | Absolute access denial enforced by public authorities.              | `traffic:impact="full_closure"`                     |

### XML Implementation Examples

#### Public Event (City Marathon)

```xml
<geometry id="event_marathon_2026" version="1" changeset="1024" type="geojson">
  <![CDATA[ { "type": "LineString", "coordinates": [[6.773, 51.227], [6.778, 51.231]] } ]]>
  <tag k="traffic:restriction" v="special_event"/>
  <tag k="traffic:event:name" v="Düsseldorf City Marathon"/>
  <tag k="traffic:direction" v="both"/>
  <tag k="traffic:impact" v="full_closure"/>
  <tag k="traffic:validity:end" v="2026-04-26T16:00:00Z"/>
</geometry>

```

#### Environmental Hazard (Flash Flooding)

```xml
<geometry id="flood_hazard_a46" version="2" changeset="1025" type="openlr-binary">
  <![CDATA[CwkBcns4OCQZBwD9/1YAFgEFAw==]]>
  <tag k="traffic:restriction" v="weather_hazard"/>
  <tag k="traffic:hazard:type" v="flooding"/>
  <tag k="traffic:direction" v="forward"/>
  <tag k="traffic:impact" v="partial_closure"/>
  <tag k="traffic:lanes:vector" v="closed,open,open"/>
  <tag k="traffic:flow:speed_max" v="60"/> <!-- Temporary max speed limit in km/h -->
</geometry>

```

#### Immediate Structural Damage (Bridge Joint Failure)

```xml
<geometry id="bridge_emergency_99" version="1" changeset="1026" type="gml">
  <![CDATA[
  <gml:Point xmlns:gml="http://www.opengis.net/gml/3.2" gml:id="p_99">
    <gml:pos srsDimension="2">51.228120 6.774910</gml:pos>
  </gml:Point>
  ]]>
  <tag k="traffic:restriction" v="structural_damage"/>
  <tag k="traffic:direction" v="both"/>
  <tag k="traffic:restriction:maxweight" v="3.5"/> <!-- Metric tons -->
  <tag k="traffic:restriction:hgv" v="prohibited"/>
  <tag k="traffic:impact" v="partial_closure"/>
</geometry>

```

---

## 5. Real-Time Flow & Measured Speed Transfer

This profile transmits floating car data (FCD), loop detector measurements, and performance tracking directly along the designated geometry vector. Speed values are always absolute values expressed in kilometers per hour (km/h).

### Tag Reference Matrix

| Key                             | Value Type | Example                | Description                                                                       |
| ------------------------------- | ---------- | ---------------------- | --------------------------------------------------------------------------------- |
| `traffic:flow:speed_measured`   | Integer    | `42`                   | The actual observed spatial mean speed across the geometry in km/h.               |
| `traffic:flow:speed_freeflow`   | Integer    | `120`                  | The historical reference free-flow speed for this segment in km/h.                |
| `traffic:flow:travel_time`      | Integer    | `185`                  | Time required to traverse the geometry segment in seconds.                        |
| `traffic:flow:jam_factor`       | Decimal    | `6.4`                  | Normalized congestion index spanning from `0.0` (free flow) to `10.0` (gridlock). |
| `traffic:flow:sample_timestamp` | ISO 8601   | `2026-07-19T01:25:00Z` | Precise UTC time boundary when the measurement window closed.                     |

---
