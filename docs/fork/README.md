# Fork notes

This repository is a fork of [REA](https://github.com/morluto/rea), maintained by
[Pradyumna Chakraborty Probal](https://github.com/probal776225-art) at
[probal776225-art/rea](https://github.com/probal776225-art/rea).

REA was created by [morluto](https://github.com/morluto) and is released under the
MIT license. The fork keeps the original copyright notice and adds one for its own
modifications; see the [license](https://github.com/probal776225-art/rea/blob/main/LICENSE).
The official npm package (`rea-agents`), the MCP Registry entry, and
[rea.tools](https://rea.tools/) belong to the upstream project. Install commands in the
[README](https://github.com/probal776225-art/rea#readme) use that official release.

[বাংলায় পড়ুন (Bengali quick start)](bn.md)

## What this fork changes

- **Maintainer information.** Package metadata, the README notice, and contribution
  links point to this fork, with upstream credited.
- **Clearer macOS recovery for unsupported Node.js.** When setup runs on an
  unsupported Node.js release on macOS, such as Node.js 25, it now names a
  Homebrew route that keeps the current Node.js in place: install the keg-only
  `node@24` formula and run the global install and `rea setup` from its `PATH`.
  Setup registers the runtime that executes it, so agents keep using Node.js 24.
- **Bengali quick start.** A [step-by-step guide in Bengali](bn.md) covers macOS
  setup, Ghidra on Apple silicon, and day-to-day use.

## Build and install from source on macOS

These steps build the fork with the pinned toolchain from `.nvmrc` (Node.js 24.22.1)
without replacing your default Node.js.

```bash
brew install node@24
export PATH="$(brew --prefix node@24)/bin:$PATH"
git clone https://github.com/probal776225-art/rea.git
cd rea
npm ci
npm run build:cached
npm pack
npm install --global --allow-scripts=frida ./rea-agents-*.tgz
rea setup
```

`--allow-scripts=frida` lets newer npm releases build the Frida binding used for
runtime instrumentation. Run `rea setup` from the same shell so it registers
Node.js 24 with your agents, then restart them. `rea update` installs the official npm release, which replaces
this build.

## Ghidra on Apple silicon

Official Ghidra releases include native decompiler binaries for Linux x64 and
Windows x64 only. On an Apple silicon Mac, build them once after extracting
Ghidra 12.1.x and installing JDK 21:

```bash
brew install openjdk@21
export JAVA_HOME="$(brew --prefix openjdk@21)/libexec/openjdk.jdk/Contents/Home"
cd /path/to/ghidra_12.1.4_PUBLIC/support/gradle
./gradlew --no-daemon buildNatives
```

Then point REA at the installation and refresh the registration:

```bash
export GHIDRA_INSTALL_DIR=/path/to/ghidra_12.1.4_PUBLIC
rea doctor --provider ghidra --json
rea setup
```

See the [Ghidra section of the installation guide](../installation.md#ghidra) for
the full requirements.

## Keep the fork current

```bash
git remote add upstream https://github.com/morluto/rea.git
git fetch upstream
git merge upstream/main
```

Send changes that belong in REA itself upstream to
[morluto/rea](https://github.com/morluto/rea).
