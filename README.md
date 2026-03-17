In the following notebook I use a Hidden Markov Model to segment users. If you have similar data, I hope this can be of help!  


# HMM User Segmentation

This is a V1 analysis notebook exploring a proof of concept for segmenting users into behavioral states using a Hidden Markov Model. The actual data, credentials, schema names, and any company-specific identifiers have been redacted — this is shared as an example of how I approach a problem like this from scratch.

---

## What It Does

The notebook uses a **Gaussian Hidden Markov Model (HMM)** to segment users into latent behavioral states based on their weekly activity. The idea is that instead of giving each user a single static label, the HMM lets you see how users *move between states over time* — which is a lot more useful for understanding engagement, churn risk, and lifecycle patterns.

**The pipeline:**

1. **Data pull** — Samples ~60,000 users from a transactional database via parameterized SQL.
2. **Feature engineering** — Builds behavioral features at the ride/session level: day-of-week patterns, cancellations, payment mode, platform (iOS/Android), rolling price averages, time on platform.
3. **Weekly aggregation** — Reshapes into a `(user, week)` panel and forward-fills signals across inactive weeks so sequences stay continuous.
4. **Scaling** — StandardScaler on continuous features; binary features passed through as-is.
5. **HMM fitting** — Fits a `GaussianHMM` (diagonal covariance) via `hmmlearn`, model selection tested across 2–13 states.
6. **State assignment** — Predicts state sequences per user and pulls out each user's dominant state across their history.
7. **Visualization** — Transition matrix heatmap, state mean profiles, segment distributions.

---

## Why HMM

Personas are used to construct fictional characters that, based on data, can represent the different user types that might use our service. There are many classification or categorization methods that range from theory based (grouping from an idea or notion) to purely data driven groupings.

We have rich, time series data hence it makes sense for us to use both time and features in any modeling efforts. "Personas" are often conceptualized as static and perpetual: "She's a *weekend-partier*"; "He's an occasional *commuter*," but the reality is everybody has some degree of '*weekend partier*-ness' and '*commuter*-ness' in them, can switch between them, AND can have the propensity to evolve over time. For example, as a user matures they can transition from a weekend partier to a commuter - *age comes for us all*. **Our thinking, and therefore our model, should be able to accommodate this evolution.**

To address our problem statement and accommodate customer evolution, time, and available features, I turned to a Hidden Markov Model (HMM). 

In (very) short, an HMM uses observable signals or data to estimate “hidden” or observable states. In the case with our data, we ideally want to understand if a user is going to be in a position where they would be likely to take a ride in a given week. If we have this information we can try to positively affect the likelihood of ordering a ride with coupons or communication, as well as try to encourage a user to be in this “potential-ride-ordering-state” more often. 

This “state” however, is not something we can observe directly. What we do instead is use the data listed above to try and predict what state a user will be in based on this information, as well as, their current state. We label the states below to make them more concrete. 


---

## What's Redacted

- Database credentials, hostnames, schema and table names
- Country/city identifiers (replaced with `COUNTRY_A`, `COUNTRY_B`, etc.)
- Product-specific column names and business logic

Redacted sections are marked with `# REDACTED:` comments throughout.

---

## Stack

| Tool | Purpose |
|------|---------|
| `pandas` / `numpy` | Data manipulation |
| `hmmlearn` | Gaussian HMM |
| `sklearn` | Feature scaling |
| `matplotlib` / `seaborn` | Visualization |
| `psycopg2` | Database connection |
| `graphviz` | Transition graph |
