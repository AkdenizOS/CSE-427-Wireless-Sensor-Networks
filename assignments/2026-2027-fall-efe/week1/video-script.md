# Week 1 video — environment + Sky mote projects

Target length: **12–14 min** after editing. Rules: full screen, you on camera, clear
English, explain *how* and *why*. OBS records the whole screen with your webcam in the
bottom-right corner.

Record in **7 short parts**. To start a part press **Ctrl+Alt** (releases the keyboard from the VM), then **F9**; to end it, **Ctrl+Alt** then **F10**. (F9/F10 do not reach OBS while the VM has the keyboard.)
If you make a mistake, pause, repeat the sentence and continue — the pauses get cut in
editing. Re-record a whole part only if it went badly.

`[DO]` = what you click or type. Plain text = what you say (read naturally, paraphrase freely).

What the instructor asked for, and where this video covers it:
- Teams: "installation of VMware, Instant Contiki 3.0, the configuration" → parts 2–3.
- Teams: "a basic Sky Mote project that can send packets when the simulation is running" → part 6.
- Week 1 slides, last slide: new simulation with Network / Mote Output / Timeline /
  Simulation Control, a Sky mote type from **hello-world**, three motes, save → part 5.

---

## Before you press record

- [ ] VM is running, logged in (`user` / `user`), desktop is clean, Cooja is **closed**.
- [ ] Close the film tab and any private windows on Windows.
- [ ] Sit so the webcam sees your face (the corner box shows it).
- [ ] Recordings land in `C:\Users\my156\Videos` as `.mp4` — one file per part.

---

## Part 1 — Intro and background, on camera (2 min)

[DO] **Ctrl+Alt**, then **F9**.

Hello, my name is Efe Kuruçay. This is my Week 1 submission for CSE 427, Wireless Sensor
Networks. I will show my lab environment — VMware, the Instant Contiki 3.0 virtual
machine and its configuration — then run the hello-world example on Sky motes in Cooja,
and finally a Sky mote project that sends packets while the simulation is running.

A wireless sensor network is a set of small embedded nodes, called motes, that sense
something in the physical world, process it a little, and send it over a low-power radio
to a sink or gateway. Compared to Wi‑Fi or cloud IoT, the big difference is the
constraints: motes run on batteries, have kilobytes of memory, and the radio is both the
most expensive part in energy and the least reliable part, because wireless links are
lossy and change over time.

The mote we use is the **Sky mote**, also called TelosB. It has an **MSP430**
microcontroller and a **CC2420** radio, which implements IEEE 802.15.4 at 2.4 GHz. The
operating system is **Contiki 3.0** — an event-driven OS where each program is a
*process*, and timers like `etimer` let the mote sleep instead of busy-waiting.

We do not have real hardware, so we use **Cooja**, Contiki's network simulator. For Sky
motes, Cooja uses **MSPSim** to emulate the real MSP430 and CC2420 — so the same firmware
that would run on the real board runs inside the simulator.

[DO] **Ctrl+Alt**, then **F10**.

## Part 2 — VMware and the VM configuration (2 min)

[DO] **Ctrl+Alt**, then **F9**. Show the VMware Workstation window. Menu **Help → About VMware Workstation** — show the version, close.

The host is my Windows 11 laptop. I use **VMware Workstation Pro**, which Broadcom now
offers free for personal use; I downloaded it from the Broadcom support portal.

[DO] Open **VM → Settings…** (only look — do not change anything while it is running).

The course uses the **Instant Contiki 3.0** image. I downloaded it from Contiki's
SourceForge page as a zip, extracted it, and opened the `.vmx` file in VMware. It is a
32-bit Ubuntu with the whole toolchain already installed: the MSP430 compiler, Java and
Ant, and Cooja.

[DO] Point at **Memory** and **Processors**.

For the configuration: the image ships with only 1 GB of RAM and one CPU. Cooja is a Java
application and every Sky mote is a full MSP430 emulation, so I increased the memory to
**4 GB** and the processors to **2 cores**. The network adapter is **NAT**, so the VM can
reach the internet through the host.

[DO] Close the settings dialog (Cancel). **Ctrl+Alt**, then **F10**.

## Part 3 — Inside the VM (1.5 min)

[DO] **Ctrl+Alt**, then **F9**. Double-click **Terminal** on the VM desktop. Type:
```
ls ~/contiki
```

This is the Contiki 3.0 source tree. `core` is the OS and networking code, `cpu` and
`platform` contain hardware support — `platform/sky` is our mote — `examples` has example
programs, and `tools/cooja` is the simulator.

[DO] Type:
```
msp430-gcc --version
ls ~/contiki/tools/mspsim
```

This is the cross-compiler that builds firmware for the MSP430. And this folder is
**MSPSim**. One problem I hit: in this image the MSPSim folder was empty, because it is a
git submodule that was never downloaded. Without it, Cooja shows the error "Could not find
the MSPSim build file" and there is no Sky mote type. This is the problem mentioned in the
lab notes. The fix was to put the MSPSim source at the commit this Contiki version
expects into that folder and rebuild Cooja with `ant jar`. Now the folder is populated and
Sky motes work.

[DO] **Ctrl+Alt**, then **F10**.

## Part 4 — The code (1.5 min)

[DO] **Ctrl+Alt**, then **F9**. Type:
```
gedit ~/contiki/examples/rime/example-broadcast.c &
```
Scroll to `PROCESS(example_broadcast_process ...`.

