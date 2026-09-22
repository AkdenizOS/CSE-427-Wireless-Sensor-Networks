# CSE 427 — Course Information

Syllabus, lab environment and glossary. Source: the
[week 1 slides](resources/2026-2027-fall/Week_1_WSN_Introduction_Hardware_Anatomy.pptm)
and the instructor's Teams announcements.

## Syllabus

### Weeks 1-7 — foundation and core protocols
The first half builds the protocol stack from hardware upward.

| Week | Topic | Main focus |
|------|-------|-----------|
| 1 | Introduction & Hardware Anatomy | WSN concepts, applications, mote architecture, Cooja setup |
| 2 | Radio & Physical Layer | Path loss, shadowing, fading, RSSI/LQI/PRR, IEEE 802.15.4 PHY |
| 3 | Energy | Radio states, idle listening, Energest, battery lifetime, harvesting |
| 4 | MAC I: Contention-Based | Hidden/exposed terminals, B-MAC, X-MAC, ContikiMAC, RI-MAC |
| 5 | MAC II: Scheduled & Hybrid | TDMA, graph coloring, TSCH, Orchestra, 6TiSCH, industrial MACs |
| 6 | Naming, Addressing & 6LoWPAN | Data names, IPv6 adaptation, compression, fragmentation, CoAP/MQTT-SN |
| 7 | Routing I: Collection & RPL | Collection trees, ETX, DODAGs, Trickle, storing vs. non-storing |

### Weeks 8-14 — suggested continuation
Later weeks connect protocols to reliability, security, data, and reproducible experiments.

| Week | Topic | Main focus |
|------|-------|-----------|
| 8 | Routing II: Robustness & Mobility | Repair, multipath, mobility, opportunistic forwarding |
| 9 | Transport & Reliability | End-to-end vs hop-by-hop reliability, congestion, queues |
| 10 | Time Synchronization | Clock drift, synchronization protocols, timestamped sensing |
| 11 | Security Foundations | Threat model, cryptographic primitives, key management constraints |
| 12 | Secure WSN Protocols | Link-layer security, secure routing, jamming, node compromise |
| 13 | Data Processing & Edge Intelligence | Aggregation, compression, anomaly detection, edge decisions |
| 14 | Integration & Project Demo | Experiment design, reproducibility, final review, student demos |

## Lab environment

- **Instant Contiki 3.0** virtual machine in **VMware** — a frozen Linux environment with
  Contiki, Cooja, Java/Ant and the build tools. Everyone uses the same image, so paths
  and compiler behaviour match. Take a VM snapshot before modifying toolchain files.
- Say "Contiki 3.0" unless explicitly comparing with Contiki-NG.
- Directory map inside the VM:
  ```
  ~/contiki/
  ├── core/        OS core and networking code
  ├── cpu/         CPU-specific support
  ├── platform/    board/platform definitions
  ├── examples/    hello-world/, rime/, ipv6/ …
  └── tools/cooja/ the Cooja simulator
  ```
- Launch Cooja: `cd ~/contiki/tools/cooja && ant run` (first run compiles; wait).
- Mote types: **Sky** (default for labs, MSP430 + 802.15.4 radio), Cooja mote (faster,
  less hardware-faithful), Java mote, Z1 / Wismote.
- Report problems as *path, command, exact error message* — never "it did not work".

Links: [Contiki OS](https://github.com/contiki-os/contiki) ·
[wiki](https://github.com/contiki-os/contiki/wiki) ·
[Introduction to COOJA](https://github.com/contiki-os/contiki/wiki/An-Introduction-to-COOJA) ·
[Instant Contiki download](https://sourceforge.net/projects/contiki/files/Instant%20Contiki/) ·
[examples](https://github.com/contiki-os/contiki/tree/master/examples)

## Glossary

| Term | Meaning |
|------|---------|
| Mote | Small embedded sensor node with computation and radio |
| Sink / gateway | Collection point or bridge to another network |
| Neighbor | Node reachable over a wireless link |
| Topology | Current connectivity and forwarding structure |
| Duty cycle | Fraction of time a component is active |
| Event | Physical condition that triggers reporting |
| RSSI / LQI / PRR | Received signal strength / link quality indicator / packet reception ratio |
| RDC | Radio duty cycling — the layer that sleeps and wakes the radio |
| 6LoWPAN | Adapts IPv6 to small IEEE 802.15.4 frames |
| RPL | IPv6 routing for low-power and lossy networks |
| Rime | Lightweight Contiki communication stack used in old examples |
