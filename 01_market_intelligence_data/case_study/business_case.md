# Business Case, Case Study & Power BI Specification
## Market Intelligence Data Platform

## 1. Document purpose

Define the business narrative, public-demo use case, analytical scope, dashboard design, KPIs, and initial DAX measures for the Market Intelligence Data Platform. Power BI consumes curated Analytics-layer views from the Data Warehouse; it should not independently reimplement ingestion or data processing logic.

## 2. Product definition

The Market Intelligence Data Platform is a configurable labor-market intelligence pipeline. It collects job-posting data based on search criteria defined in YAML configuration, processes and normalizes the postings, stores them in a warehouse, and exposes analytical datasets for exploring labor-market demand.

The platform is **not hardcoded to one profession, one candidate profile, one country, or one skills list**. The configuration determines the search scope: role/query groups, markets, languages, geography, remote options, and skill taxonomy. The same pipeline can support a personal job-search scenario or a public demonstration of a different labor market.

## 3. Business Case

### Problem
Labor-market information is distributed across job boards and postings, frequently repetitive, inconsistent, and difficult to compare at scale. A person, analyst, recruiter, or business stakeholder may struggle to understand what roles are being requested, which skills recur, where opportunities are concentrated, how demand changes, and what requirements appear together.

### Opportunity
Create a repeatable data pipeline that turns configured job-search results into structured and comparable market signals. The platform can be reconfigured to investigate different roles, markets, geographies, or skill sets without redesigning the processing and analytics layers for each scenario.

### Value proposition
- Consolidate job-posting observations into a reusable analytical dataset.
- Explore role, skill, company, geography, seniority, work mode, and time patterns.
- Identify frequently requested skills and combinations of requirements.
- Compare markets and role groups using consistent definitions.
- Support evidence-based career research, talent analysis, and market exploration.
- Demonstrate an end-to-end data engineering and analytics workflow with reproducible stages and quality gates.

### Intended users
- Job seekers researching roles and skill requirements.
- Career-transitioners and students exploring market demand.
- Recruiters and talent teams exploring role requirements.
- Analysts and business stakeholders investigating workforce trends.
- Portfolio reviewers evaluating the data platform and analytical product.

## 4. Case Study

### Scenario
A user wants to investigate a labor market—for example, Customer Service & Operations in Peru—or switch the configuration to Data Analytics, Business Intelligence, SQL, another country, or another set of search terms. The user configures the search scope in YAML. The platform executes the same pipeline and creates curated warehouse/analytics outputs. Power BI then lets the user explore the resulting market without requiring a dashboard redesign for every search scenario.

### Case-study question
**What does the configured job market currently demand, where is that demand concentrated, and which skills, roles, companies, and work arrangements appear most often?**

### Demonstration principle
The public demo should show the system's capability, not the author's personal career profile. The selected demo market and configuration are examples. The dashboard must be driven by the dataset and configuration metadata where available, so it remains meaningful when the YAML search scope changes.

## 5. Repository and project status

The public repository is the implementation reference for this case study. The user reports that the public version currently passes its test suite. That status is a project-provided statement and should be linked to the repository and test evidence in the final portfolio publication.

**Public repository:** [Add repository URL]

**Test status/evidence:** [Add CI badge, test command, or passing-run link]

This BI specification is a downstream design document. It does not claim that the Power BI report or all DAX measures have already been implemented or tested.

## 6. End-to-end architecture and BI boundary

1. **Configuration (YAML):** defines search groups, query terms, markets, localization parameters, remote filters where supported, and skill taxonomy.
2. **Ingestion:** retrieves postings from configured sources and stores raw responses for traceability.
3. **Processing:** normalizes postings, derives structured attributes, extracts/normalizes skills, and produces staged data.
4. **Warehouse:** loads dimensions and fact observations, maintaining relationships and snapshot/deduplication rules.
5. **Data Quality:** checks required values, duplicates, referential integrity, domain consistency, and coverage.
6. **Analytics layer:** provides curated views designed for reporting and analysis.
7. **Power BI:** imports or connects to Analytics views, builds the semantic model, measures, visuals, filters, and report navigation.

**BI responsibility:** consume trusted Analytics outputs, model relationships, calculate report-level measures, provide interactive exploration, and communicate findings. Avoid repeating source extraction, normalization, deduplication, or business logic that belongs upstream.

