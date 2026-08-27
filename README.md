<p align="center">
  <img src="assets/logo.png" width="620"
       alt="CapyBooster: a Windows tuner that measures what it changed and says when it did not">
</p>

<p align="center">
  <a href="README.ru.md">Русский</a> ·
  <a href="FAQ.md">FAQ</a> ·
  <a href="CHANGELOG.md">Changelog</a> ·
  <a href="https://github.com/kur7ma/CapyBooster/releases/latest/download/CapyBooster-latest.zip"><b>Download</b></a>
</p>

Optimization packs usually ship a list of registry edits and a promise.
CapyBooster runs a short measurement after every module, compares it against the
previous one, and prints the verdict even when the verdict is "the difference is
smaller than our own noise".

It backs up every value before touching it, and one command puts any module back.
Each of the 118 tweak cards names what you pay for the tweak, not only what you
get.

---

## Quick start

Download the [archive](https://github.com/kur7ma/CapyBooster/releases/latest/download/CapyBooster-latest.zip),
unpack it **whole** into any folder, and run **START.cmd**.

```
-- CapyBooster - Windows optimization wizard
   CapyBooster v4.4.8   built 2026-08-26   commit c07dd18

  Язык / Language

    [Enter]  English  (detected from your system)
    [r]      русский

    >
```

The wizard asks for a language, remembers the answer, and walks the whole run:
pre-flight checks, a restore point, leftovers from the earlier pack, tools, a
baseline measurement, the tweak groups with a measurement after each, and an
HTML report.

```
==> Pre-flight check:
[+]   encoding: all scripts are fine
[+]   PresentMon: installed (PresentMon-2.5.1-x64.exe)
[+]   system protection: on (a restore point will be created)
[+]   no reboot is pending
[+]   Windows is not centrally managed

  -- Preparation --
   ok  Restore point and system snapshot
   ok  Leftovers from the earlier pack: check and decide
   ok  Tools: PresentMon and the NVIDIA profiler
   ok  Baseline quick measurement (no game needed)
  -- OS --
   ok  Power plan
   ok  Services
   ok  Telemetry
   ok  Scheduled tasks
   ok  Storage
   ok  Drivers
   ok  Windows 11 tweaks
   ok  Network adapter
       VBS / HVCI: turning off virtualization-based security  (needs a reboot)
  -- CPU --
   ok  Interrupt layout  (needs a reboot)
   ok  Quick measurement after the reboot
  -- GPU --
   ok  NVIDIA profile for CS2
  -- Game --
   ok  MMCSS: multimedia priorities
       Scheduler quanta and game priority
       Background video recording
       Memory hygiene: a pre-game cleanup task
  -- Wrap-up --
       HTML report
       Diagnostics archive to send
```

Press `a` at any step to finish the rest unattended. Reboots are confirmed even
then.

---

## What makes it different

### The measurement can come back empty, and you will be told

After every module the pack measures thread lateness and compares it against the
previous run:

```
LABEL             P99, US   P99.8, US   RUN SPREAD   VS PREVIOUS
baseline            93,46      216,3     ±21,9 P99   first
after-power          21,9     146,31     ±29,8 P99   lateness went down
                                                     P99 -71,6 us, noise ±29,81
after-nvprofile     27,41     150,98       ±21 P99   indistinguishable against
                                                     the measurement's own spread
after-win11           5,7       25,1      ±0,6 P99   not comparable
                                                     SMT was toggled between them
```

A difference smaller than the spread between repeated runs earns the verdict
"indistinguishable". If the CPU topology changed between two measurements, the
pack marks them as not comparable rather than subtracting one from the other.

On a very quiet machine, where the instrument resolves nothing, the pack skips
the synthetic measurements and explains why, instead of printing a column of
zeroes and eleven verdicts of "indistinguishable".

### Every tweak card names the cost

Each card carries what the tweak affects, how confident that is (`measured`,
`likely`, `disputed`), what kind of effect to expect (`average FPS`, `lows`,
`input latency`, `not about FPS`), and the trade-off. From the VBS/HVCI module:

> Turns off virtualization-based security and memory integrity, including the
> hypervisor launch in BCD. This trades protection and virtualization for
> performance; it is not a free optimization.

### Popular tweaks get refused, with the reason

The report lists 22 techniques the pack leaves alone. A sample:

| Refused | Why |
|---|---|
| Turning off UAC | On Windows 11 it breaks every Store app, Settings, Terminal and Start menu search |
| Disabling HPET through BCD | `disabledynamictick` hurts boost on Zen 5 |
| Resident memory cleaners (ISLC) | The standby list is a file cache, not garbage |
| A global 1 ms timer | Timer resolution has been per-process since Windows 10 2004 |
| Disabling SysMain on NVMe | Debunked: no gain, and prefetching stops working |
| Deleting services with `sc delete` | Irreversible without repairing the component store |
| `dism /ResetBase` | Frees disk space and makes update rollback impossible |
| Turning off Volume Shadow Copy | The restore point the pack creates stands on it |
| Nagle, TcpAckFrequency, TCPNoDelay | Game traffic runs over UDP, where Nagle does not apply |
| Removing system AppX packages | An app you never open does not use the CPU |
| Disabling fullscreen optimizations | On Windows 11 it costs Auto HDR and variable refresh rate |
| Turning on Game Mode and HAGS | Helps on some driver and game pairings, hurts on others |

### Your games, found and named

Profiles in `games/` cover Counter-Strike 2, Valorant, Apex Legends and Delta
Force. The pack finds what you have installed, reads the real executable name off
disk, and writes the high-priority key only for that name. A key written under a
guessed name would look applied and do nothing.

Profiles carry anti-cheat facts too, and the pack acts on them. Vanguard's
on-demand mode needs memory integrity, so the VBS/HVCI step warns you before you
turn it off if Valorant is installed, and the system check reports the conflict
if you already did.

### Nothing is written without a backup

Every module writes a backup before it changes anything, into the pack folder
**and** into a machine-wide store, so deleting the pack folder keeps your undo
path intact. Each module has its own `revert.ps1`, and the run starts with a
system restore point.

**One click undoes everything.** `UNDO.cmd` sits next to `START.cmd` and returns
the system to the state before the pack was ever run — not merely to the state
before the last apply, which is a different thing once a module has been run
twice.

```
UNDO.cmd
START.cmd undo             the same thing
START.cmd undo -Preview    print the plan and change nothing
```

### It checks its own work afterwards

```
-- System state check

  Core map
    core 0  (LP 0,1  ) - shared
    core 3  (LP 6,7  ) - shared + USB interrupts
    core 7  (LP 14,15) - shared + network interrupts

  01 Core isolation
    [--]   ReservedCpuSets       not performed: all cores are of one class
           -> the system would have to be given real cores; instead module 02
              spreads the background work, and the game keeps every core

  Summary - OK: 32   OFF: 7   WARN: 4
```

`verify.ps1` reads the real registry rather than the pack's own log, so it
catches changes made by hand, by a second copy of the pack, or by a driver update
that wiped a setting.

---

## What it changes

| Module | What it is | Area | Risk |
|---|---|---|---|
| `00-baseline` | Restore point, registry export, earlier-pack leftover scan | Baseline | low |
| `01-cpu-isolation` | Core isolation, **off by default**, see [FAQ](FAQ.md#why-is-core-isolation-off-by-default) | CPU | medium |
| `02-interrupts` | Interrupt layout across cores | CPU | medium |
| `03-gpu-affinity-bench` | Picking a core for GPU interrupts, by measurement | CPU | medium |
| `04-power-plan` | Power plan | CPU | low |
| `05-services` | Services, startup type only, safe list | OS | medium |
| `06-telemetry` | Telemetry and advertising suggestions | OS | low |
| `07-scheduled-tasks` | Scheduled tasks | OS | low |
| `08-win11-tweaks` | Windows 11 tweaks, mouse acceleration off | OS | low |
| `09-network-nic` | Network adapter, RSS queues | Network | medium |
| `10-storage` | TRIM and automatic maintenance | Storage | low |
| `11-vbs-hvci` | VBS / HVCI | OS | **high** |
| `12-drivers` | Automatic driver installation | Drivers | low |
| `13-nvidia-profile` | NVIDIA Profile Inspector, CS2 profile | GPU | low |
| `14-mmcss` | MMCSS priorities for the Games task | CPU | low |
| `15-cs2` | CS2 settings from a pro player's profile | Game | low |
| `16-cs2-bench` | Automated measurement in CS2 | Game | low |
| `17-memory-hygiene` | One-time standby list purge, on demand | Memory | low |
| `18-autobench` | Automatic responsiveness measurement | Baseline | low |
| `19-priority-separation` | Scheduler quanta, high priority for your games | CPU | low |
| `20-recording` | Xbox Game Bar background recording | GPU | low |
| `21-amd-profile` | Radeon settings walkthrough for competitive play | GPU | low |
| `22-cfg-dx12` | Control Flow Guard for a DirectX 12 game | Game | medium |
| `23-power-share` | Power split between CPU and GPU on a laptop | GPU | low |
| `95-report` | HTML report | Baseline | low |
| `exp-timer` | Experiment: is TimerTool needed on Windows 11 | Experiment | low |

---

## Requirements

* Windows 11, build 26100 (24H2) or newer. It runs on Windows 10, but that is not
  what it is tuned against.
* Administrator rights.
* Windows PowerShell 5.1, the one that ships with Windows. Nothing to install.
* No external modules, no `wmic`, no VBScript. A quality gate refuses to build a
  release if any of those come back, see
  [FAQ](FAQ.md#what-are-the-quality-gates).

---

## Language

The wizard asks on the first run, offers a default detected from your Windows
interface language, and remembers the answer. To change it later:

```
START.cmd -Lang en
```

`ru` and `en` are supported. The wizard, the system check, the HTML report and
all 118 tweak cards are translated. Verbose output inside individual modules
stays Russian; it is hidden by default and shown on request.

---

## Updating

The wizard checks for updates on start and offers to install them, showing what
changed first. Measurements, backups and run progress stay where they are.

```
powershell -NoProfile -ExecutionPolicy Bypass -File ".\tools\Update-Pack.ps1"
```

Check without installing:

```
powershell -NoProfile -ExecutionPolicy Bypass -File ".\tools\Update-Pack.ps1" -Check
```

Updates rewrite files **in place**. If you unpack a new archive into a *new*
folder instead, scheduled tasks created by the old copy keep pointing at the old
path. The pack detects that and tells you, see
[FAQ](FAQ.md#i-unpacked-a-new-version-into-a-new-folder-what-now).

---

## This repository

| File | What it is |
|---|---|
| [Releases](https://github.com/kur7ma/CapyBooster/releases/latest) | the pack in one archive, plus `version.json` |
| [CHANGELOG.md](CHANGELOG.md) · [ИЗМЕНЕНИЯ.md](ИЗМЕНЕНИЯ.md) | what changed, English and Russian |
| [FAQ.md](FAQ.md) · [FAQ.ru.md](FAQ.ru.md) | questions people asked |
| [CONTRIBUTING.md](CONTRIBUTING.md) | what this project accepts, and what it does not |
| [SECURITY.md](SECURITY.md) | what the pack does to your system, and what leaves your machine |

**Bring your measurements** to
[Discussions → Results](https://github.com/kur7ma/CapyBooster/discussions/categories/results).
Reports from other people's machines produced most of this changelog, including
the entries where the pack changed its own defaults.

The repository stays readable without authentication: the updater fetches
anonymously and says so plainly rather than parsing a login page.

---

## How it is maintained

The changelog records the bugs this pack shipped, including the ones that made
things worse. Two of them:

**Core isolation used to be on by default, and it cost frames.** Reserving cores
only *removes* them from the pool Windows hands out; it cannot put your game
there. On one machine PUBG dropped from roughly 400 FPS to roughly 300. The step
is off by default now, and isolation that is already applied shows up as a
finding with an undo command.

**The measurement once called 0.3 microseconds a regression.** On a very quiet
machine both runs returned identical numbers, the spread came out at exactly
zero, and any margin multiplied by zero is zero. There is a noise floor of one
microsecond now, and three runs instead of two.

If something here makes your machine worse, report it. `START.cmd` collects a
diagnostics archive with your account name masked out.
