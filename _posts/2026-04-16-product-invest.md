---
layout: single
title: "Product Invest: Barcode to Stock Portfolio Scanner"
excerpt: "A native Android utility that scans physical product barcodes and aggregates real-time stock and institutional investment data for the public parent company."
last_modified_at: 2026-07-14
permalink: /product-invest/
read_time: false
show_date: false
categories:
  - Mobile Development
tags:
  - Android SDK
  - Java
  - Firebase
  - Gemini API
  - Yahoo Finance API
toc: true
toc_label: "App Architecture"
toc_sticky: true
header:
  teaser: /assets/images/productinvest/appIcon.png
sidebar:
  - title: "Platform"
    text: "Android (Native)"
  - title: "Role"
    text: "Solo Developer"
  - title: "Tech Stack"
    text: "Java, Firebase, Gemini API, Yahoo Finance API"

gallery_scanning:
  - url: /assets/images/productinvest/barcodescanning.jpeg
    image_path: /assets/images/productinvest/barcodescanning.jpeg
    alt: "Native Barcode Scanning Interface"
  - url: /assets/images/productinvest/productmanualinputscreen.jpeg
    image_path: /assets/images/productinvest/productmanualinputscreen.jpeg
    alt: "Manual Brand Input Failsafe"

gallery_investment:
  - url: /assets/images/productinvest/investmentinfo.jpeg
    image_path: /assets/images/productinvest/investmentinfo.jpeg
    alt: "Public Company Financial Insights"
  - url: /assets/images/productinvest/privatecompanyscreen.jpeg
    image_path: /assets/images/productinvest/privatecompanyscreen.jpeg
    alt: "Private Company Designation"
---

## Project Scope
Product Invest is a utility application designed to bridge the gap between everyday consumer goods and actionable financial market data. By simply scanning a product's barcode, the app identifies the manufacturer, determines its ultimate public-facing parent company, and aggregates real-time stock pricing and top institutional investors into a clean, readable dashboard.

![App Icon](/assets/images/productinvest/appIcon.png)

## The Challenge
Tracing a consumer product back to a publicly traded ticker symbol requires chaining together multiple APIs, which quickly becomes expensive and inefficient.
* **API Redundancy:** Scanning two different flavors of the same snack shouldn't require two separate, paid calls to financial APIs.
* **Corporate Nesting:** A barcode often registers to a small subsidiary brand, not the actual public parent company that is traded on the US stock exchange.
* **Database Gaps:** Standard barcode lookup APIs frequently lack coverage for niche or proprietary hardware items.

---

## 1. Local Caching & Database Matching
To drastically reduce API overhead, the application relies heavily on Firebase as a local caching layer. When a product is scanned, the app uses Android's built-in barcode scanning function to capture the digits and cross-references them against codes already stored in the database. 

If it finds a matching company code prefix, it verifies the age of the financial data, updates only the stale pricing metrics if necessary, and instantly displays the information. This ensures that any two products sharing a manufacturer bypass the expensive identification APIs entirely. Developing this mobile application using Java in Android Studio allowed for highly optimized native camera handling and rapid database polling.

---

## 2. The 3-Step Failsafe Resolution
If a company is completely new to the database, the app initiates a waterfall resolution sequence to find the manufacturer:
1. **Primary Lookup:** It queries a highly generous, free-tier barcode API.
2. **Secondary Fallback:** If the first fails, it drops down to a secondary, slightly more restrictive API database.
3. **Manual Override:** If both APIs fail to return a result, such as scanning a local brand's barcode, a popup dialog seamlessly intercepts the flow, prompting the user to manually type the brand name printed on the product.

{% include gallery id="gallery_scanning" layout="half" caption="**Left:** The primary hardware scanning viewfinder. **Right:** The manual input failsafe triggered by an unrecognized hardware barcode." %}

---

## 3. AI-Powered Parent Company Resolution
Identifying the brand is only half the battle. To find actionable investment data, the raw company name is sent securely to the Gemini API. 

Gemini acts as an analytical filter, specifically prompted to research the corporate hierarchy of the brand and return the US ticker symbol of its ultimate public-facing owner. If the company is independently owned or not traded in the US, Gemini accurately flags the entity as a private company.

---

## 4. Financial Data Aggregation
Armed with the correct US ticker symbol, the app executes its final data fetch across two distinct endpoints:
* **Market Pricing:** A dedicated financial API pulls the current stock price.
* **Institutional Backing:** The Yahoo Finance API retrieves the top institutional holders (e.g., Vanguard, Blackrock).

This complete financial profile is displayed to the user and permanently cached back into Firebase. Future scans of any product associated with that company will now pull directly from the database, creating a highly efficient, self-sustaining loop.

{% include gallery id="gallery_investment" layout="half" caption="**Left:** A successful resolution identifying the parent company (CPB) and institutional holders. **Right:** The system correctly flagging a manufacturer as a private entity." %}