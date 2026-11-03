# Week 3 — Efe

Energy accounting lab: one UDP client, one UDP server, Powertrace every 30 s, three scenarios.

| File | What it is |
|------|-----------|
| `energy-lab-week3/energy-client.c` | Contiki 3.0 `udp-ipv6/udp-client.c` plus Powertrace; `DEBUG_NONE` and a short `TX:` line so the Sky firmware fits in ROM |
| `energy-lab-week3/energy-server.c` | Contiki 3.0 `udp-ipv6/udp-server.c` plus Powertrace |
| `energy-lab-week3/Makefile` | Lab deck Makefile plus `CONTIKI_WITH_IPV6 = 1` from the stock example; the RDC line selects the scenario |
| `powertrace_to_csv.py` | Saved Mote Output → CSV (`Time, Mote, CPU %, LPM %, TX %, RX %, Radio On %`), averages, average current, lifetime, PDR |

Copy `energy-lab-week3/` to `~/contiki/examples/` in the VM.

| Scenario | Makefile RDC line | `SEND_INTERVAL` in `energy-client.c` |
|---|---|---|
| A — continuous listening | `nullrdc_driver` | `15 * CLOCK_SECOND` |
| B — duty cycling | `contikimac_driver` | `15 * CLOCK_SECOND` |
| C — higher traffic | `contikimac_driver` | `5 * CLOCK_SECOND` |

Per scenario: `make clean`, recompile both mote types, run 10–15 simulated minutes on the
same topology, save Mote Output (File → Save to file), then

```bash
python3 powertrace_to_csv.py scenario-a.txt scenario-a.csv
```

Lab steps and submission rules: [weeks/03](../../../weeks/03-energy.md).
