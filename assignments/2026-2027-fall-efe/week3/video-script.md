# Week 3 video — energy accounting in Cooja

Target length: **11–13 min** after editing. You on camera, full screen, clear English.
Required by the lab deck: topology + mote output, a Powertrace excerpt per scenario, the
completed table, one graph, listen % across scenarios, which state dominates and why, limitations.

Record in **5 parts**: **Ctrl+Alt** (releases the keyboard from the VM), then **F9** to start a
part; **Ctrl+Alt**, then **F10** to end it. Mistakes and pauses get cut in editing.

`[DO]` = what you click or type. Plain text = what you say.

---

## Before you press record

- [ ] VM logged in (password `user`), one terminal open, Cooja **closed**.
- [ ] On Windows, `C:\WSN\video\results.html` open in the browser (table, graph, logs).
- [ ] Numbers you will say (all measured, first 60 s skipped as warm-up):

| | Listen % | Radio on % | I_avg | Lifetime | PDR |
|---|---|---|---|---|---|
| A NullRDC 15 s | 100 | 100 | 18.9 mA | 5.5 days | 59/59 |
| B ContikiMAC 15 s | 0.9 / 1.0 | 1.4 / 1.7 | 0.34 / 0.40 mA | 302 / 263 days | 59/59 |
| C ContikiMAC 5 s | 1.4 / 1.7 | 2.7 / 3.4 | 0.59 / 0.74 mA | 175 / 140 days | 179/179 |

(server / client)

---

## Part 1 — Intro and theory, on camera (2 min)

[DO] **Ctrl+Alt**, then **F9**.

Hello, I am Efe Kuruçay, and this is my Week 3 submission for CSE 427. The topic is energy.
I built a two-node UDP experiment in Instant Contiki 3.0 and Cooja, measured where each mote
spends its time with Powertrace, and compared three scenarios: continuous listening, duty
cycling, and duty cycling with higher traffic.

A Sky mote has four states that matter for energy: the CPU is active, the CPU is in low
power mode, the radio is transmitting, or the radio is listening. Contiki's Energest module
counts how many clock ticks the mote spends in each state, and Powertrace prints these
counters every 30 seconds.

The current model from the lab is: CPU 1.8 milliamps, low power mode 0.0545 milliamps,
transmit 17.4 milliamps, and listen 18.8 milliamps. Listening costs even more than transmitting. A radio that waits for packets all the time
is the most expensive thing a sensor node does, and my experiment measures how much of the
energy goes there.

[DO] **Ctrl+Alt**, then **F10**.

---

## Part 2 — Code and configuration, in the VM (2 min)

[DO] **Ctrl+Alt**, then **F9**. In the VM terminal type:

```
cd ~/contiki/examples/energy-lab-week3
grep -n "powertrace\|SEND_INTERVAL" energy-client.c
```

The client and the server are the UDP client and server examples from Contiki 3.0. I added
Powertrace to both: I include `powertrace.h`, and right after `PROCESS_BEGIN` I call
`powertrace_start` with 30 seconds. The client sends a numbered "Hello" message to the
server; `SEND_INTERVAL` sets how often.

[DO] Type:

```
cat Makefile
```

The Makefile adds the Powertrace app and IPv6, uses CSMA as the MAC layer, and this one
line selects the radio duty cycling layer. NullRDC keeps the radio on all the time; that is
scenario A. ContikiMAC wakes the radio eight times per second for a very short channel
check and sleeps the rest of the time; that is scenarios B and C. Scenario C also changes the send interval
from 15 seconds to 5 seconds. Nothing else changes between the scenarios: same two motes,
same positions, same radio medium, same 15 simulated minutes, same random seed.

One problem I had to solve: in Contiki 3.0 the Sky platform turns off IPv6 neighbour
discovery, because it expects RPL to fill the neighbour table. With only two motes and no
RPL, the client's packets were dropped before they reached the radio. Even the unmodified
example failed. So both programs add the other mote to the neighbour table directly. I
also set `UIP_CONF_TCP=0` and turned debug printing off, because the server firmware did not
fit into the Sky's 48 kilobytes of ROM with ContikiMAC.

[DO] **Ctrl+Alt**, then **F10**.

---

## Part 3 — Cooja topology and mote output (2 min)

[DO] **Ctrl+Alt**, then **F9**. In the terminal type:

```
~/w3-cooja.sh b
```

[DO] Wait until Cooja opens. Point at the **Network** window.

