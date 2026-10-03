# AGENTS.md - anshlambda-dbt-tutorial

dbt + Databricks tutorial. Python >=3.12.15, `uv` managed. dbt project root is `ansh_dbt/` (not repo root).

## Commands

```bash
uv sync                                          # install deps (repo root)
cd ansh_dbt && uv run dbt debug                  # verify connection first
cd ansh_dbt && uv run dbt run                    # run all models
cd ansh_dbt && uv run dbt run -s bronze_sales    # single model
cd ansh_dbt && uv run dbt build                  # run + test
```

`uv run` from `ansh_dbt/` resolves the parent `pyproject.toml`; always invoke dbt from `ansh_dbt/` so `dbt_project.yml` + `profiles.yml` are found.

## Layout

- `ansh_dbt/models/bronze/bronze_sales.sql` — the only real model (untracked); uses `{{ source('source', 'fact_sales') }}`.
- `ansh_dbt/models/source/sources.yml` (untracked) declares source `source` (`dbt_tutorial_dev.source`) with 6 tables: `fact_sales`, `fact_returns`, `dim_date`, `dim_store`, `dim_product`, `dim_customer`. New models should use `{{ source(...) }}` refs, not hardcoded catalog paths.
- `silver/`, `gold/` are empty (`.gitkeep` only).
- `ansh_dbt/seeds/`, `macros/`, `tests/`, `snapshots/`, `analyses/` — empty scaffolding, no `schema.yml`, so `dbt test` is currently a no-op.
- `src/anshlambda_dbt_tutorial/__init__.py` — hello-world stub; unrelated to dbt, ignore it.
- `dbt_project.yml` has a stale stock-template `models.example +materialized: view` block pointing at a nonexistent dir; harmless, don't copy the pattern.

## Gotchas

- `ansh_dbt/profiles.yml` is gitignored and NOT committed — single `dev` target, Databricks token auth. Fresh clones have no profile; never commit one with a real token. Do not paste secret values into docs or code.
- Catalog/database binding (`dbt_tutorial_dev`) lives in `models/source/sources.yml`; models only run where that source exists.
- No lint, typecheck, CI, or pre-commit. Nothing to run besides dbt commands above.
- `target/`, `dbt_packages/`, `logs/` are gitignored build artifacts; don't commit them.
