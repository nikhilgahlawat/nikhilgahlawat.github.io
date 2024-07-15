---
layout: post
title: Scraping the Supreme Court
truncated_preview: true
excerpt_separator: <!--more-->
---

[SCOTUS Scraper](https://github.com/nikhilgahlawat/scotus_scraper/) is a project to collect US Supreme Court opinions, briefs, and other documents filed with the Court and make them available in machine-readable format.

<!--more-->
-----

A few weeks ago I was reading [an article](https://www.nytimes.com/2024/06/24/us/supreme-court-abortion-briefs.html) about new research that takes an NLP-like approach to understanding how arguments are presented before the Supreme Court. The researchers collected amicus briefs filed in cases having to do with the same topic - in this case, reproductive rights - and analyzed their text to see how the arguments changed over time, and what that might indicate about how rhetorical strategies have evolved.

In short, they found that opponents to abortion had done a better job of adapting their strategies over time, making efforts to match the way the Court itself was changing.

I was curiuos to dig a bit deeper into the analysis. See what methods they used and if they could be applied to other issues commonly brought before the Court. 

But when I looked through the [full paper](https://scholarship.law.missouri.edu/cgi/viewcontent.cgi?article=4664&context=mlr) I saw that they'd limited their analysis to reproductive rights, and they hadn't made the data available.

So I went digging around the internet in search of a dataset of Supreme Court opinions text, and had a surprisingly difficult time finding one. There were some datasets on how justices voted, data on lower and state courts, data on caselaw, and even a couple resources that had the text of oral arguments. Here are a few interesting resources I found along the way:
 - [Supreme Court Database](http://scdb.wustl.edu/) from Washington University Law
 - [Caselaw Access Project](https://case.law/) from Harvard Law School
 - [Supreme Court Oral Arguments Corpus](https://convokit.cornell.edu/documentation/supreme.html) from ConvoKit made by researchers at Cornell
 - [Supreme Court Data](https://free.law/projects/supreme-court-data) from [Free Law Project](https://free.law/) and [CourtListener](https://www.courtlistener.com/)

But I couldn't seem to find what I was looking for: a freely available dataset of Supreme Court opinions text in machine-readable format.

So I tried making it myself. [SCOTUS Scraper](https://github.com/nikhilgahlawat/scotus_scraper/tree/main) is a project in-progreess that collects data from [Justia](https://supreme.justia.com/), [Oyez](https://www.oyez.org/), and the [US Supreme Court](https://www.supremecourt.gov/) and processes them into datasets that can be used for data science and research.

Some analses I'm hoping can be done with the data:
 - Compare amicus briefs and opinions to see if certain organizations that frequently submit briefs are more influential than others.
 - Compare amicus briefs and opinions to see whether certain justices more influenced by amicus briefs than others.
 - Analyze the content of influential amicus briefs to understand what makes them persuasive.
 - Identify and compare rhetorical strategies used in majority opinions, dissenting opinions, and amicus briefs.
 - Examine how consistent justices are in their rulings over time, particularly in cases involving similar legal principles.
 - Identify instances where justices have changed their stance on key issues and analyze the potential reasons for these shifts.

This is an ongoing project so I'm hoping to add more features and additional data. Check out the [Github repo](https://github.com/nikhilgahlawat/scotus_scraper/) for more.
 


