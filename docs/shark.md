# shark - The SeatalkHS Engineering Tool

**shark** --
**[winRAYDP](winRAYDP.md)** --
**[winSniffer](winSniffer.md)** --
**[winShark](winShark.md)** --
**[winFILESYS](winFILESYS.md)** --
**[winDBNAV](winDBNAV.md)**

repos: **[phorton1](https://github.com/phorton1)** --
**[Ray Library](https://github.com/phorton1/base-Pub-Ray/blob/master/docs/readme.md)** --
**shark Tool** --
**[navMate App](https://github.com/phorton1/base-apps-navMate/blob/master/docs/readme.md)**

**shark** (`shark.pm`) is the engineering application for probing and
operating the SeatalkHS protocols. It is a **wxPerl** GUI application that uses the
**[NET library](https://github.com/phorton1/base-Pub-Ray/blob/master/NET/docs/readme.md)** to connect to an E80 via [RAYDP](https://github.com/phorton1/base-Pub-Ray/blob/master/NET/docs/RAYDP.md), [WPMGR](https://github.com/phorton1/base-Pub-Ray/blob/master/NET/docs/WPMGR.md), [TRACK](https://github.com/phorton1/base-Pub-Ray/blob/master/NET/docs/TRACK.md), [FILESYS](https://github.com/phorton1/base-Pub-Ray/blob/master/NET/docs/FILESYS.md), and
other services. shark also starts a serial command interface, a tshark-based packet
sniffer, and an HTTP server (`s_server.pm`, port 9882) for Google Earth waypoint
serving. The HTTP server extends `NET/h_server.pm`, the shared base class also used
by navMate.

## Feature Flags

Compile-time feature flags in `a_defs.pm` enable or disable components:

| Flag                              | Default | Description                              |
| --------------------------------- | ------- | ---------------------------------------- |
| `$WITH_SERIAL`                    | 1       | Serial command interface                 |
| `$WITH_RAYDP`                     | 1       | RAYDP discovery listener                 |
| `$WITH_HTTP_SERVER`               | 1       | HTTP server for Google Earth (port 9882) |
| `$WITH_SNIFFER`                   | 1       | tshark packet sniffer                    |
| `$WITH_TCP_SCANNER`               | 0       | TCP port scanner (disabled by default)   |
| `$WITH_UDP_SCANNER`               | 0       | UDP port scanner (disabled; requires sniffer off) |
| `$WITH_WX`                        | 1       | wxPerl GUI                               |
| `$WITH_TRACK`                     | 1       | TRACK service                            |
| `$WITH_WPMGR`                     | 1       | WPMGR service                            |
| `$WITH_FILESYS`                   | 1       | FILESYS service                          |
| `$WITH_DBNAV`                     | 1       | DBNAV multicast listener                 |
| `$WITH_DB`                        | 1       | Database TCP service                     |
| `$AUTO_START_IMPLEMENTED_SERVICES`| 1       | Auto-start services on RAYDP discovery   |

## Serial Command Interface

shark accepts commands over a Win32 serial port connection. Commands are typed
into a serial terminal and processed by `handleSerialCommand()`.

**System:**

| Command             | Description                                         |
| ------------------- | --------------------------------------------------- |
| `wakeup`            | Send E80 wakeup packet                              |
| `db`                | Show local waypoint database                        |
| `kml`               | Print RAYSYS KML to console (kml_RAYSYS)            |
| `s`                 | Clear shark.log                                     |
| `r`                 | Clear rns.log                                       |
| `log <message>`     | Write labeled separator to both log files           |

**Database (DB):**

| Command             | Description                                         |
| ------------------- | --------------------------------------------------- |
| `i`                 | Call DB `uiInit()`                                  |
| `fids`              | Show all known FIDs via `showFids()`                |

**[DBNAV](https://github.com/phorton1/base-Pub-Ray/blob/master/NET/docs/DBNAV.md):**

| Command             | Description                                         |
| ------------------- | --------------------------------------------------- |
| `v`                 | Show current DBNAV field values                     |

**[FILESYS](https://github.com/phorton1/base-Pub-Ray/blob/master/NET/docs/FILESYS.md):**

| Command                    | Description                                  |
| -------------------------- | -------------------------------------------- |
| `f <cmd> <path>`           | File command: cmd = DIR, SIZE, FILE, or ID   |

**[TRACK](https://github.com/phorton1/base-Pub-Ray/blob/master/NET/docs/TRACK.md):**

| Command                    | Description                                  |
| -------------------------- | -------------------------------------------- |
| `t start`                  | Start Current Track                          |
| `t stop`                   | Stop Current Track                           |
| `t save`                   | Save Current Track                           |
| `t discard`                | Discard unsaved Current Track                |
| `t state`                  | Get recording state                          |
| `t bump`                   | Bump the default track name counter          |
| `t cur`                    | Get Current Track MTA                        |
| `t cur2`                   | Get Current Track MTA + points               |
| `t mta <name>`             | Get MTA for named saved track                |
| `t name <name>`            | Set Current Track name                       |
| `t nth <n>`                | Get Nth point of Current Track               |
| `t erase <name>`           | Erase saved track by name                    |
| `t rename <old> <new>`     | Rename a saved track                         |

**[WPMGR](https://github.com/phorton1/base-Pub-Ray/blob/master/NET/docs/WPMGR.md):**

| Command                    | Description                                  |
| -------------------------- | -------------------------------------------- |
| `q`                        | Query all waypoints, routes, and groups      |
| `create wp <num>`          | Create a test waypoint                       |
| `create route <num> [...]` | Create a test route                          |
| `create group <num>`       | Create a test group                          |
| `delete wp <name>`         | Delete waypoint by name                      |
| `delete route <name>`      | Delete route by name                         |
| `delete group <name>`      | Delete group by name                         |
| `route <num> + <wp_num>`   | Add waypoint to route                        |
| `route <num> - <wp_num>`   | Remove waypoint from route                   |
| `wp <wp_num> <group_num>`  | Set waypoint group                           |
| `wp <name>`                | Show waypoint info                           |
| `route <name>`             | Show route info                              |
| `group <name>`             | Show group info                              |

**Port scanning:**

| Command                    | Description                                  |
| -------------------------- | -------------------------------------------- |
| `scan <low> <high>`        | TCP port scan range                          |
| `udp [a] <low> <high>`     | UDP scan (a = aggressive mode)               |

**FSH writing:**

| Command    | Description                           |
| ---------- | ------------------------------------- |
| `write`    | Invoke `fshWriter::write()`           |

## GUI Panels

| Panel       | Description                                        |
| ----------- | -------------------------------------------------- |
| [winShark](winShark.md)    | Protocol monitoring control - per-port active/log/only checkboxes |
| [winSniffer](winSniffer.md)  | tshark sniffer control - per-port active/log/only/self checkboxes and live packet counts |
| [winRAYDP](winRAYDP.md)    | Live RAYDP service discovery - connect/spawn controls per advertised service |
| [winFILESYS](winFILESYS.md)  | CF card file browser - directory navigation and file download |
| [winDBNAV](winDBNAV.md)    | Live navigation data - decoded DBNAV multicast field values |

## Credits

- [**Wireshark / tshark**](https://www.wireshark.org/) - the packet capture
  engine behind shark's sniffer; tshark performs the raw ethernet capture
  that shark decodes and displays.

- [**wxPerl / wxWidgets**](https://www.wxwidgets.org/) - the cross-platform
  GUI toolkit used for all of shark's windows and panels.

## License

Copyright (C) 2026 Patrick Horton

This repository is free software, released under the
[GNU General Public License v3](../LICENSE.TXT) or any later version.
See [LICENSE.TXT](../LICENSE.TXT) or <https://www.gnu.org/licenses/> for details.

## Please Also See

- [**phorton1/base-apps-shark**](https://github.com/phorton1/base-apps-shark) -
  this repository on GitHub

- [**Ray Library**](https://github.com/phorton1/base-Pub-Ray/blob/master/docs/readme.md) -
  the reverse-engineered SeatalkHS protocols, FSH file format, and CSV
  conversion library that shark is built on.

- [**navMate**](https://github.com/phorton1/base-apps-navMate/blob/master/docs/readme.md) -
  the navigation knowledge management application built on the same library.

---

**Next:** [winRAYDP](winRAYDP.md)
