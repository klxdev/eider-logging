# Changelog

eider-logging's versions (semver). Before 1.0 the MAJOR.MINOR is the Eider toolchain's it goes
with (`eider` in eider.toml), the PATCH its own.

## Unreleased

First release, for Eider 0.7 (`eider = "0.7"`): `std/log` of Eider 0.7.2 moved out of std (eider-lang #216).

- The API is std/log's under the package name: `import logging`, `logging.new`, `to_stdout`, `fields`, `format`, `rfc3339`, `Logger` with `with`, `at`, `enabled`, `log`, `debug`, `info`, `warn`, `error`.
- Added (eider-lang #213): the level `Off` (`logging.off()`; nothing is written at or below it) and a memory sink for tests (`logging.memory()`, `logging.to_memory`, `Memory.records`, `lines`, `len`, `clear`, `Record.field`).
- Added: `Logger.with_context(f)`, a function supplying fields for every record written, the hook for trace and span ids (eider-lang #215).
- Changed: `Logger` has a fourth field (`ctx`) and `Dest` a variant `Mem`; build a logger with `new`, `to_stdout` or `to_memory`.
