# ADR-002: Leaflet & OpenStreetMap Integration Over Google Maps Platform

## Status
Accepted

## Context
A primary capability of Rentify is visual property discovery via an interactive map. We evaluated two mapping engines:
1. **Google Maps JavaScript API**
2. **Leaflet 1.9 + OpenStreetMap / CartoDB raster tiles**

Key requirements:
- Cost sustainability at high monthly active user (MAU) volume.
- Freedom to render custom polygon masks (Ukraine territorial borders).
- Custom SVG clustered markers styled with Tailwind CSS tokens.
- Fast initial bundle download and zero proprietary vendor lock-in.

## Decision
We chose **Leaflet 1.9** and **React Leaflet 5.0** backed by OpenStreetMap and CartoDB tile layers.

## Consequences

### Positive
- **Zero Cost**: Eliminates usage charges from Google Maps API (which can scale to thousands of dollars per month).
- **Customizable**: Allows precise GeoJSON inverted masking of Ukrainian sovereign borders with custom fill opacity.
- **Lightweight**: Leaflet core library is less than 40KB gzipped, compared to Google Maps SDK overhead.
- **Custom HTML/SVG Markers**: Easy styling of price tag markers matching the Rentify design system.

### Negative
- Geocoding and place autocompletion rely on Nominatim or custom backend services, requiring rate-limit awareness and fallbacks.
- Lacks satellite 3D photogrammetry out-of-the-box, which is non-essential for 2D rental search.
