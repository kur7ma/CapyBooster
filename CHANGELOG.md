# Changelog

Only what shows from the outside. Technical detail lives in the code and in the
module READMEs.

Format: each section starts with a version and a date, each change with a bold
heading. The updater shows those headings before it installs anything.

---

## 5.5.1 - 8 September 2026

**Diagnostics archives no longer arrive unreadable.** On machines where the
regional format and the language for non-Unicode programs are set differently,
the fresh system check inside the archive turned into a mess of symbols. Found
in an archive someone sent in, where exactly that happened. The pack reads the
answers of system utilities the same way, so on such machines some checks could
quietly answer wrong - fixed together.

**Anonymising no longer eats hardware names.** The account name is replaced
throughout the archive as a whole word. When it matched part of the processor
or graphics card name, the model vanished from the archive and the word "user"
stood in its place - in four places at once, the hardware card and the report
included. That is precisely the diagnostic information the archive is collected
for. Processor and graphics card names are now protected from the replacement;
in paths and in the rest of the text the name is masked as before.

**"Run not finished" no longer alarms for nothing.** The archive is collected by
the wizard's last step, and the wizard marks the run finished only after it.
Because of that, everyone whose run went through without a single error saw a
warning telling them to resume. The pack now tells a run that is in progress
right now from an abandoned one: it says so in the first case, and keeps the
warning in the second.

**Windows Server is no longer named twice.** The version line printed the build
number twice in a row.

---

## 5.5.0 - 6 September 2026

**New step: optimizations for windowed games.** Windows 11 has a stock switch
that speeds up how the picture reaches the screen in games running in a window
or borderless window: the frame gets there without an extra intermediate step.
It shows up as responsiveness to the mouse, not as average FPS, and does not
concern exclusive fullscreen at all.

The step is not there to improve anything: the switch is on out of the box, and
on an untouched machine the step says exactly that and writes nothing. It is
there to notice that the switch was turned OFF. Third-party "optimizers" do
that along with disabling desktop effects; people are not told, and by then the
lost responsiveness can no longer be traced back to someone else's script.

If the switch was off, the step turns it back on and leaves the neighbouring
graphics settings alone: the default graphics card, Auto HDR and the variable
refresh rate optimization stay as they are - those are a matter of the person
and their monitor. Per-game settings are left alone too.

Undo restores exactly what was there before the step, not "on": the person may
have turned the switch off themselves, and it is their choice that has to come
back. The system check now reports this step, and on Windows 10 it plainly says
the setting does not exist there.

---

## 5.4.2 - 6 September 2026

**An English run is now English all the way through.** The restore and
diagnostics scripts printed their questions and key hints in Russian only, even
with English chosen in the wizard: people got Russian text in the middle of an
English run, precisely where an answer was expected. More than a hundred and
fifty strings were moved; verified by running in both languages.

The same applied to the verdict printed after each measurement, the description
of a changed processor topology, and the confirmations in automatic mode - those
stayed Russian too.

Rare error messages in those scripts are still Russian: they are the branches
reached when the restore itself fails, and translating them in haste is more
dangerous than leaving them.

---

## 5.4.1 - 6 September 2026

**The system check no longer calls the pack's own work foreign.** The game
launch wrapper and the resume-after-reboot task were reported as traces of some
other tool, and the check offered to remove them. The cause was in how paths
were compared, not in the traces themselves.

**Restore now deletes values whose name contains square brackets.** Such names
appear when the path to a game contains brackets. The deletion used to silently
do nothing while the log and the counter reported success.

**Restore stopped wiping other tools' interrupt settings.** The rule by which
the pack recognises its own traces was too broad: the same entries are written
by the inbox drivers of some network and storage controllers. Only what the
pack actually writes now counts as its own.

**A backup is not lost when the pack folder is read-only.** A failed first write
used to abort the whole save, leaving no copy anywhere although the system had
already been changed. The two attempts are now independent. Reading backups also
looks into the previous machine-wide store, not only the current one.

**The wizard does not die on machines with restricted compilation.** Keeping the
machine awake during a run could abort everything before the first tweak - on
machines where security software or policy forbids compiling on the fly.

