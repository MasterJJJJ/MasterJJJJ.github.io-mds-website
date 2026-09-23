# Jerry Jin's MDS website

This Quarto website contains my MDS blog and computational posts comparing recorded club goals for Cristiano Ronaldo and Lionel Messi. The Python, R, and mixed-language bonus posts execute code when the site is rendered.

## Prerequisites

Install Git, [Quarto](https://quarto.org/docs/get-started/), [uv](https://docs.astral.sh/uv/getting-started/installation/), and [R](https://cran.r-project.org/) before running the commands below. Make sure `git`, `quarto`, `uv`, and `Rscript` are available in your terminal.

The site was built on macOS (Apple Silicon) with:

| Tool | Version used |
| --- | --- |
| Quarto | 1.10.18 |
| uv | 0.12.5 |
| R | 4.5.2 |
| Python | 3.14.7 |
| renv | 1.2.4 |

Use R 4.5.2 to match `renv.lock`. Python's minor version is pinned to 3.14 in `.python-version`; uv installs a compatible interpreter if needed, so its patch version may differ from the version above. Python package versions are locked in `uv.lock`, and R package versions are locked in `renv.lock`. You do not need to install renv manually: the project's `.Rprofile` and `renv/activate.R` bootstrap it.

## Build from a clean clone

Run the following commands in a terminal (macOS/Linux shell), starting in the directory where you want to keep the project. No RStudio or Positron session is required.

```bash
git clone https://github.com/MasterJJJJ/MasterJJJJ.github.io-mds-website.git
cd MasterJJJJ.github.io-mds-website
uv sync --locked
Rscript -e 'renv::restore(prompt = FALSE)'
uv run --locked quarto render
```

Run every command after `cd` from this repository's root, where `_quarto.yml` lives. `uv sync --locked` creates `.venv` using the committed lockfile. `Rscript` starts R in the project, automatically activates renv, and restores the project R library. The final command renders all pages using these environments; do not substitute a plain `quarto render`, which may select another Python environment.

The equivalent restore command, if using an R console instead of `Rscript`, is `renv::restore(prompt = FALSE)`, with the working directory set to the repository root.

## View the site locally

The complete rendered website is written to `docs/`, with its home page at `docs/index.html`. To serve that built output, run this command from the repository root:

```bash
uv run --locked python -m http.server 8000 --directory docs
```

Open <http://localhost:8000> in your browser. Stop the server with Ctrl+C. To rebuild after editing a post, run `uv run --locked quarto render` again, then refresh the browser.

## Data and network requirements

All three analyses read `data/mds521_cr7_lm10.csv`, included in this repository (about 164 KB). From each post's folder, code uses the relative path `../../data/mds521_cr7_lm10.csv` because Quarto executes chunks in the folder containing the post.

The source is [Lionel Messi vs Cristiano Ronaldo | Club Goals](https://www.kaggle.com/datasets/azminetoushikwasi/lionel-messi-vs-cristiano-ronaldo-club-goals), compiled by Azmine Toushik Wasi on Kaggle. The database is licensed under [ODbL 1.0](https://opendatacommons.org/licenses/odbl/1-0/), and its contents under [DbCL 1.0](https://opendatacommons.org/licenses/dbcl/1-0/). The included data retains these licences; any adapted database published with these analyses is also available under ODbL 1.0. All three posts include source and licence attribution.

The CSV is a historical snapshot with records ending in March 2023, not current career totals. No Kaggle login, API key, or data download is needed to render the posts.

Internet access is required initially to clone the repository, download Python if needed, bootstrap renv, and restore Python/R packages. Once the environments are restored, the analysis reads local data and does not fetch data from the network.

## Environment files

Keep `pyproject.toml`, `uv.lock`, `.python-version`, `renv.lock`, `.Rprofile`, `renv/activate.R`, and `renv/settings.json` in version control. Do not commit `.venv/` or `renv/library/`; those directories are recreated by the build instructions above.

## R and Python bonus post

`posts/r-and-python/index.qmd` uses the knitr engine and reticulate, included in `renv.lock`. Python creates annual goal counts, R receives them through `py$annual_counts` and calculates differences, and Python uses R's results through `r.ronaldo_years`, `r.messi_years`, and `r.tied_years`. Assertions check the counts and the transfer in both directions.

The setup chunk points reticulate to this clone's `.venv/bin/python` before starting Python. The existing restore and render commands above build this post too; there is no separate environment or additional manual setup. These instructions target macOS/Linux.

After rendering, view the bonus post at <http://localhost:8000/posts/r-and-python/> while the local server is running. For the Gradescope PDF, include the bonus post's direct published URL as well as the repository and website URLs, and name the one assignment that should receive all five bonus marks (for example, "Apply the bonus to Milestone 3.").

## Build verification

On September 23, 2026, these restore and render commands passed in a fresh local Git clone of commit `2e6374f`, starting without `.venv/`, `renv/library/`, or `.quarto/`. renv bootstrapped successfully, both posts executed all four code chunks, and all six website pages rendered into `docs/`. The local server command returned HTTP 200 for the home page. Existing machine-level package caches were available during restoration; this was not a test on a machine with empty caches or a verification of the remote GitHub repository.
