# FAQ

[Русский](FAQ.ru.md) · [README](README.md) · [Changelog](CHANGELOG.md)

Every question here came up for real: from a field report, a log someone sent,
or a friend complaining that something got worse. Nothing is invented to fill a
page.

---

## Getting started

### Do I need to install anything first?

No. Windows PowerShell 5.1 ships with Windows and is all this pack uses. There
are no external modules, no Python, no .NET to install. Two optional tools
(PresentMon for frame times, NVIDIA Profile Inspector for the GPU profile) are
downloaded by a dedicated step that shows you what it fetches and from where,
and asks first.

### Which scripts do I run, and in what order?

Only `START.cmd`. It is the single entry point. It checks encodings, rights, the
Windows version, system protection and the presence of PresentMon, then runs the
whole sequence itself and opens the report at the end.

Everything else in the pack can be run separately, but nothing has to be.

### Is it safe to run on the machine I work on?

The pack creates a system restore point, exports the registry hives it touches,
and writes a per-module backup before every change. The backup goes into the
pack folder and into a machine-wide store, so deleting the pack folder leaves
your undo path intact.

One module is marked high risk and is worth reading before you accept it:
`11-vbs-hvci`. It trades hypervisor-level memory protection for a few percent in
games, breaks Hyper-V, WSL and virtual machines, and Vanguard in Valorant needs
memory integrity for its on-demand mode. If Valorant is installed, the step
warns you before you accept it.

### How long does a run take?

Roughly seven to twelve minutes, depending on how much your machine needs
changing. Two steps are longer and optional: picking a core for GPU interrupts
(about a quarter of an hour, screen blinking, no participation needed) and an
in-game FPS measurement in CS2 (about five minutes).

---

## Results and measurements

### The report says "indistinguishable" for almost everything. Is it broken?

No, the pack is working, and this is the most common thing people misread.

Every measurement is repeated three times. The spread between those repeats is
the measurement's own noise. If the difference between two measurements is
smaller than that spread, the difference cannot be attributed to the tweak, and
saying otherwise would be a guess presented as a result.

On a quiet machine most system tweaks do not move thread lateness at all. That
is information: those tweaks are not what limits your frames.

### Why did it skip the measurements entirely on my machine?

Calibration found that the instrument resolves nothing here. It walks the
wake-up period down to the shortest one it supports, and if both P99 and the
P99.8 tail come back zero at every step, the machine is quieter than the
measurement can see.

Before this was added, such machines got twelve measurements per run, all
returning zeros, and eleven verdicts of "indistinguishable". That cost about
four and a half minutes out of an eleven-minute run and produced no information.

On such a machine, judge tweaks by measuring in the game itself.

### Two measurements say "not comparable". Why?

The CPU topology changed between them, because SMT was turned on or off in the
BIOS. Both numbers are real, but the difference between them describes two
different machines rather than the effect of a tweak, so the pack refuses to
subtract one from the other.

### FPS did not go up. Was this pointless?

Look at the 1% low rather than the average. Almost everything in this pack
treats the dips, and a difference under 2% in average FPS proves nothing: that
is what the measurement itself varies by between runs.

---

## Things that went wrong for someone

### Why is core isolation off by default?

On its own it makes things worse, and it did.

`ReservedCpuSets` can do exactly one thing: remove the fast cores from the pool
Windows hands out by default. It cannot put your game there. Only the launch
wrapper can, and it has to be used for every game, every launch, by hand.

A game started the usual way gets the default pool, which is precisely the cores
that were left to the system. On a hybrid Intel CPU that means all the E-cores,
together with every background process. On one machine PUBG went from about 400
FPS to about 300 with dips to 140, and Explorer opened folders noticeably
slower.

The step is still available, offered with an inverted default: Enter skips it,
and it takes an explicit `y` after you read what it costs. If isolation is
already applied on your machine, the system check reports it as a finding with
an undo command.

### I unpacked a new version into a new folder. What now?

Run this from the new folder:

```
powershell -NoProfile -ExecutionPolicy Bypass -File "troubleshooting\Check-StalePaths.ps1"
```

The pack writes its absolute path into scheduled tasks, into the registry, and
into Steam launch options. Updating rewrites files in place and nothing goes
stale. Unpacking into a new folder leaves all of those pointing at the old one.

The worst case is not "it stopped working", it is "the old one is still
working": the interrupt-restoring task runs at every sign-in, as SYSTEM, with a
hidden window. If the old folder still exists, that task runs the *old* script,
which rewrites the registry with the *old* layout. No error, no window, nothing
to see.

