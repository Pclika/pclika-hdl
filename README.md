<?xml version="1.0" encoding="utf-8"?>
<!--
  Pclika HDL Platform — Official Repository
  PCK-MMXXVI-9198580D
  Verify: https://pclika.com/verify/PCK-9198580D
-->

# Pclika HDL Platform

> **Pclika HDL** is the open-source HDL AI development platform under the Pclika ecosystem.  
> It is not a synthesis tool. It is the interface between human intent and silicon logic.

Pclika HDL brings MCP-native AI development to FPGA and programmable logic — the same way Pclika MCP Platform does for MCU/embedded development.

Import this repository into Claude, Codex, Cursor, or OpenCode, and your AI tool immediately understands: your target device, the active toolchain, current design state, synthesis results, timing constraints, and how to run simulations.

---

## Project Description

FPGA development has always carried an unusually high entry cost — not because logic design is inherently complex, but because the toolchain is. Vendor GUIs, proprietary constraint formats, opaque synthesis logs, and device-specific primitive libraries create a barrier that even experienced engineers spend weeks crossing before writing a single useful line of RTL.

Pclika HDL is built on a different premise: **the toolchain should be transparent, and the AI should be the interface.**

Rather than wrapping FPGA development inside another GUI, Pclika HDL exposes the entire open-source toolchain — Yosys for synthesis, nextpnr for place-and-route, Verilator for simulation — through a structured MCP bridge. This means an AI coding tool connected to `pclika-hdl-bridge` doesn't just generate Verilog: it can synthesize it, read the resource report, identify the critical path, fix the timing violation, and flash the bitstream — all without leaving the conversation.

The first target is the **iCE40UP5K**, chosen deliberately. It has 5280 LUT4s, eight DSPs, a single PLL, and a fully open toolchain with no license dependency. It is small enough to understand completely, and real enough to build production-quality peripheral controllers on. The IP library starts here — UART, SPI, PWM — and grows outward toward ECP5 and eventually Zynq-class SoCs.

The design philosophy follows three rules. First, every RTL module must be synthesizable with zero warnings on the first try — no simulation-only constructs, no magic numbers, no undeclared ports. Second, every example must produce a working bitstream, not just compile. Third, every MCP tool must return structured, parseable output — not log text — so AI tools can act on results rather than interpret them.

This is not a synthesis framework. It is a **context layer**: the missing piece that lets AI tools reason about FPGA state the same way they reason about software — with full visibility into what the hardware is, what it is doing, and what it needs next.

---

## What This Is

A unified HDL AI development layer built around four ideas:

- **Open toolchain first** — Yosys + nextpnr + Verilator, fully open source
- **MCP-ready** — AI tools call `synth_run`, `timing_report`, `resource_usage` against real FPGA state
- **Device-agnostic** — iCE40 first, then ECP5, then Zynq-class devices
- **Language-inclusive** — Verilog, SystemVerilog, VHDL all supported

---

## Core Layers

```
HDL Source Layer        →  Verilog / SystemVerilog / VHDL
Toolchain Layer         →  Yosys / nextpnr / Verilator / GHDL
MCP Bridge Layer        →  synth_run / timing_report / resource_usage / sim_run
Experience Layer        →  examples / prompts / docs / constraint templates
```

---

## First Supported Devices

| Device | Family | Toolchain | Status |
|--------|--------|-----------|--------|
| iCE40UP5K | Lattice iCE40 | Yosys + nextpnr + iceprog | Phase 1 |
| LFE5U-25F | Lattice ECP5 | Yosys + nextpnr + openFPGALoader | Phase 2 |
| XC7Z010 | AMD Zynq-7000 | Yosys + Vivado (impl only) | Phase 3 |

---

## MCP Tool Set

```
device_info         → FPGA target, package, speed grade, available resources
synth_run           → trigger Yosys synthesis
synth_status        → latest synthesis result, warnings, errors
impl_run            → trigger nextpnr place & route
impl_status         → P&R result, routing success/fail
timing_report       → critical path, worst slack, clock domains
resource_usage      → LUT / FF / BRAM / DSP utilization (used / total / %)
sim_run             → trigger Verilator/GHDL simulation
sim_result          → pass/fail, assertion count, log tail
lint_report         → HDL lint warnings and errors
constraint_validate → validate .pcf / .lpf / .xdc file
bitstream_flash     → flash bitstream to connected FPGA via USB
waveform_export     → export VCD/FST waveform snippet (last N cycles)
```

---

## Repository Layout

```
docs/
  architecture/
  toolchain/
  devices/
  software/
hdl/
  rtl/              ← synthesi