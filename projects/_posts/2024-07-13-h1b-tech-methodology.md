---
layout: post
title: How to Analyze H-1B Data
truncated_preview: true
excerpt_separator: <!--more-->
---

*The data sources, cleaning process, and methods used to analyze H-1B data. The full analysis appears in the [main story](https://www.nikhilgahlawat.com/projects/h1b-tech/), and code is available on [Github](https://github.com/nikhilgahlawat/immigration-h1b/).*

<!--more-->

-----

The US technology industry is increasingly relying on the H-1B visa program. This represents a shift in the program’s use, which for years had been dominated by companies in the IT and professional services industries that historically have hired foreign guest workers in order to then contract their labor to other companies. This outsourcing business model has come to define perceptions of the H-1B program and shaped its criticism and proposals for reform.

New analysis of data from the Department of Labor and U.S. Citizenship and Immigration Services shows the shift towards the tech industry unfolding. Among the key findings are:
 - Over the 15 year period from 2009 to 2023, total visa approvals for big tech companies are up over 300%.
 - Visa denial rates for outsourcing companies reached 24% in 2019, up from under 5% in prior years.
 - Denial rates for tech companies have remained essentially flat for the past 15 years, hovering below 5%.
 - Twenty of the largest tech companies have formally declared “H-1B dependency” at least once since 2015, a distinction indicating that at least 15% of their workforce are on H-1B visas.
 - Semiconductor companies are among the tech companies most reliant on H-1Bs. Ten of the largest semiconductor companies have declared H-1B dependence at least once since 2015. The largest, Nvidia, has been dependent every year since 2018.
 - Wages for H-1B workers in big tech are 60% higher than those at outsourcing companies, and tech wages for H-1B workers have grown faster in the past 15 years compared to other companies.

While previous research and analysis of the H-1B program have tended to focus on [wages](https://nfap.com/wp-content/uploads/2020/10/Analysis-of-DOL-H-1B-Wage-Rule.NFAP-Policy-Brief.October-2020.pdf), [outsourcing companies](https://www.epi.org/blog/the-biden-administration-can-stop-h-1b-visas-from-fueling-outsourcing-half-of-the-top-30-h-1b-employers-were-outsourcing-firms-in-2021/), and implications of the [annual visa limit](https://www.cato.org/blog/h-1b-lottery-results-announced-3/4-rejected). Fewer have focused specifically on the tech industry’s growing use of the program or on sub-industry analysis. This study hopes to help fill that gap in a way that might help understand the program’s future.

### H-1B visa data sources

Employers applying to hire a foreign guest worker through the H-1B visa program work with two federal government agencies. The first step is to submit a Labor Condition Application (LCA) to the Department of Labor.[^fn-DOL-form] The DOL acts as a sort of clearing house for H-1B applications. Employers submit information such as the type of visa they’re seeking, the type of occupation they are hiring for, the number of foreign guest workers they hope to hire, the intended pay, and more.[^fn-DOL] While submitting an LCA doesn’t necessarily mean that the employer will continue with the H-1B application process - or get selected for a visa - the LCA data is where we can find occupation-level data, wages in particular. The DOL posts this data on their [Foreign Labor Certification Performance Data](https://www.dol.gov/agencies/eta/foreign-labor/performance) page.

Once the employer has an approved LCA, they can petition U.S. Citizenship and Immigration Services for a visa, submitting one petition per guest worker.[^fn-USCIS-form] Certain types of organizations, such as nonprofit research institutions, universities, and teaching hospitals can receive as many visas as they’re willing to pay for. Most other employers, though, are subject to a cap that limits the total number of new visas to 85,000. If the USCIS receives more petitions than available visas - as has been the case every year since 2014 - they run a lottery to see which petitions are selected. The [USCIS H-1B Employer Data Hub](https://www.uscis.gov/tools/reports-and-studies/h-1b-employer-data-hub)[^fn-data-hub] makes available employer-level data on the number of submitted, approved, and denied petitions, It also breaks out petitions by those for new visas and those for renewals.

### Standardizing employer names
For both the LCA and USCIS datasets, the data reflects exactly what petitioners submit on the required forms. This means that any typos or other quirks of manually entered data are reflected in the LCA and USCIS files. One implication of this is that a single company might have different names across petitions. For instance, the data shows that Google is sometimes listed as “Google” and other times as “Google Inc.” Or there are instances when a company has changed their official name or the name they commonly use, as is the case with Meta which appears as “Facebook” prior to 2021 and then as “Meta” or “Meta Platforms” afterward.

Often one company name captures most of the records from a single company. But to ensure that all records for the same company are grouped together, I created regex terms for ~250 of the largest companies and most prominent users of the H-1B program. The raw company name in the original datasets are compared against these search terms, and companies are assigned to a standardized company name when the regex search returns a match.

This method might accidentally include some false positives, which may result in slightly inflated counts, for instance. But for the ~100 companies I manually checked, mis-identified companies were <1% of the total records. There’s also a chance that this method may miss some company name variations, resulting in some false negatives.

The top 250 companies alone represent 41% of all H-1B petitions between 2019 and 2023. Since this analysis focuses on the most prominent users of the H-1B program, this method provides coverage to move forward. Given more time, and in order to make analyzing smaller companies a bit easier, a more scalable approach to standardizing company names would make sense.[^fn-standardization]

### Employer categories

Standardizing company names enables a couple types of analysis that I didn’t see much of during background research: employer-level and industry-level analysis. While tech’s heavy use of the H-1B program was often mentioned, there didn’t seem to be a lot of data to provide more specific details.

One way to group companies into industries is to use the [NAICS codes](https://www.census.gov/naics/) provided in both the LCA and USCIS datasets. However the NAICS codes, like other LCA and USCIS data, reflect what the employer provided in their application, which could sometimes prove inconsistent or unexpected. The top H-1B companies have on average 4 different NAICS codes associated with them across different records. Some companies also have NAICS codes that are unexpected and not always intuitive. For instance, Apple is most commonly listed as a “Manufacturing” company as opposed to a technology company as perhaps it’s most commonly considered. JPMorgan Chase is most commonly listed under “Management of Companies and Enterprises” instead of something more akin to Financial Services. Microsoft is most commonly listed simply as “Information.”

Instead, I wanted to use company categories that were (1) consistent, and (2) reflect a more common understanding of the company’s industry. The website [CompaniesMarketCap](https://companiesmarketcap.com/) provides company categories that are more intuitive, as well as other useful data such as market capitalization and employee headcount. The data on the site is collected from a number of sources, including [EDGAR](https://www.sec.gov/edgar), a repository of company filings from the Security and Exchange Commission (SEC). 

The company classifications provided by CompaniesMarketCap allow for various levels of industry analysis, including larger industry groups  (Tech, Financial Services, Manufacturing, IT Services, Professional Services, etc) as well as sub-industry groups (AI, Semiconductors, Renewable Energy, Electric Vehicles, and more).

To supplement the data, some companies that are prominent users of the H-1B program but are not in the CompaniesMarketCap data were manually categorized. For instance, LinkedIn appears frequently in the H-1B data; however it does not appear in CompaniesMarketCap since LinkedIn is technically part of Microsoft.

Industry data was joined to LCA and USCIS tables using the standardized employer names.

### Tech companies

Companies in the tech industry were one of the groups at the center of this analysis. While CompaniesMarketCap identifies over 900 tech companies, only companies that use the H-1B program will be in the USCIS and LCA datasets, and likely only company names that were standardized in the USCIS/LCA data will get matched with CompaniesMarketCap data when the tables are joined by company name. However, this still allows for 136 tech companies representing over 581,000 total petitions.

### Outsourcing companies

An “outsourcing” company is more a description of the company’s practices than an industry classification. Very basically, these companies enable their clients to move part of the client’s internal operations to an outside firm. Typically these operations tend to focus on IT and professional services. Since “outsourcing” is more of an informal designation, I leaned on previous analyses to flag which companies use outsourcing business models. Specifically, I took the list of outsourcing companies identified by the [Economic Policy Institute](https://www.epi.org/blog/tech-and-outsourcing-companies-continue-to-exploit-the-h-1b-visa-program-at-a-time-of-mass-layoffs-the-top-30-h-1b-employers-hired-34000-new-h-1b-workers-in-2022-and-laid-off-at-least-85000-workers/) and [The New York Times](https://www.nytimes.com/interactive/2015/11/06/us/outsourcing-companies-dominate-h1b-visas.html). This led to a list of 31 outsourcing companies representing over 1.1 million petitions. While only 17 outsourcing companies appear in the CompaniesMarketCap data, all but two of those are categorized in either the IT Services or Professional Services industries.

One company, IBM, seems to straddle both tech and outsourcing. In the CompaniesMarketCap data, it is part of the Tech, Software, and AI categories. However, IBM is also flagged as an outsourcing company in both the Economic Policy Institute report and New York Times analysis. To remain consistent with past work and to simplify the analysis, this project categorizes IBM as an outsourcing company and not a tech company.

### Analysis

For the full analysis, check out the [article](https://www.nikhilgahlawat.com/projects/h1b-tech/).

-----

[^fn-DOL-form]: [ETA-9035 & 9035E](https://flag.dol.gov/programs/LCA): Labor Condition Application for Nonimmigrant Workers
[^fn-DOL]: The DOL essentially just makes sure that the data provided meets the requirements for a visa (for instance, that the occupation is allowed under the program or the wage is above the minimum requirement). LCAs are usually only rejected if a form is incomplete or if some requirement isn’t met (e.g., the intended wage is below the minimum allowed).
[^fn-USCIS-form]: [Form I-129](https://www.uscis.gov/i-129): Petition for a Nonimmigrant Worker
[^fn-data-hub]: The USCIS has made data available in two places: H-1B Employer Data Hub and H-1B Employer Data Hub Files. However the Files page states it was last updated January 8, 2021. The Data Hub has the most up-to-date data.
[^fn-standardization]: Using something like Levenshtein distance or Jaccard index may be a good place to start.

