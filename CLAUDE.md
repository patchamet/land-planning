# Land Planning Project

## Project Overview

This project contains SVG diagrams for land plot planning in Thailand.
Currently focused on visualizing a single land parcel with accurate measurements and spatial references.

## Current Files

- [land-plot-v2.svg](land-plot-v2.svg) — Main land plot diagram (latest version)

## Land Plot Details

### Plot Shape & Nodes
Pentagonal plot with 5 corner nodes:

| Node | SVG Coords | Description |
|------|------------|-------------|
| 1 | (150, 130) | Top-left (road frontage, start) |
| 2 | (430, 130) | Top-right (road frontage, end) |
| 3 | (430, 540) | Right (waterway corner) |
| 4 | (320, 780) | Bottom-right (waterway end) |
| 5 | (150, 780) | Bottom-left |

### Sides & Measurements

| Side | Edge | Length |
|------|------|--------|
| A | 1→2 (top) | ~40ม. — Road frontage |
| B | 2→3 (right, vertical) | ~59ม. — Waterway side |
| C | 3→4 (diagonal) | ~38ม. — Waterway side |
| D | 4→5 (bottom) | ~24ม. |
| E | 5→1 (left, vertical) | ~93ม. — Longest side |

### Scale
- **7 SVG units = 1 metre** (real-world scale)
- Grid pattern: 1m × 1m cells

## Surrounding Features

- **Road (ถนน)** — Top edge, drawn as dashed outer + solid inner line (y=80 outer, y=130 inner)
- **Waterway/Canal (ลำน้ำ)** — Along sides B and C, width ~50 SVG units (~7m), fill `#b8dff5`
- **Adjacent land (ที่ดินข้างเคียง)** — Relative's plot, shown bottom-right as gray lines

## SVG Conventions

- Main plot stroke: `#222`, stroke-width `2`
- Node circles: white fill, `#222` stroke, radius `7`
- Node labels: red `#c00`, font-size `16`, bold
- Side labels: blue `#0055cc`, font-size `16`, bold
- Measurements: blue `#0055cc`, font-size `12`, suffix `ม.` (metres)
- Grid ticks: gray `#999`, every 5m interval
- Road outer edge: dashed `8,5`, stroke `#444`
- Waterway outer edge: dashed `8,5`, stroke `#444`

## Language

Labels and measurements use **Thai** (ม. = เมตร = metres).
Comments in SVG are in English.
