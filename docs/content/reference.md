# Reference

## API

### `expandvars(vars_, nounset=False)`

Expand system variables from `os.environ`.

| Parameter | Type | Description |
|-----------|------|-------------|
| `vars_` | `str` or file | Text (or file object) to expand |
| `nounset` | `bool` | If `True`, unset variables raise `UnboundVariable` |

Returns expanded `str`.

```python
from expandvars import expandvars

expandvars("$HOME/bin:${EXTRA:-}")
expandvars("$MUST_EXIST", nounset=True)
```

### `expand(vars_, nounset=False, environ=os.environ, var_symbol="$")`

Same expansion rules, with a custom mapping and variable symbol.

| Parameter | Type | Description |
|-----------|------|-------------|
| `vars_` | `str` or file | Text (or file object) to expand |
| `nounset` | `bool` | Strict unbound-variable mode |
| `environ` | mapping | Values used for lookup (default: `os.environ`) |
| `var_symbol` | `str` | Character that starts a variable (default: `$`) |

```python
from expandvars import expand

expand("%FOO", environ={"FOO": "x"}, var_symbol="%")
```

`expandvars` is `expand` with the process environment and `$`.

## Expansion syntax

| Form | Result |
|------|--------|
| `$VAR` / `${VAR}` | Value of `VAR`, or `""` if unset |
| `$$` | Current process id |
| `${VAR-default}` | Default if unset |
| `${VAR:-default}` | Default if unset or empty |
| `${VAR=default}` | Default if unset; also assign in `environ` |
| `${VAR:=default}` | Default if unset/empty; also assign |
| `${VAR+alt}` | `alt` if set (including empty), else `""` |
| `${VAR:+alt}` | `alt` if set and non-empty, else `""` |
| `${VAR?}` / `${VAR:?}` | Error if unset / unset-or-empty |
| `${VAR?msg}` / `${VAR:?msg}` | Same, with custom message |
| `${VAR:offset}` | Substring from `offset` |
| `${VAR:offset:length}` | Substring slice |
| `${!VAR}` | Indirect expansion (name taken from value of `VAR`) |
| `\$` | Escaped literal `$` (or your `var_symbol`) |

Modifiers may themselves contain expansions (for example `${FOO:-$BAR}`).

## Environment

| Variable | Effect |
|----------|--------|
| `EXPANDVARS_RECOVER_NULL` | If set, its value replaces failures from `nounset=True` and `${VAR:?}` / `${VAR?}` |

## Exceptions

All handleable errors inherit from `ExpandvarsException`.

| Exception | When |
|-----------|------|
| `ParameterNullOrNotSet` | `${VAR?}` / `${VAR:?}` and the variable is missing (or empty for `:?`) |
| `UnboundVariable` | `nounset=True` and the variable is unset |
| `MissingClosingBrace` | Unclosed `${...}` |
| `BadSubstitution` | Invalid `${...}` form (e.g. `${}` / `${VAR:}`) |
| `MissingExcapedChar` | Trailing `\` with nothing to escape |
| `NegativeSubStringExpression` | Negative length in `${VAR:offset:length}` |
| `OperandExpected` | Non-integer operand where an integer was required |

```python
from expandvars import expandvars, ParameterNullOrNotSet, UnboundVariable

try:
    expandvars("${SECRET:?}")
except ParameterNullOrNotSet as e:
    ...
```
