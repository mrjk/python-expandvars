# Guide

## Basic expansion

```python
from expandvars import expandvars
import os

os.environ["FOO"] = "bar"
os.environ["BIZ"] = "buz"

expandvars("$FOO")          # "bar"
expandvars("${FOO}")        # "bar"
expandvars("${FOO}:$BIZ")   # "bar:buz"
expandvars("$$")            # current process id
```

Unset variables expand to an empty string by default:

```python
expandvars("$MISSING")  # ""
```

You can also pass an open file instead of a string:

```python
with open("config.txt") as f:
    print(expandvars(f))
```

## Defaults and assignment

| Syntax | Meaning |
|--------|---------|
| `${VAR-default}` | Use `default` if `VAR` is unset |
| `${VAR:-default}` | Use `default` if `VAR` is unset or empty |
| `${VAR=default}` | Like `-`, and set `VAR` in the environ |
| `${VAR:=default}` | Like `:-`, and set `VAR` in the environ |
| `${VAR+alt}` | Use `alt` if `VAR` is set (even if empty) |
| `${VAR:+alt}` | Use `alt` if `VAR` is set and non-empty |

```python
expandvars("${FOO:-fallback}")           # "fallback" if FOO missing/empty
expandvars("${FOO:=fallback}")           # same, and sets FOO in os.environ
expandvars("${FOO:+present}")            # "present" only if FOO is non-empty
expandvars("${FOO:-$OTHER}")             # defaults can nest expansions
```

## Require a variable

Fail fast when a value must exist:

```python
expandvars("${SECRET:?}")                 # raises if unset or empty
expandvars("${SECRET?}")                  # raises if unset
expandvars("${SECRET:?missing secret}")   # custom message
```

Or enable bash-like `nounset` for the whole string:

```python
expandvars("$A:$B:$C", nounset=True)  # UnboundVariable if any is unset
```

### Temporary escape hatch for tests

Set `EXPANDVARS_RECOVER_NULL` to a fallback value to soften both `nounset=True` and `${VAR:?}` without changing code:

```bash
EXPANDVARS_RECOVER_NULL=foo myapp --config production.ini
```

Prefer a one-shot prefix over `export`, so strict mode is not disabled for your whole shell session.

## Substrings and indirection

```python
os.environ["FOO"] = "damnbigfoobar"

expandvars("${FOO:4}")     # "bigfoobar"   (offset)
expandvars("${FOO:4:3}")   # "big"         (offset + length)

os.environ["NAME"] = "FOO"
expandvars("${!NAME}")     # "damnbigfoobar"  (indirect: value of FOO)
```

## Escaping

```python
expandvars("\\$FOO")   # "$FOO"  (literal)
expandvars("\\\\$FOO") # "\\bar" if FOO=bar
```

## Custom symbol or environ

When you need `%VAR%`-style markers, or a private mapping instead of `os.environ`, use `expand`:

```python
from expandvars import expand

expand(
    "%PATH:%{MISSING:-/default}",
    environ={"PATH": "/example"},
    var_symbol="%",
)
# "/example:/default"
```

Only the chosen `var_symbol` is expanded; other `$...` text stays literal.

## Practical pattern: config templates

```python
from expandvars import expandvars

def load_setting(raw: str) -> str:
    return expandvars(raw, nounset=False)

db_url = load_setting("${DATABASE_URL:-sqlite:///local.db}")
api_key = load_setting("${API_KEY:?API_KEY must be set}")
```

Keep critical secrets with `${VAR:?}`, and put safe defaults on optional knobs with `${VAR:-...}`.
