# India Junior Salary Gap: Nominal vs PPP

A Python analysis testing whether Indians are paid less than workers in other countries at the junior level, and how much of pay depends on seniority and skills. The comparison is done in nominal USD and again after adjusting for cost of living (PPP).

## Research question

> Are Indians paid less at the junior starting level than workers in other countries, and does pay depend on skills and seniority?

Follow-up questions explored:

- Does the gap shrink once cost of living (PPP) is accounted for?
- Does a country's total GDP explain pay levels?
- Where does ₹15 lakh sit in India's pay distribution, and what does it buy in purchasing-power terms?

## Key findings

| Question | Result |
|---|---|
| Junior median pay, India vs US (nominal) | **$3,488 vs $41,600**, India is about 8% of the US |
| Same comparison after PPP adjustment | **$14,668 vs $41,600**, India is about 35% of the US |
| Statistically significant? | Yes, Mann-Whitney p ≈ 0, in nominal and PPP terms |
| Same role family, junior | India earns 5-11% of US juniors nominally, 22-48% in PPP |
| Gap after controlling for seniority, role, skill count | India pays about **89% less** (nominal), about **57% less** (PPP) |
| Effect of seniority | About **+15% pay per seniority step** (r = 0.54 India, 0.48 US) |
| Effect of skills | Number of skills adds only about **+0.4% each**; which skills you have matters more (Java, Azure, AWS roles pay about 3x customer-service roles in India) |
| What drives pay most (random forest) | Country 78%, seniority 14%, skill count 2.5% |
| Country GDP vs median pay | Weak and slightly negative (Spearman r = -0.24, n = 15). Total GDP reflects population, not pay |
| ₹15 lakh in India | Only **19.6%** of Indian postings pay this; it is about $73,000 in PPP terms |
| Purchasing-power match to a US worker | About **₹8.5 lakh** matches the US junior median; about **₹12.3 lakh** matches the US overall median |

**Conclusion:** The hypothesis holds. Indian juniors are paid much less, the gap shrinks but stays large after PPP adjustment, and country explains far more of the difference than skills or seniority.

## Datasets

| File | Description | Rows |
|---|---|---|
| `skill_salary_clean.csv` | Global job-posting salaries by country, role family, seniority and skills (USD and local currency) | 100,060 |
| `india_salary_clean.csv` | India job-posting salaries by experience band and city (INR) | 12,958 |
| `economic_indicators_clean.csv` | World Bank GDP (current US$), 1960-2025, by country | 266 |

PPP conversion factors (LCU per international $, 2024) come from the World Bank indicator `PA.NUS.PPP` and are hardcoded in `PPP_analysis.ipynb`.

> Check each source's licence before committing raw data to a public repo. The salary files include profile URL columns.

## Notebooks

| Notebook | Purpose |
|---|---|
| `File_cleaning.ipynb` | Loads the three raw files, tidies headers, trims text, drops duplicates and blank rows, and writes the `*_clean.csv` files |
| `Salary_analysis.ipynb` | Main analysis: junior pay by country (mean, median, mode), hypothesis test, role-controlled comparison, seniority progression, correlations, regression and random forest, GDP comparison, the ₹15 lakh analysis |
| `PPP_analysis.ipynb` | Repeats the comparison in PPP dollars: country table, hypothesis test, role-family and seniority comparison, adjusted regression, purchasing-power match |

## Methods

- **Junior definition:** seniority is `entry` or `intern`.
- **Statistics:** mean, median and mode (mode on salary rounded to the nearest $1,000), Mann-Whitney U (non-parametric, salaries are skewed), bootstrap confidence interval for the median ratio, Spearman correlation.
- **Models:** linear regression and random forest on log salary with an India flag, seniority rank, number of skills and role family.
- **PPP adjustment:** PPP dollars = salary in local currency / PPP factor. A sanity check confirms implied market exchange rates and price levels, for example India's price level is about 0.24 of the US.

## Setup

```bash
git clone https://github.com/<your-username>/india-junior-salary-gap-ppp.git
cd india-junior-salary-gap-ppp
pip install pandas numpy scipy scikit-learn matplotlib jupyter
```

Put the raw CSVs in `data/raw/`, run `File_cleaning.ipynb` to produce the clean files in `data/`, then run `Salary_analysis.ipynb` and `PPP_analysis.ipynb`. Charts are saved to `out/`.

## Limitations

- **Missing seniority:** about 62% of global rows have no seniority label, so junior results cover labelled rows only.
- **Small samples:** Germany, the UK, France and the Netherlands have fewer than 60 junior postings each. Treat their figures as indicative.
- **Skills coverage:** only India, the US and Singapore have skills listed, so the skills analysis cannot be extended to other countries.
- **Hourly pay:** US and Canada intern pay looks annualised from hourly rates, so use the entry-level comparison instead of intern.
- **Floor on posted pay:** very low-wage US jobs appear to be missing, which flatters the US comparison.
- **PPP is national average:** it uses a whole-economy basket. Large Indian metros cost more than the national average.
- **"Comfortable living" is not measured directly:** the purchasing-power match benchmarks India against typical US and Canadian workers.

## Possible extensions

- Tech-role destination comparison (for example Germany), which needs more tech postings than the current data has.
- Add take-home pay after tax and a cost-of-living index for individual cities.
- Per-capita GDP using World Bank population data.

## Author

Jahaan Kachwala
