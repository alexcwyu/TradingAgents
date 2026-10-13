# TradingAgents: portable verification records

<!-- current-documentation-provenance -->
Documentation reviewed: 2026-10-13 · Git source base: `be027e55e7b59c0a48804f2d42c6404d36e5c15b` · [Current architecture and feature guide](../ayu-develop.md).
Historical execution commands/results below retain their original dates and scope; this metadata update is not a new application test run.
<!-- /current-documentation-provenance -->


Full raw logs are retained in the workspace maintenance evidence, not distributed here. Hashes identify those original logs. A successful build or selected test subset does not imply a full test-suite pass. Commands containing <workspace> describe the original execution environment.

[Machine-readable records](verification.json) · [Repository validation summary](../validation.md)

## Executed commands

| Check | Exit | Duration | Command | Outcomes |
| --- | ---: | ---: | --- | --- |
| build | 0 | 5.81s | `uv build --python 3.14` | Successfully built dist/tradingagents-0.6.0.tar.gz<br>Successfully built dist/tradingagents-0.6.0-py3-none-any.whl |
| build-final | 0 | 2.15s | `uv build --python 3.14` | Successfully built dist/tradingagents-0.6.0.tar.gz<br>Successfully built dist/tradingagents-0.6.0-py3-none-any.whl |
| compile | 0 | 0.3s | `/Users/alex/.local/share/uv/python/cpython-3.14-macos-aarch64-none/bin/python3.14 -m compileall -q src` | See exit status; no test-count assertion. |
| compile-final | 0 | 0.13s | `uv run --no-sync python -m compileall -q src` | See exit status; no test-count assertion. |
| lock-3.14 | 0 | 2.28s | `uv lock --upgrade --python 3.14` | See exit status; no test-count assertion. |
| lock-3.15 | 0 | 1.48s | `uv lock --upgrade --python 3.15` | See exit status; no test-count assertion. |
| python315-source-probe | 1 | 36.06s | `uv sync --directory /tmp/trading-python315-probes/TradingAgents --all-extras --all-groups --python 3.15` | See exit status; no test-count assertion. |
| sync-3.14 | 0 | 1.21s | `uv sync --all-extras --all-groups --python 3.14` | See exit status; no test-count assertion. |
| tests | 0 | 37.1s | `uv run --no-sync python -m pytest` | ================ 1263 passed, 1 skipped, 20 warnings in 25.97s ================= |
| versions-final | 0 | 0.19s | `uv run --no-sync python -c import sys,importlib.metadata,json; print(sys.version); print(json.dumps({d.metadata["Name"]: d.version for d in importlib.metadata.distributions()},indent=2,sort_keys=True))` | See exit status; no test-count assertion. |
| wheel-probe-3.14 | 0 | 0.56s | `uv sync --all-extras --all-groups --python 3.14 --no-build --no-install-project` | See exit status; no test-count assertion. |
| wheel-probe-3.15 | 2 | 3.38s | `uv sync --all-extras --all-groups --python 3.15 --no-build --no-install-project` | See exit status; no test-count assertion. |
