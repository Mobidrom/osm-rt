---
title: GeoChangeset Introduction (v0.1-Draft)
shortTitle: GeoChangeset Introduction
date: 2026-07-20
status: Proposed Draft / RCC
domain: Mobility Data Exchange, Intelligent Transport Systems (ITS), and Dynamic Map Updates
---

# GeoChangeset Specification

## Introduction

Modern routing and navigation systems increasingly rely on dynamic traffic information to produce accurate route calculations and travel time estimations. Such information may include road works, detour recommendations, traffic congestion, temporary speed restrictions, incidents, or other events affecting road network accessibility and performance.

Integrating these data sources into routing engines presents several challenges. Incoming traffic information may be referenced using a variety of geospatial location models and encodings, including:

- OpenStreetMap (OSM) node and way identifiers
- DATEX II geometries encoded as GML Position Lists
- OpenLR location references
- GeoJSON geometries such as LineStrings, MultiLineStrings, and Feature Collections
- Other linear or area-based geospatial reference systems

In addition, the level of detail provided by source systems can vary significantly. Some sources provide highly structured traffic events with rich metadata, while others only deliver basic information about a disruption or restriction.

Many routing engines internally operate on OpenStreetMap-based network models and can efficiently process changes represented as OSM-style key/value attributes. Existing formats such as **OsmChange** provide mechanisms for describing modifications to OSM entities. However, these formats assume that affected network elements have already been mapped to specific OSM nodes or ways.

For traffic and mobility applications, this assumption is often insufficient. Real-world events are frequently described using geospatial references that do not correspond directly to existing OSM objects. Converting all incoming references to OSM nodes and ways as a preprocessing step may introduce information loss, increase processing latency, and reduce the precision of event localization.

To address these limitations, the **GeoChangeset** format extends the concepts established by OSM change formats by introducing geometry-based change definitions. Instead of requiring modifications to be expressed exclusively through OSM entities, a GeoChangeset can associate attribute changes with a wide range of geospatial location representations, including:

- OSM nodes and ways
- GeoJSON geometries
- GML geometries
- OpenLR location references
- Future geometry encodings through extensible location descriptors

The objective of GeoChangeset is to provide a lightweight, interoperable, and routing-oriented exchange format for dynamic transportation information. The format preserves the simplicity of OSM-style key/value attributes while enabling the use of heterogeneous geospatial references common in traffic management and mobility systems.

Implementations may resolve GeoChangeset geometries into internal network representations, transform them into conventional OSM change sets, or export them for processing by other OSM-based systems. This approach enables a consistent processing pipeline while supporting a broad range of traffic information providers and geospatial reference mechanisms.

## GeoChangeset-Specific Attributes

While GeoChangeset aims to reuse established OpenStreetMap key/value semantics wherever possible, certain transportation and traffic-related use cases require attributes that are not suitable for inclusion in the regular OpenStreetMap data model.

OpenStreetMap is primarily designed to represent relatively stable characteristics of physical infrastructure. In contrast, traffic information systems often need to exchange operational data that changes frequently, may be derived from real-time observations, and is only valid for a limited period of time.

For this reason, the GeoChangeset specification may define additional keys that are outside the scope of conventional OpenStreetMap tagging. These keys are intended to represent dynamic traffic conditions and network performance indicators rather than permanent road attributes.

Examples include, but are not limited to:

- `current_speed`: Current observed travel speed for a road segment, typically derived from Floating Car Data (FCD) or other real-time traffic measurements.
- `average_speed_hourly`: Historical average speed for a specific day of the week and hour of the day.
- `average_speed_daily`: Historical average speed aggregated by day.
- `travel_time`: Estimated traversal time for the affected segment under current conditions.
- `congestion_level`: Normalized indication of current traffic congestion.
- `traffic_state`: Operational traffic condition status such as free flow, dense traffic, stop-and-go traffic, or blocked.
- Additional traffic, mobility, or operational attributes defined by future versions of the specification.

GeoChangeset-specific keys are intended to **complement, not replace**, existing OpenStreetMap tags. Implementations should continue to use standard OSM keys wherever a suitable representation exists and reserve GeoChangeset-specific keys for dynamic or transient information that would be inappropriate for permanent inclusion in OpenStreetMap datasets.

The validity period of such attributes may be explicitly defined within a GeoChangeset or by metadata accompanying the change set. Consumers should therefore treat these values as time-dependent observations rather than persistent network properties.
