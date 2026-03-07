# Land Planning Project

## Project Overview

SVG land plot planning diagram for a single parcel in Thailand.
Scale: **7 SVG units = 1 metre**. Grid origin at node 1 (150,130).
- A-axis: horizontal (metres from left, A0=x=150)
- E-axis: vertical (metres from top, E0=y=130)

## Current Files

- [01-plot-overview.svg](01-plot-overview.svg) — ผังที่ดินรวม (เวอร์ชันเก่า ไม่มีบ้าน)
- [02-plot-full-plan.svg](02-plot-full-plan.svg) — ผังที่ดินรวม + ผังบ้าน
- [03-house-zone.svg](03-house-zone.svg) — ผังโซนบ้านแบบซูม
- [04-house-zone-3d.html](04-house-zone-3d.html) — โมเดล 3D โซนบ้าน

## Plot Shape (Pentagon)

Outer boundary nodes (original 5 corners): 1, 3, 8, 14, 13

| Side | Edge (new nodes) | Length | Feature |
|------|-----------------|--------|---------|
| A | 1→3 (top) | ~40ม. | Road frontage |
| B | 3→8 (right, vertical) | ~59ม. | Waterway side |
| C | 8→14 (diagonal) | ~38ม. | Waterway side |
| D | 14→13 (bottom) | ~24ม. | — |
| E | 13→1 (left, vertical) | ~93ม. | Longest side |

## All 14 Nodes

| Node | SVG (x,y) | A,E coords | Description |
|------|-----------|------------|-------------|
| 1 | (150, 130) | A0,E0 | Plot corner top-left / entrance left |
| 2 | (185, 130) | A5,E0 | Entrance right / road start |
| 3 | (430, 130) | A40,E0 | Plot corner top-right |
| 4 | (297, 305) | A21,E25 | House top-left |
| 5 | (430, 305) | A40,E25 | House top-right |
| 6 | (297, 480) | A21,E50 | House bottom-left |
| 7 | (430, 480) | A40,E50 | House bottom-right |
| 8 | (430, 540) | A40,E59 | Plot corner, waterway bend |
| 9 | (185, 620) | A5,E70 | Road junction top-left |
| 10 | (395, 620) | A35,E70 | Road junction top-right (bridge top) |
| 11 | (150, 648) | A0,E74 | Road junction bottom-left |
| 12 | (381, 648) | A33,E74 | Road junction bottom-right (bridge bottom) |
| 13 | (150, 780) | A0,E93 | Plot corner bottom-left |
| 14 | (320, 780) | A24,E93 | Plot corner bottom-right |

## Internal Road & Shoulder

- **Shoulder (ไหล่ทาง)**: A0–A1 (x=150–157), E0–E74, fill `#c8b8a0`
- **Vertical road**: A1–A5 (x=157–185), E0–E70, fill `#e8e4dc`
- **Horizontal road**: A1–C∩E70 (top), A1–C∩E74 (bottom), fill `#e8e4dc`
  - Intersections: E70∩C=(393.3,620), E74∩C=(380.5,648)
- Road boundaries: dashed `8,5`, stroke `#444`, stroke-width `1.5`

## Zone Fills (drawn before grid layer)

| Zone | Nodes | Fill | Opacity | Meaning |
|------|-------|------|---------|---------|
| Lower trapezoid | 11,12,14,13 | `#4dbb4d` green | 0.22 | บ้านใหม่ |
| Open/free area | 2,3,5,4,6,7,8,10,9 | `#ffa040` amber | 0.20 | ว่าง |

## Features

- **House to demolish**: A21–A40, E25–E50 → rect (297,305)–(430,480), fill `#f5c5c5`, dashed red border
- **Bamboo row (แนวไผ่)**: along rear boundary 11→13→14→12 starting A0,E76, 61 trees, 1m spacing, offset 5 SVG inward, fill `#4e8c20`
- **Bridge (สะพาน)**: polygon (395,620)–(440,640)–(427,669)–(381,648), fill `#ddd0b8`, perpendicular to side C
- **Entrance arrow (ทางเข้า)**: between nodes 1–2, pointing down from road, fill `#cc3300`
- **Legend**: right of outer waterway (x≈492), y=215–363, shows all zone colors

## Surrounding Features

- **Road (ถนน)**: y=80 (outer dashed) to y=130 (inner solid)
- **Waterway (ลำน้ำ)**: sides B+C, ~50 SVG wide (~7m), fill `#b8dff5`, outer dashed polyline (480,130)→(480,552)→(366,801)
- **Adjacent land**: bottom-right, gray lines at y=595 and y=845

## SVG Conventions

- Main plot stroke: `#222`, stroke-width `2`
- Node circles: white fill, `#222` stroke, radius `7`
- Node labels: red `#c00`, font-size `16`, bold
- Side labels: blue `#0055cc`, font-size `16`, bold
- Measurements: blue `#0055cc`, font-size `12`, suffix `ม.`
- Segment length labels: gray `#aaa`, font-size `10`
- Area labels: gray `#bbb`, font-size `16`, bold, prefix `~`, suffix `ตร.ม.`
- Grid ticks: gray `#999`, every 5m
- Grid cells: `#ccc`, stroke-width `0.4`, 1m×1m

## Language

Labels use **Thai** (ม.=metres, ตร.ม.=sq.metres). SVG comments in English.
