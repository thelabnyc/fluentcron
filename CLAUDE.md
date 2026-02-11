# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

fluentcron is a Python library providing a fluent interface for constructing crontab schedule expressions. It has zero runtime dependencies and requires Python 3.13+.

## Development Commands

```bash
# Install dev environment
uv sync

# Run all tests
uv run python -m unittest fluentcron.tests

# Run a single test
uv run python -m unittest fluentcron.tests.TestCronSchedule.test_daily_schedule

# Type checking (strict mypy)
uv run mypy fluentcron/

# Lint and format
uv run ruff check fluentcron/
uv run ruff format fluentcron/

# Run full test matrix via tox
uvx tox

# Coverage
uv run coverage run -m unittest && uv run coverage report -i -m
```

## Architecture

The library is a single Python package (`fluentcron/`) with three modules:

- **`schedule.py`** — Core `CronSchedule` class. It's an immutable `NamedTuple` with five string fields (minute, hour, day, month, weekday). Methods return new instances via `_replace()` to support fluent chaining. Contains `WEEKDAY_MAPPING` for string-to-int weekday normalization.
- **`shortcuts.py`** — Convenience functions (`daily_at`, `weekly_on`, etc.) that return cron expression strings directly, plus `CommonSchedules` with predefined constants.
- **`types.py`** — Type aliases using Python 3.13 `type` statement (PEP 695) for `Literal`-based parameter validation.

Tests live in `fluentcron/tests.py` using `unittest`.

## Code Standards

- Strict mypy configuration (see `pyproject.toml`)
- Ruff for linting/formatting, 160 char line length
- Python 3.13+ syntax (uses `type` statements)
- Commitizen for conventional commit messages
