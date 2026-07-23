# Introduction

**expandvars** expands variables in strings the Unix / bash way — a richer alternative to Python's [`os.path.expandvars`](https://docs.python.org/3/library/os.path.html#os.path.expandvars).

Use it when you need defaults, required variables, substrings, or indirection in config values and templates.

## Fork

This is a **fork** of [sayanarijit/expandvars](https://github.com/sayanarijit/expandvars).

It was forked to publish **bug fixes** and **new features** as `mrjk.expandvars` on PyPI (import name stays `expandvars`), with independent maintenance and releases. Upstream is the original project; this fork exists so those improvements can ship without blocking on upstream release timing.

Compared to upstream: `ExpandParser`, `feat_pid` for `$$`, `**kwargs` into the parser, plus fixes for non-string environ values, empty var names, and multi-`$` / `$$` edge cases. See the project [CHANGELOG](https://github.com/mrjk/python-expandvars/blob/develop/CHANGELOG.md).

## Install

```bash
pip install mrjk.expandvars
```

## 30-second example

```python
from expandvars import expandvars

expandvars("$PATH:${HOME:?}/bin:${MISSING:-/default/path}")
# /usr/bin:...:/home/you/bin:/default/path
```

Typical config use:

```toml
[default]
access_code = "${ACCESS_CODE:-default_access_code}"
important = "${IMPORTANT_VARIABLE:?}"
path = "$PATH:$HOME/.bin"
```

## What next?

- [Guide](guide.md) — how to use it day to day
- [Reference](reference.md) — syntax, API, and exceptions

> This library copies common bash behaviours, but it is not a full bash emulator (no arrays, for example).
