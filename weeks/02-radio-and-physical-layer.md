# Week 2 — Radio & Physical Layer

> Mon Sep 21 (theory) · Thu Sep 24 (practice) · Syllabus focus: *Path loss, shadowing, fading, RSSI/LQI/PRR, IEEE 802.15.4 PHY*

**Previous:** [Week 1](01-introduction-and-hardware-anatomy.md) · **Next:** [Week 3](03-energy.md)

*A wireless link is not a cable. It is a probability distribution with packets attached.*

## Goals
- Compute and interpret dBm, dB and simple link budgets.
- Explain path loss, shadowing, multipath and fading qualitatively — and keep them separate.
- Distinguish RSSI, LQI and PRR without mixing them.
- Explain transitional regions and directional link asymmetry.
- Recognize the core IEEE 802.15.4 PHY facts used by WSN platforms.
- Run a Cooja experiment and avoid overclaiming from the radio model.

## Key concepts
- **dBm vs dB**: dBm is absolute power relative to 1 mW (`P_dBm = 10 log10(P_mW / 1 mW)`, 0 dBm = 1 mW, −80 dBm is stronger than −90 dBm). dB is a ratio, gain or loss. +3 dB ≈ ×2, +10 dB = ×10. Classic exam trap.
- **Link budget**: `P_r = P_t + G_t + G_r − PL − L_s`. Example: 0 dBm, 0 dB antennas, 75 dB path loss, 2 dB losses → −77 dBm. Then ask: above sensitivity? what is the noise floor?
- **Sensitivity is not a cliff**: minimum signal for a given performance target; delivery falls gradually as signal weakens.
- **SNR**: `SNR_dB = P_signal,dBm − P_noise,dBm`. Same RSSI (−80 dBm) with noise −100 → 20 dB (good) vs noise −82 → 2 dB (poor). RSSI alone is incomplete.
- **Three propagation effects**: *path loss* (average loss with distance), *shadowing* (slow variation from obstacles), *fading* (short-term multipath variation).
- **Free space**: `PL_FS(d) = 20 log10(4πd/λ)`, λ = c/f ≈ 0.125 m at 2.4 GHz; ~40 dB at 1 m, ~60 dB at 10 m — ×10 distance → +20 dB.
- **Log-distance**: `PL(d) = PL(d₀) + 10n log10(d/d₀)`; n = 2 in free space, larger indoors. PL(1 m)=40, n=3 → 70 dB at 10 m.
- **Log-normal shadowing**: add `Xσ` (zero-mean Gaussian in dB); larger σ = more variability at the same distance.
- **Multipath**: reflection, diffraction, scattering → copies with different phase/delay; constructive or destructive. λ ≈ 12.5 cm, so moving a mote a few cm can matter. Static nodes still fade (people, doors move).
- **Flat vs frequency-selective fading**: all frequencies attenuated equally vs differently; depends on bandwidth vs delay spread.
- **Combined model**: `P_r(d,t) = P_r(d₀) − 10n log10(d/d₀) − Xσ + F(t)` → received power is not a deterministic function of distance. Measure, do not assume from geometry.
- **RSSI**: received power per received packet; platform-specific scale; includes noise/interference. Strong RSSI + collision → bad PRR.
- **LQI**: implementation-specific quality of a *successfully received* frame (CC2420: 7-bit chip correlation). Not a universal unit; invisible for lost packets.
- **PRR**: `N_received / N_transmitted` over a window; needs sequence numbers. Short window reacts fast, long window is stable but slow. Most directly reflects application delivery.
- **Metric stack**: RSSI = how strong; LQI = how well decoded; PRR = how often it arrived. High LQI + low PRR → missing packets are invisible to LQI.
- **Link regions**: connected (high PRR) · transitional (variable PRR, bursts, asymmetry) · disconnected. Transitional links are the expensive ones — retransmissions, routing flapping. **Hysteresis**: add neighbor at PRR > 0.8, drop below 0.5.
- **Asymmetry**: A→B ≠ B→A (Tx power, sensitivity, antenna, local noise). Exchange success = `PRR_fwd × PRR_rev` (0.90 × 0.50 = 0.45) — ACKs need the reverse link.
- **IEEE 802.15.4 ≠ Zigbee**: 802.15.4 defines PHY + MAC; Zigbee adds network/application layers on top.
- **2.4 GHz O-QPSK PHY**: 16 channels (11–26), 5 MHz spacing, ch 11 = 2405 MHz … ch 26 = 2480 MHz; 250 kb/s raw; 4 bits → 1 symbol → 32 chips (DSSS); 62.5 ksymbol/s, 2 Mchip/s. Application throughput is much lower (headers, ACKs, backoff).
- **PPDU**: preamble 4 B · SFD 1 B · PHR 1 B · PSDU 0–127 B (MAC frame).
- **ED / CCA**: energy detection and clear-channel assessment support CSMA/CA; CCA sees the channel at the sender, not the receiver.
- **Coexistence**: Wi‑Fi, Bluetooth, microwaves share 2.4 GHz → loss, retransmissions, apparent asymmetry.
- **Cooja radio media**: UDGM Distance Loss (range/interference disk, success ratios), Directed Graph Radio Medium (manual directional links — asymmetry), MRM (multi-path ray tracer, richer but still an approximation). *Simulation result = code + topology + radio model.*

