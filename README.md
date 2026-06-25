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
  rtl/              ← synthesizable RTL source
  tb/               ← testbenches
  constraints/      ← .pcf / .lpf / .xdc per device
  ip/               ← reusable IP blocks
bridge/
  mcp-server/       ← Python MCP bridge for HDL toolchain
  tool-schemas/     ← JSON Schema for all MCP tools
toolchain/
  scripts/          ← build / synth / impl / sim scripts
  docker/           ← Docker image with full open toolchain
examples/
  blink/            ← LED blink on iCE40UP5K
  uart-echo/        ← UART loopback example
  i2c-controller/   ← I2C master implementation
  spi-bridge/       ← SPI to UART bridge
  pwm-gen/          ← Configurable PWM generator
prompts/
  common/
  synth-workflow.md
  timing-debug.md
  rtl-review.md
configs/
  mcp/
    claude-code.commands.md
    cursor.mcp.json
    codex.config.toml
    vscode.mcp.json
```

---

## Start Here

1. `README.md` — this file
2. `AGENTS.md` — AI tool guide
3. `docs/architecture/platform.md` — platform architecture
4. `docs/toolchain/setup.md` — toolchain installation
5. `examples/blink/` — first working example

---

## Quick Start

```bash
# Install bridge
pip install pclika-hdl-bridge

# Connect your iCE40 board via USB
pclika-hdl-bridge --device ice40up5k --port /dev/ttyUSB0

# In Claude / Codex / Cursor — the following tools are now available:
# device_info / synth_run / timing_report / resource_usage / bitstream_flash
```

---

## Repository Status

Early-stage open-source foundation. Current focus:

- toolchain integration architecture
- MCP bridge contract definition
- iCE40UP5K first device support
- example project structure

---

## License

- Software: Apache-2.0
- Hardware design files: CERN-OHL-S v2
- Documentation: CC BY 4.0

See `LICENSE`, `HARDWARE_LICENSE.md`, `DOCS_LICENSE.md`

---

## Part of the Pclika Ecosystem

| Repository | Role |
|------------|------|
| [Pclika/mcp-platform](https://github.com/Pclika/mcp-platform) | MCU / Embedded MCP Platform (ESP32, STM32) |
| [Pclika/pclika-hdl](https://github.com/Pclika/pclika-hdl) | FPGA / HDL AI Development Platform |
