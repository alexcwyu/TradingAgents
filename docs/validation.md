# Validation evidence: TradingAgents

Run date: 2026-10-12. Branch: `feature/ayu_develop`. Interpreter: `3.14.8 (main, Oct  9 2026, 15:11:09) [Clang 22.1.3 ]`.

| Check | Result |
|---|---|
| lock | passed |
| sync | passed |
| compile | passed |
| build | passed |
| tests | passed |

## Current result and compatibility decisions

- The upstream graph/vendor/CLI refactor is retained under src. Setuptools dynamic version metadata matches the existing version test and ships CLI assets.
- Cache retry uses the portable EDEADLK errno alias; existing errno and i18n path cases were updated without adding cases.
- Python 3.15 actual source build rejects ormsgpack 1.12.2: its PyO3 0.27.2 declares maximum Python 3.14.

Current unresolved checks:

All supplied checks recorded below passed.

## Latest full-suite output

```text
....                         [ 55%]
tests/test_openai_reasoning_effort.py .................                  [ 56%]
tests/test_openrouter_model_select.py ..........                         [ 57%]
tests/test_polymarket.py ........                                        [ 58%]
tests/test_portfolio_context.py ....................                     [ 59%]
tests/test_post_screen.py ..............................                 [ 62%]
tests/test_prompt_integrity.py .............                             [ 63%]
tests/test_provider_registry.py .................                        [ 64%]
tests/test_rating_integrity.py ......................................... [ 67%]
..................                                                       [ 69%]
tests/test_reddit_fallback.py .................................          [ 71%]
tests/test_report_html.py ..............                                 [ 73%]
tests/test_reporting.py ..........                                       [ 73%]
tests/test_risk_router_path_map.py ..................                    [ 75%]
tests/test_safe_ticker_component.py ........                             [ 75%]
tests/test_sec_edgar.py ........................                         [ 77%]
tests/test_signal_processing.py .............                            [ 78%]
tests/test_social_lookahead.py ...............                           [ 79%]
tests/test_statement_knowledge_time.py ..........                        [ 80%]
tests/test_stocktwits_resilience.py ................                     [ 82%]
tests/test_structured_agent_prompts.py .......                           [ 82%]
tests/test_structured_agents.py ........................................ [ 85%]
..                                                                       [ 85%]
tests/test_suite_isolation.py .........                                  [ 86%]
tests/test_symbol_normalization_paths.py ...                             [ 86%]
tests/test_symbols.py ..................                                 [ 88%]
tests/test_temperature_config.py ...........                             [ 89%]
tests/test_ticker_symbol_handling.py ...                                 [ 89%]
tests/test_tier_providers.py .....                                       [ 89%]
tests/test_tool_date_enforcement.py .................................... [ 92%]
.....                                                                    [ 93%]
tests/test_undated_tools_as_of.py ........................               [ 94%]
tests/test_vendor_errors.py .......                                      [ 95%]
tests/test_vendor_routing.py .......                                     [ 96%]
tests/test_version.py .                                                  [ 96%]
tests/test_yahoo_rate_limit.py .....................................     [ 99%]
tests/test_yahoo_snapshot.py ......                                      [ 99%]
tests/test_yfinance_stale_ohlcv_guard.py ......                          [100%]

=============================== warnings summary ===============================
tests/test_anthropic_effort.py::TestEffortGate::test_unsupported_models_do_not_receive_effort[claude-haiku-5-0]
  /Users/alex/workspaces/trading/TradingAgents/src/tradingagents/llm_clients/anthropic_client.py:61: RuntimeWarning: Model 'claude-haiku-5-0' is not in the known model list for provider 'anthropic'. Continuing anyway.
    self.warn_if_unknown_model()

tests/test_anthropic_effort.py::TestEffortGate::test_unsupported_models_do_not_receive_effort[claude-haiku-4-7-preview]
  /Users/alex/workspaces/trading/TradingAgents/src/tradingagents/llm_clients/anthropic_client.py:61: RuntimeWarning: Model 'claude-haiku-4-7-preview' is not in the known model list for provider 'anthropic'. Continuing anyway.
    self.warn_if_unknown_model()

tests/test_anthropic_effort.py::TestEffortGate::test_unsupported_models_do_not_receive_effort[claude-sonnet-4-5]
  /Users/alex/workspaces/trading/TradingAgents/src/tradingagents/llm_clients/anthropic_client.py:61: RuntimeWarning: Model 'claude-sonnet-4-5' is not in the known model list for provider 'anthropic'. Continuing anyway.
    self.warn_if_unknown_model()

tests/test_anthropic_effort.py::TestEffortGate::test_unsupported_models_do_not_receive_effort[claude-sonnet-4-0]
  /Users/alex/workspaces/trading/TradingAgents/src/tradingagents/llm_clients/anthropic_client.py:61: RuntimeWarning: Model 'claude-sonnet-4-0' is not in the known model list for provider 'anthropic'. Continuing anyway.
    self.warn_if_unknown_model()

tests/test_anthropic_effort.py::TestEffortGate::test_current_opus_and_sonnet_receive_effort[claude-opus-4-5]
  /Users/alex/workspaces/trading/TradingAgents/src/tradingagents/llm_clients/anthropic_client.py:61: RuntimeWarning: Model 'claude-opus-4-5' is not in the known model list for provider 'anthropic'. Continuing anyway.
    self.warn_if_unknown_model()

tests/test_anthropic_effort.py::TestEffortGate::test_current_opus_and_sonnet_receive_effort[claude-opus-4-6]
  /Users/alex/workspaces/trading/TradingAgents/src/tradingagents/llm_clients/anthropic_client.py:61: RuntimeWarning: Model 'claude-opus-4-6' is not in the known model list for provider 'anthropic'. Continuing anyway.
    self.warn_if_unknown_model()

tests/test_anthropic_effort.py::TestEffortGate::test_current_opus_and_sonnet_receive_effort[claude-sonnet-4-6]
  /Users/alex/workspaces/trading/TradingAgents/src/tradingagents/llm_clients/anthropic_client.py:61: RuntimeWarning: Model 'claude-sonnet-4-6' is not in the known model list for provider 'anthropic'. Continuing anyway.
    self.warn_if_unknown_model()

tests/test_anthropic_effort.py::TestEffortGate::test_future_opus_sonnet_inherit_effort_via_pattern[claude-opus-5-0]
  /Users/alex/workspaces/trading/TradingAgents/src/tradingagents/llm_clients/anthropic_client.py:61: RuntimeWarning: Model 'claude-opus-5-0' is not in the known model list for provider 'anthropic'. Continuing anyway.
    self.warn_if_unknown_model()

tests/test_anthropic_effort.py::TestEffortGate::test_future_opus_sonnet_inherit_effort_via_pattern[claude-sonnet-5-0]
  /Users/alex/workspaces/trading/TradingAgents/src/tradingagents/llm_clients/anthropic_client.py:61: RuntimeWarning: Model 'claude-sonnet-5-0' is not in the known model list for provider 'anthropic'. Continuing anyway.
    self.warn_if_unknown_model()

tests/test_anthropic_effort.py::TestEffortGate::test_claude_5_family_receives_effort[claude-mythos-5]
  /Users/alex/workspaces/trading/TradingAgents/src/tradingagents/llm_clients/anthropic_client.py:61: RuntimeWarning: Model 'claude-mythos-5' is not in the known model list for provider 'anthropic'. Continuing anyway.
    self.warn_if_unknown_model()

tests/test_anthropic_effort.py::TestEffortGate::test_mythos_preview_receives_effort
  /Users/alex/workspaces/trading/TradingAgents/src/tradingagents/llm_clients/anthropic_client.py:61: RuntimeWarning: Model 'claude-mythos-preview' is not in the known model list for provider 'anthropic'. Continuing anyway.
    self.warn_if_unknown_model()

tests/test_anthropic_effort.py::TestEffortGate::test_unknown_anthropic_model_does_not_receive_effort
  /Users/alex/workspaces/trading/TradingAgents/src/tradingagents/llm_clients/anthropic_client.py:61: RuntimeWarning: Model 'claude-experimental-x' is not in the known model list for provider 'anthropic'. Continuing anyway.
    self.warn_if_unknown_model()

tests/test_google_thinking_level.py::test_latest_alias_remaps_minimal_to_low[gemini-flash-latest]
  /Users/alex/workspaces/trading/TradingAgents/src/tradingagents/llm_clients/google_client.py:46: RuntimeWarning: Model 'gemini-flash-latest' is not in the known model list for provider 'google'. Continuing anyway.
    self.warn_if_unknown_model()

tests/test_google_thinking_level.py::test_latest_alias_remaps_minimal_to_low[gemini-pro-latest]
  /Users/alex/workspaces/trading/TradingAgents/src/tradingagents/llm_clients/google_client.py:46: RuntimeWarning: Model 'gemini-pro-latest' is not in the known model list for provider 'google'. Continuing anyway.
    self.warn_if_unknown_model()

tests/test_ollama_base_url.py::test_resolver_does_not_affect_other_providers
  /Users/alex/workspaces/trading/TradingAgents/src/tradingagents/llm_clients/openai_client.py:279: RuntimeWarning: Model 'm' is not in the known model list for provider 'xai'. Continuing anyway.
    self.warn_if_unknown_model()

tests/test_ollama_base_url.py::test_resolver_does_not_affect_other_providers
  /Users/alex/workspaces/trading/TradingAgents/src/tradingagents/llm_clients/openai_client.py:279: RuntimeWarning: Model 'm' is not in the known model list for provider 'deepseek'. Continuing anyway.
    self.warn_if_unknown_model()

tests/test_openai_reasoning_effort.py::test_non_reasoning_model_drops_effort
tests/test_temperature_config.py::TestTemperatureForwarding::test_temperature_reaches_client_when_set[openai-gpt-4.1]
tests/test_temperature_config.py::TestTemperatureForwarding::test_temperature_omitted_leaves_provider_default
  /Users/alex/workspaces/trading/TradingAgents/src/tradingagents/llm_clients/openai_client.py:279: RuntimeWarning: Model 'gpt-4.1' is not in the known model list for provider 'openai'. Continuing anyway.
    self.warn_if_unknown_model()

tests/test_temperature_config.py::TestTemperatureForwarding::test_temperature_reaches_client_when_set[deepseek-deepseek-chat]
  /Users/alex/workspaces/trading/TradingAgents/src/tradingagents/llm_clients/openai_client.py:279: RuntimeWarning: Model 'deepseek-chat' is not in the known model list for provider 'deepseek'. Continuing anyway.
    self.warn_if_unknown_model()

-- Docs: https://docs.pytest.org/en/stable/how-to/capture-warnings.html
=========================== short test summary info ============================
SKIPPED [1] tests/test_deepseek_reasoning.py:209: DEEPSEEK_API_KEY not set (or placeholder); skipping live API call
================ 1263 passed, 1 skipped, 20 warnings in 25.97s =================

```

