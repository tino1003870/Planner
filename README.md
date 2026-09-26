# Planner

## Cross-platform project planning with WBS and Gantt

**Planner** is a cross-platform project planning system for hierarchical task management, Work Breakdown Structure (WBS), and Gantt-style project planning.

The project originated as **TB Planner**, a Thunderbird add-on, and has evolved into a family of applications for different operating systems, architectures, and device classes.

The common idea is:

> **The project data should not depend on the platform on which the project is edited.**

Planner combines a common task model based on **iCalendar VTODO** with platform-specific user interfaces and integrations.

---

# Project family

The current Planner implementations are:

| Repository | Target | Application |
|---|---|---|
| [`tb-planner`](https://github.com/tino1003870/tb-planner) | Thunderbird | Thunderbird Add-on |
| [`tb-planner-ut`](https://github.com/tino1003870/tb-planner-ut) | Ubuntu Touch | Mobile QML/Python application |
| [`tb-planner-nx`](https://github.com/tino1003870/tb-planner-nx) | Linux / ARM Linux | Standalone Qt/Python application |
| [`tb-planner-win`](https://github.com/tino1003870/tb-planner-win) | Windows | Standalone Qt/Python application |
| **Planner** | All targets | Project overview and architecture |

The standalone Linux implementation is also released for ARM-based systems, including **postmarketOS** and the **PINE64 PineTab2**.

---

# Architecture

Planner separates the common project model from the platform-specific application.

```text
                         PLANNER
                            |
                 Project / Task / WBS
                            |
                      Gantt planning
                            |
                     iCalendar VTODO
                            |
                          CalDAV
                            |
        +-------------------+-------------------+
        |                   |                   |
        v                   v                   v
   Thunderbird          Standalone            Mobile
      Add-on             clients              clients
        |                   |                   |
        |          +--------+--------+          |
        |          |                 |          |
        v          v                 v          v
 Thunderbird    Linux            Windows   Ubuntu Touch
                x86/x64           x86/x64
                   |
             +-----+------+
             |            |
             v            v
        postmarketOS   PineTab2
           ARM64         ARM64
```

The platform-specific applications provide different user interfaces and integrations, while the task information follows the same conceptual model.

---

# Common task model

Planner tasks are hierarchical.

A project can be represented for example as:

```text
1       Project
2       Sensorik
2.1     Sensor auswählen
2.2     Sensor beschaffen
2.3     Sensor installieren
2.4     Sensor testen
3       Dokumentation
```

The hierarchy determines the WBS numbering.

Tasks contain information such as:

- UID
- summary / title
- description
- status
- completion
- start date
- due date
- duration
- WBS
- parent task
- order

The parent relationship and task order allow the project hierarchy to be reconstructed when the tasks are loaded again.

---

# iCalendar / VTODO

The common data representation is based on **iCalendar VTODO** objects.

Standard VTODO properties are combined with Planner-specific properties:

```text
X-TB-PLANNER-WBS
X-TB-PLANNER-PARENT
X-TB-PLANNER-ORDER
```

A simplified task looks like:

```text
BEGIN:VCALENDAR
PRODID:-//TB Planner//EN
VERSION:2.0
BEGIN:VTODO
UID:...
DTSTAMP:...
SUMMARY:Example task
STATUS:NEEDS-ACTION
PERCENT-COMPLETE:0
DTSTART;VALUE=DATE:20260914
DUE;VALUE=DATE:20260923
X-TB-PLANNER-WBS:1.1
X-TB-PLANNER-PARENT:...
X-TB-PLANNER-ORDER:1
END:VTODO
END:VCALENDAR
```

This is an important part of the cross-platform architecture.

The task remains a standard calendar task object, while the additional Planner properties contain the information required to represent the project hierarchy.

---

# CalDAV synchronization

The standalone applications use **CalDAV** for synchronization.

Conceptually:

```text
             Planner application
                    |
              local task model
                    |
               VTODO / iCal
                    |
                  CalDAV
                    |
             Calendar server
                    |
          +---------+---------+
          |         |         |
          v         v         v
       Desktop   Mobile   Thunderbird
```

Existing tasks are identified by their UID.

The synchronization layer can create, update, and delete VTODOs and synchronize the local task model with the calendar.

This allows different Planner implementations to work with the same calendar-based project data.

---

# Target platforms

## 1. Thunderbird

### Repository

[`tb-planner`](https://github.com/tino1003870/tb-planner)

### Target

**Mozilla Thunderbird**

### Application model

Thunderbird Add-on / WebExtension.

The Thunderbird implementation is the original TB Planner application.

It integrates directly into Thunderbird and uses Thunderbird calendars as the task data store.

The main functionality includes:

- Gantt-like task presentation
- start and end dates
- task duration
- hierarchical task structure
- WBS numbering
- indenting and moving tasks
- task descriptions
- task creation and deletion
- Thunderbird calendar integration

The Thunderbird version stores tasks as VTODOs in Thunderbird calendars.

Additional Planner information such as WBS, parent task, and hierarchy order is stored with the VTODO.

---

# 2. Ubuntu Touch

### Repository

[`tb-planner-ut`](https://github.com/tino1003870/tb-planner-ut)

### Target

**Ubuntu Touch**

### Device class

Mobile Linux devices such as smartphones and tablets.

### Technology

- QML
- Python
- Qt
- CalDAV
- iCalendar/VTODO

The Ubuntu Touch version is a **standalone application** and does not depend on Thunderbird.

The application provides:

- hierarchical task lists
- WBS numbering
- parent/child relationships
- task ordering
- start dates
- task duration
- due dates
- Gantt chart
- CalDAV synchronization
- VTODO import/export
- creation, modification and deletion of VTODOs

The QML layer provides the user interface while Python contains the task model, VTODO processing and CalDAV synchronization.

---

# 3. Linux Desktop

### Repository

[`tb-planner-nx`](https://github.com/tino1003870/tb-planner-nx)

### Target

**Linux Desktop**

### Architecture

The standalone Linux application is based on:

- Python 3
- Qt 6
- PySide6
- CalDAV
- VTODO

The primary desktop architecture is x86/x64.

The application does not require Thunderbird and provides its own graphical user interface.

The Linux version combines:

- hierarchical task management
- WBS-style numbering
- Gantt-style project timeline
- start date
- due date
- duration
- CalDAV synchronization
- VTODO support

It can be distributed as an AppImage containing the required Python and Qt runtime.

---

# 4. Windows

### Repository

[`tb-planner-win`](https://github.com/tino1003870/tb-planner-win)

### Target

**Microsoft Windows**

### Architecture

The Windows application uses:

- Python 3
- Qt 6
- PySide6
- CalDAV
- VTODO

It is packaged using **PyInstaller** as a standalone Windows application.

The user therefore does not need a separate Python installation.

The Windows application provides:

- hierarchical WBS task planning
- Gantt diagram
- start date and duration
- automatic end-date calculation
- CalDAV synchronization
- VTODO support

---

# 5. ARM Linux

The standalone `tb-planner-nx` application is not limited to x86/x64 Linux systems.

ARM-based Linux targets are part of the project as well.

## postmarketOS

A release for **postmarketOS on ARM/aarch64** is available.

This extends the standalone Linux application to mobile-oriented ARM Linux systems.

The architecture is:

```text
postmarketOS
     |
   ARM64
     |
   Linux
     |
 Qt 6 / PySide6
     |
   Python
     |
tb-planner-nx
```

The postmarketOS target therefore uses the standalone Linux implementation rather than the Ubuntu Touch application.

---

# 6. PINE64 PineTab2

### Target

**PINE64 PineTab2 / ARM64**

A dedicated ARM release of `tb-planner-nx` exists for the PineTab2 environment.

The PineTab2 combines:

- ARM64 hardware
- Linux
- touchscreen
- tablet form factor
- desktop-capable Linux software

The `tb-planner-nx` repository contains a PineTab2 AppImage release.

The PineTab2 therefore demonstrates that Planner can run on ARM tablet hardware without requiring the Ubuntu Touch application stack.

This gives two different approaches to mobile/tablet Linux:

```text
Ubuntu Touch
     |
 QML / Python
     |
tb-planner-ut
```

and:

```text
Linux / PineTab2
     |
 Qt 6 / PySide6 / Python
     |
tb-planner-nx
```

---

# Mobile and ARM strategy

Planner deliberately supports more than one approach to mobile computing.

## Mobile-native approach

Ubuntu Touch has its own application:

```text
Ubuntu Touch
      |
   Lomiri
      |
     QML
      |
   Python
      |
tb-planner-ut
```

## Desktop-on-ARM approach

ARM Linux systems use the standalone desktop application:

```text
ARM64 Linux
      |
    Qt 6
      |
  PySide6
      |
   Python
      |
tb-planner-nx
```

These approaches address different Linux application environments.

---

# Platform matrix

| Target | Architecture | UI / Runtime | Data | Repository |
|---|---|---|---|---|
| Thunderbird | Host architecture | Thunderbird WebExtension | VTODO / Calendar | `tb-planner` |
| Linux Desktop | x86/x64 | Qt 6 / PySide6 / Python | VTODO / CalDAV | `tb-planner-nx` |
| Windows | x86/x64 | Qt 6 / PySide6 / Python | VTODO / CalDAV | `tb-planner-win` |
| Ubuntu Touch | ARM / mobile | QML / Python | VTODO / CalDAV | `tb-planner-ut` |
| postmarketOS | ARM64 | Qt 6 / PySide6 / Python | VTODO / CalDAV | `tb-planner-nx` |
| PineTab2 | ARM64 | Qt 6 / PySide6 / Python | VTODO / CalDAV | `tb-planner-nx` |

---

# Repository structure

The repositories have clearly separated responsibilities.

### `Planner`

The umbrella repository.

It contains the documentation and architectural overview of the complete Planner project.

### `tb-planner`

Thunderbird implementation.

### `tb-planner-ut`

Ubuntu Touch implementation.

### `tb-planner-nx`

Standalone Qt/Python implementation for Linux and ARM Linux targets.

### `tb-planner-win`

Standalone Qt/Python implementation for Windows.

---

# Future platform integration

The architecture allows additional clients to be added.

One possible future target is **Microsoft Outlook / Microsoft 365**.

A modern Outlook implementation would preferably use an **Outlook Web Add-in based on Office.js**, rather than relying exclusively on VBA.

Conceptually this could become another Planner client:

```text
                         PLANNER
                            |
                     VTODO / CalDAV
                            |
        +-------------------+-------------------+
        |                   |                   |
   Thunderbird          Outlook             Standalone
     Add-on            Office.js              Clients
                                             |
                                      +------+------+
                                      |             |
                                    Linux        Windows
```

An Outlook implementation is not currently part of the project and is therefore considered a possible future extension.

---

# Project philosophy

Planner follows several principles.

## One project model, multiple clients

The same project concepts should be usable from different applications.

## Standards where possible

The project uses iCalendar/VTODO and CalDAV as important interoperability mechanisms.

## Platform-specific user interfaces

The user interface should match the target environment instead of forcing one interface onto every device.

## Desktop and mobile

Planner is designed for conventional desktop systems as well as mobile and ARM-based Linux systems.

## Independent implementations

Each repository can evolve according to the requirements of its target platform while remaining part of the same overall Planner ecosystem.

---

# Current status

Planner is an actively developed multi-platform project.

The individual repositories contain the implementation details, development documentation, build instructions, and releases for their respective targets.

This repository serves as the central entry point for the overall Planner project.

---

# Links

- [Planner – project overview](https://github.com/tino1003870/Planner)
- [TB Planner – Thunderbird](https://github.com/tino1003870/tb-planner)
- [TB Planner UT – Ubuntu Touch](https://github.com/tino1003870/tb-planner-ut)
- [TB Planner NX – Linux / ARM](https://github.com/tino1003870/tb-planner-nx)
- [TB Planner Windows](https://github.com/tino1003870/tb-planner-win)

---

## License

See the individual repositories for the applicable license information.