## 7. Reporting principles for a configurable platform

- Do not assume the market is always Data Analyst or Customer Service.
- Use configured role groups and markets as slicer dimensions whenever the Analytics layer exposes them.
- Display the selected search scope in report titles/subtitles or a configuration summary.
- If the dataset does not contain configuration metadata (run ID, search group, market, query, config snapshot), consider adding it upstream before building a highly configurable report. Do not infer the scope from job titles alone.
- Use consistent definitions for posting counts, unique jobs, companies, skills, and dates.
- Distinguish posting observations/snapshots from unique job listings. A fact table at snapshot grain can count the same listing multiple times over time.
- Label dates and metrics clearly: postings observed, active listings, first-seen postings, or snapshot observations are not interchangeable.
- Show data freshness and the selected dataset's date range.
- Respect Data Quality results and avoid presenting a metric if its underlying data is incomplete or invalid without a warning.

## 8. Suggested Power BI report structure

### Page 1 — Market Overview
**Goal:** provide a fast executive overview of the selected market.

Suggested visuals:
- KPI cards: posting observations, unique postings (if available), companies, distinct skills, latest data date.
- Trend line: posting volume by date/week/month, with the date grain selectable or appropriate to dataset volume.
- Bar chart: postings by role/category.
- Map or ranked bar: postings by location/region (use a map only if location data is sufficiently reliable).
- Donut or stacked bar: remote/hybrid/on-site distribution, including an Unknown category if data is incomplete.
- Scope panel: selected market(s), search groups/configuration, date window, and refresh timestamp.

### Page 2 — Roles & Demand
**Goal:** explore which roles are present and how demand differs.

Suggested visuals:
- Ranked role/title list by posting count.
- Posting trend by selected role or role group.
- Company count by role.
- Seniority distribution, if reliably populated.
- Work mode and location breakdown for selected role.
- Drill-through from role to posting detail table.

### Page 3 — Skills Intelligence
**Goal:** understand what skills employers request.

Suggested visuals:
- Top skills ranked by number of postings mentioning each skill.
- Skill category breakdown.
- Skill frequency over time.
- Skills by role/group (matrix or heatmap-style matrix).
- Skill co-occurrence analysis, if the Analytics layer provides a posting-skill bridge or equivalent.
- Posting detail table with title, company, date, location, and skill list.

Important: Skill counts should count distinct postings that mention a skill, not raw skill rows, unless the measure is explicitly named as skill mentions.

### Page 4 — Companies & Geography
**Goal:** identify where opportunities are concentrated.

Suggested visuals:
- Companies ranked by posting volume.
- Postings by country, region, and city.
- Role mix by company.
- Skills associated with selected companies.
- Remote/hybrid/on-site breakdown by geography.

### Page 5 — Work Mode & Requirements (optional, based on available data)
**Goal:** compare work arrangements and posting attributes.

Suggested visuals:
- Remote, hybrid, on-site, unknown distribution.
- Work mode by role and market.
- Seniority, experience, education, language, or contract requirements where those attributes are reliably extracted.
- Filters for role, location, company, and date.

### Page 6 — Job Explorer / Detail
**Goal:** provide a searchable table for individual postings.

Suggested fields:
- Job title and normalized role/group.
- Company.
- Location and market.
- Work mode.
- Posted/observed date (label precisely).
- Source.
- Skills/requirements.
- Original posting link, if stored and valid.

Use this page for detailed exploration, not as the primary overview page.

### Page 7 — Data Quality & Coverage (recommended for technical/portfolio transparency)
**Goal:** show the coverage and limitations of the dataset.

Suggested indicators:
- Data freshness and date range.
- Number of rows and unique job IDs (where available).
- Missingness rate for company, location, date, work mode, role, and skills.
- Duplicate rate or duplicate-control result.
- Source/market/query coverage.
- Phase/test status summary, if these results are available in a reportable format.

## 9. Core KPI definitions

Confirm the exact table grain and fields in the Analytics views before implementing these definitions.

