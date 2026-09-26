# High-Current Buck-Boost Power Module for Compute Payloads

## Overview
This repository contains the KiCad schematic and PCB layout files for a custom, high-efficiency synchronous buck-boost power delivery network (PDN). Designed specifically to power a Raspberry Pi 4 and advanced vision payloads (such as the Camera Module 3 NoIR) during heavy compute loads, this module prevents undervoltage throttling and system reboots. The architecture is optimized for autonomous robotics, drones, and edge-computing applications running on Ubuntu, ensuring that high-frequency switching noise does not disrupt MIPI CSI video streams or I2C bus communications. 

## Technical Specifications

| Parameter | Specification | Description |
| :--- | :--- | :--- |
| **Input Voltage (Vin)** | 3.3V – 12.6V | Supports deeply discharged to fully charged 3S LiPo batteries. |
| **Output Voltage (Vout)** | 5.5V | Compensates for high-current trace/cable voltage drops. |
| **Continuous Current** | 5.0A | Sustained current delivery across the full input voltage range. |
| **Peak/Burst Current** | 6.0A | Handles sudden compute spikes and Raspberry Pi 4 boot sequences. |
| **Reverse Polarity** | Active (Ideal Diode) | Handles up to 20A transients with near-zero voltage and heat loss. |
| **Short Circuit** | Hiccup Mode + Fuse | Active IC protection paired with a passive fast-blow SMD fuse. |

## System Architecture

*   **Input Protection (Ideal Diode):** Standard Schottky diodes waste significant power and generate excess heat at 5A+ loads. This design utilizes an active ideal diode controller paired with a low-Rds(on) N-Channel MOSFET. It strictly blocks reverse currents while maintaining thermal efficiency, ensuring the downstream components remain safe if battery terminals are accidentally crossed.
*   **4-Switch Synchronous Buck-Boost:** The core power stage utilizes a wide-Vin controller driving four external power MOSFETs around a high-saturation (15A+) shielded power inductor. This topology allows the module to seamlessly step down a fully charged 11.1V battery and smoothly transition into step-up (boost) mode if the battery voltage drops below the 5.5V threshold, maximizing usable battery capacity.
*   **EMI Suppression & Output Filtering:** High-current switching generates electromagnetic interference that can cause camera dropouts (e.g., grey screen errors on port 8080) or I2C sensor disconnections. The output stage features an array of low-ESR solid polymer bulk capacitors to absorb boot transients, paired with closely placed ceramic capacitors (MLCCs) to filter high-frequency noise. 
*   **Ground Isolation:** The schematic enforces strict separation between the noisy Power Ground (PGND) and the sensitive Analog Ground (AGND). These planes are connected at a single star-ground point directly beneath the controller IC's thermal pad to ensure absolute stability in the feedback loop.

## Hardware Layout & Fabrication

To successfully fabricate and assemble this board, adhere to the following layout constraints:
1.  **Copper Weight:** A minimum of 2oz copper thickness is required for the top and bottom layers to support continuous 5A+ loads without thermal degradation. 
2.  **Switching Nodes (SW1, SW2):** The copper polygons connecting the power inductor to the MOSFETs must be routed as wide and short as physically possible to prevent them from acting as antennas that broadcast RF noise.
3.  **Thermal Management:** The central controller IC and the four primary switching MOSFETs require extensive thermal vias dropping down to a solid internal or bottom-layer ground plane to dissipate heat efficiently.
4.  **Component Sourcing:** Select an inductor with a saturation current rating significantly higher than the peak output (minimum 15A) to prevent core saturation during heavy load steps.

**Author:** Harsh Vardhan Singh
**License:** MIT License (See `LICENSE` file for details)
