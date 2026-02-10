---
title: "CNC Fabrication: Save the Ponds Project"
excerpt: "Design and manufacturing of custom steel donor recognition signage for the historic Paris Park Fish Hatchery restoration."
last_modified_at: 2026-02-09
categories:
  - Manufacturing
tags:
  - CNC Plasma
  - CAD/CAM
  - Fusion 360
  - Metal Fabrication
read_time: false
show_date: false
toc: true
toc_label: "Fabrication Process"
toc_sticky: true
header:
  teaser: /assets/images/plasma-cutter/fish-plaque.JPG
sidebar:
  - title: "Client"
    text: "Save the Ponds (Paris, MI)"
  - title: "Material"
    text: "16-Gauge Steel"
  - title: "Software"
    text: "Autodesk Fusion 360"
gallery:
  - url: /assets/images/plasma-cutter/fusion-template.png
    image_path: /assets/images/plasma-cutter/fusion-template.png
    alt: "Fusion 360 CAD Design"
    title: "1. CAD Design"
  - url: /assets/images/plasma-cutter/fish-cutting.jpg
    image_path: /assets/images/plasma-cutter/fish-cutting.jpg
    alt: "CNC Plasma Cutting Process"
    title: "2. Plasma Cutting Operation"
  - url: /assets/images/plasma-cutter/fish-plaque.jpg
    image_path: /assets/images/plasma-cutter/fish-plaque.jpg
    alt: "Finished Steel Plaques"
    title: "3. Cut Steel Plaque"
---

## Project Scope
I was commissioned to design and fabricate custom metal "donor plaques" for the **Save the Ponds** project in **Paris, Michigan**. The initiative aims to restore the historic ponds at the Paris Park Fish Hatchery. This project required a scalable manufacturing process to produce unique, weather-resistant steel plaques for each donor level.

![Paris Ponds Fish](/assets/images/plasma-cutter/fish-plaque.JPG)

## The Challenge
The project required producing a high volume of aesthetic metal parts that were durable enough for permanent outdoor installation in a Michigan winter.
* **Customization:** Each plaque needed to potentially bear a different donor name, requiring a parametric workflow in Fusion 360 to update text without breaking the geometry.
* **Manufacturability:** The design had to account for the "Kerf" (cut width) of the plasma torch to ensure legible text on smaller fish.
* **Material Efficiency:** Parts needed to be "nested" tightly on the steel sheet to minimize scrap cost for the non-profit fundraiser.

## Technical Implementation

### CAD Design & Stenciling (Fusion 360)
I utilized **Autodesk Fusion 360** to design the parametric fish geometry. A critical engineering decision was the **Typography Selection** to ensure successful plasma cutting:
* **Design for Manufacture (DFM):** Standard fonts are unsuitable for CNC cutting because the centers of "closed loop" letters (like 'O', 'A', and 'D') fall out when cut. I selected a specialized **Stencil Typeface** with pre-engineered bridges to retain internal geometry.
* **Kerf Consideration:** I chose a bold, heavy-weight font variant to ensure the bridge width exceeded the plasma torch's **kerf** (cut width). This prevented the connecting material from melting away during the cut, ensuring structural integrity without requiring manual vector repair.

### CAM & G-Code Generation
* **Nesting Strategy:** I optimized the layout of parts on the 4x8 steel sheet to maximize yield.
* **Toolpath Strategy:** Configured lead-ins and lead-outs (where the torch starts cutting) to occur on the scrap skeleton, ensuring the edge quality of the finished fish remained smooth.
* **Cut Parameters:** Tuned the cutting amperage and feed rate (IPM) specifically for the steel gauge to minimize "dross" (slag) and reduce post-processing time.

## Community Impact
This project directly supports the preservation of a local historical landmark. By providing in-house fabrication, I significantly reduced the cost compared to outsourcing to a commercial sign shop, maximizing the funds going directly to the hatchery restoration.

## Gallery
From digital vector design to physical steel installation.

{% include gallery id="gallery" layout="third" caption="**Process:** (1) Parametric design in Fusion 360. (2) The CNC plasma torch in operation. (3) The cut fish plaque." %}

## Video

{% include video id="yvMcQSzmuUQ" provider="youtube" %}