**System utility output is read correctly.** On both Russian and non-Russian
systems the text came back in the wrong encoding, which made some checks answer
quietly wrong. Verified by measurement on the build machine.

---

## 5.4.0 - 6 September 2026

**Restore brings back what was there.** In five modules the restore did not
return the original state: some values were lost, some settings that belonged to
other tools were deleted instead of restored. The success message was printed
either way, so there was no way to notice. A full restore now also removes the
pack's power plan - it used to stay active.

**The MMCSS module no longer stops on a foreign value.** If the setting was put
there by another tweaker rather than by the pack, the module ended with an error
before it even said what it would cost.

**The report.** The headline accounts for regressions found. The document
language follows the chosen one instead of always being Russian. The computer
name no longer reaches the report. The reason a step was skipped is stated more
precisely: the report could previously offer a command that was certain to
refuse.

**Launch switches survive a reboot.** A run started in automatic mode used to
stop and ask a question after the reboot. The mode is now kept.

**The update outcome is checked.** The wizard no longer reports a successful
update when nothing was installed, and does not restart for nothing.

**Three new settings, all behind an explicit switch.** Nothing new happens by
default. What each of them changes and what it costs is in the README of the
module concerned and on the module card inside the report.

**The system check gained nine lines.** Among them a warning about the input
service that third-party "optimizers" switch off: the pack does not touch it,
but now shows it and suggests how to bring it back.

---

## 5.3.1 - 6 September 2026

**A full restore now removes the pack's power plan.** The plan is found by name,
and the pattern still carried the old name, "WinOpt". Module 04 has been naming
it "CapyBooster - Gaming" since 4.3.0, so there were no matches at all and the
plan never reached the restore list: people ran a full restore and kept the
pack's active power plan forever, without a single message. Checked on the build
machine: the old pattern finds zero plans, the new one finds exactly one.

**The MMCSS module no longer crashes on a foreign SystemResponsiveness value.**
The parameter declares a set of allowed values, and PowerShell re-validates that
set on every assignment to the variable, not only when arguments are parsed. The
module read the value from the registry and assigned it back into that same
parameter - if someone's registry held a value outside the set (1, 7 and
0xFFFFFFFF turn up in third-party guides), the module died before it even
printed what it would cost. The value read from the system now lives in a
separate variable.

**The GPU interrupt core search takes the median, not the best run.** With an
even number of passes the old line took the upper of two values. The default is
two passes, so each core was judged by its best result, and a core with one good
and one bad run beat a steady one.

**The leftover check no longer reports unreadable as fine.** `bcdedit` cannot
open the BCD store without administrator rights, and its exit code was not
checked: the timer names were not found in the refusal text, so the section
answered "BCD timers are fine". The `-Check` mode, which the README suggests
running from an ordinary console, gave a false green on any machine. It now says
what happened: could not read, rights required.

**The memory cleanup task runs under SYSTEM again.** Module 17 read an
environment variable through a library function, while the library is imported
twenty lines further down - in a fresh process that call failed. The
`CapyBooster-PurgeStandby` task starts exactly that way: a new process with the
library not yet loaded. The variable is now read directly, under both names.

---

## 5.3.0 - 6 September 2026

**Launch switches survive a reboot.** A run started as `-Auto` used to stop
after the reboot on the "step by step or all at once" question: the mode never
reached the second half of the setup. All switches now carry over, including the
language choice and the services preset.

**The wizard no longer reports an update that did not happen.** An update can
fail for several reasons - no access to the source, the source answering with
something else, the archive failing to download. The wizard printed "The pack
has been updated. Restarting the wizard…" regardless, so people got a success
report and the same version after the restart. The outcome is now checked: if
the update did not install, it says so and the run continues on the current
version.

**The "checking the source" line is visible again.** It was added in 5.1.1 so
the window would not look frozen while a network request is in flight - but in
normal mode it was not shown at all.

---

## 5.2.0 - 3 September 2026