## Reading
- [Week 2 slides — Radio & Physical Layer](../resources/2026-2027-fall/Week_2_WSN_Radio_PHY_60slides_Contiki3.pptx)
- [Week 2 lab deck — Cooja Radio Propagation & Link Quality](../resources/2026-2027-fall/week2_cooja_radio_lab_contiki3.pptx) (the "Thursday document": steps, parameters, full `sender.c` / `receiver.c` / `Makefile`)
- [Karl & Willig — Ch. 4.2 (wireless channel fundamentals)](../resources/books/karl-willig-protocols-and-architectures-for-wireless-sensor-networks.pdf#page=113)
- [Karl & Willig — Ch. 4.3 (physical layer and transceiver design)](../resources/books/karl-willig-protocols-and-architectures-for-wireless-sensor-networks.pdf#page=130)
- [Dargie & Poellabauer — Ch. 5 (physical layer)](../resources/books/dargie-poellabauer-fundamentals-of-wireless-sensor-networks.pdf#page=115)
- [CC2420 datasheet](https://www.ti.com/lit/ds/symlink/cc2420.pdf) · [Contiki 3.0 release](https://github.com/contiki-os/contiki/releases/tag/3.0)

## Lab — radio propagation & link quality
One Sky sender broadcasts a sequence number every second over Rime (channel 129); one Sky
receiver prints `RX seq=… RSSI=… dBm LQI=…`. Broadcast keeps routing, addressing and IPv6 out
of the way. Code is in the lab deck appendix — put it in `examples/week2-radio/`.

1. Launch Cooja (`cd ~/contiki/tools/cooja && ant run`; the lab deck writes `~/contiki-3.0` —
   use whichever path your VM has). If Sky mote is missing / "Could not find the MSPSim build
   file": `git submodule update --init --recursive` in the Contiki root.
2. New simulation `Week2-Radio`, radio medium **UDGM: Distance Loss**. Two Sky mote types
   from `sender.c` and `receiver.c`, one of each. Open Mote Output and Network.
3. **A — first packets**: start, check RX seq follows TX seq for ~10 packets.
4. **B — distance**: close / medium / near edge, 10 packets each; record typical RSSI and LQI.
5. **C — cross the boundary**: drag the receiver out of range while running (RX stops), back in (RX resumes).
6. **D — controlled loss**: UDGM TX = 1.0, RX success 1.0 → **0.6**, motes stationary; watch missing sequence numbers.
7. **E — PRR**: count received out of 20 in the reduced-success run, `PRR = received / sent`.
8. Optional: second simulation `Week2-MRM` with Multi-path Ray-tracer Medium, compare.

Pitfalls:
- Rime channel 129 is a software ID, not an 802.15.4 PHY channel — mismatch = no RX.
- UDGM transmission range is a simulation knob, not real Sky range.
- The lab deck's Makefile does not link: add `CONTIKI_WITH_RIME = 1` (otherwise `undefined reference to broadcast_send`).
- Set **Speed limit → 100%** in Simulation control, or packets scroll past faster than you can count.
- UDGM's RX success ratio is distance-scaled: `P = 1 − (d/range)² × (1 − RX)` (`UDGM.java`). With RX = 0.6 the receiver sees ~99% next to the sender, ~90% at half range and 60% only at the edge — put the receiver at the edge for the loss experiment (Cooja prints the probability next to the mote).
- ContikiMAC repeats each broadcast for a wake-up interval, so a moderate per-copy loss (≈ 90% success) still delivers every packet.
- The printed RSSI is 45 dB too low: the lab code subtracts 45, and Contiki's cc2420 driver has already applied `RSSI_OFFSET`. Real dBm = printed + 45.
- LQI is constant (37) in UDGM — it does not model decoding quality.

## Submission — due Sun Oct 4, 23:59
Same rules as week 1: a video (≥ 10 min, full screen, you on camera, clear English,
explaining how and why). Show:
- two Sky motes with UDGM range visible;
- Mote Output with TX seq and RX seq/RSSI/LQI;
- the boundary-crossing and reduced-success experiments;
- the filled table and one PRR value:

| Experiment | Sent | Received | Typical RSSI | Typical LQI |
|---|---|---|---|---|
| Close UDGM | 10 | | | |
| Medium UDGM | 10 | | | |
| Near-edge UDGM | 10 | | | |
| Reduced success (RX 0.6) | 20 | | | |

## Practice
- [ ] Convert 10 mW, 0.1 mW, 10⁻⁹ mW to dBm without a calculator
- [ ] Link budget: P_t = 0 dBm, n = 3, PL(1 m) = 40 dB — P_r at 10 m?
- [ ] Why can strong RSSI coexist with low PRR? high LQI with low PRR?
- [ ] Forward PRR 0.9, reverse 0.5 — probability of a successful data+ACK exchange?
- [ ] 802.15.4 2.4 GHz: channel count, spacing, raw rate, chips per symbol

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
Submitted on Teams as a link to a personal lab page with the video. Files in [`assignments/2026-2027-fall-efe/week2`](../assignments/2026-2027-fall-efe/week2/).

Setup: 1 Sky sender + 1 Sky receiver, Rime broadcast every second, UDGM with TX range 20 m / interference 40 m, speed 100%.

| Experiment | Distance | Sent | Received | RSSI printed | RSSI real | LQI |
|---|---|---|---|---|---|---|
| B close | ≈ 4 m | 20 | 20 | −71 dBm | −26 dBm | 37 |
| B medium | ≈ 10 m | 20 | 20 | −98 dBm | −53 dBm | 37 |
| B far | ≈ 15 m | 20 | 20 | −117 dBm | −72 dBm | 37 |
| C outside range | > 20 m | 15 | 0 | — | — | — |
| D RX ratio 60% | ≈ 10 m | 20 | 20 | −99 dBm | −54 dBm | 37 |

- RSSI falls linearly with distance (UDGM: `−10 − 85 × d/range` dBm); delivery inside the range stays at 100%.
- Crossing the boundary: 15 consecutive packets lost (seq 493–507), reception back immediately inside. No transitional region in UDGM.
- RX ratio 60% at ≈ 10 m: Cooja showed 89.2%, and none of 70 packets was lost (PRR = 1.00) — distance scaling plus ContikiMAC's repeated broadcasts.
- Not measured: the receiver at the very edge with RX 60%, where loss becomes visible.

### Questions
<!-- Unclear things. Ask, then answer them here. -->

### Exam-worthy
- dBm = absolute power relative to 1 mW (0 dBm = 1 mW, −60 dBm stronger than −90 dBm); dB = ratio. +3 dB ≈ ×2, +10 dB = ×10. A path loss is in dB, never dBm.
- Link budget: `P_r = P_t + G_t + G_r − PL − L_s`. Log-distance: `PL(d) = PL(d₀) + 10·n·log10(d/d₀)`, n = 2 in free space, larger indoors.
- Path loss (average, distance) vs shadowing (obstacles, slow) vs fading (multipath, fast). λ ≈ 12.5 cm at 2.4 GHz.
- RSSI = strength of a received packet; LQI = decode quality of a received frame (radio-specific); PRR = received / sent over a window, needs sequence numbers. RSSI and LQI exist only for packets that arrived — only PRR sees losses.
- Strong RSSI + low PRR → collision/interference. High LQI + low PRR → lost packets are invisible to LQI.
- Link regions: connected / transitional / disconnected; transitional links cost the most (retransmissions, route flapping). Hysteresis: add at PRR > 0.8, drop at < 0.5.
- Asymmetry: exchange success = PRR_forward × PRR_reverse (0.90 × 0.50 = 0.45).
- 802.15.4 2.4 GHz: channels 11–26, 5 MHz spacing, 250 kb/s, 4 bits → 1 symbol → 32 chips, O-QPSK + DSSS; PPDU = preamble 4 B, SFD 1 B, PHR 1 B, PSDU ≤ 127 B. 802.15.4 ≠ Zigbee.
- Rime channel (software ID) ≠ radio channel.
- Simulation result = code + topology + radio model; never report a result without the radio medium, and do not read real range off a Cooja parameter.
