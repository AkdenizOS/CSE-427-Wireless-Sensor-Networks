# Week 1 — Introduction & Hardware Anatomy

> Mon Sep 14 (theory) · Thu Sep 17 (practice) · Syllabus focus: *WSN concepts, applications, mote architecture, Cooja setup*

**Next:** [Week 2](02-radio-and-physical-layer.md)

Attendance this week is not mandatory; regular attendance applies from Monday, September 21.
The Thursday September 17 practice session was cancelled (instructor ill) — the lab became
a video submission, and this week's attendance and lab count as bonus points.

## Goals
- Define a WSN and distinguish it from generic Wi‑Fi or cloud-only IoT.
- Explain why WSN design is constrained by energy, memory, radio uncertainty, and deployment conditions.
- Classify common WSN applications by reporting pattern and physical domain.
- Draw and explain a mote hardware architecture.
- Start a simple Cooja simulation in Instant Contiki 3.0 and identify basic windows.

## Key concepts
- **WSN**: spatially distributed embedded nodes (motes) that sense, process and communicate wirelessly; a sink / gateway / border router collects the data. Think of it as measurement infrastructure.
- **WSN vs IoT vs CPS**: WSN is the field layer where measurements are born; IoT connects devices to Internet services; a CPS monitors or controls a physical process.
- **Path of one measurement**: sensor samples → MCU converts/filters → radio sends a small frame → intermediate motes forward/aggregate → sink exports. Every step costs energy, delay, reliability and security.
- **Vocabulary**: mote, sink/gateway, neighbor, topology, duty cycle (fraction of time a component is active), event.
- **Six constraints**: energy (radio and idle listening dominate), small memory/CPU, lossy and asymmetric links, dynamic topology, scale and density, reliability/timing/security. Improving one often hurts another — there is no universal best WSN protocol.
- **Reporting patterns**: periodic, event-driven, query-driven, hybrid — they decide duty cycle, latency and buffers.
- **Domains**: environmental, agriculture, industrial/smart grid, buildings and cities, energy. Monitoring tolerates missing data; control/actuation does not.
- **Mote anatomy**: sensors → ADC → MCU → radio → antenna, plus memory and power subsystem. Radio and power dominate lifetime.
- **Radio states**: transmit, receive, idle, sleep; idle listening costs energy even with no useful packets.
- **Reference mote**: Sky / TelosB (MSP430 + IEEE 802.15.4 radio). Cooja mote = faster, less hardware-faithful; Java mote for special simulations.
- **Contiki 3.0**: event-driven C, processes and protothreads (`PROCESS`, `AUTOSTART_PROCESSES`, `PROCESS_THREAD`, `PROCESS_BEGIN/END`), `etimer` for periodic work without busy waiting. Stacks: Rime, uIP/uIPv6, 6LoWPAN, RPL, RDC/MAC drivers.
- **Cooja windows**: Network, Mote Output, Timeline, Simulation Control, Radio Messages.
- *A simulation screenshot is not a result; an interpreted measurement is a result.*

## Reading
- [Week 1 slides — Introduction & Hardware Anatomy](../resources/2026-2027-fall/Week_1_WSN_Introduction_Hardware_Anatomy.pptm) (includes the course syllabus)
- [Karl & Willig — Ch. 1 (introduction)](../resources/books/karl-willig-protocols-and-architectures-for-wireless-sensor-networks.pdf#page=28)
- [Karl & Willig — Ch. 2.1 (hardware components)](../resources/books/karl-willig-protocols-and-architectures-for-wireless-sensor-networks.pdf#page=45)
- [Dargie & Poellabauer — Ch. 1 (motivation)](../resources/books/dargie-poellabauer-fundamentals-of-wireless-sensor-networks.pdf#page=23)
- [Dargie & Poellabauer — Ch. 2 (applications)](../resources/books/dargie-poellabauer-fundamentals-of-wireless-sensor-networks.pdf#page=37)
- [Dargie & Poellabauer — Ch. 3 (node architecture)](../resources/books/dargie-poellabauer-fundamentals-of-wireless-sensor-networks.pdf#page=67)
- [Dargie & Poellabauer — Ch. 4 (operating systems)](../resources/books/dargie-poellabauer-fundamentals-of-wireless-sensor-networks.pdf#page=89)
- [An Introduction to COOJA](https://github.com/contiki-os/contiki/wiki/An-Introduction-to-COOJA)
- [Contiki wiki](https://github.com/contiki-os/contiki/wiki) · [Contiki OS GitHub](https://github.com/contiki-os/contiki) · [examples](https://github.com/contiki-os/contiki/tree/master/examples)

## Lab — environment validation
1. Install VMware and open the [Instant Contiki 3.0](https://sourceforge.net/projects/contiki/files/Instant%20Contiki/) VM. Give it enough RAM/CPU for Java-based Cooja. Take a snapshot before changing toolchain files.
2. Launch Cooja from the VM terminal:
   ```
   cd ~/contiki/tools/cooja
   ant run
   ```
   If it fails, record the exact path, command and error before changing anything.
3. File → New Simulation (clear name). Open Network, Mote Output, Timeline, Simulation Control.
4. Motes → Add Motes → Create New Mote Type → **Sky mote**; pick `~/contiki/examples/hello-world`, compile, add three motes.
5. Start the simulation, read Mote Output, connect it to the code. Save the simulation and reopen it.

Observe: how many motes and what they run, what each prints and when, whether they are in range, which radio medium is selected.

## Submission — due Sunday night (Sep 20)
A video showing your work:
- VMware installation, Instant Contiki 3.0, the configuration.
- A basic Sky mote project that **sends packets while the simulation is running**.

Video rules (same as any practice-session video): at least **10 minutes**, full screen, you
explaining **how you completed the task and why you got your results** in clear English,
you visible on camera. No written report.

## Practice
- [ ] Classify a greenhouse WSN's messages: periodic, event-driven, query-driven, control — which need reliability, low latency or authentication?
- [ ] Draw the mote block diagram from memory
- [ ] Point to each Cooja window and say what it shows

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