**Core isolation works on dual-chiplet X3D CPUs (7950X3D, 9900X3D, 9950X3D).**
The module used to refuse them: Windows reports all cores as identical, so by
its data the chiplets are indistinguishable. Cache size tells them apart, and
the layout now handles that: system and background go to the chiplet without
cache, the game gets the cached chiplet in full, not a single core lost.
Processors with equal cache on both chiplets get no layout: which one is better
for the game has not been measured, and isolation would take half the game's
cores. The branch is covered by a unit test; it has not been run on such a
processor for real.

**On a dual-chiplet X3D the fork the pack used to carry as a contradiction is
now named.** AMD has its own way to split the chiplets - Windows Game Mode
together with the 3D V-Cache service. Our isolation and that do not work
together: enabling one quietly cancels the other. The system check used to
demand Game Mode while the isolation module effectively required it off. The
advice now depends on what was chosen, and Game Mode with isolation applied is
called a conflict. The module README has a table of what does not combine with
what.

**The system check tells a written assignment apart from actual execution.** It
used to read back what it had written - which is intent, not result: an
assignment onto a sleeping chiplet is accepted and reads back without a single
error even though no thread runs there. The check now runs a short probe and
looks at which cores it actually found itself on. It reports separately when a
reservation is written but not yet in force - usually until a reboot.

**The pack sees processor parking.** The check warns when the cores handed to
the system and background are asleep - background work will not move there - and
reports when a whole chiplet is asleep.

**A third-party owner of the assignment is stated as fact.** The mechanism the
pack uses to place a game on chosen cores has no owner: whoever wrote last wins,
and there are no notifications. The system check prints how many processes carry
an assignment that is not the pack's, and which. The launch wrapper compares the
game's assignment with its own every few seconds and logs it when something
overwrites it.

---

## 5.1.1 - 1 September 2026

**The wizard no longer hangs when the update source is unreachable.** If the
network does not let it through, the window could sit motionless without a
single line in the console: the update check runs before the first output. A
timeout was set, but it did not cover everything that happens before the request
itself. The wait now has a hard ceiling: however long the system takes, the
wizard waits no longer than that.

The check also stopped being silent: a line is printed before going online, and
if the source did not answer it says so, together with a hint to run
`START.cmd -NoUpdateCheck` when there is no access at all.

**The update download got timeouts.** There were none at all before: agreeing to
update while the source was unreachable left a process hanging forever. Both the
time to establish a connection and the time for the whole download are now
bounded - a connection can be established and then deliver one byte per minute.

**Arguments are no longer lost when administrator rights are requested.** This
fixes the most important door in the pack. Double-clicking a shortcut is never
elevated, and the elevated relaunch lost everything that had been passed to it:

`UNDO.cmd` → undo → not admin → relaunch with no arguments → **the wizard ran
instead of the rollback**.

Someone whose machine got worse after a run pressed "undo", approved the Windows
prompt, and got the optimizer applying tweaks again.

---

## 5.1.0 - 31 August 2026

**The pack's power plan no longer lowers the GPU power policy.**

The plan was created as a copy of the stock "Balanced" scheme. A copy inherits
every parameter of its base, and the module only set a handful explicitly - the
base decided the rest. Comparing all 32 parameters against "High Performance"
produced exactly two disagreements, and both were graphics ones:

- `AMD Power Slider -> Overlay` - 2 instead of 3;
- `Switchable Dynamic Graphics -> Global Settings` - 2 instead of 3.

Both lines are registered by the graphics driver rather than by Windows, and
they are not visible in Control Panel. The consequence: in GPU-bound games the
card could fail to reach full load while still drawing close to its full power.
In CPU-bound games it did not show up at all.

The base is now "High Performance". The minimum processor state (5%, the single
parameter the old base was chosen for) is set explicitly on top of it - "High
Performance" pins it to 100%, which works against boost on Zen 5. Both graphics
lines are written explicitly too: they survive a change of base and a stock
scheme rewritten by third-party "optimizers". The "Power plan" step is bumped to
version 2, so the wizard offers it again to anyone who has already completed a
run.

