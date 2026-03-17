# HMM User Segmentation — Redacted Concept Notebook

This notebook is a **sanitized, redacted version** of a segmentation model built as part of proprietary work. All company-specific identifiers, credentials, schema names, and column values have been replaced with generic placeholders. The underlying methodology and code structure are preserved to demonstrate the approach.

---

## What This Does

The notebook implements a **Gaussian Hidden Markov Model (HMM)** to segment users into latent behavioral states based on their weekly activity patterns. Rather than assigning a single static segment per user, the HMM captures how users *transition* between behavioral states over time — giving a richer picture of engagement, churn risk, and lifecycle stage.

**Pipeline at a glance:**

1. **Data pull** — Samples ~60,000 users from a transactional database via parameterized SQL (credentials and schema names redacted).
2. **Feature engineering** — Constructs ride/session-level features: day-of-week patterns, cancellation behavior, payment mode, platform (iOS/Android), app version, rolling price averages, and time-on-platform.
3. **Weekly aggregation** — Builds a dense `(user, week)` panel, forward-filling behavioral signals across inactive weeks to maintain sequence continuity.
4. **Scaling** — Applies `StandardScaler` to continuous features; binary/categorical features are passed through unscaled.
5. **HMM fitting** — Fits a `GaussianHMM` (diagonal covariance) via `hmmlearn`, with model selection across 2–13 states using log-likelihood and BIC.
6. **State assignment** — Predicts the most likely state sequence per user and derives each user's *dominant* state across their observed history.
7. **Validation & visualization** — Transition matrix heatmap, state mean profiles, and distribution checks across named behavioral segments.

---

## Why HMM (vs. K-Means or Static Clustering)

Static clustering methods assign users to segments at a point in time and can't capture lifecycle dynamics. An HMM treats each user's history as a sequence and learns:

- **Emission probabilities** — what behavioral patterns characterize each state
- **Transition probabilities** — how likely users are to move between states week-to-week

This makes it well-suited for identifying states like "high-frequency commuter," "lapsing user," or "re-engaged after churn" — and tracking how users flow between them.

---

## What's Redacted

- Database credentials, hostnames, and schema/table names
- Country and city identifiers (replaced with `COUNTRY_A`, `COUNTRY_B`, etc.)
- Product-specific column names and business logic
- Internal path references (`sys.path.append`)

The redacted sections are clearly marked with `# REDACTED:` comments.

---

## Stack

| Tool | Purpose |
|------|---------|
| `pandas` / `numpy` | Data manipulation |
| `hmmlearn` | Gaussian HMM implementation |
| `sklearn` | Feature scaling |
| `matplotlib` / `seaborn` | Visualization |
| `psycopg2` | Database connection (Redshift/Postgres) |
| `graphviz` | Transition graph visualization |

---

## Note

This is a **v1 concept notebook** — the kind of exploratory, research-grade code written to validate whether the approach works before it goes into a production pipeline. It is intentionally not production-hardened. The goal here is to show the modeling logic and analytical thinking, not a deployable artifact.
