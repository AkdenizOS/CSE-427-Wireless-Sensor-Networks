# Week 4 video — Duty-Cycle MAC Bench

Target length: **10–12 min** after editing (the tutorial says 12 max). You on camera, full
screen, clear English. Name and student number at the start, settings for every
experiment, evidence on screen, answers Q1–Q13 aloud.

No VM this week: the bench runs in the Windows browser — `C:\WSN\video\Duty-Cycle MAC Bench.html`
(needs internet for React). Record in **6 parts**: **F9** starts a part, **F10** ends it.

`[DO]` = what you click. Plain text = what you say. Numbers in *(expect …)* are what I
measured; the **Compare all five** tables use a fixed seed, so you will see exactly these.
The live counters in A and B depend on timing, so yours will be close, not identical —
**say the numbers you see**.

---

## Before you press record

- [ ] Bench open in the browser, zoom so the whole page width fits.
- [ ] Defaults: Wake/check interval **100 ms**, Radio range **21 m**, Mean packet interval **1200 ms**, Retry limit **3**.

---

## Part 1 — Intro and the page (1.5 min)

[DO] **F9**. Look at the camera.

Hello, I am Efe Kuruçay, student number <student number>, and this is my Week 4 submission
for CSE 427: contention-based and duty-cycled MAC protocols, measured with the Duty-Cycle
MAC Bench. For every experiment I set a preset, run it, read the evidence in the counters, the radio
timeline and the event log, and explain the mechanism behind it.

[DO] Point at each area while you name it.

At the top I choose the MAC protocol: B-MAC, X-MAC, ContikiMAC, RI-MAC and A-MAC. Here are
Run or Pause, Reset, and "Burst: all senders", which makes every sender queue one packet at
the same moment. On the left is the sensor field with the layout presets, under it the radio
timeline, which shows each mote's radio state over the last 400 milliseconds. On the right
are the counters: delivered and PDR, hidden-terminal losses, other collisions, exposed
deferrals, overhearing, over-emitting, idle listening and radio duty cycle. Below them are the event
log and the parameters: wake interval, radio range, packet interval and retry limit. At the
bottom, "Compare all five" runs every MAC for 60 simulated seconds with the same seed.

[DO] **F10**.

---

## Part 2 — Experiment A: hidden terminal (2 min)

[DO] **F9**. Layout → **Hidden terminal (S1 ↛ S2)**. Click **ContikiMAC**. Tick **Synchronised traffic**. Speed **1× real time**. **Reset**.

The preset has two senders, S1 and S2, and one receiver, R1, in the middle. R1 hears both
senders, but S1 and S2 are out of each other's range. The red dashed line says "can't hear
each other". I use ContikiMAC and synchronised traffic, so both senders generate at the same
instant.

[DO] Click **Burst: all senders** three times, about one second apart. After each, read **Delivered / PDR** and **Hidden-terminal losses** aloud. *(expect PDR 75 → 88 → 92 %, losses 12 → 27 → 41 frames)*

[DO] Set **Pause on → hidden terminal**, click **Burst** once. When it pauses, point at the event log line "HIDDEN TERMINAL — R1 lost S1's DATA + S2's DATA" and at the red overlap on R1's timeline row. Set **Pause on → nothing**, click **Run**.

Here is one hidden-terminal event: R1 lost S1's data and S2's data because they overlapped
at R1.

**Q1.** Both senders transmit even though they use carrier sensing, because carrier sensing
is local. Before sending, S1 checks the channel at its own position. S2 is out of S1's range,
so S1 hears nothing and decides the channel is free; S2 decides the same.

**Q2.** The collision happens at the common receiver, R1. R1 is inside both ranges, so it
receives both frames at the same time, and with no capture effect both are corrupted. The
senders cannot detect this; they only notice the missing ACK and retry.

[DO] Drag **Radio range** to the maximum, **32 m**. **Reset**, **Burst** twice.

**Q3.** First I increased the range to the maximum, 32 metres. The losses continue, because
S1 and S2 are still more than 32 metres apart.

