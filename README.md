# What holds a co-review network together? Taste, time, or social play on Food.com

Final project for **02805 Social Graphs and Interactions** (DTU, Fall 2026).

## Research question

Communities in a co-review network are usually read as groups with shared culinary taste (the assumption behind
collaborative filtering). **Do the communities of active Food.com users reflect taste, the time users joined, or
social ties such as forum games, and does the review text reveal which?**

## Data

*Food.com Recipes and User Interactions* (Majumder et al., EMNLP 2019), available on Kaggle
(`shuyangli94/food-com-recipes-and-user-interactions`).

The raw CSVs are **not** in this repository (≈600 MB). Download `RAW_recipes.csv` and `RAW_interactions.csv` and put
them in a folder called `data/` next to the notebooks.

## How to run

```
pip install pandas numpy networkx scipy scikit-learn matplotlib
```

Open the notebooks in Jupyter / VS Code and run all cells. The main notebook takes about 20–25 minutes.

## Notebooks

| File | What it does |
|---|---|
| `notebooks/01_exploration.ipynb` | Data exploration: counts, review text, bipartite network, first communities and quick tests of three candidate ideas |
| `notebooks/02_main_analysis.ipynb` | Main analysis: network, communities, null model, robustness, cohorts, game markers, text classifier, ratings, hand labels, paper figures |

## Repository structure

```
README.md
notebooks/        analysis notebooks (run from this folder, with data/ next to them)
figures/          figures produced by the main notebook
archive/          first approach (ingredient network), kept for reference
```

## Pipeline

1. **Bipartite network** user–recipe (a user is linked to a recipe they reviewed): 226,570 users, 231,637 recipes, 1.13 M reviews.
2. **Active core** (iterative filter on the bipartite network): users with ≥ 20 reviewed recipes, recipes with ≥ 10 reviewers → 3,477 users, 10,636 recipes.
3. **Projection** to a user–user network (weight = number of recipes reviewed in common): 2.8 M edges, density 0.46.
4. **Backbone** with the disparity filter (Serrano et al. 2009), α = 0.05: 130,905 edges, all users kept.
5. **Communities** with Louvain (Blondel et al. 2008), 10 seeds, best Q = 0.203 → 4 communities (2,360 / 608 / 445 / 64 users).
6. **Null model**: degree-preserving rewiring of the bipartite network, then the same pipeline. Q = 0.20 vs 0.06 ± 0.002 (z ≈ 88).
7. **Text**: game markers (regex: `zwt`, `tag`, `pac`, `swap`, …), TF-IDF + logistic regression with user-grouped cross-validation.

## Main results

- **A forum-game community.** One community (445 users, 13% of active users, 27% of their reviews) is defined by forum games such as the Zaar World Tour: 25% of its reviews contain game markers vs < 1% elsewhere, and 78% of its users played at least once (7–14% elsewhere). Louvain never sees the text.
- **Robust.** The same group reappears with 10 Louvain seeds (Jaccard 0.86–1.00), α = 0.01 / 0.10 (0.76 / 0.84) and three other cores (0.54–0.64), always with 17–27% game reviews vs < 1% elsewhere, although the global partition is only moderately stable (NMI 0.52).
- **Markers are valid.** 20/20 random matches of "tag" were forum games; game reviews peak in May–July (20% of June reviews), matching the Zaar World Tour calendar.
- **Not a cohort, but a cohort exists.** The game community joined at the same time as the mainstream (KS = 0.15). Another community is an early cohort: 80% joined before 2006 vs 35–39%.
- **Text reveals the community.** A classifier predicts a reviewer's community from one review with balanced accuracy 0.48–0.51 (null ≤ 0.27, chance 0.25), also after removing game jargon and proper names and when restricting to shared recipes.
- **Ratings (negative result).** Game reviews get +0.05 stars relative to the same recipe, but the difference disappears within the same user (+0.001, p = 0.92): players are more generous raters, the game context does not inflate ratings.

## Figures

In `figures/`: `fig1_network_vs_game_reviews.png`, `fig2_game_activity_robustness_calendar.png`,
`fig3_text_reveals_community.png`, plus supplementary figures (`supp_*.png`).

## History

The project started from a different approach (ingredient co-occurrence network), kept in `archive/ingredient_network/`
together with the reasons for changing direction.

## Pending

- **Hand labelling** (created by the main notebook, section 10): `hand_labels.csv` (52 reviews, blind; key in
  `hand_labels_key.csv`). Fill `social_game` with 1/0. A second person labelling a copy (`hand_labels_B.csv`) gives Cohen's kappa.
- Paper (PNAS template, Overleaf), three slides and reflection — due Friday 27 November, 23:59.
