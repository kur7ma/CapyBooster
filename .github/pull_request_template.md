<!--
Спасибо за вклад. Несколько вещей, которые сэкономят время обоим.
Thanks for contributing. A few things that save time for both of us.
-->

## Что меняется / What changes

<!-- Коротко и по делу. / Short and concrete. -->

## Зачем / Why

<!--
Если это новый твик — чем подтверждается эффект. Пак не принимает настройки
"потому что все советуют": нужен либо замер, либо объяснимый механизм.

If this is a new tweak: what evidence backs it. This pack does not accept
settings because "everyone recommends them".
-->

## Проверено / Verified

- [ ] `tools\Test-Pack.ps1` проходит все ворота / passes all quality gates
- [ ] Скрипты в UTF-8 с BOM / scripts are UTF-8 with BOM
- [ ] Только конструкции Windows PowerShell 5.1 (без `??`, `?.`, тернарников)
- [ ] Если добавлены строки интерфейса — они есть в `lang\ru.json` **и** `lang\en.json`
- [ ] Если модуль меняет систему — есть бэкап и `revert.ps1`

## Ничего не сломается у тех, у кого пак уже стоит? / Backward safe?

<!--
Пак стоит у людей. Списки только расширяются, имена задач и путей не меняются
без миграции. Если правка это затрагивает — опишите, что будет у человека,
который обновится с версии двухмесячной давности.
-->
