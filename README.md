# Airbnb Host Dynamics Analysis

**Author:** Maksimilian Štajer  

## Project Overview

This project investigates Airbnb hosting dynamics, particularly focusing on the comparison between **single hosts** (hosts managing one property) and **multi hosts** (hosts managing multiple properties). It analyzes how the COVID-19 pandemic influenced these dynamics, examines geographical distribution and pricing of listings, and assesses differences in service quality through guest ratings.

## Research Questions

The analysis addresses three primary research questions:

1. **Multi-host Trends:**  
   How has the proportion or number of multi-hosts changed over the years, and did the COVID-19 pandemic impact this trend?

2. **Geographical Distribution and Pricing:**  
   What is the geographical distribution of Airbnb listings managed by single vs. multi hosts, and how do prices differ by neighborhood?

3. **Guest Ratings Comparison:**  
   Is there a statistically significant difference in average guest ratings between single-host and multi-host listings?

## Methodology

### Data Modeling

- **Conceptual Model:**  
  Created an Entity Relationship Diagram (ERD) featuring three entities: `Host`, `Listing`, and `Rating`.

- **Logical Model:**  
  Selected relevant attributes focusing on price, neighborhood, host details, and rating metrics.

- **Physical Model:**  
  Implemented the relational schema with clear foreign key relationships (`Host` → `Listing` → `Rating`).

### Data Cleaning

- Addressed null values, duplicates, and data inconsistencies.
- Standardized pricing data into numeric format.
- Applied pseudonymization to sensitive host and listing information for privacy compliance.

### Data Processing

- Built and populated database tables consistent with the physical ERD.
- Executed SQL queries for detailed data analysis and visualization.

## Key Findings

### Multi-host Dynamics Over Time

- Host registrations peaked around 2015 and subsequently declined.
- Post-2020 registrations show a noticeable increase in the proportion of multi-hosts, suggesting a trend toward professionalization.

### Geographical Distribution and Pricing

- Central, tourist-heavy areas (e.g., Westminster, City of London) exhibit higher proportions of multi-host listings.
- Multi-host listings consistently charge higher prices compared to single-host listings, especially in central neighborhoods.

### Guest Ratings Analysis

- Multi-host listings generally receive more guest reviews.
- Single-host listings score marginally higher on average in categories like overall satisfaction and accuracy, whereas multi-host listings score better for location.

## Conclusion

Airbnb's hosting dynamics reveal a shift towards multi-host operations, indicating growing market concentration and potential implications for urban housing affordability. While multi-hosts dominate tourist-centric areas with premium pricing, service quality ratings between single and multi-hosts show subtle, nuanced differences rather than clear dominance by one group.

## Repository Structure

- **Code:** Database scripts and analysis queries available in the repository root.
- **Data:** Data was downloaded from [InsideAirbnb](https://insideairbnb.com/).
- **Figures:** Visualizations and graphs are located within the `figures/` folder.

---

*This project is for academic research purposes, offering insights into Airbnb’s role in urban real estate markets and hospitality trends.*
