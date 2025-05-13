# 🏘️ Airbnb Host Dynamics in London  
**Maksimilian Štajer – Individual Integrative Assignment (IIA)**  

[![SQLite](https://img.shields.io/badge/DB-SQLite-003B57?logo=sqlite&logoColor=white)](https://www.sqlite.org/) 
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

> A data-driven exploration of how **single-listing** and **multi-listing** hosts shape the Airbnb market in London (2008 – 2023) – covering data cleaning, pseudonymisation, relational modelling, SQL analytics, and business insights.

---

## Table of Contents
1. [Project Overview](#project-overview)  
2. [Methodology](#methodology)  
   * [Data Cleaning & Pseudonymisation](#data-cleaning--pseudonymisation)  
   * [Relational Schema](#relational-schema)  
   * [Core Queries](#core-queries)  
3. [Key Findings](#key-findings)
4. [Conclusion](#conclusion)
5. [Repository Structure](#repository-structure)
6. [License](#license)  

---

## Project Overview
Airbnb began as a **sharing-economy** venture but is trending toward a **quasi-hotel** model run by professional multi-hosts.  
This project  

* builds a clean, pseudonymised **SQLite** database for London listings (2008-2023);  
* defines **Host**, **Listing**, and **Rating** entities with full referential integrity;  
* answers three research questions  
  1. How has the share of multi-hosts evolved over time (and around COVID-19)?  
  2. How are listings geographically distributed by host type and price?  
  3. Do single- and multi-host listings differ in customer ratings?  
* provides a full write-up in `Štajer_Maksimilian_682271_report.pdf`.

---

## Methodology
### Data Cleaning & Pseudonymisation
* Removed incomplete rows, normalised `price` to `REAL`, and filled rating subscores with the overall rating when missing.  
* Replaced personally identifying **host_id** and **listing_id** with pseudonyms:

    ```sql
    -- Host-ID remap (excerpt)
    INSERT INTO Mapping_host_id(old_host_id)
    SELECT DISTINCT host_id FROM listings;

    UPDATE Mapping_host_id
       SET new_host_id = hex(randomblob(16));
    ```

  Complete, reproducible steps are documented in *Task 3* (cleaning) and *Task 4* (processing) of the report.

### Relational Schema

- **Conceptual Model:**  
  Created an Entity Relationship Diagram (ERD) featuring three entities: `Host`, `Listing`, and `Rating`.

- **Logical Model:**  
  Selected relevant attributes focusing on price, neighborhood, host details, and rating metrics.

- **Physical Model:**  
  Implemented the relational schema with clear foreign key relationships (`Host` → `Listing` → `Rating`).

### Core Queries

#### Q1 - Temporal evolution
```sql
SELECT strftime('%Y', host_since) AS year,
       SUM(calculated_host_listings_count = 1)  AS single_hosts,
       SUM(calculated_host_listings_count > 1)  AS multi_hosts,
       ROUND(1.0 * multi_hosts / COUNT(*), 2)   AS multi_share
FROM Host
GROUP BY year;
```
#### Q2 - Top neighbourhoods (multi-host share, volume, price)
```sql
WITH RankedNeighbourhoods AS (
    SELECT l.neighbourhood_cleansed,
           COUNT(*)                                           AS total_listings,
           SUM(h.calculated_host_listings_count = 1)          AS single_host_listings,
           SUM(h.calculated_host_listings_count > 1)          AS multi_host_listings,
           ROUND(
               1.0 * SUM(h.calculated_host_listings_count > 1) / COUNT(*),
               2
           )                                                  AS multi_host_prop,
           RANK() OVER (ORDER BY multi_host_prop DESC)        AS rnk
    FROM   Listing l
    JOIN   Host    h USING (host_id)
    GROUP  BY l.neighbourhood_cleansed
)
SELECT *
FROM   RankedNeighbourhoods
WHERE  rnk <= 5;
```

#### Q3 - Rating comparison (single- vs. multi-host)
```sql
SELECT
    AVG(CASE WHEN h.calculated_host_listings_count = 1
             THEN r.review_scores_rating END)                 AS avg_rating_single,
    AVG(CASE WHEN h.calculated_host_listings_count > 1
             THEN r.review_scores_rating END)                 AS avg_rating_multi,

    AVG(CASE WHEN h.calculated_host_listings_count = 1
             THEN r.review_scores_accuracy END)               AS avg_accuracy_single,
    AVG(CASE WHEN h.calculated_host_listings_count > 1
             THEN r.review_scores_accuracy END)               AS avg_accuracy_multi,

    AVG(CASE WHEN h.calculated_host_listings_count = 1
             THEN r.review_scores_value END)                  AS avg_value_single,
    AVG(CASE WHEN h.calculated_host_listings_count > 1
             THEN r.review_scores_value END)                  AS avg_value_multi
FROM   Listing l
JOIN   Host    h USING (host_id)
LEFT   JOIN Rating r USING (listing_id);

```

---

## Key Findings
| Research question | Insight |
|-------------------|---------|
| **Temporal trend** | Multi-host registrations rose from **17 % → 26 %** of new hosts after 2020. |
| **Geographical pattern** | Central boroughs (Westminster, City of London, Kensington & Chelsea) show ≥ **64 %** multi-host saturation *and* the **highest prices**. |
| **Price gap** | Multi-host listings are consistently **more expensive** – e.g. in Westminster the average single-host price is **£209** vs **£391** for multi-hosts. |
| **Ratings** | Differences are marginal: single hosts edge ahead in **accuracy** and **value**, while multi-hosts slightly win on **location**; overall rating gap is **0.06 / 5**. |

*(Detailed statistics, tables, and plots are available in the PDF report.)*

## Conclusion

Airbnb's hosting dynamics reveal a shift towards multi-host operations, indicating growing market concentration and potential implications for urban housing affordability. While multi-hosts dominate tourist-centric areas with premium pricing, service quality ratings between single and multi-hosts show subtle, nuanced differences rather than clear dominance by one group.

## Repository Structure

- **Code:** Database scripts and analysis queries available in the repository root.
- **Data:** Data was downloaded from [InsideAirbnb](https://insideairbnb.com/).
- **Figures:** Visualizations and graphs are located within the `figures/` folder.

---

*This project is for academic research purposes, offering insights into Airbnb’s role in urban real estate markets and hospitality trends.*

---

## License
Released under the **MIT License** – see [`LICENSE`](LICENSE).
