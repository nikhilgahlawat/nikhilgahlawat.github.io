---
layout: post
title: How to Analyze H-1B Data
truncated_preview: true
excerpt_separator: <!--more-->
---

*The data sources, cleaning process, and methods used to analyze H-1B data. The full analysis appears in the [main story](https://www.nikhilgahlawat.com/projects/h1b-tech/), and code is available on [Github](https://github.com/nikhilgahlawat/immigration-h1b/).*

<!--more-->

-----

The U.S. technology sector is increasingly relying on the H-1B visa \- a shift away from the IT and professional services firms that had previously dominated the program. The outsourcing business model advanced by these companies \- contracting foreign labor to clients and, in many cases, offshoring jobs \- has shaped public perception of the H-1B and fueled bipartisan criticism.

[New analysis](https://www.nikhilgahlawat.com/projects/h1b-tech/) of data from the Department of Labor and U.S. Citizenship and Immigration Services sheds light on the current make-up of H-1B employers and how the shift toward tech is unfolding. Among the key findings are:

* Over the 15 year period from 2009 to 2023, total visa approvals for big tech companies rose over 400%.  
* Visa denial rates for outsourcing companies peaked at 24% in 2019, up from under 5% in earlier years.  
* Denial rates for tech companies remained consistently below 5% over the past 15 years.  
* Since 2015, 20 major tech companies have formally declared “H-1B dependency” at least once, meaning at least 15% of their workforce holds an H-1B visa.  
* Semiconductor companies are among the most reliant on H-1Bs: Ten of the largest have declared H-1B dependence at least once since 2015\. Nvidia, the largest, has been dependent every year since 2018\.  
* Median H-1B wages at big tech firms are 60% higher than those at outsourcing firms, and tech wages have grown faster in the past 15 years.

Much of the existing research on H-1B visas has focused on [wages](https://nfap.com/wp-content/uploads/2020/10/Analysis-of-DOL-H-1B-Wage-Rule.NFAP-Policy-Brief.October-2020.pdf), [outsourcing companies](https://www.epi.org/blog/the-biden-administration-can-stop-h-1b-visas-from-fueling-outsourcing-half-of-the-top-30-h-1b-employers-were-outsourcing-firms-in-2021/), or the effects of the [annual visa cap](https://www.cato.org/blog/h-1b-lottery-results-announced-3/4-rejected). Fewer have examined how the tech industry’s use of the program is evolving \- especially sub-industries like AI. This project aims to help fill that gap and contribute to understanding where the H-1B system may be headed.

### H-1B visa data sources

Employers seeking to hire foreign workers through the H-1B visa program interact with two federal agencies: the Department of Labor (DOL) and U.S. Citizenship and Immigration Services (USCIS).

The first step is to submit a Labor Condition Application (LCA) to the Department of Labor.[^fn-DOL-form] The LCA contains details such as the type of visa, the job title, wage, work location, and number of positions.[^fn-DOL] While filing an LCA doesn’t guarantee that the employer will ultimately seek or receive an H-1B visa, it provides key information \- particularly around wages and occupations. The DOL publishes this data on its [Foreign Labor Certification Performance Data](https://www.dol.gov/agencies/eta/foreign-labor/performance) site.

Once the LCA is certified, the employer can submit an H-1B petition to the USCIS \- one petition per worker.[^fn-USCIS-form] The USCIS caps the total number of new visas at 85,000 per year. When applications surpass that cap \- as they have every year since 2014 \- USCIS runs a lottery to determine which petitions are selected. The [USCIS H-1B Employer Data Hub](https://www.uscis.gov/tools/reports-and-studies/h-1b-employer-data-hub)[^fn-data-hub] offers employer-level data on petition approvals, denials, and submission volume. It also distinguishes between petitions for new employment and renewals.

### Getting accurate visa counts

Both the LCA and USCIS datasets reflect exactly what petitioners submit on their forms. That means any typos, inconsistencies, or variations in how companies refer to themselves are preserved in the raw data. As a result, a single employer might appear under multiple variations.

For example, Google is listed both as “Google” and “Google Inc.” In other cases, name changes over time show up pin the data. Meta, for instance, appears as “Facebook” before 2021 and as “Meta” or “Meta Platforms” afterward.

In many cases, one version of a name accounts for the bulk of a company’s records. But to create more accurate counts of H-1B workers at key companies, I created regex terms for around 250 of the largest or most prominent H-1B employers. These search terms were used to match raw company names to a single standardized name across both datasets.

This method isn’t perfect. Some false positives may be included, slightly inflating totals. However, after manually reviewing around 100 companies, fewer than 1% of records appeared to be misidentified. There’s also a chance that some company name variations were missed, which would result in an undercount.

All together, the top 250 companies account for 41% of all H-1B petitions between 2019 and 2023\. Since this analysis focuses on the program’s most active users, this approach offers broad enough coverage to move forward. A more scalable solution would be valuable for future work, especially for analyzing smaller companies.[^fn-standardization]

### Enabling industry analysis

Standardizing company names makes it possible to conduct both employer- and industry-level analysis \- something that wasn’t common in my review of previous research. While many sources mention tech’s heavy use of the H-1B program, few provided more detailed, company-level data that added nuance and specificity to the overall trend.

One approach to grouping companies by industry is to use the North American Industry Classification System ([NAICS](https://www.census.gov/naics/)) codes provided in both the LCA and USCIS datasets. But like employer names, these fields reflect what employers report in their applications, leading to inconsistencies and unexpected classifications.

For instance, among the top H-1B employers, each company is associated with an average of four different NAICS. And in many cases, the codes themselves don’t alight with how the companies are commonly understood. Apple is most commonly classified as a “Manufacturing” in the datasets rather than a technology company. JPMorgan Chase is categorized as “Management of Companies and Enterprises,” not Financial Services. Microsoft is frequently listed simply as “Information.”

To create more consistent and intuitive classifications, I turned to data from [CompaniesMarketCap](https://companiesmarketcap.com/), a site that provides data on market capitalization and company headcounts, and also assigns companies to straightforward industry categories. CompaniesMarketCap compiles data from a variety of sources, including [EDGAR](https://www.sec.gov/edgar), the U.S. Securities and Exchange Commission’s collection of corporate filings.

This classification system allows for flexible, multi-level industry analysis \- ranging from broad sectors like Tech, Financial Services, and Manufacturing to more specific sub-industries like AI, Semiconductors, Renewable Energy, and Electric Vehicles.

For prominent H-1B employers that don’t appear in CompaniesMarketCap \- typically private companies or subsidiaries \- I assigned categories manually. For example, LinkedIn appears frequently in the H-1B data but isn’t listed on CompaniesMarketCap since it’s a Microsoft subsidiary.

The company names on CompaniesMarketCap also serve as the standardized employer names discussed in the previous section, making it easy to join industry data to cleaned LCA and USCIS datasets.

### Identifying tech and outsourcing companies

For this analysis, an H-1B employer is categorized as a tech company if it appears under the [Tech category](https://companiesmarketcap.com/tech/largest-tech-companies-by-market-cap/) in the CompaniesMarketCap dataset. This yields a group of 136 companies accounting for more than 581,000 total visa petitions.

On the other hand, “outsourcing” is not a formal industry classification but a description of business practices \- specifically, companies that enable clients to shift internal operations, often IT services, to an external provider.

Since outsourcing is an informal and sometimes subjective designation, I relied on prior work by subject-matter experts to identify relevant companies. Specifically, I used the list of outsourcing firms compiled by the [Economic Policy Institute](https://www.epi.org/blog/tech-and-outsourcing-companies-continue-to-exploit-the-h-1b-visa-program-at-a-time-of-mass-layoffs-the-top-30-h-1b-employers-hired-34000-new-h-1b-workers-in-2022-and-laid-off-at-least-85000-workers/) and [The New York Times](https://www.nytimes.com/interactive/2015/11/06/us/outsourcing-companies-dominate-h1b-visas.html), resulting in a set of 31 companies responsible for more than 1.1 million petitions. While only 17 of these firms appear  in the CompaniesMarketCap dataset, all but two are classified in either [IT Services](https://companiesmarketcap.com/it-services/largest-it-service-companies-by-market-cap/) or [Professional Services](https://companiesmarketcap.com/professional-services/largest-professional-service-companies-by-market-cap/).

One edge case is IBM, which straddles both categories. CompaniesMarketCap lists it under Tech, but the Economic Policy Institute and The New York Times have both flagged IBM as an outsourcing company. To stay consistent with previous research on the H-1B program, this project classifies IBM as an outsourcing company.

*All code for data cleaning and analysis is available on [GitHub](https://github.com/nikhilgahlawat/immigration-h1b/), and the full findings are detailed in the [story](https://www.nikhilgahlawat.com/projects/h1b-tech/).*

---

[^fn-DOL-form]: [ETA-9035 & 9035E](https://flag.dol.gov/programs/LCA): Labor Condition Application for Nonimmigrant Workers
[^fn-DOL]: The DOL essentially just makes sure that the data provided meets the requirements for a visa (for instance, that the occupation is allowed under the program or the wage is above the minimum requirement). LCAs are usually only rejected if a form is incomplete or if some requirement isn’t met (e.g., the intended wage is below the minimum allowed).
[^fn-USCIS-form]: [Form I-129](https://www.uscis.gov/i-129): Petition for a Nonimmigrant Worker
[^fn-data-hub]: The USCIS has made data available in two places: H-1B Employer Data Hub and H-1B Employer Data Hub Files. However the Files page states it was last updated January 8, 2021. The Data Hub has the most up-to-date data.
[^fn-standardization]: Using something like Levenshtein distance or Jaccard index may be a good place to start.