**The system check now looks at the GPU power policy.** Separately from the
module: any plan can lower it, and those lines are not visible in Control Panel.
If the policy is below maximum, the check says so plainly and hands over the
command. Where the subgroups do not exist - no AMD graphics, no switchable
adapters - the line is not shown at all.

**On AC the machine no longer sleeps or blanks the screen.** These timeouts used
to be inherited from the base; they are now set explicitly: a wizard run takes
about forty minutes and the machine should not fall asleep in the middle of it.
The cost is stated honestly: the screen will not blank on its own, so lock the
machine if you leave it unattended. The battery profile is untouched.

---

## 5.0.2 - 27 August 2026

**The wizard no longer starts twice at once.** It looked like this: after an
update the CS2 settings question was asked twice, the report opened in the
browser twice, and log lines came in pairs. The cause is a continuation of the
same START.cmd story fixed in 5.0.1: the old launcher seeked back to its byte
offset inside the new file, landed on the line that starts the wizard and
started it a second time. Two wizards ended up in one console: they shared the
keyboard (a keypress went to one or the other) and, worse, both wrote the same
run state file.

The wizard now takes a named machine-wide lock. A second instance waits twenty
seconds - enough for the wizard's own handover during an update or elevation -
and if the first one is still running, it says so plainly and exits. A wizard
killed from Task Manager does not leave the lock stuck: the next launch picks
it up and runs normally.

**The summary line no longer contradicts itself.** It printed "steps completed:
23 of 23" and directly below "NOT performed (skipped): 2 of 23". The completed
list holds both executed and deliberately skipped steps - by design, otherwise
a skipped step would be offered again on every run - but they must not count as
completed. Skipped steps are now subtracted, and the skipped list itself
survives leaving the wizard: before, it lived only in the run's memory.

**Skip the report and the browser stays shut.** The "HTML report" step can be
skipped, and the wizard used to open the newest report file in results anyway -
showing last time's numbers, without today's changes. Only a report created in
this run is opened.

---

## 5.0.1 - 27 August 2026

