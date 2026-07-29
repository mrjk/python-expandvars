# Changelog

All notable changes to this project are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Deprecated

- This project is **deprecated**. Development continues at
  [mrjk/python-varstore](https://github.com/mrjk/python-varstore).

## [1.0.0] - 2026-07-23

First release of the `mrjk.expandvars` distribution (import remains `expandvars`).

Fork of [sayanarijit/expandvars](https://github.com/sayanarijit/expandvars), created to ship bug fixes and new features under independent maintenance and PyPI releases.

### Added

- `ExpandParser` class: expansion logic lives in a reusable parser instance.
- `feat_pid` option on `expand` / `expandvars` / `ExpandParser` to control `$$` handling
  (`True` = real PID, `False` = leave `$$` literal, or a string override).
- `**kwargs` forwarded from `expand` / `expandvars` into `ExpandParser`.

### Fixed

- Non-string environ values no longer break concatenation during expansion.
- Empty / invalid variable names resolve more reliably.
- Edge cases with multiple `$` / `$$` sequences.

### Changed

- Internal helpers made private; public API remains `expand`, `expandvars`, and exceptions.
- Packaging metadata and CI updated for the fork (`mrjk.expandvars`, Python >= 3.9).

[1.0.0]: https://github.com/mrjk/python-expandvars/releases/tag/v1.0.0
