# Contributing

[Русский ниже](#по-русски)

## What this project accepts

One rule shapes everything else here: **a tweak gets in only if its effect can
be measured, or explained by a mechanism.** "Everyone recommends it" is not
evidence. A setting nobody can show to do anything goes into the "what the pack
deliberately does not do" list, with a reason. That is a normal outcome for a
proposal, and it says nothing about the person who made it.

## Before you open a pull request

* `tools\Test-Pack.ps1` must pass all quality gates. It runs syntax checks,
  encoding checks, PowerShell 5.1 compatibility, dictionary parity and more.
  A failing gate blocks the release build.
* Scripts are **Windows PowerShell 5.1** only. No `??`, `?.`, ternaries,
  `-AsHashtable`, `ForEach-Object -Parallel`.
* All `.ps1` and `.psm1` files are **UTF-8 with BOM**. Without the BOM they
  misbehave on a Russian Windows.
* Any module that changes the system needs a backup before the write and a
  working `revert.ps1`.
* New interface strings go into **both** `lang\ru.json` and `lang\en.json`.
  A gate checks that the key sets match exactly.

## The constraint behind every review

**People already have this pack installed.** No change may break what works for
them: lists only grow, and scheduled task names and stored paths change only
through a migration step that recognises both the old and the new form. If your
change touches any of that, say in the pull request what happens to someone who
updates from a two-month-old version.

---

## По-русски

### Что проект принимает

У пака одно правило, из которого следует всё остальное: **твик попадает внутрь,
только если эффект можно измерить или объяснить механизмом.** «Все советуют» —
не доказательство. Настройка, про которую нельзя показать, что она что-то
делает, попадает в раздел «чего пак намеренно не делает» — с объяснением. Это
нормальный исход предложения, а не отказ человеку.

### Перед тем как открыть pull request

* `tools\Test-Pack.ps1` обязан проходить все ворота качества: синтаксис,
  кодировка, совместимость с PowerShell 5.1, согласованность словарей и
  остальное. Провал ворот останавливает сборку релиза.
* Только **Windows PowerShell 5.1**. Никаких `??`, `?.`, тернарников,
  `-AsHashtable`, `ForEach-Object -Parallel`.
* Все `.ps1` и `.psm1` — **UTF-8 с BOM**. Без BOM они ведут себя неверно на
  русской Windows.
* Модуль, который меняет систему, обязан иметь бэкап до записи и рабочий
  `revert.ps1`.
* Новые строки интерфейса кладутся **и** в `lang\ru.json`, **и** в
  `lang\en.json`. Ворота проверяют, что наборы ключей совпадают ровно.

### Ограничение, из которого исходит любая проверка

**Пак уже стоит у людей.** Ни одна правка не имеет права сломать то, что у них
работает: списки только расширяются, а имена задач и записанные пути меняются
только через шаг миграции, который понимает и старую форму имени, и новую. Если
правка это затрагивает — напишите в pull request, что будет у человека, который
обновится с версии двухмесячной давности.
