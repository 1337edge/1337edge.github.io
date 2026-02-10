---
title: "Automated Press & Sortation System"
excerpt: "Custom embedded control system for a Tier 2 metal casting supplier, processing Ford engine components."
last_modified_at: 2026-02-08
categories:
  - Embedded Systems
tags:
  - C++
  - Circuit Design
  - Industrial Automation
  - Arduino
read_time: false
show_date: false
toc: true
toc_label: "System Design"
toc_sticky: true
header:
  teaser: /assets/images/industrial-machine/full-board.JPG
sidebar:
  - title: "Industry"
    text: "Metal Casting (Tier 2 Auto)"
  - title: "Tech Stack"
    text: "C++, Arduino Mega, Custom PCB"
gallery:
  - url: /assets/images/industrial-machine/wiring.JPG
    image_path: /assets/images/industrial-machine/wiring.JPG
    alt: "Hand-drawn circuit design"
    title: "1. Circuit Design Sketch"
  - url: /assets/images/industrial-machine/custom-circuit.JPG
    image_path: /assets/images/industrial-machine/custom-circuit.JPG
    alt: "Finished 3D printed board"
    title: "2. Custom Isolation Module"
  - url: /assets/images/industrial-machine/full-board.JPG
    image_path: /assets/images/industrial-machine/full-board.JPG
    alt: "Full Electrical Board"
    title: "3. Final Panel Wiring"
  - url: /assets/images/industrial-machine/machine.JPG
    image_path: /assets/images/industrial-machine/machine.JPG
    alt: "Assembled Machine"
    title: "4. Deployment"
---

## Project Scope
I designed and built the control system for a semi-automated pneumatic press used by a **metal casting facility**. The machine performs the critical **final press operation** on engine brackets before they are sorted and shipped to the Tier 1 supplier for final assembly into **Ford vehicles**.

{% include video id="Wl9DSPGAM94" provider="youtube" %}

## The Challenge
The facility needed a custom solution to automate the final assembly step and the subsequent sorting process. The previous manual workflow resulted in inconsistent press cycles and occasional counting errors during packing. Additionally, the environment was electrically noisy (high-voltage induction furnaces, motors, and AC power), which poses a major challenge for sensitive microcontrollers.

## Technical Implementation

### Electrical Engineering & Signal Conditioning
To ensure reliable operation in an electrically noisy environment, I engineered a custom **Signal Conditioning Board** to resolve signal integrity issues caused by the long wire runs and industrial equipment.

* **Interference Mitigation:** The initial prototype suffered from **"Crosstalk"** and floating inputs, where triggering one sensor would falsely trigger neighbors. I solved this by designing a custom interface that physically isolated the signal paths.
* **Hardware Debouncing:** I utilized **RC Low-Pass Filters** (Resistor-Capacitor circuits) on every input channel. This smoothed the noisy signals from the mechanical limit switches, preventing "ghost switching" and ensuring the microcontroller only registered deliberate actuations.
* **Galvanic Isolation:** Implemented relays to segregate the 24V industrial sensor loop from the Arduino's 5V logic. This ensured that the microcontroller received clean, digital square waves regardless of the electrical noise on the factory floor.
* **Rapid Prototyping:** Due to budget constraints, I designed and printed a custom **non-conductive chassis** to house the terminal blocks. I used **point-to-point soldering** to bridge the components directly between terminals, securing everything with structural adhesive to create a rugged, vibration-proof module.

![custom pcb](/assets/images/industrial-machine/custom-circuit.JPG)

### Mechanical Operation & Sorting Logic
1.  **Loading:** Operator places the casting into the fixture.
2.  **Actuation:** Upon dual-hand input, the pneumatic cylinder presses the casting.
3.  **Sortation & Indexing:**
    * **Counting:** The Arduino maintains a software counter for the current bin. Once the bin reaches capacity, the system automatically triggers the index sequence.
    * **Linear Actuation:** A high-torque **NEMA 34 Stepper Motor** drives a **lead screw assembly** to linearly translate the sorting mechanism to the next empty bin position.
    * **Capacity:** The system autonomously fills **6 bins** in sequence, allowing the operator to run for extended periods without stopping to swap crates.

### Software Design: Built for Maintainability
Since the machine is maintained by floor technicians without programming experience, I structured the C++ codebase to strictly separate the **Configuration** from the **Logic**.

