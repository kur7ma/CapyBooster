# Security Policy

[Русский ниже](#по-русски)

## What this project does to the system

CapyBooster is a set of PowerShell scripts that change Windows settings: the
registry, the power plan, scheduled tasks, device interrupt policy. It runs with
administrator rights. It is not a background program, a service or anything
resident: a script does its work and exits.

Every change is backed up before it is written, every module that
changes settings has its own undo step, and a system restore point is created before a run.

## What goes over the network

Two things only, and both are visible in the code:

* the update check: downloading `version.json`, the changelog and the archive
  from the address written in `version.json`;
* downloading third-party tools when you ask for it in the "Tools" step:
  PresentMon, GoInterruptPolicy and (on NVIDIA only) NVIDIA Profile Inspector.
  Versions are pinned, and the SHA256 of each file is checked.

The account name, the computer name, Steam account numbers and the
organisation domain name are removed from the diagnostics archive. Paths stay
readable: `C:\Users\user\...` can still be followed, but it no longer says who
you are.

There is no telemetry. The diagnostics archive is created locally and sent only
by you, by hand.

## How to report a security problem

If you found something that lets someone gain privileges, run someone else's
code or damage the system in a way the pack does not describe, **do not open a
public issue**. Write through
[Security Advisories](https://github.com/kur7ma/CapyBooster/security/advisories/new).

Ordinary bugs are not covered by this: they belong in an issue, where everyone
can see them.

## What is not a vulnerability

* The pack changes Windows settings. That is its purpose, and the report
  describes every change along with what it costs.
* The `11-vbs-hvci` module deliberately turns off virtualization-based
  security. It is marked high risk, asked about separately and explained in
  detail: a deliberate trade, not a defect.
* The scripts need administrator rights. Without them they cannot do what they
  are written for.

---

## По-русски

### Что этот проект делает с системой

CapyBooster — набор скриптов PowerShell, которые меняют настройки Windows:
реестр, схему электропитания, задачи планировщика, политику прерываний
устройств. Он запускается с правами администратора. Это не фон, не служба и не
резидентная программа: скрипт отработал и завершился.

Каждое изменение сохраняется в резервную копию до записи, у каждого модуля, который меняет настройки,
есть свой шаг отката, а перед прогоном создаётся точка восстановления системы.

### Что уходит в сеть

Ничего, кроме двух вещей, и обе видны в коде:

* проверка обновлений — скачивание `version.json`, журнала изменений и архива
  с адреса, записанного в `version.json`;
* загрузка сторонних инструментов по явному запросу шага «Инструменты»:
  PresentMon, GoInterruptPolicy и (только на NVIDIA) NVIDIA Profile Inspector.
  Версии закреплены, SHA256 каждого файла сверяется.

Из архива диагностики убираются имя учётной записи, имя компьютера, номера
аккаунтов Steam и название домена организации. Пути при этом остаются
читаемыми: `C:\Users\user\...` разбирать можно, а кто это — уже нет.

Телеметрии нет. Архив диагностики создаётся локально и отправляется только
вами и вручную.

### Как сообщить о проблеме безопасности

Если вы нашли что-то, что позволяет повысить привилегии, выполнить чужой код
или испортить систему способом, который пак не описывает, — **не открывайте
публичный issue**. Напишите через
[Security Advisories](https://github.com/kur7ma/CapyBooster/security/advisories/new).

На обычные баги это не распространяется: их лучше в issue, там их видят все.

### Что не считается уязвимостью

* Пак меняет настройки Windows — в этом его назначение, и каждое изменение
  описано в отчёте с указанием, чем за него платят.
* Модуль `11-vbs-hvci` намеренно отключает защиту на основе виртуализации.
  Это помечено высоким риском, спрашивается отдельно и подробно объясняется:
  осознанный размен, а не дефект.
* Скрипты требуют прав администратора. Без них они не могут сделать то, ради
  чего написаны.
