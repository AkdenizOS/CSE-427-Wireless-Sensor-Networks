# CSE 427 — Wireless Sensor Networks

Akdeniz University, Faculty of Engineering, Computer Engineering (English)
4th year elective · Fall 2026-2027

**Instructor:** Manolya Atalay · Course communication on Microsoft Teams
**Schedule:** Monday theory · Thursday practice (lab)

## Grading

| Component | Weight |
|-----------|--------|
| Monday theory attendance | 10% |
| Thursday practice attendance | 10% |
| Midterm | **40%** |
| Final | **40%** |

**Practice attendance by video.** If you cannot attend the Thursday session, you may
submit a video of your work instead; depending on its quality and completeness you may
not get full attendance credit. The video must:
- be at least **10 minutes** long;
- show the full screen of your work and explain **how you completed the task and why
  you obtained your results** — an essay in video form;
- have you speaking **English** clearly and understandably;
- show you in the video.

No additional document or written report is required.

Attendance in week 1 is not mandatory. Get any work or internship permissions before
Monday, **September 21** — regular attendance applies from that date.

## Weekly plan

The syllabus is in the [week 1 slides](resources/2026-2027-fall/Week_1_WSN_Introduction_Hardware_Anatomy.pptm).
The instructor aims to complete at least the first 10 weeks as planned.

| # | Topic | Note |
|---|-------|------|
| 1 | Introduction & Hardware Anatomy | [weeks/01](weeks/01-introduction-and-hardware-anatomy.md) |
| 2 | Radio & Physical Layer | [weeks/02](weeks/02-radio-and-physical-layer.md) |
| 3 | Energy | [weeks/03](weeks/03-energy.md) |
| 4 | MAC I: Contention-Based | [weeks/04](weeks/04-mac-i-contention-based.md) |
| 5 | MAC II: Scheduled & Hybrid | [weeks/05](weeks/05-mac-ii-scheduled-and-hybrid.md) |
| 6 | Naming, Addressing & 6LoWPAN | [weeks/06](weeks/06-naming-addressing-and-6lowpan.md) |
| 7 | Routing I: Collection & RPL | [weeks/07](weeks/07-routing-i-collection-and-rpl.md) |
| 8 | Routing II: Robustness & Mobility | [weeks/08](weeks/08-routing-ii-robustness-and-mobility.md) |
| 9 | Transport & Reliability | [weeks/09](weeks/09-transport-and-reliability.md) |
| 10 | Time Synchronization | [weeks/10](weeks/10-time-synchronization.md) |
| 11 | Security Foundations | [weeks/11](weeks/11-security-foundations.md) |
| 12 | Secure WSN Protocols | [weeks/12](weeks/12-secure-wsn-protocols.md) |
| 13 | Data Processing & Edge Intelligence | [weeks/13](weeks/13-data-processing-and-edge-intelligence.md) |
| 14 | Integration & Project Demo | [weeks/14](weeks/14-integration-and-project-demo.md) |

Workflow every week: **theory → simulation → evidence** — name the mechanism, build a
controlled case in Cooja, measure logs/packets/timing, explain what changed and why.

## Lab environment

**Instant Contiki 3.0** VM in **VMware**, simulator **Cooja**, reference mote **Sky**.
Old Contiki 3.0 commands and paths are the source of truth for labs (not Contiki-NG).
Details in [course-info.md](course-info.md#lab-environment).

## Layout

```
README.md        This page
course-info.md   Syllabus, lab environment, glossary
weeks/NN-*.md    One file per week: the shared plan on top, everyone's notes below
exams/           Midterm and final: papers, patterns, preparation
resources/       Slides and handouts; resources/<term>/ for what the instructor issued that term
```

## Taking notes

Open the week, scroll to the bottom, write under your own heading:

```markdown
## Notes — <Name> (<term>)
### Lecture
### Lab
### Questions
### Exam-worthy
```

Add your heading below the existing ones and never edit someone else's section —
different sections merge in git without conflicts.

## Who changes what

| What | Who edits it | When |
|------|-------------|------|
| Top of `weeks/NN-*.md` (goals, reading, lab) | **anyone** | When the course itself changes — new slides, a better reading, a correction. |
| `## Notes — <you>` in a week file | **only you** | Every week. |
| `course-info.md`, `exams/README.md` | **anyone** | When you learn something durable: an exam pattern, a lab trick. |
| `assignments/<term>-<you>/` | **only you** | Your lab videos' notes, Cooja simulations (`.csc`), code. |
| `resources/<term>/` | **anyone in that term** | Slides and handouts the instructor shared on Teams that term. |
| `exams/` | **anyone** | When you get hold of a paper — blank or answered. Put the writer's surname in the filename. |

Two students in different years never touch the same file except to improve the
shared plan — which is the point.

## Terms

| Term | Instructor | Schedule | Midterm | Final | Notes |
|------|-----------|----------|---------|-------|-------|
| Fall 2026-2027 | Manolya Atalay | Mon theory · Thu practice | TBD | TBD | Efe — in every week file |