| KPI | Definition | Caveat |
|---|---|---|
| Posting Observations | Count of fact rows at observation/snapshot grain | Can include repeat observations of the same listing |
| Unique Job Listings | Distinct count of stable job/listing identifier | Only valid if stable identifier exists and is reliable |
| Companies | Distinct count of normalized company identifier/name | Name normalization affects this metric |
| Skills | Distinct count of normalized skill values present in the selected scope | Taxonomy and extraction coverage influence it |
| Skill Demand | Distinct postings that mention a skill | Avoid counting repeated skill rows as separate jobs |
| Market Share by Role | Selected role's postings divided by postings in current filter context | Define denominator and filter behavior explicitly |
| Remote Share | Remote postings divided by postings with known work mode | Decide whether Unknown is excluded or shown in denominator |
| Posting Trend | Count of postings by posting/first-seen/observation date | Select one date meaning and label it clearly |
| Data Freshness | Days/hours since most recent successful data date | Based on dataset timestamp, not report refresh alone |

## 10. Initial DAX measure catalog

These are starting templates. Replace table and column names with the actual Analytics model names and validate against table grain. Example names assume a fact table called `FactJobPosting` and dimensions/bridge tables as described below.


### Base measures

```DAX
Posting Observations =
COUNTROWS ( FactJobPosting )

Unique Job Listings =
DISTINCTCOUNT ( FactJobPosting[JobListingKey] )

Companies =
DISTINCTCOUNT ( FactJobPosting[CompanyKey] )
```

If the fact is at snapshot grain, `Posting Observations` is intentionally not the same as `Unique Job Listings`.

### Skill measures (assuming a posting-skill bridge)

```DAX
Skill Mentions =
COUNTROWS ( FactPostingSkill )

Jobs With Skill =
DISTINCTCOUNT ( FactPostingSkill[JobListingKey] )

Distinct Skills =
DISTINCTCOUNT ( DimSkill[SkillKey] )
```

Use `Jobs With Skill` for skill ranking, not `Skill Mentions`, unless the analysis explicitly concerns the number of extracted skill occurrences. If the bridge links skills to snapshots instead of stable job listings, use the appropriate observation key and name the measure accordingly.

### Remote share (adjust categories and denominator after inspecting the model)

```DAX
Known Work Mode Postings =
CALCULATE (
    [Posting Observations],
    DimWorkMode[WorkMode] <> "Unknown"
)

Remote Postings =
CALCULATE (
    [Posting Observations],
    DimWorkMode[WorkMode] = "Remote"
)

Remote Share =
DIVIDE ( [Remote Postings], [Known Work Mode Postings] )
```

This definition excludes Unknown work mode from the denominator. If the business wants all postings as the denominator, create and label a separate measure.

### Role share

```DAX
Role Share =
DIVIDE (
    [Posting Observations],
    CALCULATE (
        [Posting Observations],
        REMOVEFILTERS ( DimRole )
    )
)
```

Use this measure in a role visual; verify that it removes only role filters and retains market, date, company, and other desired filters.

### Posting trend

Use a proper calendar dimension related to the fact's selected date field. The measure itself can remain:

```DAX
Posting Trend =
[Posting Observations]
```
Configure the visual with the Calendar date/month axis. Confirm whether the relationship uses posted date, first-seen date, or observation date; avoid mixing meanings.

### Latest data date / freshness

```DAX
Latest Data Date =
MAX ( FactJobPosting[ObservationDate] )

Data Age (Days) =
DATEDIFF ( [Latest Data Date], TODAY(), DAY )
```
These examples assume `ObservationDate` is the correct refresh/observation timestamp. Prefer a dedicated dataset refresh/ingestion metadata table if available; report refresh time is not necessarily the source data's latest date.

## 11. Suggested semantic model (verify against Analytics outputs)

A star-schema-like model is preferred for Power BI, but use the curated Analytics view structure if it already provides a suitable model.


- **FactJobPosting / FactJobObservation:** posting observation fact at its documented grain.
- **DimDate:** calendar dimension.
- **DimRole:** normalized role and role group.
- **DimCompany:** normalized company.
- **DimLocation / DimMarket:** geography and configured market.
- **DimWorkMode:** remote/hybrid/on-site/unknown.
- **DimSkill:** normalized skill taxonomy.
- **FactPostingSkill / BridgePostingSkill:** many-to-many posting-to-skill relationship.
- **Run/config metadata (recommended):** run ID, source, market, search group, query/config identifier, ingestion timestamp, config version or captured configuration snapshot.

Relationships should be documented and preferably use surrogate keys. Avoid ambiguous many-to-many relationships; use a bridge for skills and test filter propagation. Hide technical keys from report users where appropriate.

