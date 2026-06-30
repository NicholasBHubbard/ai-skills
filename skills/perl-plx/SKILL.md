---
name: perl-plx
description: Use plx, the Perl Layout Executor, for Perl project setup and commands inside a project-specific .plx layout. Use when working in Perl repositories that use or should use plx to select a project Perl, add checkout source libraries with .dir libspecs, activate local::lib roots with .ll libspecs, run Perl scripts/tests/tools through the project layout, inspect plx config/env/libs/paths, bootstrap dependencies with cpanm, or avoid raw perl/prove/cpanm commands that would bypass the layout.
---

# Perl plx

plx is the Perl Layout Executor. It finds a project `.plx` layout, selects the configured Perl, adds configured source library directories, optionally activates `local::lib` roots such as `local` and `devel`, sets paths and environment, then runs Perl commands inside that layout. Treat the official docs as the source of truth:

- Official docs: https://metacpan.org/pod/App::plx
- Local full manual when plx is installed: `perldoc plx`
- Local help: `plx --help`

When exact behavior, command choices, or flags matter, read the official docs, run `perldoc plx`, or run `plx --help` instead of guessing.

## Local Defaults

Use `plx` in commands. If it is not on `PATH`, check for it in `$HOME/.local/bin/plx`.

In a Perl project that uses plx, prefer `plx ...` over raw `perl`, `prove`, `cpanm`, or project scripts so the configured Perl, `PERL5LIB`, `PATH`, source `lib` directories, and any configured `local::lib` directories are active. One of plx's main strengths is making project library wiring boring: commands can automatically see checkout modules from `.dir` libspecs and, when desired, modules installed into configured local libraries from `.ll` libspecs.

Prefer a custom Perl for real project layouts. Use the system Perl only for quick inspection or when the project requires it. Do not install project CPAN dependencies into the system Perl by default.

If a custom Perl is needed, ask where to install it. Recommend `/opt/perl-$PERL_VERSION` when the user has no preference. Build it with [Perl::Build](https://metacpan.org/pod/Perl::Build):

```bash
PERL_VERSION=5.42.0
PERL_PREFIX="/opt/perl-$PERL_VERSION"
curl -L https://raw.githubusercontent.com/tokuhirom/Perl-Build/master/perl-build | perl - "$PERL_VERSION" "$PERL_PREFIX"
plx --init "$PERL_PREFIX/bin/perl"
```

Before running project commands, confirm the layout when needed:

```bash
plx --base
plx --perl
plx --libs
plx --paths
plx --env
```

If no `.plx` layout exists, do not silently initialize one. Ask or report the setup need unless the user explicitly asked to create the layout.

## Common Workflows

Initialize a layout only when setup is requested:

```bash
plx --init
plx --init 5.38.0
plx --init /path/to/perl
```

Add checkout source libraries so `-Ilib` and sibling `-I../repo/lib` flags become automatic:

```bash
plx --config libspec add 50-core.dir ../core-perl/lib
plx --config libspec add 75-self.dir lib
plx --libs
```

Use `.dir` entries for source trees under active development. This is the plx equivalent of making editable checkout libraries visible without installing them into the configured Perl.

Install dependencies into the configured Perl directly when the project uses a single project Perl install. Do not use `plx --cpanm` for this mode; `plx --cpanm` requires `-l` or `-L` because it is specifically a local::lib install helper. Instead, run `cpanm` through the configured Perl environment:

```bash
plx perl /path/to/project-perl/bin/cpanm --notest --installdeps .
```

If the configured Perl's `bin` directory is on the layout `PATH`, this shorter form may work:

```bash
plx cpanm --notest --installdeps .
```

When the project Perl is explicit and already has `cpanm`, direct execution is also acceptable:

```bash
/path/to/project-perl/bin/cpanm --notest --installdeps .
```

Install dependencies into a project-local `local::lib` only when the project explicitly uses local-lib dependency roots:

```bash
plx --cpanm -llocal --notest --installdeps .
```

For Carton projects:

```bash
plx --cpanm -ldevel --notest Carton
plx carton install
```

Use separate `local::lib` roots when useful. A common pattern is `local` for normal project dependencies and `devel` for developer-only tools such as Carton, cpm, or ack. Do not add `.ll` entries when dependencies are intentionally installed into the configured Perl's own site directories.

Run commands through the layout:

```bash
plx perl -c lib/Module.pm
plx prove -lv t/some-test.t
plx prove -r t
plx -E 'say for @INC'
plx bin/tool --help
plx dev/tool --help
plx project-command --args
```

Inspect command resolution before running something unfamiliar:

```bash
plx --which <command>
plx --commands
plx --commands <prefix-or-/regex/>
```

Use plx-managed environment overrides as leading plx arguments:

```bash
plx NAME=value <command> <args>
```

Do not rely on `PERL...=value plx ...` for Perl environment variables; plx clears existing `PERL*` variables before setting up the layout.

## Configuration

Use `plx --config` to inspect and adjust the layout:

```bash
plx --config
plx --config perl
plx --config perl set <perl-spec-or-path>
plx --config libspec
plx --config libspec add <name.ll-or-name.dir> <path>
plx --config env
plx --config env add <name> <value>
```

Use `.dir` libspec entries for bare source library directories such as `lib` or `../some-project/lib`. Use `.ll` libspec entries only for `local::lib` roots such as `local` or `devel`. If CPAN dependencies are installed directly into the configured Perl's site directories, no `.ll` entry is needed. Do not use `.ll` for checkout source trees; it activates local::lib behavior rather than just adding a source directory to `PERL5LIB`.
