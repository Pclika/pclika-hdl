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

## Supported Hardware

| Board | FPGA | Toolchain | Status |
|-------|------|-----------|--------|
| iCEBreaker v1.0 ★ | iCE40UP5K SG48 | Yosys + nextpnr + iceprog | Phase 1 — primary |
| Upduino v3.1 | iCE40UP5K SG48 | Yosys + nextpnr + iceprog | Phase 1 — compatible |
| iCE40-HX8K Breakout | iCE40HX8K CT256 | Yosys + nextpnr + iceprog | Phase 1 — partial |
| ColorLight i5 | Lattice ECP5 LFE5U-25F | Yosys + nextpnr + openFPGALoader | Phase 2 |
| Arty A7-35T | AMD Artix-7 XC7A35T | Yosys + Vivado (impl) | Phase 3 |

**IP Library × Hardware mapping** → [`docs/hardware/hardware-list.md`](docs/hardware/hardware-list.md)

| Interface | IP Module | Supported Hardware |
|-----------|-----------|-------------------|
| UART 8N1 | `pclika_uart_rx` + `pclika_uart_tx` | iCEBreaker (FT2232H), Upduino (CH340), any PMOD-UART |
| SPI Master (Mode 0) | `pclika_spi_master` | W25Q128 Flash, BME280, MAX31865, SSD1309, ST7789 |
| PWM / Servo 50 Hz | `pclika_pwm` | SG90 / MG996R servos, ESC, RGB LED dimming |
| I2C Master | `pclika_i2c_master` | MPU6050, BMP280, SSD1306 I2C — *Phase 2* |
| WS2812B | `pclika_ws2812` | Addressable RGB LED strips — *Phase 2* |

---

## MCP Tool Set

```
device_info         → FPGA target, package, speed grade, available resources
synth_run      