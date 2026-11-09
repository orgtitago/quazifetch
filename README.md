<!-- Copyright (c) 2026 titago -->
<!-- SPDX-License-Identifier: AGPL-3.0-only -->

# quazifetch

![Version](https://img.shields.io/badge/version-26.0-555555?style=flat-square)
![Language](https://img.shields.io/badge/language-Quazi-7062a8?style=flat-square)
![Platforms](https://img.shields.io/badge/platforms-Linux%20%7C%20Windows%20%7C%20NetBSD-555555?style=flat-square)
![License](https://img.shields.io/badge/license-AGPL--3.0-7062a8?style=flat-square)

A modular system information utility written in Quazi. quazifetch prints hardware and operating system details in the terminal, with a configurable module order and a native executable.

## Features

- Hardware and system information: OS, kernel, CPU, GPU, memory, swap, disk, battery, and uptime.
- Session details: shell, terminal, desktop environment, window manager, and display information where available.
- Linux package counts, including Nix profiles and Flatpak.
- Compile-time module order and colors, with support for `NO_COLOR`.
- Small, independent providers. Missing information is omitted by default.

Most detailed hardware detection is Linux-specific. Windows and NetBSD builds use the information exposed by their supported system interfaces. Android/Termux detection is best-effort when running the Linux executable through QEMU user mode; access to host devices depends on the environment.

## Build

Install the Quazi compiler with its matching `std` and `prelude` libraries, then run from the project directory:

```sh
qz build
./build/quazifetch
```

The executable is placed in `build/` according to `quazi.toml`.

## Configuration

Edit [`src/config.qz`](src/config.qz) to select module order and terminal colors. Configuration is compiled into the executable, so rebuild after making changes.

```qz
pub fn label_color() str { "\e[1;38;5;147m" }
pub fn percent_low_max() u64 { 39 }
pub fn percent_medium_max() u64 { 79 }
```

The `layout()` function specifies which modules are shown and in what order. For example:

```qz
lines.push("os");
lines.push("kernel");
lines.push("packages");
lines.push("wm");
lines.push("cpu");
lines.push("ram");
```

Module labels are defined in [`src/modules/labels.qz`](src/modules/labels.qz). Providers live in [`src/modules/providers/`](src/modules/providers/). A module with unavailable data is skipped unless `--all` is used.

## Command-line options

| Option | Description |
| --- | --- |
| `-h`, `--help` | Show usage information. |
| `--list` | List available module IDs. |
| `--all` | Show missing values as `n/a`. |
| `--no-color` | Disable ANSI color output. |
| `--timings` | Print timing information to stderr. |

The `NO_COLOR` environment variable also disables colors when set to a nonempty value.

## Project structure

```text
quazifetch/
├── quazi.toml
└── src/
    ├── main.qz
    ├── config.qz
    └── modules/
        ├── labels.qz
        ├── registry.qz
        ├── template.qz
        └── providers/
```

## License

Copyright (c) 2026 titago.

Licensed under the **GNU Affero General Public License v3.0 only** (`AGPL-3.0-only`). See [LICENSE](LICENSE) and the [full license text](https://www.gnu.org/licenses/agpl-3.0.html).
