# A-Share Production-Grade Factor Workflow — Claude Code Skills Pack

> A complete Claude Code skills pack for production-grade, systematic quantitative factor research on China A-shares. Decouples LLM hypothesis generation from deterministic Harness execution via restricted DSL AST compilation, 4-layer caching, and ARIS cross-model adversarial review.

[![GitHub](https://img.shields.io/github/license/zhangpelf/ashare-factor-workflow)](https://github.com/zhangpelf/ashare-factor-workflow/blob/main/LICENSE)
![GitHub stars](https://img.shields.io/github/stars/zhangpelf/ashare-factor-workflow)

**Companion repo**: [ashare-factor-mining](https://github.com/zhangpelf/ashare-factor-mining) — Production-grade Python factor computation, AST compiler, 4-layer cache engine, and visualization pipeline.

---

## Overview

This skills pack defines a **structured, production-grade workflow** for quantitative factor research. It replaces ad-hoc Python script generation with a deterministic Harness architecture:

```
Literature ──► Restricted DSL Mining ──► AST & 4-Cache Testing ──► ARIS Review ──► Final Deliverable
  (G001)             (G002)                    (G003)              (G004)            (G005/G006)
```

Each stage enforces strict boundaries between LLM open decisions and deterministic execution rules, producing traceable artifacts in `paper_research/` and `output/`.

### Key Features

- **Decoupled Agent Harness**: LLM focuses on hypothesis generation and operator selection; deterministic core handles AST parsing, PIT time alignment, and backtests.
- **Restricted Expression DSL**: Enforces strong-typed DSL formulas (e.g. `cs_zscore(ts_return(close, 5))`) with automatic lookback window inference and intermediate node deduping.
- **4-Layer Persistent Cache Integration**: Interfaces with Data Matrix, AST Node, Factor Matrix, and Evaluation Cache to prevent redundant compute.
- **Structured Research Memory**: Replaces noisy Context Windows with SQLite candidate tracking (IC/IR, turnover, cross-factor correlation, failed parent variants).
- **43 factors** across 10 categories (CH-4, q-factor, technical, risk, volume, etc.).
- **9 mining methods**: LASSO, ElasticNet, RandomForest, GBDT, XGBoost, LightGBM, Bayesian Ridge, MLP, Genetic Programming.
- **6-dimension evaluation & ARIS adversarial review**: IC, IR, Sharpe, Fama-MacBeth t-stat, long-short annualized return, IC positive ratio, and cross-model critique (Claude ↔ GPT).

---

## Pipeline Architecture

```
                         ┌──────────────────────────────┐
                         │  G001: Literature Survey      │
                         │  paper_research/factor_ideas  │
                         └───────────┬──────────────────┘
                                     ▼
                         ┌──────────────────────────────┐
                         │  G002: Factor Mining           │
                         │  9 methods, 43 factors         │
                         └───────────┬──────────────────┘
                                     ▼
                         ┌──────────────────────────────┐
                         │  G003: Factor Testing          │
                         │  IC/IR/Group Backtest/FM       │
                         └───────────┬──────────────────┘
                                     ▼
                ┌────────────────────────────────────────┐
                │  G004: ARIS Adversarial Review          │◄──────┐
                │  Cross-model critique loop              │       │
                └───────────┬────────────────────────────┘       │
                            │                                    │
                     ┌──────▼──────┐                      ┌──────┴──────┐
                     │   Passed?    │                     │   Rejected   │
                     └──────┬──────┘                     └──────┬──────┘
                            │ Yes                               │ No
                            ▼                                   │
                ┌────────────────────────────────────────┐       │
                │  G005: Charts + Evaluation              │       │
                │  24 figures, 6-dimension scoring        │       │
                └───────────┬────────────────────────────┘       │
                            │                                    │
                     ┌──────▼──────┐                             │
                     │  Results OK? │──── No ─────────────────────┘
                     └──────┬──────┘      (back to G002)
                            │ Yes
                            ▼
                ┌────────────────────────────────────────┐
                │  G006: Final Report                     │
                │  8-module structured deliverable         │
                └────────────────────────────────────────┘
```

---

## Skills

| Skill | Stage | Purpose |
|-------|-------|---------|
| [`auto-因子提取`](skills/auto-因子提取/SKILL.md) | G001–G002 | **Orchestrator** — full pipeline from literature to code. 43-factor formula table, 9 mining methods, literature-driven mining workflow |
| [`auto-因子分析`](skills/auto-因子分析/SKILL.md) | G003 | **Analysis** — IC/IR/Sharpe interpretation, factor ranking, direction judgment. Literature cross-reference comparison |
| [`auto-因子评估`](skills/auto-因子评估/SKILL.md) | G005 | **Evaluation** — 6-dimension scoring (有效/可疑/无效), FM t significance grading, cross-method consensus mechanism |
| [`auto-因子图表`](skills/auto-因子图表/SKILL.md) | G005 | **Charts** — 24 publication-quality figures: IC series, cumulative returns, correlation heatmap, IC decay, dashboard |
| [`auto-因子报告`](skills/auto-因子报告/SKILL.md) | G005 | **Report generation** — structured Markdown + Excel (4 sheets) with buy recommendations |
| [`auto-撰写报告`](skills/auto-撰写报告/SKILL.md) | G006 | **Final report** — 8-module standardized deliverable with decision gates, literature cross-validation, and action plan |
| [`manual-指数RL训练`](skills/manual-指数RL训练/SKILL.md) | — | **RL training** — reinforcement learning for index constituent selection (standalone) |

---

## Factor Catalog (43 Factors)

### Factor Categories

| Category | Factors | Source |
|----------|---------|--------|
| **CH-4 China Factors** | size, EP, turnover, RE | Liu, Stambaugh & Yuan (2019, JFE) |
| **q-factor** | IA, ROE, EG | Hou, Xue & Zhang (2015, RFS; 2021) |
| **Higher-Moment Risk** | coskewness, cokurtosis, idiosyncratic skewness | Harvey & Siddique (2000, JF) |
| **Tail Risk** | VaR (95/99), CVaR (95/99), tail_risk | Kelly & Jiang (2014, JF) |
| **Technical** | RSI (6/14), BB_width, BB_position, st_reversal (1w/1m), st_momentum (5d) | Wilder (1978), Jegadeesh & Titman (1993) |
| **Volume** | dollar_volume, volume_trend, amihud_illiq, turnover | Amihud (2002, JFM) |
| **Volatility** | beta, ivol_capm, ivol_ff3, max_ret_1m, ulcer_index | Ang et al. (2006, JF) |
| **Fundamental** | book_to_price, earnings_yield, div_yield, cash_flow_yield, leverage | Fama & French (1993, 2015) |
| **Quality** | ROE, ROA, gross_margin, asset_turnover, accruals | Novy-Marx (2013, JFE) |
| **Growth** | earnings_growth, sales_growth | Lakonishok et al. (1994, JF) |

### Factor Formula Reference

Each factor in `auto-因子提取` includes: formula, academic reference, expected A-share direction, and data dependency (yfinance = price-only, akshare = fundamental-capable).

---

## Mining Methods

| Method | Type | Best For | Key Hyperparameters |
|--------|------|----------|-------------------|
| LASSO | L1 linear | Sparse linear selection | alpha=0.01, max_iter=10000 |
| ElasticNet | L1+L2 linear | Correlated feature groups | alpha=0.01, l1_ratio=0.5 |
| RandomForest | Tree ensemble | Non-linear interactions | n_estimators=500, max_depth=6 |
| GradientBoosting | Tree ensemble | Sequential refinement | n_estimators=300, learning_rate=0.05 |
| XGBoost | Boosted tree | High-dim non-linear | n_estimators=300, max_depth=4 |
| LightGBM | Boosted tree | Large-scale efficiency | n_estimators=300, num_leaves=20 |
| Bayesian Ridge | Bayesian linear | Prior-constrained selection | alpha_1=1e-6, alpha_2=1e-6 |
| MLP Neural Net | Deep learning | Deep non-linear patterns | hidden_layer_sizes=(64,32), alpha=0.001 |
| Genetic Programming | Symbolic regression | New factor discovery | pop_size=500, max_generations=10 |

---

## Evaluation Framework

### 6-Dimension Scoring

| Dimension | Metric | Fail | Pass | Excellent |
|-----------|--------|------|------|-----------|
| Predictive power | Mean IC | \|IC\| < 0.01 | \|IC\| > 0.01 | \|IC\| > 0.03 |
| Stability | IR | < 0.1 | > 0.3 | > 0.5 |
| Economic significance | Long-short annualized | < 2% | > 2% | > 8% |
| Risk-adjusted | Sharpe | < 0 | > 0.5 | > 1.0 |
| Statistical significance | FM t-stat | \|t\| < 1.0 | > 1.96 | > 2.58 |
| Direction stability | IC positive ratio | < 50% | > 55% | > 60% |

### Consensus Mechanism (Cross-Method)

| Consensus Level | Condition | Confidence |
|----------------|-----------|------------|
| Strong | Selected by ≥4 methods | Very robust |
| Consensus | Selected by 2-3 methods | Referable |
| Single | Selected by 1 method | Possible overfit |

---

## Quick Start

### Prerequisites

- [Claude Code](https://claude.ai/code) (CLI or IDE extension)
- [ashare-factor-mining](https://github.com/zhangpelf/ashare-factor-mining) companion repo cloned locally
- Python 3.11+ with dependencies installed

### Run Full Pipeline

```bash
# Clone both repos
git clone https://github.com/zhangpelf/ashare-factor-workflow.git
git clone https://github.com/zhangpelf/ashare-factor-mining.git

# The skills reference hardcoded paths — configure BASE_DIR in each skill
# or symlink the repos to the expected paths

# Inside Claude Code, invoke:
/auto-因子提取 "[path-to-factors-repo]"
```

### Run Individual Stages

```bash
# Analysis only
/auto-因子分析 "[path-to-factor-report-csv]"

# Evaluation only
/auto-因子评估 "[path-to-factor-report-csv]"

# Generate charts
/auto-因子图表 "[path-to-factor-results]"

# Write final report
/auto-撰写报告 "[path-to-pipeline-output]"
```

### Python Pipeline Commands

```bash
cd /path/to/ashare-factor-mining

# Full pipeline
python3 -m src.workflow_orchestrator --mode full --real-data

# Generate figures only
python3 -m src.workflow_orchestrator --mode figures

# Generate report only
python3 -m src.workflow_orchestrator --mode report

# Analyze results
python3 -m src.workflow_orchestrator --mode analyze
```

---

## Workflow Stages Detail

| Stage | Steps | Artifacts |
|-------|-------|-----------|
| **G001: Literature Survey** | Search papers → extract factors → map to code | `paper_research/search_results/`, `factor_ideas.md`, `literature_to_factor.csv`, `decision_log.md` |
| **G002: Factor Mining** | Compute factors → run mining methods → cross-temporal validation | `output/factor_data.parquet`, mining method outputs |
| **G003: Factor Testing** | IC/IR analysis → quintile backtest → Fama-MacBeth regression | `output/ashare_factor_report.csv`, `output/analysis_summary.json` |
| **G004: ARIS Review** | Cross-model code review → result audit → claim verification | Review logs, iteration history |
| **G005: Charts + Eval** | 24 figures → 6-dimension scoring → structured report | `figures/*.pdf`, `output/factor_report.md`, `output/factor_analysis.xlsx` |
| **G006: Final Report** | 8-module report → literature cross-validation → action plan | `output/因子分析报告.md`, `paper_research/comparison_report.md`, `paper_research/consensus_matrix.md` |

---

## Audit Trail

Every step in the workflow leaves traceable artifacts for manual review:

| Area | Path | Purpose |
|------|------|---------|
| Literature | `paper_research/` | Search results, factor ideas, decision logs, comparison reports |
| Data & models | `output/` | Factor values, test summaries, analysis JSON, code snapshots |
| Figures | `figures/` | 24 PDF charts + 1 PNG dashboard |
| Reports | `output/` | Markdown reports, Excel exports, HTML renderings |
| Logs | `output/logs/` | Pipeline execution logs |

---

## Related Projects

| Project | Description | Link |
|---------|-------------|------|
| **ashare-factor-mining** | Python factor computation, testing pipeline, charts | [GitHub](https://github.com/zhangpelf/ashare-factor-mining) |
| **ARIS Adversarial Review** | Cross-model research critique methodology | Part of Claude Code ecosystem |

---

## License

MIT

## Author

**Zhang Peifu** — [GitHub](https://github.com/zhangpelf)

---

*Built with Claude Code. Part of the ARIS (Adversarial Research Improvement System) methodology.*
