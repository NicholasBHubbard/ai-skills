---
name: perl-plx
description: Use plx, the Perl Layout Executor, for Perl project setup and commands inside a project-specific .plx layout, especially projects that rely on local::lib-managed dependencies. Use when working in Perl repositories that use or should use plx, running Perl scripts/tests/tools through the project layout, inspecting plx config/env/libs/paths, bootstrapping dependencies with cpanm, or avoiding raw perl/prove/cpanm commands that would bypass the layout.
---

# Perl plx

plx is the Perl Layout Executor. It finds a project `.plx` layout, selects the configured Perl, activates project-local `local::lib` roots such as `local` and `devel`, sets library paths and environment, then runs Perl commands inside that layout. Treat the official docs as the source of truth:

- Official docs: https://metacpan.org/pod/App::plx
- Local full manual when plx is installed: `perldoc plx`
- Local help: `plx --help`

When exact behavior, command choices, or flags matter, read the official docs, run `perldoc plx`, or run `plx --help` instead of guessing.

## Local Defaults

Use `plx` in commands. If it is not on `PATH`, check for it in `$HOME/.local/bin/plx`.

In a Perl project that uses plx, prefer `plx ...` over raw `perl`, `prove`, `cpanm`, or project scripts so the configured Perl, `PERL5LIB`, `PATH`, and `local::lib` directories are active. One of plx's main strengths is making project-local dependency layers boring: commands automatically see modules installed into configured local libraries without hand-written `-Ilocal/lib/perl5`, shell activation, or global Perl pollution.

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

Install dependencies into a project-local `local::lib`:

```bash
plx --cpanm -llocal --notest --installdeps .
```

For Carton projects:

```bash
plx --cpanm -ldevel --notest Carton
plx carton install
```

Use separate `local::lib` roots when useful. A common pattern is `local` for normal project dependencies and `devel` for developer-only tools such as Carton, cpm, or ack.

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

Use `ll` libspec entries for `local::lib` roots such as `local` or `devel`, and `dir` entries for bare library directories such as `lib`. Prefer `ll` for installed dependencies so plx can activate the matching `local::lib` environment consistently.
