# CLAUDE.md

Personal academic website of Osmar Bolivar Rosales (economist; applied AI, econometrics, data science), served by GitHub Pages from `master` at https://osmarbolivar.github.io/. Built with R Markdown as an `rmarkdown` site (`_site.yml`, `output_dir: '.'`), so rendered `.html` files sit next to their sources and are committed. There is no CI: what is pushed to `master` is what goes live, usually within about 2 minutes.

## Rendering

R and pandoc are not on PATH. Use these explicitly, from the repo root:

```bash
export RSTUDIO_PANDOC="C:/Program Files/RStudio/resources/app/bin/quarto/bin/tools"
"/c/Program Files/R/R-4.5.2/bin/x64/Rscript.exe" -e 'rmarkdown::render_site("Papers.Rmd", quiet=TRUE)'
```

- **English R Markdown pages** (`index`, `portfolio`, `Papers`, `About`, `R_poverty`, `R_multipliers`, `R_Fiscal_Multiplier`, `R_built_up`, `R_Education`, `A_early_child`, `D_Macro`, `R_Decompositions`, `R_nl_gdp`): render one file at a time with `rmarkdown::render_site("<page>.Rmd")`. Never run `render_site()` with no argument (see below).
- **Spanish pages** in `es/`: `rmarkdown::render("es/<page>.Rmd")`. They are not part of the rmarkdown site; each carries its full output settings in its YAML, and they share `es/site_libs` (R's HTML tooling will not reference `../site_libs`).
- **Weekly inflation page**: built with Quarto from `R_Week_Inflation.qmd` (`quarto render R_Week_Inflation.qmd`; no R chunks). It writes `R_Week_Inflation_files/`; when Quarto's version changes, remove the old library files that `R_Week_Inflation.html` no longer references.
- After any render, run `git checkout -- site_libs`. Rendering rewrites those files with only line-ending changes.
- A change to a shared include (`include_head.html`, `include_header.html`, `include_footer.html`) or to `_site.yml` requires re-rendering every page that uses it: the 13 English R Markdown pages, the Quarto page and the four `es/` pages. CSS-only changes to `css/site.css` do not need a re-render.
- `kableExtra` is required by `R_Education.Rmd` (installed in the user library).

### Do not render

- `R_Week_Inflation.Rmd`: stale. Rendering it would overwrite the Quarto-built `R_Week_Inflation.html`.
- `NowcastGDP.Rmd`: not part of the site and needs data that isn't in the repo.
- `index.qmd`, `R_Education.qmd`, `R_Fiscal_Multiplier.qmd`, `R_multipliers.qmd`, `R_nl_gdp.qmd`, `R_poverty.qmd`: stale Quarto copies of `.Rmd` pages. They reference the deleted `css/rmarkdown.css`.

## Layout and design

- **One stylesheet**: `css/site.css` holds the whole design (shared with the diesel blog): Source Sans 3 / Source Serif 4, a 740px reading column (`--read`), a 1140px wide container (`--wide`), and color tokens on `:root` that all pass 4.5:1 contrast. Keep new colors passing: `--link #00786f`, `--accent #146eb4`, `--text #40474f`, `--text-soft #5d6570`, `--warm #b35900` (labels only); `--warm-fill #ff9900` is decorative, never text. The Bootstrap 3 "sandstone" theme still loads underneath (needed by rmarkdown); site.css overrides it.
- **Shared includes**: `include_head.html` (fonts, favicon, Search Console verification tag, which must stay), `include_header.html` (sticky header, phone menu, EN/ES switch; its script runs before the page body exists), `include_footer.html` (footer plus article helpers: the "On this page" box on articles with 3+ sections, and tap-to-enlarge figures), `include_header_navpage.html` (hides rmarkdown's auto title). `include_head_quarto.html` is the Quarto page's copy of the head assets.
- **Page types**:
  - **Listing pages** (Home, Portfolio, Papers, About) are raw HTML inside `<!--html_preserve-->`. They remove `standardPadding` and use `.page-title`, `.page-block`, `.wrap-wide`, `.card-grid`/`.card`, `.pub-list`, `.award-grid` and `.logo-row` from site.css.
  - **Research articles** are markdown. Each has a kicker (`.article-kicker`), `# Title`, then a `<p class="dek">` summary, written as `{=html}` raw blocks. Their width comes from `.articleBandContent > .section.level1`.
- **Tables** are real HTML in `<figure class="data-table">` inside `{=html}` blocks, not images. When converting a picture of a table, take the values from its source data and check every one; don't read numbers off the image.
- **Figures**: markdown images carry `{loading="lazy"}`, except the first figure near the top of a page. Quarto figures also need `fig-alt`. CSS sizes figures to the column, so percentage widths in Rmd are ignored.
- **Standalone blog**: `Portfolio/subvencion_diesel_inflacion/diesel_subsidy_pass_through.html` is hand-built, not generated. It has its own inline CSS, Open Graph and JSON-LD metadata, and a responsive Plotly renderer (figure specs live in `window.FIGS`; the phone layout applies below 640px). Edit that HTML directly.

## Content conventions

- **Papers**: `Papers.Rmd` is the single source of the publication list: one `<li class="pub">` per entry, newest first within each section. Tags: `tag` for PDF, `tag lang` for ES, `tag award` for First Place. English and Spanish editions of the same journal article are one entry with a `pub-alt` "Spanish edition" line. `es/Papers.Rmd` reads that list at knit time and translates only the labels, so after editing papers, render `Papers.Rmd` and then `es/Papers.Rmd`.
- **Home "Recent publications"** (`index.Rmd` and `es/index.Rmd`) is a hand-picked short list, kept newest first and chosen by the owner. It is not generated from Papers.
- **Portfolio Articles**: a list of opinion columns (El Deber, Urgente.bo): date, headline exactly as published, outlet, and a one-line summary written for the site, not copied from the article. Strip tracking parameters such as `fbclid` from links.
- **Spanish pages** (`es/`): Home, Portfolio and About are separate translations, so mirror any change to their English counterparts. Research pages stay English-only and get an `EN` tag on Spanish listings. Paper titles, journal names and column headlines are never translated.
- **Bilingual metadata**: `i18n/en_<page>.html` and `i18n/es_<page>.html` hold the canonical and hreflang tags for each page pair, and are loaded through each page's `in_header`. A page's own `in_header` replaces the `_site.yml` list, so include `include_head.html` and `google_analytics.html` in it as well.
- **Link previews**: research pages use `og_tags/<page>.html` (wired through `in_header`). Preview images must stay under about 300 KB, or WhatsApp won't show them; light copies live in `images/og/`.
- **Images**: pages reference lightweight copies (`images/thumbs/`, `images/og/`, compressed figures). Keep full-size originals out of Git. When an original is no longer needed, move it to the Recycle Bin rather than deleting it, since untracked files can't be recovered. The home and About photo is `images/foto_perfil_redondo.webp`.
- **SEO**: `sitemap.xml` (update it when pages are added or removed) and `robots.txt` live at the root. The unlinked Early Childhood stub (`A_early_child.html`) is intentionally left out of the sitemap.
- **Analytics**: every page loads both Google Tag Manager (`GTM-5SMWF48P`, an empty container) and the gtag snippet (`G-F5HJ5VRY62`, which is what actually collects data). The owner decided to keep both as they are; don't remove either.

## Verifying changes

- Preview through a local server, not `file://`: `python -m http.server 8766` from the repo root.
- Check phone layout at 375px and desktop at 1280px. Headless Chrome enforces a minimum window width, so for a true 375px viewport load the page inside a 375px-wide `<iframe>` and screenshot that.
- Lighthouse: `npx -y lighthouse@12 <url>` with `CHROME_PATH` set to Chrome. Performance scores from localhost aren't comparable to GitHub Pages; measure performance on the live site.
- After pushing, poll the live URL until the change appears.

## Working agreements

- Commit and push only when asked. The owner reviews each change first and then says "commit and push".
- Commit messages: a short summary line and a body describing what changed, ending with the co-author trailer.
- Stage explicit paths; leave the owner's untracked local files out of commits.
- The site is in English; the owner writes in English or Spanish.
