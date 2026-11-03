# Week 3 — Energy

> Mon Sep 28 (theory) · Thu Oct 1 (practice) · Syllabus focus: *Radio states, idle listening, Energest, battery lifetime, harvesting*

**Previous:** [Week 2](02-radio-and-physical-layer.md) · **Next:** [Week 4](04-mac-i-contention-based.md)

## Reading
- [Week 3 slides — Energy in Wireless Sensor Networks](../resources/2026-2027-fall/Week_3_WSN_Energy_60slides_InstantContiki3_Cooja.pptx)
- [Week 3 lab deck — Energy Accounting Lab](../resources/2026-2027-fall/Week_3_Energy_Lab_Tutorial_InstantContiki3_Cooja_STYLE_FIXED.pptx) (steps, code changes, Makefile lines per scenario, troubleshooting)
- [How to Read the Powertrace](../resources/2026-2027-fall/How%20to%20Read%20the%20Powertrace.pdf) (every field of a powertrace line, and which table column it feeds)
- [Karl & Willig — Ch. 2.2 (energy consumption of sensor nodes)](../resources/books/karl-willig-protocols-and-architectures-for-wireless-sensor-networks.pdf#page=63)
- [Dargie & Poellabauer — Ch. 8 (power management)](../resources/books/dargie-poellabauer-fundamentals-of-wireless-sensor-networks.pdf#page=227)

## Lab — energy accounting
One Sky UDP client sends numbered messages to one Sky UDP server; both print Powertrace
lines every 30 s. Run three scenarios on the same topology for the same duration
(10–15 min simulated) and compare where the time goes: CPU, LPM, radio TX, radio listen.

| Scenario | RDC | Send interval | Shows |
|---|---|---|---|
| A — continuous listening | NullRDC | 15 s | baseline cost of an always-on receiver |
| B — duty cycling | ContikiMAC | 15 s | what duty cycling saves, and its trade-off |
| C — higher traffic | ContikiMAC | 5 s | how workload changes radio activity |

1. Copy `udp-client.c` and `udp-server.c` from `examples/udp-ipv6/` into a new
   `examples/energy-lab-week3/` as `energy-client.c` and `energy-server.c` (this VM has no
   `rpl-udp` folder).
2. In both files: `#include "powertrace.h"` and `powertrace_start(CLOCK_SECOND * 30);` right
   after `PROCESS_BEGIN();`.
3. Makefile: `CONTIKI_PROJECT = energy-client energy-server`, `APPS += powertrace`,
   `CONTIKI_WITH_RIME = 0`, `CFLAGS += -DNETSTACK_CONF_MAC=csma_driver`, and per scenario
   `CFLAGS += -DNETSTACK_CONF_RDC=nullrdc_driver` (A) or `contikimac_driver` (B, C).
4. Scenario C: `#define SEND_INTERVAL 5 * CLOCK_SECOND` in `energy-client.c` (A and B keep 15).
   Recompile after every change — Cooja does not pick up edited sources by itself.
5. Cooja: new simulation, one Sky mote from `energy-server.c` first, then one from
   `energy-client.c`, close together; keep positions and radio medium the same in all scenarios.
6. Per scenario, copy the Powertrace lines from Mote Output after the warm-up and count
   sent and received packets for PDR.

Calculations — Powertrace counters are cumulative, so work on differences between two lines:
- `ΔCPU = CPU_t − CPU_(t−1)`, likewise `ΔLPM`, `ΔTX`, `ΔRX`.
- `CPU duty cycle = ΔCPU / (ΔCPU + ΔLPM) × 100`.
- `TX duty = ΔTX / ΔT × 100`, `RX/listen duty = ΔRX / ΔT × 100`, `Radio on = (ΔTX + ΔRX) / ΔT × 100`.
- `I_avg = Σ(D_i × I_i)` with CPU 1.8 mA, LPM 0.0545 mA, TX 17.4 mA, listen 18.8 mA;
  `lifetime hours = battery capacity (mAh) / I_avg (mA)`.

The instructor recommends a Python script that parses the Powertrace log into a CSV with
the columns `Time, Mote, CPU %, LPM %, TX %, RX %, Radio On %`.

Pitfalls:
- Never `sudo ant run` or `sudo make`: root cannot find `msp430-gcc` and leaves root-owned
  `obj_sky` files (`Permission denied` later). Repair with `sudo chown -R $USER:$USER ~/contiki-3.0`.
- ``region `rom' overflowed by 12 bytes`` on the client: set `#define DEBUG DEBUG_NONE` and
  shorten the client's `printf`; keep `powertrace_start`.
- The percentages Powertrace prints in parentheses can come out malformed (`100.-8735%`) —
  compute them from the tick counters instead.
- Do not compare a start-up window of one scenario with a steady-state window of another.

## Submission — due Sun Oct 18, 23:59
A video, and you must appear in it; if you cannot, arrange an office hour with the
instructor to explain your work. Worth 20 points counted toward the midterm; late
submissions are not accepted. Explain in the video:
- the Cooja topology and mote output;
- a Powertrace log excerpt for each scenario;
- the completed table (CPU, LPM, TX, listen, duty cycle, average current) and one graph;
- radio listen percentage compared across the scenarios;
- which state dominates and why, plus the limitations of the estimate.

## Checklist
- [ ] Theory lecture attended (Monday)
- [ ] Practice session attended or video submitted (Thursday)
- [ ] Slides read
- [ ] Lab done
- [ ] Submission video uploaded

---

This file is the shared plan — improve it if the course changes, but keep it general.
Personal notes go below, one `## Notes — <Name> (<term>)` section per person.

## Notes — Efe (2026-2027 Fall)

### Lecture
<!-- What was actually covered, and what the lecturer emphasised. -->

### Lab
<!-- What you built in Cooja, what you measured, what it means. -->

### Questions
<!-- Unclear things. Ask, then answer them here. -->

### Exam-worthy
<!-- Definitions, trade-offs, protocol steps, pitfalls. -->
