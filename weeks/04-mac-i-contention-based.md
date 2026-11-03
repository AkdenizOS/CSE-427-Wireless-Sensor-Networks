# Week 4 — MAC I: Contention-Based

> Mon Oct 5 (theory) · Thu Oct 8 (practice) · Syllabus focus: *Hidden/exposed terminals, B-MAC, X-MAC, ContikiMAC, RI-MAC*

**Previous:** [Week 3](03-energy.md) · **Next:** [Week 5](05-mac-ii-scheduled-and-hybrid.md)

## Reading
- [Week 4 slides — MAC I: Contention-Based](../resources/2026-2027-fall/Week_4_WSN_MAC_I_Contention_theo.pptx)
- [Hidden & Exposed Terminals in Real WSNs](../resources/2026-2027-fall/Hidden%20&%20Exposed%20Terminals%20in%20Real%20WSNs.pptx) (three real deployments: body area networks, smart-meter meshes, drone-served farms)
- [Week 4 lab tutorial — Duty-Cycle MAC Bench](../resources/2026-2027-fall/Week_4_React_Duty_Cycle_MAC_Bench_Tutorial.pptx) (interface, counter glossary, model assumptions, grading rubric)
- [Week 4 lab worksheet](../resources/2026-2027-fall/Week_4_Duty_Cycle_MAC_Bench_Lab_Worksheet_FINAL2.docx) (experiments A–E, tables to fill, Q1–Q13)
- [Karl & Willig — Ch. 5.1 (MAC fundamentals)](../resources/books/karl-willig-protocols-and-architectures-for-wireless-sensor-networks.pdf#page=139)
- [Karl & Willig — Ch. 5.2 (low duty cycle and wakeup)](../resources/books/karl-willig-protocols-and-architectures-for-wireless-sensor-networks.pdf#page=147)
- [Karl & Willig — Ch. 5.3 (contention-based protocols)](../resources/books/karl-willig-protocols-and-architectures-for-wireless-sensor-networks.pdf#page=156)
- [Dargie & Poellabauer — Ch. 6 (medium access control)](../resources/books/dargie-poellabauer-fundamentals-of-wireless-sensor-networks.pdf#page=145)

## Lab — Duty-Cycle MAC Bench
No Cooja this week: the instrument is [Duty-Cycle MAC Bench](../resources/2026-2027-fall/Duty-Cycle%20MAC%20Bench.html),
a single HTML file — download it and open it in a browser (it loads React from a CDN, so it
needs internet). It simulates B-MAC, X-MAC, ContikiMAC, RI-MAC and A-MAC and reports PDR,
hidden-terminal losses, other collisions, exposed deferrals, overhearing, over-emitting,
idle listening, radio duty cycle and mean latency. For every experiment: predict, run,
observe, explain.

| Experiment | Setup | Record |
|---|---|---|
| A — hidden terminal | Preset *Hidden terminal*, ContikiMAC, synchronised traffic ON, *Burst: all senders* ≥ 3 times; then raise the radio range until S1 and S2 hear each other | generated, delivered, PDR, hidden-terminal losses per run |
| B — B-MAC vs X-MAC | Preset *Overhearing crowd*; B-MAC, reset, X-MAC, same settings | PDR, duty cycle, overhearing, over-emitting, timeline pattern |
| C — wake/check interval | Two interval values, everything else unchanged | PDR, mean latency, duty cycle, energy per delivered packet, over-emitting, idle listening |
| D — ContikiMAC phase lock | Same topology, phase lock OFF then ON | PDR, mean latency, duty cycle, energy per delivered packet, over-emitting; the *learned wake-up phase* log line |
| E — compare all five | Preset *Funnel — 4 senders to 1 sink*, *Compare all five → Run 60 s comparison* | PDR, mean latency, duty cycle, energy per packet, hidden losses for each MAC |

Read mean latency and energy per delivered packet from the *Compare all five* table; the
Event log shows the latency of single packets (`delivered · XX ms after generation`).

Pitfalls:
- Reset before comparing two protocols, and change one parameter at a time.
- Hidden-terminal collision (two senders that cannot hear each other overlap at the receiver)
  is not exposed-terminal deferral (a sender waits although its transmission would be safe).
- The bench is a teaching model: unit-disk radio, carrier-sense range = communication range,
  no capture effect, 1 ms slots, no clock drift, simplified CC2420 current model. Explain
  mechanisms and trade-offs with it; do not present its numbers as real-world performance.

## Submission — due Sun Oct 18, 23:59
Experiments A, B, C and E are worth 15 points, D is a 5-point bonus; all are added to the
midterm. Late submissions are not accepted. A video of about 10–12 minutes, explained aloud:
- your name and student number at the start, then the page and its main controls;
- the preset and parameter settings of each experiment;
- one hidden-terminal event in the log or timeline;
- the B-MAC vs X-MAC timelines and counters;
- the wake-interval table, the phase-lock OFF/ON comparison;
- the *Compare all five* table and one trade-off;
- answers to Q1–Q13 in your own words, including three model limitations.

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
