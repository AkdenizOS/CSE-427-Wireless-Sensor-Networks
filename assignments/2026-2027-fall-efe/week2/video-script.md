# Week 2 video — radio propagation & link quality in Cooja

Target length: **13–15 min** after editing. Same rules as week 1: full screen, you on camera,
clear English, explain *how* and *why*. Follows the "Thursday document" (week 2 lab deck).

Record in **6 short parts**: **Ctrl+Alt** (releases the keyboard from the VM), then **F9** to
start a part; **Ctrl+Alt**, then **F10** to end it. Mistakes and pauses get cut in editing.

`[DO]` = what you click or type. Plain text = what you say.

---

## Before you press record

- [ ] VM running, logged in, Cooja **closed**, desktop clean.
- [ ] Have this table ready on paper (or in a text editor) — you fill it during the video:

| Experiment | Sent | Received | RSSI printed | LQI |
|---|---|---|---|---|
| Close | 10 | | | |
| Medium | 10 | | | |
| Near edge | 10 | | | |
| Near edge, RX success 0.6 | 20 | | | |


What you should expect (measured on this VM, so you are not surprised on camera):
close ≈ −67 dBm printed, medium ≈ −97, near edge ≈ −135, LQI always 37,
out of range = nothing, RX 0.6 near the edge ≈ 14–15 of 20.

---

## Part 1 — Intro and theory, on camera (3 min)

[DO] **Ctrl+Alt**, then **F9**.

Hello, I am Efe Kuruçay, and this is my Week 2 submission for CSE 427. The topic is the
radio and physical layer. I will build a one-sender, one-receiver experiment in Cooja,
measure RSSI, LQI and packet reception ratio at different distances and with a lossy
link, and explain why these three metrics do not say the same thing.

A wireless link is not a cable; it is better described as a probability of delivering a
packet. Received power is measured in **dBm**, which is absolute power relative to 1 mW —
so −60 dBm is stronger than −90 dBm. **dB** is only a ratio, like a path loss of 70 dB.

Received power falls with distance — that is **path loss**. On top of that, obstacles
cause **shadowing** and multipath causes **fading**, so the same distance can give very
different results.

We measure the link with three metrics. **RSSI** is the received signal strength of a
packet that arrived. **LQI** is a radio-specific quality value for a frame that was
successfully decoded — on the CC2420 it comes from chip correlation. **PRR**, packet
reception ratio, is received divided by sent over a window, and it needs sequence
numbers so we can see which packets are missing. RSSI and LQI only exist for packets
that arrived; PRR is the only one that sees the lost packets.

Real links have a **transitional region** between "connected" and "disconnected", where
delivery is partial and bursty. Cooja's simplest radio model, **UDGM**, does not have
that — which is one thing this experiment shows.


[DO] **Ctrl+Alt**, then **F10**.

## Part 2 — The code (2 min)

[DO] **Ctrl+Alt**, then **F9**.

[DO] Open Terminal. Type:
```
cd ~/contiki/examples/week2-radio
ls
gedit sender.c receiver.c Makefile &
```

These are the three files from the lab notes.

[DO] Show **sender.c**.

The sender opens a Rime broadcast connection on channel 129, sets an `etimer` of one
second, and in the loop increments a **sequence number**, copies it into the packet
buffer, broadcasts it, and prints `TX seq=`. Broadcast is used so that routing and IPv6
do not hide the radio behaviour.

[DO] Show **receiver.c**.

The receiver opens the same Rime channel — 129 is a software channel ID, not the
802.15.4 radio channel, so both sides must match. In the callback it copies out the
sequence number, reads `PACKETBUF_ATTR_RSSI` and `PACKETBUF_ATTR_LINK_QUALITY`, and
prints `RX seq=… RSSI=… LQI=…`.

[DO] Show **Makefile**.

One thing I had to fix: the Makefile in the notes did not link Contiki's Rime stack, so
the build failed with "undefined reference to broadcast_send". In Contiki 3.0 you have to
enable Rime with `CONTIKI_WITH_RIME = 1` — Contiki's own Rime examples do the same.
After adding that line both programs compile.

[DO] Close gedit.


[DO] **Ctrl+Alt**, then **F10**.

## Part 3 — Build the simulation (2 min)

[DO] **Ctrl+Alt**, then **F9**.

[DO] Type:
```
cd ~/contiki/tools/cooja
ant run
```

[DO] **File → New simulation…** Name `Week2-Radio`, radio medium **UDGM: Distance Loss**, **Create**.

[DO] **Motes → Add motes → Create new mote type → Sky mote…** Description `Sender`, browse `/home/user/contiki/examples/week2-radio/sender.c`, **Compile**, **Create**, add **1** mote.

[DO] Again: Sky mote, Description `Receiver`, browse `receiver.c`, **Compile**, **Create**, add **1** mote.

[DO] In the **Network** window open **View** and tick, one at a time: **Mote IDs**, **Radio environment (UDGM)**, **10m background grid**. (Off by default; the menu closes after each click.)