The tool changes nothing by itself. Two copies of the pack on one machine is a
legitimate setup, and silently re-registering a task onto the new copy would
break the other one. It shows the findings and prints ready-to-run fix commands.
You decide.

### I press Play in Steam, the button blinks, and the game does not start

Same cause as above, in its heaviest form. If you set up the launch wrapper and
later moved the pack folder, Steam still has the old path in the game's launch
options, and there is nothing at that path any more. The failure happens when no
pack script is running, and the Steam message says nothing about the pack.

`Check-StalePaths.ps1` reads Steam's config across all profiles and reports it.

### After signing in, a PowerShell window opens with an error

An old auto-continue task from an interrupted run. It is one-shot by design, but
only the wizard removed it, so if you rebooted, never continued the run, and
started fresh somewhere else, it stayed behind. Since v4.1.2 any run that is not
a resume removes it at the start.

### Windows feels slower, and folders open with a delay

If core isolation is applied, that is the cost of the tweak: the fast cores are
reserved for the game, so Explorer and everything else live on the slow ones.
Opening a folder is single-thread work, so it feels it.

Three ways out, in order of what the game loses:

```
# give the system one fast core back (the game loses one of eight)
powershell -NoProfile -ExecutionPolicy Bypass -File "01-cpu-isolation\apply.ps1" -SystemCores 3

# give it two
powershell -NoProfile -ExecutionPolicy Bypass -File "01-cpu-isolation\apply.ps1" -SystemCores 4

# undo isolation entirely
powershell -NoProfile -ExecutionPolicy Bypass -File "01-cpu-isolation\revert.ps1"
```

All three need a reboot. The game does **not** lose the fast cores in the third
case: on hybrid CPUs the wrapper puts it on the P-cores without any reservation.

---

## Undoing things

### How do I undo one module?

Each module folder has its own `revert.ps1`:

```
powershell -NoProfile -ExecutionPolicy Bypass -File "02-interrupts\revert.ps1"
```

The report prints the exact command next to every applied module.

### How do I undo everything?

`troubleshooting\Restore-Original.ps1` walks every module that has a backup and
offers to restore it. The system restore point created at the start of the run
is the fallback if something goes wrong beyond that.

### The report says a module is applied, but the registry disagrees

Trust the registry. There is one of it for the whole system, while the pack's
log only knows what *that copy* of the pack did. It cannot see a manual edit or
an undo performed from another copy. The report has a separate section for this
case with a command that brings the log back in line.

---

## Under the hood

### What are the quality gates?

Eleven checks that run before a release can be built. A failure stops the build:

1. **Syntax.** Every script parses.
2. **UTF-8 with BOM.** A script without it will not run correctly on a Russian
   Windows.
3. **JSON.** Every data file parses.
4. **PowerShell 5.1 only.** No `??`, `?.`, ternaries or PS7-only parameters,
   checked with the tokenizer rather than by text search.
5. **`impact.json` values.** `confidence` and `expected` must come from the
   known set, or the report would silently show a blank.
6. **Printed commands use full paths.** A relative path is useless in a console
   that opened in `system32` after a reboot.
7. **No dead Windows mechanisms.** `wmic`, `cscript`/`wscript`, `.vbs` files and
   `Get-WmiObject` cannot come back into the pack.
8. **`ru`/`en` dictionaries agree.** A missing key does not crash anything, it
   quietly shows `<step.power.why>` in the middle of a sentence.
9. **Game profile schema.** A typo in a profile field would leave a game without
   its priority key, and the module would read it as "no priority wanted".
10. **Core layout unit test.**
11. **`$Error` is clean after the system check.** `-ErrorAction SilentlyContinue`
    hides an error from the screen but still records it, and accumulated noise
    buries the real problem in someone else's diagnostics.

### Why is `wmic` mentioned everywhere if the pack does not use it?

Microsoft removed `wmic.exe` from Windows 11 24H2. Half the tweaks in the earlier
pack rested on it, so they stopped doing anything, without errors and
without any sign, for years. The pack explains that wherever the old behaviour
is relevant, and a quality gate makes sure it can never call `wmic` itself.

### Why does the pack say what it deliberately does not do?

"We did not do X" is information, and a tuner that lists only its wins is asking
to be trusted rather than checked. The report has a section for refused
techniques with a reason for each, see the table in the
[README](README.md#popular-tweaks-get-refused-with-the-reason).

### What data leaves my machine?

None, unless you send it yourself. The pack downloads two tools and checks for
its own updates; that is all the network traffic there is. The HTML report loads
no external resources and opens offline.

The diagnostics archive is created locally on your desktop, and you send it by
hand if you want to. Your account name is masked out of it.
