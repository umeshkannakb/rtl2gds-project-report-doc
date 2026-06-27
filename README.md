# RTL to GDSII â€” Radix-4 Booth Multiplier (Cadence Tool Suite)

Complete ASIC implementation flow for an 8-bit **Radix-4 Booth Multiplier**, from Verilog RTL all the way to a tapeout-ready GDSII layout, using the Cadence EDA tool suite.

---

## Overview

The Booth Multiplier reduces the number of partial products in binary multiplication by encoding groups of bits, making it more efficient than a naive shift-and-add approach. The Radix-4 variant (also called Modified Booth Encoding) groups **3 consecutive bits** at a time, halving the number of partial products compared to Radix-2.

This project covers the complete ASIC design flow:

```
Verilog RTL  â†’  Functional Simulation  â†’  Logic Synthesis (Genus)
     â†’  Floorplan  â†’  Placement  â†’  CTS  â†’  Routing  â†’  GDSII (Innovus)
```

---

## Radix-4 Booth Encoding â€” How It Works

Standard multiplication of two N-bit numbers generates N partial products. Radix-4 Booth encoding reduces this to N/2 partial products.

Each group of 3 overlapping bits `{B[2i+1], B[2i], B[2i-1]}` is encoded as:

| B[2i+1] | B[2i] | B[2i-1] | Operation |
|---------|-------|---------|-----------|
| 0 | 0 | 0 | 0 Ã— A |
| 0 | 0 | 1 | +1 Ã— A |
| 0 | 1 | 0 | +1 Ã— A |
| 0 | 1 | 1 | +2 Ã— A |
| 1 | 0 | 0 | âˆ’2 Ã— A |
| 1 | 0 | 1 | âˆ’1 Ã— A |
| 1 | 1 | 0 | âˆ’1 Ã— A |
| 1 | 1 | 1 | 0 Ã— A |

---

## Design Architecture

```
        â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
A[7:0]  â”‚         Booth Encoder               â”‚
B[7:0]  â”‚  Generates N/2 Partial Products     â”‚
        â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
                         â”‚  PP[0..3]
                         â–¼
        â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
        â”‚      Wallace Tree / CSA Array        â”‚
        â”‚  Compresses partial products to 2   â”‚
        â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
                         â”‚  Sum + Carry
                         â–¼
        â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
        â”‚         Final Adder (CPA)            â”‚
        â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
                         â”‚
                    Product[15:0]
```

---

## Repository Contents

| File | Description |
|------|-------------|
| `booth4.v` | RTL design â€” Radix-4 Booth Multiplier (Verilog) |
| `test1.v` | Testbench for functional simulation |
| `radix4_GDS.gds` | Final GDSII layout file (tapeout-ready) |
| `RTL_RADIX-4_BOOTH_MULTIPLIER.pdf` | RTL design documentation and analysis |
| `RTL_TO_GDSII_radix4.pdf` | Full flow report â€” synthesis to physical design |
| `Elaborated_design_radix4.pdf` | Elaborated schematic (post-synthesis view) |
| `synthesized_design_radix4.pdf` | Synthesized netlist schematic |

---

## Recommended Folder Structure

> The files are currently in the repo root. Organizing them into folders (as below) would significantly improve readability:

```
rtl2gds-project-report-doc/
â”œâ”€â”€ README.md
â”œâ”€â”€ .gitignore
â”œâ”€â”€ rtl/
â”‚   â””â”€â”€ booth4.v               â† RTL design
â”œâ”€â”€ tb/
â”‚   â””â”€â”€ test1.v                â† Testbench
â”œâ”€â”€ synthesis/
â”‚   â”œâ”€â”€ constraints.sdc        â† Timing constraints (to be added)
â”‚   â””â”€â”€ genus_scripts.tcl      â† Synthesis scripts (to be added)
â”œâ”€â”€ pnr/
â”‚   â”œâ”€â”€ floorplan.tcl          â† Innovus scripts (to be added)
â”‚   â””â”€â”€ route.tcl
â”œâ”€â”€ gds/
â”‚   â””â”€â”€ radix4_GDS.gds         â† Final GDSII
â””â”€â”€ reports/
    â”œâ”€â”€ RTL_RADIX-4_BOOTH_MULTIPLIER.pdf
    â”œâ”€â”€ RTL_TO_GDSII_radix4.pdf
    â”œâ”€â”€ Elaborated_design_radix4.pdf
    â””â”€â”€ synthesized_design_radix4.pdf
```

---

## Tools Used

| Tool | Purpose |
|------|---------|
| Verilog HDL | RTL Design |
| Xilinx Vivado | Functional Simulation & Testbench |
| Cadence Genus | Logic Synthesis (with SDC constraints) |
| Cadence Innovus | Physical Design â€” Floorplan, Place, CTS, Route |

---

## Flow Summary

### 1. RTL Simulation
Run `test1.v` testbench against `booth4.v` to verify functional correctness before synthesis.

### 2. Synthesis (Cadence Genus)
```tcl
read_hdl booth4.v
elaborate
read_sdc constraints.sdc
syn_generic
syn_map
syn_opt
write_hdl > netlist.v
```

### 3. Physical Design (Cadence Innovus)
- **Floorplan:** Define die size, I/O pad placement, power rings
- **Placement:** Place standard cells with timing-driven placement
- **CTS (Clock Tree Synthesis):** Build balanced clock tree with target skew < 50ps
- **Routing:** Global + detail routing with DRC clean
- **GDSII Export:** `streamOut radix4_GDS.gds`

---

## Key Results

| Metric | Value |
|--------|-------|
| Design | 8Ã—8 Radix-4 Booth Multiplier |
| Technology Node | (Add your PDK node, e.g. 180nm / 45nm) |
| Clock Frequency | (Add from timing report) |
| Cell Count | (Add from area report) |
| Total Area | (Add from area report) |

> Fill in the values from your synthesis/PnR reports in `RTL_TO_GDSII_radix4.pdf`.

---

## What to Add Next

- [ ] Organize files into `rtl/`, `tb/`, `synthesis/`, `pnr/`, `gds/`, `reports/` folders
- [ ] Add `synthesis/constraints.sdc` and Genus TCL scripts
- [ ] Add `pnr/` Innovus TCL scripts
- [ ] Add simulation waveform screenshot
- [ ] Add GDSII layout screenshot
- [ ] Fill in Key Results table from your reports
- [ ] Add `.gitignore` to exclude Cadence log/journal files
- [ ] Set GitHub topics: `verilog` `asic` `cadence` `rtl-to-gdsii` `booth-multiplier` `vlsi` `physical-design`

---

## License

MIT License