[DO] Drag **S2** next to R1 (the dashed "can't hear" line disappears). **Reset**, **Burst** three times. Point at **Hidden-terminal losses 0** and a "DEFERRAL … channel busy" line. *(expect PDR 100 %, hidden 0)*

So I moved S2 closer, until S1 and S2 can hear each other. Now hidden-terminal losses stay at
zero and PDR is 100 percent. In the log, S1 now says "deferred: channel busy". Its carrier
sense hears S2 and waits. Carrier sensing only works when the senders can hear each other.

[DO] **F10**.

---

## Part 3 — Experiment B: B-MAC versus X-MAC (2 min)

[DO] **F9**. Radio range back to **21 m**. Layout → **Overhearing crowd**. **Untick Synchronised traffic**. Click **B-MAC**, **Reset**, Speed **5×**. Let it run to **t ≈ 30 s**, then **Pause**.

Overhearing crowd: one sender S1, one receiver R1, and five idle neighbours, N1 to N5, that
are in range. I run B-MAC for 30 seconds.

[DO] Read the counters, then point at the long violet bar on the timeline. *(expect PDR 100 %, duty 7.2 %, overhearing ≈ 7100 ms / 130 frames, over-emitting ≈ 1300 ms)*

[DO] Click **X-MAC**, **Reset**, **Run** to **t ≈ 30 s**, **Pause**. Read the counters, point at the short strobes and the ACK on the timeline. *(expect PDR 100 %, duty 3.7 %, overhearing ≈ 135 ms / 57 frames, over-emitting ≈ 650 ms)*

**Q4.** While it waits for the receiver to wake, B-MAC transmits one long preamble that is
longer than the whole check interval, this violet bar, and then the data. X-MAC instead
sends a train of short strobes that carry the destination address, with small gaps between
them.

**Q5.** X-MAC produced much less overhearing: about 135 milliseconds against 7100 for B-MAC.
The reason is the mechanism. In B-MAC a neighbour that wakes up during the
preamble cannot tell who it is for, so it must stay awake until the data arrives and only
then discards it. In X-MAC a neighbour decodes one short strobe, sees that it is addressed to
someone else, and goes back to sleep.

**Q6.** In X-MAC the receiver answers a strobe with an early ACK in the gap. The sender stops
strobing immediately and sends the data, so it does not waste the rest of a full preamble.
That saves sender energy: over-emitting fell from about 1300 to 650 milliseconds. And the
data leaves sooner, so latency drops too. The radio duty cycle went from 7.2 to 3.7 percent.

[DO] **F10**.

---

## Part 4 — Experiment C: wake/check interval (1.5 min)

[DO] **F9**. Keep **Overhearing crowd**, synchronised traffic **off**. Set **Wake / check interval = 50 ms**. Click **Run 60 s comparison**. Point at the **B-MAC** row. Then set **500 ms**, click **Run again**, point at the B-MAC row.

The page allows 50 to 500 milliseconds, so I compare the two ends with B-MAC, where the
effect is clearest. I take the values from the comparison table.

| Wake interval | PDR | Mean latency | Duty cycle | Energy / pkt | Over-emitting | Idle listening |
|---|---|---|---|---|---|---|
| 50 ms | 100 % | 62 ms | 7.0 % | 33.47 mJ | 1.11 s | 16.10 s |
| 500 ms | 100 % | 512 ms | 24.6 % | 114.98 mJ | 12.31 s | 1.02 s |

**Q7.** Mean latency rose from 62 to 512 milliseconds. A packet can only be delivered when
the receiver wakes up. With a 500 millisecond interval the sender has to wait, on average,
much longer for that rendezvous, and B-MAC's preamble has to cover the whole interval.

**Q8.** No, the energy did not disappear. It moved somewhere else. Idle listening on the receivers dropped
from 16 seconds to 1 second, because they check the channel ten times less often. But the
senders' preambles became ten times longer, so over-emitting rose from 1.1 to 12.3 seconds,
and every neighbour that wakes during a long preamble overhears it. In total, energy per
delivered packet more than tripled. For rare monitoring traffic a long interval can still be
right, because the receivers' saving happens all the time; for urgent alarms I would use a
short interval to keep latency low.

[DO] **F10**.

