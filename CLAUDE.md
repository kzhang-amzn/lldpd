# CLAUDE.md - lldpd Development Guide

## Project Overview
lldpd is an ISC-licensed implementation of IEEE 802.1ab (LLDP) for various Unix systems.
- Upstream: https://github.com/lldpd/lldpd
- Fork: https://github.com/kzhang-amzn/lldpd.git
- Maintainer: Vincent Bernat <vincent@bernat.im>

## Git Remotes
- `origin` = fork (kzhang-amzn/lldpd) — push feature branches here
- `upstream` = lldpd/lldpd — the upstream repo to PR against

## Build System (Autotools)
```sh
./autogen.sh          # bootstrap (generates configure)
./configure           # configure build (see ./configure --help for options)
make                  # build
make check            # run unit tests
sudo make install     # install
```

### Running from source (without installing)
```sh
sudo libtool execute src/daemon/lldpd -L $PWD/src/client/lldpcli -d
```

## Testing
- **Unit tests:** `make check` (uses the Check framework, tests in `tests/check_*.c`)
- **Integration tests:** `sudo pytest -vv -n10 --boxed` in `tests/integration/` (requires Linux 3.11+, root)
- **Fuzzing:** libfuzzer and AFL++ targets in `tests/`

## Coding Style — OpenBSD style(9)
- **Tabs for indentation**, tab width = 8
- **~80 column limit** (clang-format uses 88)
- Return type on its own line for function definitions; opening brace on next line for functions only
- Opening braces on same line for control structures
- Pointers: `char *p` (right-aligned)
- Run `clang-format -i <file>` to auto-format (config in `.clang-format`)
- ForEachMacros: TAILQ_FOREACH

### Example
```c
int
main(int argc, char *argv[])
{
	if (argc > 1) {
		/* ... */
	}
	return 0;
}
```

## Commit Message Convention
```
subsystem: short description

Optional longer explanation.
```
Subsystem prefixes: `daemon:`, `lib:`, `client:`, `interface:`, `config:`,
`fix:`, `build:`, `docs:`, `test:`, `osx:`, `daemon/protocols:`, etc.

## PR Checklist (for upstream contributions)
1. Branch from `upstream/master`
2. Follow OpenBSD coding style; run `clang-format`
3. Add/update unit tests in `tests/` when applicable
4. Update `NEWS` file with a new entry (see existing format)
5. Update docs (`src/client/lldpcli.8.in`, man pages) if adding CLI options
6. Keep commits focused — one logical change per commit
7. Push branch to `origin`, open PR against `upstream master`

## Key Directories
- `src/daemon/` — core lldpd daemon
- `src/client/` — lldpcli command-line client
- `src/lib/` — liblldpctl shared library
- `include/` — public headers
- `tests/` — unit tests, integration tests, CI scripts, fuzz corpus
- `m4/` — autoconf macros

## CI
GitHub Actions (`.github/workflows/`): builds on Ubuntu, macOS, OpenBSD, FreeBSD, NetBSD.
Style checks run via `ci.yml` and `style.yml`.
