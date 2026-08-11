<p align="center">
  <h1 align="center">Detecting Collusion Between Cooperating Agents</h1>
  <p align="center"><strong>Build monitors for covert coordination in small multi-agent LLM setups.</strong></p>
</p>

---

## Overview

This repository implements experimental profiles for **Detecting Collusion Between Cooperating Agents**. Config, caching, hooks, metrics, ablations, reporting, and CI support local pilots on small open-weight models.

Hypothesis (one line): Build monitors for covert coordination in small multi-agent LLM setups.

## Status

Shared infrastructure is in place; domain stages must pass harness validation before any measured claim.

| Command | Purpose |
|---|---|
| `make install-dev` | editable install + pinned requirements |
| `make test` | full unit suite |
| `make ci` | lint + test + typecheck |
| `make pilot` | end-to-end pilot profile |
