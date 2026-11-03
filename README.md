# CML network dashboard

An interactive map of the staff of the Institute of Environmental Sciences (CML), Leiden University: the Industrial Ecology and Environmental Biology departments. People are shown as dots and grouped in three ways, so you can see who works on what and with which methods.

Open `dashboard.html` in a browser. It is a single self-contained file (data embedded, no server needed).

## What the dashboard shows

Use **Group by** above the map:

| Grouping | How people are grouped |
|---|---|
| **CML themes** | The institute's five research themes: Biodiversity, Future food, Resources, Urban, Water. Theme areas may overlap; a person with several themes sits in the overlap. |
| **Field cluster** | Clusters of people who study similar topics, found from harmonized tags and original keywords. |
| **Method cluster** | Groups of people who use the same methods (life cycle assessment, input-output analysis, material flow analysis, modelling, data science & AI, ...). |

In the cluster groupings a person can belong to several clusters: the fill shows their main cluster, small dots show the others. Hover or click a cluster name on the map to highlight its members. The left panel filters by CML theme, department, harmonized tags, original keywords and free text; the right panel lists the people. The URL hash stores the current view, so **Copy link** shares it.

## How it is built

```
leiden_professors.json              scraped staff profiles (cml_network.ipynb)
        |  classified by department
leiden_professors_classified.json
        |  harmonize.py             keyword canonicalisation + theme assignment
        |  build_dashboard.py       renders dashboard_template.html
dashboard.html                      + tag_report.md, cml-network-site/index.html
```

- **`cml_network.ipynb`**: scrapes the staff pages and classifies people by department.
- **[harmonize.py](harmonize.py)**: three layers. Aliases merge keyword variants and drop generic ones; every canonical keyword maps to one or more fine-grained themes; bio text back-fills people with few keywords. The fine themes are then scored into the five CML themes (`CML_WEIGHTS`, `CML_KEYWORDS`, `CML_TEXT`). Manual corrections live in `CML_OVERRIDES`. Run `python3 harmonize.py` to print every person's assignment and scores.
- **[build_dashboard.py](build_dashboard.py)**: embeds the data in [dashboard_template.html](dashboard_template.html), writes `dashboard.html`, copies it to `cml-network-site/index.html` (the published copy) and writes `tag_report.md` (tag quality checks).
- **[dashboard_template.html](dashboard_template.html)**: the whole front end (plain HTML/JS/SVG, no dependencies). Field names (`FIELD_NAMES`), the method list (`METHODS`) and manual method corrections (`METHOD_OVERRIDES`) are at the top of the clustering code.

### Rebuild

```bash
python3 harmonize.py          # optional: review theme assignments
python3 build_dashboard.py    # regenerate dashboard.html, tag_report.md, cml-network-site/index.html
```

Python 3 standard library only for these two scripts; the notebook additionally needs `requests` and `beautifulsoup4`.

## Adjusting the groupings

- **CML theme of a person**: add or edit an entry in `CML_OVERRIDES` in `harmonize.py`.
- **Methods of a person**: add or edit an entry in `METHOD_OVERRIDES` (first = main method). People whose profile mentions no method get one inferred from their field tags, so check those entries.
- **Field cluster names**: edit `FIELD_NAMES`. Field clusters with fewer than 3 people are merged into their nearest neighbour; method clusters with 1-2 main members likewise.

## Data and licence

The code in this repository is released under the [MIT License](LICENSE).

The staff profile data (`leiden_professors*.json`, `run_results.json` and the data embedded in the dashboard) was collected from public Leiden University staff pages. It remains the property of its original authors and Leiden University and is **not** covered by the MIT licence. Theme and method assignments are automated and partly hand-corrected; they are interpretations, not official affiliations. Check with the people concerned and with Leiden University before publishing the data or dashboard outside the institute.