The grid squares are 10 metres, so I can place the receiver at a known distance.

[DO] Right-click an empty area of the Network window → **Change transmission ranges**. A small **UDGM** box appears: set **TX range 20**, **INT range 40** (type the number, press Enter), then close the box.

The default range is 50 metres; the lab uses about 20 metres. Remember this is a
simulation parameter, not the real range of a Sky mote.

[DO] Drag the sender (mote 1) to the left, the receiver (mote 2) close to it on the right. Click the sender so its range circle is visible (2 grid squares = 20 m radius).


[DO] **Ctrl+Alt**, then **F10**.

## Part 4 — First packets and distance (2.5 min)

[DO] **Ctrl+Alt**, then **F9**.

**A — first packets.**

[DO] **Start**. After ~10 packets, **Pause**. Point at Mote output.

Mote 1 prints TX seq 1, 2, 3…, and mote 2 prints RX with the same sequence numbers, plus
RSSI and LQI. Every sequence number arrives, so the link works.

**B — distance.**

[DO] For each position: move the receiver, **Start**, watch ~10 packets, **Pause**, read the numbers into the table.
- **Close**: right next to the sender (well under one grid square, ~3 m).
- **Medium**: one grid square away (~10 m).
- **Near edge**: just inside the green circle (~18–19 m).

As the receiver moves away, RSSI goes down — about −67, then −97, then around −135 in
the printout — but all packets still arrive, so PRR stays at 100 %. And LQI stays at 37
everywhere.


[DO] **Ctrl+Alt**, then **F10**.

## Part 5 — Boundary, controlled loss, PRR (3 min)

[DO] **Ctrl+Alt**, then **F9**.

**C — cross the boundary.**

[DO] Start with the receiver just inside the circle, **Start**. After a few packets drag it **outside** the circle while running. Wait ~5 s, drag it back inside.

The sender keeps printing TX, but the receiver goes completely silent as soon as it is
outside the circle, and resumes immediately when it comes back. In UDGM the range is a
perfect disk with a sharp edge — there is no transitional region at all.

**D — controlled loss.**

[DO] Put the receiver **near the edge** again and do not move it anymore.
[DO] Right-click empty area → **Change TX/RX success ratio** → in the small box keep TX **1.0**, set RX **0.6** (type, Enter), close the box.
[DO] **Start**, let ~20 packets go, **Pause**.

Now the geometry is the same, only the reception probability changed, and sequence
numbers start disappearing — for example RX 34, 36, 38 with 35 and 37 missing.

An important detail: I placed the receiver near the edge on purpose. When I tried RX 0.6
with the receiver close to the sender, I got 20 out of 20. The reason is in Cooja's UDGM
code: the success probability is scaled with distance — it is
`1 − (d / range)² × (1 − RX ratio)`. So close to the sender it is almost 1, and it only
reaches the configured 0.6 at the edge of the range.

**E — PRR.**

[DO] Count RX lines in the 20-packet window, write the number in the table.

PRR is received divided by sent. I received ___ out of 20, so PRR = ___ — roughly what
0.6 near the edge predicts. With another run the number would be slightly different,
because it is random.


[DO] **Ctrl+Alt**, then **F10**.

## Part 6 — Why the results look like this, and wrap-up, on camera (2.5 min)

[DO] **Ctrl+Alt**, then **F9**.

Three observations.

**RSSI** decreases linearly with distance here, because UDGM models signal strength as a
straight line from −10 dBm at the sender to −95 dBm at the edge of the range. Also, the
printed values are 45 dB too low: the lab code subtracts the CC2420 offset of 45, but
Contiki's CC2420 driver already applies that offset, so it is subtracted twice. The real
values are about −22, −52 and −90 dBm, which match the UDGM formula exactly.

**LQI** is constant at 37. UDGM does not model decoding quality, so LQI carries no
information in this simulation. That is a good example of the lecture point: RSSI, LQI
and PRR are related but not interchangeable, and what you measure depends on the radio
model.

**PRR** is the only metric that saw the losses in experiment D. RSSI and LQI looked the
same for every packet that arrived — they cannot describe packets that never arrived.
And in experiments B and C, PRR was either 100 % or 0 %, because UDGM has a sharp
boundary. A richer model like MRM, or a real deployment, would show a transitional
region where PRR drops gradually.

[DO] **File → Save simulation as…** `Week2-Radio.csc`.

To summarise: I built a sender and receiver in Contiki 3.0, fixed the Makefile, measured
RSSI, LQI and PRR in Cooja, and found that distance changes RSSI but not delivery in
UDGM, that the boundary is sharp, that the RX success ratio is distance-dependent, and
that only PRR reveals lost packets. The main lesson: measure delivery, do not assume it
from distance or signal strength alone. Thank you.

[DO] **Ctrl+Alt**, then **F10**. Tell Claude "Week 2 bitti" — the parts get joined and cleaned up.
