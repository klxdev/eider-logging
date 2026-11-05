# eider-logging

Structured logging for [Eider](https://github.com/klxdev/eider-lang) as JSON lines, in the style of Go's slog. It was `std/log` in Eider 0.7 and earlier; from 0.7.3 it is this package (eider-lang #216, #213).

```eider
import logging

let lg = logging.new(logging.Info).with(logging.fields().text("service", String.from("kv")))
lg.info("listening", logging.fields().text("addr", addr).int("workers", 4))
// {"time":"2026-11-01T12:34:56.789Z","level":"info","msg":"listening","service":"kv","addr":":8080","workers":4}
```

- A `Logger` has a minimum level and the fields every record carries (`with`); each call adds its own. Values are typed (`text`, `int`, `uint`, `flag`, `error`), so the JSON has strings, numbers and booleans. Records go to stderr (`new`), stdout (`to_stdout`) or memory (`to_memory`); each line is one write.
- Levels: Debug, Info, Warn, Error and **Off**, above them all: `logging.off()` (or any logger whose minimum is `Off`) writes nothing.
- **Tests**: `let mem = logging.memory()` and `logging.to_memory(logging.Debug, mem)` keep the records in a list instead of writing them. `mem.records()` gives each record's level, message and fields (`field("addr")` is the JSON text of one), `mem.lines()` the JSON lines with the time zeroed so they are the same on every run; `len`, `clear`. Loggers made from one another share the sink, and so may tasks and threads.
- **Context**: `lg.with_context(f)` adds a function `fn() -> Fields` called for every record that is written; its fields come after the logger's and before the call's. A tracer puts the current trace and span ids on each record with it (eider-lang #215): `lg.with_context(|| tr.log_fields())`.
- `format(time_ms, level, msg, base, more)` builds a line with no clock and no file; `rfc3339(ms)` writes a time.

Pure Eider; writing a record needs `io` and `clock`.

Differences from `std/log`: the module is `logging` (`logging.new`, ...), `Level` has `Off`, `Logger` has a `ctx` field and `Dest` a `Mem` variant (build loggers with `new`, `to_stdout`, `to_memory`, not by position).

## Using it

In your package's `eider.toml`:

```toml
[deps.logging]
git = "https://github.com/klxdev/eider-logging.git"
version = "0.7.0"
```

## Not done yet

- A text (logfmt) handler next to JSON, a `Handler` trait to write records elsewhere (a file, syslog).
- A context function that needs capabilities (it is `fn() -> Fields`, pure).

## Development

`sh scripts/test.sh` (it fetches the dependencies and runs every `tests/*.ei` against its `.out`); `sh scripts/version.sh` checks the version files. Tested with the installed toolchain of the same MAJOR.MINOR (`eider`).