* **Parametric Configuration:** I grouped all physical variables (distances, timers, speeds) at the top of the file in plain English.
* **Automatic Calibration:** The system takes inputs in **millimeters** and **seconds** (human units) and automatically converts them to stepper pulses and processor cycles.
* **Scalability:** The bin positions are stored in a dynamic array (`bucketPosition`), allowing the client to add or remove bins simply by editing the list.

```cpp
// ==========================================
// USER CONFIGURATION SECTION
// Variables to modify for machine adjustment
// ==========================================

bool testMode = false;      // Set to TRUE to bypass sensors for dry-run testing
int movementSpeed = 40;     // Motor Speed (1-100)
const int neededParts = 30;  // Parts per bin before indexing

// Bin Positions (in millimeters) starting at 0 for home
// The system automatically adjusts based on the number of positions entered
long bucketPosition[] = {0, 125, 518, 902, 1296, 1676, 2071};

// Timers (in milliseconds)
long delayAfterPartDrop = 500; // Wait time before indexing
long gateDelay = 100;          // Duration gate stays open
long spreadDownDelay = 0;      // Press duration
```

### Operational Logic & Safety Architecture
To ensure safe and predictable operation, the software relies on a **Hardware Abstraction Layer (HAL)** and sensor-interlocked sequences rather than simple time-based automation.

* **Hardware Abstraction:** I encapsulated all low-level I/O operations into semantic functions (e.g., `gateSolOpen()`, `nextBucket()`). This decouples the electromechanical drivers from the main logic, allowing the `loop()` to be read and verified like a high-level script.
* **Sensor-Interlocked Sequences:** Critical actions use **blocking state verification** to prevent mechanical collisions. For example, the `getPart()` sequence uses `while` loops to pause execution until onboard sensors confirm the physical state (e.g., "Part Present" or "Gate Closed") matches the software state.
* **Closed-Loop Homing:** To prevent stepper motor drift over long shifts, the system implements a **Homing Routine** (`moveHome()`) that seeks a physical limit switch to establish a "Zero Point." The software sets a `positionKnown` flag only after this routine completes, preventing the machine from operating with invalid coordinates.

```cpp
// Example of Sensor-Interlocked Logic (Hardware Abstraction)
// The machine refuses to proceed until the laser sensor confirms safe state.

void getPart(){
  // 1. Wait for physical confirmation (Interlock)
  while(digitalRead(partLaser) == HIGH || digitalRead(gateSolStart) == LOW){
    // Blocking loop: Prevents actuation until part is safely seated
  }

  // 2. Execute Abstracted Action Sequence
  spreadSolenoidDown();  // Actuate Press
  delay(spreadDownDelay);
  spreadSolenoidUp();    // Retract
  
  // 3. Update State
  partCount++;
}
```

## Engineering Retrospective: Why Arduino?
While **Programmable Logic Controllers (PLCs)** are the industry standard for automation, this project had strict budget constraints that precluded a $2,000+ control system. 

* **Cost-Benefit Analysis:** I chose the Arduino Mega ($40) over a MicroLogix PLC ($400+) to allocate more budget to high-quality mechanical components (NEMA 34 motors, pneumatics).
* **The Trade-off:** The lower cost required significantly more engineering effort in **signal conditioning** and **noise suppression**.
* **Lesson Learned:** This constraint forced me to design my own electrical protection circuits rather than relying on off-the-shelf hardened I/O, giving me a deeper understanding of **embedded hardware design** and **industrial signal integrity**.

## Operational Impact
The machine is currently deployed on the production floor, delivering the following results:
* **Quality Assurance:** Achieved **100% sorting accuracy**, eliminating shipping errors caused by manual sorting.
* **Throughput:** Automated the final press-and-sort cycle, allowing the operator to focus purely on inspection and loading.
* **Autonomy:** The 6-bin indexing system allows the machine to run unattended for 60 minutes, reducing operator fatigue and downtime.

## Gallery
Here is the progression from the initial interference mitigation design to the final factory installation.

{% include gallery id="gallery" layout="half" caption="**System Build:** (1) Initial noise-filtering schematic. (2) The custom 3D-printed signal conditioner. (3) Integration into the main NEMA enclosure. (4) The final machine in operation on the casting floor." %}


[View Source Code on GitHub](https://github.com/1337edge/part-machine/blob/main/PartMachine.ino){: .btn .btn--primary .btn--large}