This is the topology: mote 1 is the UDP server, mote 2 is the UDP client, 10 metres apart,
with the unit disk graph radio medium, 50 metre range, no packet loss. I keep this layout
identical in all three scenarios so that only the configuration explains the differences.

[DO] Click **Start** in Simulation control. Let it run ~40 simulated seconds. Point at **Mote output**.

In the mote output the client prints "TX: Hello" and a number, and the server prints
"Server received" with the same number, so the packets arrive. Every 30 seconds both motes
print a Powertrace line. The four numbers after the sequence number are the cumulative CPU,
low power mode, transmit and listen ticks since boot. Because they are cumulative, I always
take the difference between two consecutive lines.

[DO] Click **Pause**. Close Cooja (File → Exit, don't save). **Ctrl+Alt**, then **F10**.

---

## Part 4 — Logs, table and graph, in the browser (4 min)

[DO] **Ctrl+Alt**, then **F9**. Switch to the browser with `results.html`. Scroll to **5 · Powertrace log excerpts**.

I ran each scenario for 15 simulated minutes and saved the mote output. Here are two
consecutive Powertrace lines of the server for each scenario. In scenario A the line starts
with "nullrdc"; the listen counter grows by about 983 000 ticks in 30 seconds, which is all
of the time. In B and C the line says ContikiMAC, channel check rate 8 hertz, and the listen
counter grows by only about 8 to 13 thousand ticks.

[DO] Scroll to **4 · Formulas**.

I wrote a Python script that turns the log into a CSV, using the formulas from the lab:
delta CPU over delta CPU plus delta LPM for the CPU duty cycle, delta TX and delta RX over
delta T for the radio, and the sum of each state's share times its current for the average
current. Lifetime is a 2500 milliamp-hour battery divided by the average current. For
example, scenario B, server, between 150 and 180 seconds: delta TX is 3730 and delta RX is
8650 out of about 983 000 ticks, so the radio is on 1.26 percent of the time.

[DO] Scroll to **2 · Completed measurement table**.

This is the completed table, averaged over 27 intervals after skipping the first minute as
warm-up. In scenario A the radio listens 100 percent of the time, on both motes, and the
average current is 18.9 milliamps. That is about five and a half days on two AA batteries. With
ContikiMAC in scenario B, listening drops to about 1 percent, the current drops to 0.34
milliamps on the server, which is 55 times lower, and the lifetime becomes about 300 days.
The packet delivery ratio stays at 100 percent, so duty cycling did not cost reliability here.
In scenario C, with three times more packets, the radio is on about 2.7 to 3.4 percent of
the time, and the server's current rises by 73 percent, to 0.59 milliamps.

[DO] Scroll to **3 · Graph**.

The graph shows the same data on a log scale. On the left, the blue bars are listen time:
100 percent in A, around 1 percent in B and C. The red bars are transmit time: only 0.02
percent in A, and higher in B and C. On the right is the average current and the lifetime.

[DO] **Ctrl+Alt**, then **F10**.

---

## Part 5 — Interpretation and limitations, on camera (2–3 min)

[DO] **Ctrl+Alt**, then **F9**. Scroll to **6 · Interpretation** (or look at the camera).

Which state dominates? Radio listening. In scenario A the radio transmits only 0.02 percent
of the time, but listening makes up 99.6 percent of the average current. Most of the
node's energy goes into waiting for data, not sending it. This is called idle listening.

Duty cycling targets this. ContikiMAC turns the radio off and wakes it only for short
channel checks, so listen time fell from 100 to about 1 percent. In exchange, the sender
has to repeat its frame until the receiver wakes up. That is why, in B, transmit time went up
from 0.02 to about 0.5 percent, and why the client, which sends, has a higher current than
the server.

Higher traffic in scenario C raised everything: transmit time by about 2.5 times, listen
time by about 1.6 times, and CPU time as well, because every packet means more copies,
more acknowledgements and more processing. With duty cycling, energy grows with
the traffic. Without it, energy is constant and very high.

Limitations of this estimate: it is a simulation with an ideal radio medium, two motes and
one hop, so there are no collisions, no retransmissions and no forwarding load. The currents
are constant datasheet-style values; a real battery loses voltage and capacity, and sensors
and LEDs are not counted. I measured 15 minutes and extrapolated to months. In a multi-hop
network, the nodes near the sink would forward everyone's traffic and die first.

Thank you.

[DO] **Ctrl+Alt**, then **F10**.