---

## Part 5 — Experiment D: ContikiMAC phase lock (1.5 min)

[DO] **F9**. Wake interval back to **100 ms**. Click **ContikiMAC**. **Untick ContikiMAC phase-lock**. **Reset**, **Run 60 s comparison**, point at the **ContikiMAC** row. **Tick phase-lock**, **Reset**, **Run again**, point at the row.

| Phase lock | PDR | Mean latency | Duty cycle | Energy / pkt | Over-emitting |
|---|---|---|---|---|---|
| OFF | 100 % | 57 ms | 2.9 % | 13.86 mJ | 1.90 s |
| ON | 100 % | 60 ms | 2.3 % | 11.22 mJ | 0.26 s |

[DO] Speed **1×**, **Reset**, **Run**. Within the first second the log shows "PROTOCOL S1 learned R1's wake-up phase from the ACK; its next packets start just before R1 wakes" — scroll the log down to it and point at it.

**Q9.** After the first successful delivery the sender learns from the ACK when the receiver
wakes up. That is this log line. Its next packets start just before that moment instead of
repeating copies for a whole wake interval. Over-emitting fell from 1.9 seconds to 0.26
seconds, the duty cycle from 2.9 to 2.3 percent, and energy per packet from 13.9 to 11.2
millijoules. Latency is about the same, 57 versus 60 milliseconds, because the sender now
waits for the predicted wake-up instead of starting at once.

**Q10.** In a real network the prediction is not perfect. This simulator has no clock drift,
so a learned phase stays valid until an attempt fails. Real crystals drift, so the sender
must start earlier with a safety margin, and when the prediction is wrong, it falls back to a
full wake interval of copies.

[DO] **F10**.

---

## Part 6 — Experiment E: compare all five, and limits (2.5 min)

[DO] **F9**. Layout → **Funnel: 4 senders → 1 sink**. Check: wake **100 ms**, range **21 m**, interval **1200 ms**, retry **3**, synchronised traffic **ticked** (the preset turns it on), phase-lock on. Click **Run 60 s comparison** and show the whole table.

| MAC | PDR | Mean latency | Duty cycle | Energy / pkt | Hidden losses |
|---|---|---|---|---|---|
| B-MAC | 50.5 % | 714 ms | 37.5 % | 59.65 mJ | 268 |
| X-MAC | 48.0 % | 347 ms | 27.0 % | 46.25 mJ | 2757 |
| ContikiMAC | 36.0 % | 563 ms | 30.5 % | 68.04 mJ | 712 |
| RI-MAC | 52.5 % | 110 ms | 12.5 % | 19.99 mJ | 215 |
| A-MAC | 29.0 % | 272 ms | 16.8 % | 48.74 mJ | 285 |

Four senders send to one sink at the same instant, so the sink is a contention point: every
packet must pass through it, and senders on opposite sides cannot hear each other, which is
why all five protocols lose packets.

**Q11.** RI-MAC has the highest PDR, 52.5 percent, and in this funnel it is also the lowest
energy per delivered packet, 20 millijoules, with the lowest latency. RI-MAC is
receiver-initiated: the sink sends a beacon when it wakes, so senders do not need long
preambles or strobe trains towards it.

**Q12.** A trade-off: A-MAC has a low duty cycle, 16.8 percent, but the lowest PDR, 29
percent; B-MAC delivers more, 50.5 percent, but its radios are on more than twice as long,
37.5 percent. And X-MAC has half the latency of B-MAC but ten times more hidden-terminal
losses, because its many short strobes collide at the sink. So the best protocol depends on
the metric and on the traffic. With a different topology the ranking changes, so there is
no universally best duty-cycled MAC.

**Q13.** These numbers are not real-world performance. The bench is a teaching model: a
unit-disk radio, where a node either hears a frame perfectly or not at all; carrier-sense
range equal to communication range; no capture effect, so any overlap destroys both frames;
1 millisecond time slots with simplified frame lengths; no clock drift; and a simplified
CC2420 current model. I used it to explain mechanisms and trade-offs, not to rank the
protocols for a real deployment.

Thank you.

[DO] **F10**.