**Fixed a stray command after an update: a foreign line ran right after "the
wizard is restarting".** It looked like `"the" is not recognized as an internal
or external command`. The cause is how cmd.exe works: it does not read
START.cmd into memory, it remembers a byte offset and re-reads the file from
disk after every command. An update rewrites the pack in place, START.cmd
included, so when the wizard exited cmd seeked back to its old offset inside a
now-different file, landed in the middle of some other line and ran whatever it
found there.

The update itself was fine: the pack updated, the wizard relaunched, everything
worked. Only the appearance broke — people read a baffling error at the exact
moment everything had gone right.

The wizard launch and the undo launch are now each a single line ending in
`exit`: cmd parses the whole line before running it and then terminates without
ever seeking back into the file. The trap had always been there; it only showed
once START.cmd changed noticeably between versions.

---

## 5.0.0 - 27 August 2026

**Why a major number.** The pack is renamed on the inside, not just on the
cover: the main script, the library and the run log all have new names.
Everything that recognises traces of older versions on your machine is left
where it was, and updating loses nothing — but this is the first release that
changes file names, and calling it a new number is the honest thing to do.

**A full undo is now one click away.** UNDO.cmd sits next to START.cmd and
returns the system to the state before the pack was ever run. `START.cmd undo`
does the same, and `START.cmd undo -Preview` shows the plan first without
touching anything. This existed before but lived as a script in a subfolder,
which put it out of reach of exactly the person who needs it: someone whose
machine just got worse and who is not going to go reading documentation at
that moment.

**What exactly was renamed.** The main script is capybooster. The main script and the run log were renamed to match the current pack name.log; the restore point, the scheduled task
and the measurement window are renamed too. The old name stays where it
recognises traces of older versions: task names, the machine-wide store, the
marker in the CS2 config. WINOPT_ variables you set by hand keep working — the
pack reads both names.

**Updating from an older version loses nothing.** The old main-script name is
kept as a forwarder, so the "continue after reboot" task registered by the
previous version still finds something to run.

**The GPU interrupt core search no longer runs on laptops.** The measurement
there would be reading the wrong card: the load window does not choose a
graphics card — Windows does, and on a laptop that is usually the integrated
one, while the binding would land on the discrete card. On top of that the
module restarts the display driver for every core it tries, and in a hybrid
setup the card being restarted is not the one driving the picture. The system
check now says so instead of offering to run the search.

**The system check now looks at thread scheduling.** On a processor with two
classes of cores, and on an X3D part with two chiplets, where the threads land
matters more than every tweak in the pack put together. The pack now names the
problem out loud: the cache preference service stopped, Game Mode off on a
dual-chiplet X3D, Intel dynamic tuning stopped. It turns none of them on
itself — it only says what is off and what that costs.

**A contradiction inside the pack's own report is resolved.** The pack said it
does not switch Game Mode on blind, which is true. But a dual-chiplet X3D needs
Game Mode: it is what parks the chiplet without cache. The refusal now carries
that caveat, and on such a machine the check asks you to turn it on yourself.

**A new laptop step: how power is split between the processor and the graphics
card.** It writes nothing. Three independent controls divide the watts — NVIDIA
Dynamic Boost, the vendor app's performance mode and the Windows power slider —
and the pack can reach none of them: they live in the driver and in the laptop's
own controller. The step explains where each one is, so you stop hunting for a
setting the system does not have.

**Three places where the pack was not doing what it was written for.** The
memory hygiene module returned the path to its own copy under the old store
folder while actually working in the new one. In the same module the "task
points into the store" detection never fired. And the full undo read the
machine-wide snapshot from the old path, so it never found it, and fell back to
an intermediate backup instead of the original state.

**The pack can tell a laptop from a desktop.** It could not before, at all. It
tells them apart by chassis type rather than by the presence of a battery: a
desktop on a UPS reports one too.

**A measurement taken on battery is no longer compared with one taken on
mains.** This is the important one. On battery the processor holds a different
frequency ceiling, and such a pair is two different machines. The pack used to
subtract one from the other in silence and produce a verdict that meant
nothing. Now that pair comes back as "not comparable", through the same
mechanism that already caught SMT being toggled between runs. Measurements
taken before this version are judged as before: a missing power mark means
"unknown", not "on mains".

**The wizard warns about running on battery** before the run starts, but does
not stop it: people unplug mid-way too.

**The power plan is honest about the battery now.** It always wrote its values
for mains power only, so on battery the scheme changed nothing. Nobody said so,
and people who ran the pack and then unplugged decided the tweaker did not
work. On a laptop this is stated plainly now.

**The system check no longer shows green on battery.** The scheme is active but
inert, and it says exactly that.

**The Adrenalin walkthrough comes back for Core Ultra owners.** Core Ultra
integrated graphics is called "Intel Arc", and the pack mistook it for a
discrete rival card: a Core Ultra laptop with a discrete Radeon was refused the
walkthrough although Radeon is what it plays on. Discrete Arc is now told from
integrated by the series number in the name.

**Sensor services were added to the never-touch list.** They were never in the
presets, but they idle on a desktop, and that is where the habit of switching
them off "as useless" comes from. On a laptop they drive auto-brightness and
auto-rotation.

**Turning off mouse acceleration on a laptop affects the touchpad too** — that
is now written in the step's cost. Windows has one acceleration setting for
every pointing device; it cannot be turned off for the mouse alone.

**Fixed an undo bug that could delete a setting the pack never made.** Registry
values named after a full file path were read with wildcard matching: a path
like "D:\Games\[RUS] Shooter\game.exe" was not found, the backup recorded
"there was no value", and the undo deleted a setting that existed before the
pack ran.

**The power check can no longer bring the wizard down.** On a machine where
Windows will not let the helper code compile — corporate policy, a blocked
compiler, an unavailable temp folder — the pre-flight died outright, and on the
battery check of all things, which nobody asked for. Such a machine now simply
says "could not be determined" and moves on.

**The pre-flight no longer prints a green "on mains" when it could not tell.**
There are three states, not two, and the third one now has its own name.

**The right reason is printed under "cannot be compared".** It used to always
say "this is a difference between two topologies" — under a pair that differed
by power source, people read two mutually exclusive reasons in a row.

**Diagnostics no longer pass "not determined" off as "no battery, on mains".**
Whoever reads someone else's archive was reading that as fact.

**The power module no longer leaves one hidden parameter exposed** after an
undo on hybrid processors.

**Fixed a drift between code and documentation.** Since version 3.9 the power
module forbids parking for both classes of cores, while the description and
both cards still promised the slow class was allowed to park.

---

**CS2 settings from a pro player's profile.** Module 15 has been rewritten and
switched back on. You pick a player from the list — ZywOo, donk, m0NESY, s1mple,
NiKo — and their sensitivity, viewmodel, crosshair, HUD and resolution are
transferred onto your machine. The sensitivity is recalculated for your own DPI
through eDPI: the same number at a different DPI would move the crosshair at a
different speed, and "settings like ZywOo's" would be a lie. The settings go in
as a block between markers, so anything you wrote in autoexec yourself stays
where it is. This moves preferences rather than optimising anything, and the
module says so plainly: a player's profile buys no frames.

**Module 15 no longer writes into Steam's config.** That is what got it switched
off: it edited CS2's launch options in localconfig.vdf and injected a game.cmd
wrapper there, which ran a cmd script on every game launch. The wrapper existed
for core isolation, which the pack has since turned off by default because it
cost frames. Launch options are now only printed for you to paste yourself.

**Convars are checked against the game's binaries.** Only what actually exists in
the current build goes into the block. That is how it turned out that CS2 has no
bob convar at all — not cl_bobcycle, not cl_usenewbob, none of them — even
though "Bob" sits in the settings tables on every site. It cannot be
transferred, and the pack does not pretend it was. The quality level scale was taken from the
video_defaults presets inside the game's own files, so shadows, textures,
shaders, particles, anti-aliasing and Reflex are written by the module itself.
HDR and brightness stay manual, for reasons recorded in the key map.

---

**New module: Control Flow Guard for a DirectX 12 game.** Windows checks every
indirect call inside a process, and the DX12 runtime and graphics drivers make a
great many of them. Turning the check off for one game removes a share of the
micro-stutters, though not for everyone, and the module says so plainly. The
step only appears when a game with a DX12 mode is found; your own can be named
explicitly. The bit positions were taken by measurement on a live system rather
than from guides: the value matches byte for byte what Windows itself writes,
and other protection settings on the same file survive.

**The report opens last, not before the archive question.** The browser used to
jump in front of the console at the exact moment the pack asked whether to
collect a diagnostics archive. People went off to read the report, and the
question stayed behind in a window they had already forgotten. Questions first
now, browser after.

---

## 4.4.8 - 26 August 2026

**The system check now names your memory speed.** The pack collected the number
into its hardware snapshot and said nothing about it. A submitted run on a Ryzen
7 5700X3D had memory at 2666 MHz, the SPD base speed, with no XMP profile. On a
CPU with a large cache that costs more than everything the pack does put
together. The check now shows the speed every time, and warns when the profile
looks disabled. The pack cannot change it: that is a BIOS setting, and it says
so.

**The BCD timer message is more precise.** The note about useplatformtick and
disabledynamictick ended with "harmful on Zen 5", which a Zen 3 owner read as
"not about me". The mechanism applies to current CPUs generally, and Zen 5 is
where it was confirmed by measurement. The message now says exactly that.

---

## 4.4.7 - 26 August 2026

**The AMD walkthrough no longer shows up for NVIDIA owners.** Every desktop Ryzen
has integrated graphics, which Windows reports as "AMD Radeon(TM) Graphics". The
check looked for the word Radeon in the list and found it on every Ryzen,
including machines that game on a GeForce. From a submitted run: a 9800X3D with
an RTX 5070 Ti got twenty lines about Adrenalin. The module now asks not "is
there a Radeon" but "do you game on the Radeon": if a discrete card from another
vendor sits next to it, that is what you play on. A build with only integrated
graphics still counts as gaming on Radeon, where the settings fully apply.

**The module stops when Adrenalin is not installed.** In the same run it said
"AMD Software was not found on this machine" and then printed every setting you
have no way to change. It now names the reason, says where to get the app and
stops; the list stays in the module README.

---

## 4.4.6 - 26 August 2026

**Key auto-repeat.** The Windows 11 tweaks module gained a keyboard section: the
delay before auto-repeat and its rate. The card says plainly that this has
nothing to do with input latency in games, because a game reads the keyboard
directly while the system auto-repeat exists for typing. The section is here
because the setting is real and people ask for it, not because it produces
frames.

**Two more refusals.** Turning off performance counters: they cost CPU time only
while something polls them, and what polls them is your own monitoring, so the
gain exists exactly while Afterburner is open and is paid for with Afterburner
itself. Turning off the last-access timestamp: on NTFS it has been lazy since
Vista, while search and backup software lean on it.

---

## 4.4.5 - 26 August 2026

**The cost of a step is printed before the question, not after you agree.** The
"what it costs you" block came from the module itself, which meant it arrived
after you had already pressed run. You agreed blind, and the explanation showed
up once the decision was made. A short cost line now comes before the menu. The
detailed block inside the module stays where it was: it prints before anything
is written and before the module's own second question.

**Every module card gained a cost field.** Twenty-four cards, one sentence each.
A quality gate refuses to build a release if a new module lacks the field:
without it the wizard says nothing, and people agree without knowing the price.

---

## 4.4.4 - 26 August 2026

**The diagnostics archive is anonymised.** An audit of a real collected archive
found things that should not have been in it. The computer name sat in ten
places and in the file name itself, so seeing it took no opening at all. The log
held `steam\userdata\<number>` paths, which are Steam account numbers that lead
straight to a profile; this machine had three. All of that is masked now, along
with the organisation domain name.

**The anonymising tool wrote the name into the log itself.** It printed "the
name Ivan matches a service word", and the log goes into the next archive. It
now prints only a count, and old lines of that shape are scrubbed during
collection.

**The archive name no longer carries the computer name.** The file is called
`capybooster-diag-<date>-<time>.zip`, and the timestamp is what tells archives
from different machines apart.

**The capybara is gone from the console.** Drawn in characters it looked worse
than nothing. The mark stays in the HTML report header and on the project page,
where it renders properly.

---

## 4.4.3 - 26 August 2026

**The project has a mark.** A capybara in the HTML report header, on the project
page and in the launcher window. In the report the mark is embedded inside the
file rather than linked: people forward the report, and it has to open without a
network. It still loads zero external resources.

---

## 4.4.2 - 26 August 2026

**Fixed a crash on the first run.** The wizard asks for a language at the very
start, while the function that reads the answer was declared six hundred lines
below. PowerShell creates a function only once execution reaches its
declaration, so the first run stopped with "Read-MenuChoice is not recognized".
It hit everyone starting the pack for the first time, and only them: anyone who
had already chosen a language never sees the question, so the path never ran.
The menu-reading functions moved into the library, where line order does not
matter.

**A quality gate now catches this class of bug.** The twelfth check parses every
script and fails if a function is called above its own declaration at the top
level of a file.

**The project name in the launcher window.** START.cmd printed the previous
technical name in the window title and the banner. A capybara moved in there
too.

**Cleanup of files under previous names moved into the wizard.** It used to live
in the updater, but an update is performed by the version already installed:
going from 4.4.0 to 4.4.1 ran the 4.4.0 updater, which knew nothing about the
cleanup. The wizard now does it on every start.

---

## 4.4.1 - 26 August 2026

**The archive carries the project name.** On the release page it is now
`CapyBooster-latest.zip`, and the folder inside is `CapyBooster-v4.4.1`. The old
name `win-optimization-latest.zip` stays on the source and will stay for a long
time: the archive name is hard-coded in the updater, and whoever updates once
every couple of months has the old one written in. Removing it now would break
updates for exactly the people who update least often.

**Files under previous names are cleaned up on update.** An update still deletes
nothing on its own, but the three files we renamed are now removed from an
explicit list. Otherwise the folder would hold two similar scripts and you would
not know which one is live.

---

## 4.4.0 - 26 August 2026

**Profiles for popular games.** A `games` folder now holds profiles for
Counter-Strike 2, Valorant, Apex Legends and Delta Force. The pack finds what is
installed, reads the real executable name off disk, and sets high priority only
for that name. A key written under a guessed name would look applied and do
nothing. The report gained a section for the games it found: what to change
inside the game, what to close before playing, which launch options are useless.

**A warning about the anti-cheat conflict.** Vanguard's on-demand mode needs
memory integrity. If Valorant is installed, the VBS/HVCI step says so before you
turn it off, and the system check reports the conflict if you already did.
Before this, people found out after a reboot, from a game that would not start.

**A background recording module.** It turns off Xbox Game Bar recording: three
registry values, because one is not enough. This is the only step in the pack
that asks even in no-questions mode, because many people keep instant replay on
purpose, and switching it off silently takes away something they use. NVIDIA,
AMD and Intel keep replay in their own apps, so the pack shows those along with
the path to the setting instead of writing to them.

**A walkthrough for AMD graphics.** Radeon owners got nothing from the pack on
the graphics side, because module 13 works only with NVIDIA. A new module walks
through the Adrenalin settings for competitive play. It writes nothing and
cannot: no tool edits Radeon profiles from the command line, and ADLX is a
library for developers.

**Two checks before a run.** A pending reboot means system servicing has not
finished, so the measurement will land on "indistinguishable" because of the
installer working in the background. Central management means group policy will
overwrite some values after us. Both checks warn you and name the affected
modules; neither blocks the run.

**Technical identifiers renamed.** Scheduled tasks, the ProgramData folder and
the power plan now carry the CapyBooster name. The pack recognises the previous
names too, so skipping this release changes nothing for you. The move is a
separate confirmed step that appears only if you have something to move: files
are copied and checked first, and the old copy goes after the check passes.

**The stale-path check does its job now.** It was written in the previous
release but, through an oversight, executed only when the backup migration
failed, which is to say almost never. It runs on every start.

**The task repair command is correct now.** Any orphaned task got the module 02
command. Whoever owned the memory-cleanup task was told to run an unrelated
script that does not touch it.

**The change list shows up before an update again.** The changelog was requested
under a Cyrillic file name, and GitHub renames release assets with those. People
saw "an update is available" and not one line about what was in it.

**The refusal list grew.** The "what the pack deliberately does not do" section
now holds 20 entries: removing system AppX packages, `dism /ResetBase`, bulk
registry permission changes, turning off Volume Shadow Copy, TCP parameters such
as Nagle, visual effects, turning on HAGS, and disabling fullscreen
optimizations.

---

## 4.3.0 - 26 August 2026

**The pack is called CapyBooster and moved to GitHub.** The update address
changed itself and needed nothing from you. The old address keeps serving this
version, so updating later still lands you in the right place.

**A public face.** README and FAQ in two languages, contribution rules, a
security policy, an MIT license, and a Results category in Discussions for other
people's measurements.

**Two more services on the never-touch list.** VSS and swprv joined the hard
allowlist: the restore point the pack creates before a run stands on Volume
Shadow Copy.

**Version numbers removed from names.** The power plan, the memory-cleanup task
description and the autoexec.cfg header no longer carry a version number, which
went stale at the first update. The current number is printed in the banner and
in the report.

---

## Earlier

Versions before 4.3.0 shipped from a different repository. What survived and
shaped the current defaults:

**Core isolation is off by default.** Reserving cores only removes them from the
pool Windows hands out; it cannot put your game there. On one machine PUBG
dropped from roughly 400 FPS to roughly 300.

**The measurement gained a noise floor.** On a very quiet machine the spread came
out at exactly zero, and 0.3 microseconds was declared a regression. There is a
floor of one microsecond now, and three runs instead of two.

**English translation.** The wizard, the system check, the report and every
tweak card.

**Quality gates.** Eleven mechanical checks that stop a release build.