## 12. Filters and interactions

Global filters where supported:
- Date range.
- Market/country/region.
- Role group and normalized role.
- Company.
- Work mode.
- Source.
- Search group/config/run (especially important for multi-config datasets).
- Skill category and skill.

Interactions:
- Cross-filter between visuals where it helps exploration.
- Drill-through from role, company, geography, or skill to posting details.
- Tooltip pages with additional context, not duplicate chart clutter.
- Reset filters/bookmark to return to the market overview.
- Titles that reflect selected scope, without implying a universal market if the report only covers configured searches.

## 13. Data prerequisites / questions to confirm in Analytics layer

Before building DAX, inspect the Analytics views and confirm:
1. Fact grain: one row per posting, per observation, per snapshot, or another grain?
2. Stable job/listing ID exists? Is it consistent across refreshes?
3. Which date fields exist and what each means (posting date, first seen, observation, ingestion)?
4. Is a posting-to-skill bridge available? What is its key/grain?
5. Are role groups, markets, search groups, source, and run/config metadata retained?
6. Are company and location normalized dimensions available?
7. How is work mode classified and how are unknown values represented?
8. Are seniority, experience, education, language, salary, or contract type populated sufficiently for analysis?
9. Are source URLs stored and safe/valid for drill-through?
10. Which quality metrics/results can be exposed to the BI layer?

Do not write measures against guessed fields. Resolve these questions by inspecting the actual Analytics views/schema and sample records.

## 14. Build sequence for Power BI

**BI Phase A — Analytics contract review**
- Inventory views, columns, keys, grain, and example rows.
- Agree on metric definitions and date semantics.
- Confirm config/run metadata requirements.

**BI Phase B — Semantic model**
- Connect to curated Analytics outputs.
- Set data types, relationships, date table, and hide technical fields.
- Test filter propagation and bridge behavior.

**BI Phase C — DAX measures**
- Build and validate base counts first.
- Then unique listing, company, skill, shares, trends, freshness, and quality measures.
- Reconcile measures with SQL queries against the source views.

**BI Phase D — Report pages**
- Build Market Overview first.
- Add Role Demand and Skills Intelligence.
- Add Companies & Geography and Job Explorer.
- Add Data Quality/Coverage page for transparency.

**BI Phase E — QA and publication**
- Verify totals against SQL.
- Test slicers, drill-through, date ranges, empty states, and alternate YAML markets.
- Check titles and explanatory labels.
- Confirm no personal-profile assumptions are embedded in the public demo.
- Document refresh, dataset limitations, and repository/test links.

## 15. Acceptance criteria for the BI deliverable

- Dashboard works with the public demo dataset and is not hardcoded to the author's personal job profile.
- Configured market/role context is visible and filters work when corresponding metadata is present.
- KPI definitions are documented and reconcile to SQL.
- Posting observations are distinguished from unique listings.
- Skills are counted at the correct grain (distinct postings per skill where appropriate).
- Date trends use an explicitly defined date field.
- Unknown/missing data is handled transparently.
- Main pages support coherent cross-filtering and drill-through.
- Report titles, labels, and summaries do not overclaim market coverage beyond configured search scope.
- BI QA is documented independently of the repository's pipeline test status.

## 16. Portfolio storytelling outline

1. **Challenge:** labor-market signals are scattered across individual job postings and hard to compare.
2. **Approach:** configurable searches feed a repeatable pipeline; raw data is preserved, cleaned, modeled, and quality-checked.
3. **Data product:** the Warehouse and Analytics layer make postings comparable across role, skill, company, location, work mode, and time.
4. **BI outcome:** Power BI provides an interactive market explorer to reveal demand patterns and support research.
5. **Engineering:** phased pipeline, test gates, reproducibility, configuration-driven scope, and documented quality controls.
6. **Demo disclaimer:** results represent the searches, sources, markets, and time window configured for that dataset; they are not a census of every job opening in the labor market.

## 17. Final definition

Power BI is the exploratory and storytelling layer of the Market Intelligence Data Platform. It consumes trusted Analytics views and lets users investigate a configurable job market through consistent KPIs and interactions. The public demo should prove that the same platform can be reconfigured for different market questions, while the original personal job-search use case remains one possible application—not the definition or limitation of the product.