## Exact commands and exit codes

Unsuccessful rows are retained as historical compatibility attempts; they are not claims that a current passing check remains blocked. Source-build probes use isolated temporary copies to preserve the selected repository environment.

| Command | Exit | Seconds | Evidence |
|---|---:|---:|---|
| `uv lock --upgrade --python 3.15` | 0 | 1.48 | [log](../../docs/maintenance-2026-10-12/TradingAgents-lock-3.15.log) |
| `uv sync --all-extras --all-groups --python 3.15 --no-build --no-install-project` | 2 | 3.38 | [log](../../docs/maintenance-2026-10-12/TradingAgents-wheel-probe-3.15.log) |
| `uv lock --upgrade --python 3.14` | 0 | 2.28 | [log](../../docs/maintenance-2026-10-12/TradingAgents-lock-3.14.log) |
| `uv sync --all-extras --all-groups --python 3.14 --no-build --no-install-project` | 0 | 0.56 | [log](../../docs/maintenance-2026-10-12/TradingAgents-wheel-probe-3.14.log) |
| `uv sync --all-extras --all-groups --python 3.14` | 0 | 1.21 | [log](../../docs/maintenance-2026-10-12/TradingAgents-sync-3.14.log) |
| `uv build --python 3.14` | 0 | 5.81 | [log](../../docs/maintenance-2026-10-12/TradingAgents-build.log) |
| `/Users/alex/.local/share/uv/python/cpython-3.14-macos-aarch64-none/bin/python3.14 -m compileall -q src` | 0 | 0.3 | [log](../../docs/maintenance-2026-10-12/TradingAgents-compile.log) |
| `uv run --no-sync python -m pytest` | 0 | 37.1 | [log](../../docs/maintenance-2026-10-12/TradingAgents-tests.log) |
| `uv run --no-sync python -m compileall -q src` | 0 | 0.13 | [log](../../docs/maintenance-2026-10-12/TradingAgents-compile-final.log) |
| `uv build --python 3.14` | 0 | 2.15 | [log](../../docs/maintenance-2026-10-12/TradingAgents-build-final.log) |
| `uv run --no-sync python -c import sys,importlib.metadata,json; print(sys.version); print(json.dumps({d.metadata["Name"]: d.version for d in importlib.metadata.distributions()},indent=2,sort_keys=True))` | 0 | 0.19 | [log](../../docs/maintenance-2026-10-12/TradingAgents-versions-final.log) |
| `uv sync --directory /tmp/trading-python315-probes/TradingAgents --all-extras --all-groups --python 3.15` | 1 | 36.06 | [log](../../docs/maintenance-2026-10-12/TradingAgents-python315-source-probe.log) |

The versions-final log records the exact interpreter patch release and every installed dependency version. The machine-readable [result](../../docs/maintenance-2026-10-12/TradingAgents-result.json) includes branch/default ancestry, status, artifacts and unresolved checks.

## Dependency floor refresh

| Before | After |
|---|---|
| `setuptools>=80.9.0` | `setuptools>=84.0.0` |
| `wheel>=0.45.1` | `wheel>=0.48.0` |

The table records the initial metadata refresh. Later compatibility constraints and vendor sources are explained above and the final authoritative requirements are in [pyproject.toml](../pyproject.toml) and [uv.lock](../uv.lock). No test cases were added, removed or disabled. Existing deprecated-API cases retain their assertions while explicitly checking the documented warning.
