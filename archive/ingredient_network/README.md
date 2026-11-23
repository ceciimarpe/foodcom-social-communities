# Archive: first approach (ingredient co-occurrence network)

First version of the project, kept for reference. It builds a network of the 500 most frequent ingredients
(edge = co-occurrence in ≥ 5 recipes), detects three Louvain communities (Savory, Baking, Asian), compares the
vocabulary of cooking steps with TF-IDF, and looks at bridge ingredients (betweenness) and ratings (ANOVA, VADER).

Files: `project_analysis.ipynb` (analysis), `paper.tex` + `references.bib` (PNAS draft), `fig*.png/pdf` (figures).

**Why we moved on.** Reviewing it against the course requirements (Week 8) we found issues the main analysis fixes:
- The modularity null shuffles community labels on a fixed graph; the course asks for Louvain on
  degree-preserving rewired networks (Week 4). Figure 1 reports z = −13.3 while the text reports z = +60.
- The TF-IDF result is partly circular: communities are defined by ingredients and the cooking steps name the same
  ingredients; with three concatenated documents IDF is almost uninformative.
- A global co-occurrence threshold is used instead of a backbone method (disparity filter, Week 4), giving a very
  dense network (density 0.38).

The dataset and the starting pipeline were the basis of the main analysis, which looks instead at the
**user–user co-review network and the review text** (see the repository README).