For the packet project I use Contiki's Rime **broadcast example**. `PROCESS` declares
the process and `AUTOSTART_PROCESSES` starts it at boot. Inside the process thread,
`broadcast_open` opens a Rime broadcast connection on channel **129** — a software
channel number, not the radio's physical channel — and registers a receive callback.

Then there is an infinite loop. `etimer_set` sets a timer; the comment says 2 to 4
seconds, but the code is `CLOCK_SECOND * 4 + random % (CLOCK_SECOND * 4)`, so the real
interval is **4 to 8 seconds**. `PROCESS_WAIT_EVENT_UNTIL` puts the process to sleep until
the timer expires — no busy waiting, which saves energy. Then `packetbuf_copyfrom` puts
the string "Hello" in the packet buffer, `broadcast_send` transmits it, and it prints
"broadcast message sent".

On the receiving side, the `broadcast_recv` callback prints the sender's address and the
message.

[DO] Close gedit. **Ctrl+Alt**, then **F10**.

## Part 5 — Cooja with hello-world (2 min)

[DO] **Ctrl+Alt**, then **F9**. In the terminal:
```
cd ~/contiki/tools/cooja
ant run
```

`ant run` compiles and starts Cooja.

[DO] **File → New simulation…** Name: `Week1-Hello`. Radio medium: **Unit Disk Graph Medium (UDGM): Distance Loss**. Click **Create**.

The Network, Simulation control, Mote output and Timeline windows open. UDGM is the
simplest radio model: each mote has a transmission range, and inside that circle packets
are received.

[DO] **Motes → Add motes → Create new mote type → Sky mote…** Click **Browse**, open `/home/user/contiki/examples/hello-world/hello-world.c`. Click **Compile**, wait until it finishes, then **Create**.

As the lab notes suggest, I start with hello-world to prove the toolchain works. Cooja
runs `make hello-world.sky TARGET=sky`, which compiles the C file with msp430-gcc into a
real Sky firmware image.

[DO] Number of new motes: **3**, click **Add motes**. Click **Start** in Simulation control. After a few seconds, **Pause**.

Each of the three motes prints its boot messages — the Rime address, the MAC layer — and
then "Hello, world" once. The program prints only at start-up, so nothing else appears.
No packets are sent here; this only proves the compiler, MSPSim and Cooja work.

[DO] **File → Save simulation as…** → `Week1-Hello.csc`. **Ctrl+Alt**, then **F10**.

## Part 6 — Broadcast simulation: packets on the air (3 min)

[DO] **Ctrl+Alt**, then **F9**. **File → New simulation…** (if asked, you do not need to save again). Name `Week1-Broadcast`, UDGM, **Create**.

[DO] **Motes → Add motes → Create new mote type → Sky mote…** Browse `/home/user/contiki/examples/rime/example-broadcast.c`, **Compile**, **Create**. Number of new motes **3**, **Add motes**.

[DO] In the **Network** window open its **View** menu and tick, one at a time: **Mote IDs**, **Radio environment (UDGM)**, **Radio traffic**. (They are off by default; the menu closes after each click.)

These views show the mote numbers, the radio ranges, and arrows for packets on the air.

[DO] Click on mote 1 so its green range circle shows. Make sure the other motes are inside the circle; drag them closer if needed.

The green circle is the transmission range, the grey one is the interference range.

[DO] Click **Start**. Let it run ~30 seconds.

[DO] Point at the **Mote output** window.

Each mote prints "broadcast message sent", and the others print "broadcast message
received from 2.0: 'Hello'" and so on. So packets are really being sent and received
over the simulated 802.15.4 radio.

[DO] **Tools → Radio messages**. Point at the packet list.

This window shows the actual radio frames on the air — source, destination, and the
bytes. The default MAC layer in Contiki 3.0 is **ContikiMAC** — you can see it in the
boot message "nullsec CSMA ContikiMAC". It keeps the radio off most of the time and
repeats a broadcast during a whole wake-up interval so that sleeping neighbours catch it,
which is why one "Hello" can appear as several frames here.

[DO] Point at the **Timeline** window.

The timeline shows the radio state of each mote. Most of the time the radio is off, with
short on-periods. This is the energy trade-off from the lecture: idle listening is
expensive, so the MAC layer duty-cycles the radio.

[DO] **Ctrl+Alt**, then **F10** (leave the simulation running for part 7).

## Part 7 — One experiment and wrap-up, on camera (1.5 min)

[DO] **Ctrl+Alt**, then **F9**. Drag mote 3 far away, outside mote 1's green circle. Watch Mote output for ~20 s.

If I move mote 3 outside the range of the others, it keeps printing "broadcast message
sent", but nobody receives it anymore, and it stops receiving theirs. In UDGM the range
is a sharp boundary. Real links are not like this — they have a transitional region
where some packets arrive and some do not — which is what Week 2 is about.

[DO] Drag it back inside; reception resumes. **Pause**. **File → Save simulation as…** → `Week1-Broadcast.csc`.

To summarise: I set up VMware Workstation with the Instant Contiki 3.0 VM, gave it 4 GB of
RAM and 2 CPUs, fixed the missing MSPSim component so Sky motes can be emulated, ran
hello-world on three Sky motes, and ran a three-mote Rime broadcast simulation. The motes
send packets every 4 to 8 seconds, every mote in range receives them, a mote outside the
range is cut off, and the timeline shows the radio duty-cycling that saves energy. Thank
you for watching.

[DO] **Ctrl+Alt**, then **F10**. Tell Claude "Week 1 bitti" — the parts get joined and cleaned up.
