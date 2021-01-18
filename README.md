# Hackathon: How banks can save the world!
## Team A&J: Research into novel use of SOTA NLP models for extraction of Green RWA metrics from text sources

This repository contains artefacts created by team "A&amp;J" during the How Can Banks Save The World Hackathon, 16/17 Jan 2021. Such artefacts are open source and free to use/copy etc.

## Our team
Aditya Patel is a quantitative analyst at IHS Markit specialising in products that measure financial risk in capital markets
Jim Plant is software developer (alumni of IHS Markit) specialising in software within financial risk and latterly payments technology

We are happy for our names and the contents of this Git Repo to be used in connection with this hackathon. 

This was our first hackathon. We'd been very interesting in taking part in one - waiting for the alignment of interesting subject matter, free weekend and a challenging brief. 

## Our Methodology

Our original plan was to implement some intersting and/or novel part of the GreenRWA specification, in particular we had an interest in investigation of ways in which a bank might differentiate between corporations within the same industry and geographical sectors, but whose environmental performance was markedly different. 

We identified the Advanced Model section in the "How banks can save the world" paper ( pg.11 https://www.dropbox.com/s/hciotzcamp7c0fs/How%20banks%20can%20save%20the%20planet%20-%20clean.pdf?dl=0) as an area of interst. This section describes how taxonomy eligible Capex for climate adaptation allows reduction of physical risk capital charge. We based our model of taxonomy eligibility on the EU TEG Taxonomy cited in the references of this paper (https://ec.europa.eu/info/files/200309-sustainable-finance-teg-final-report-taxonomy_en)

## Industry Sector

We looked for an industry sector where the idiosyncratic environmental performance of one company could differentiate it markedly from another, and an industry sector that had significant climate impact. The industry sector also had to be present in the TEG Taxonomy. We chose cement manufacture for it's high climate impact. Our suspicions regarding differences in environmental performance were borne out in our findings on Co2 Per Tonne of Cement per company (see Benchmarking and tracking below). 

## Mock Loan Book

We created a mock loan book consisting mostly of Cement manufacturers, with company name, bio (scraped from Wikipedia), and a hypothetical loan amount. This can be found in the Building Sector Loan book spreadsheet within this Git Repo. 

## Initial findings & pivot

We quickly found that input data for use in such a model was not trivial to acquire. We looked for capex expenditure on projects, duration, expected yield and so on. Information on climate related capex was very hard to come by, was buried in lengthy company reports and was patchy at best. 

We also attempted to extract data from standardised reports but found that even when the question set is consistent the interpretation of those questions varies wildly (e.g. In their CDP disclosures Cemex claimed to derive 51% of their revenue from low carbon products (https://www.cemex.com/documents/45903740/45904946/InvestorCdpCemex2019.pdf/6a0a4d39-fe45-02b0-1f66-72e24c1963c5) and LafargeHolcim reports 27% (https://www.lafargeholcim.com/sites/lafargeholcim.com/files/atoms/files/lafargeholcim_cdp_climate_change_2020.pdf) yet the environmental performance of the latter far exceeds the former (measured by Co2 Per Tonne of Cementious Material - Cemex 0.89 tonnes of Co2 Per Tonne of Cement, LaFargeHolcim 0.58 tonnes)).

Once we'd satisfied ourselves that no clean and readily available data source exists for our use case we pivoted our efforts towards working out how one may be synthesised from existing online content (reports, news, investigations etc). We had used Machine Learning Natural Language Processing techniques before to produce Credit Risk Early Warning systems based on news feeds, we wondered if similar techniques might be applied to the problem of data acquisition for climate risk.

We briefly assessed 'traditional' NLP techniques such as Named Entity Recognition, Sentiment Analysis and so on and decided these would not materially help for our use case. We considered the way in which we, acting as human analysts would go about the task and concluded that we'd likely design a set of questions or criteria that we could use to extract information from bodies of text. This seemed to fit the pattern used by some very recent NLP models that are designed to tackle Q&A problems (see https://rajpurkar.github.io/SQuAD-explorer/). 

We downloaded the Electra NLP model (https://github.com/google-research/electra) within a Colab notebook (Google funded free-to-use notebooks that have hardware acceleration needed for large NLP models) and designed question panels to test our hypothesis. 

## Extracting Taxonomy

We decided to 'start simple' and try use this NLP technique to score each company against the EU TEG taxonomies required for GreenRWA computation. We built a question panel (GreenRWA_Hackathon_2021_Get_Taxonomy.ipynb) and script to take a company bio and generate 'null odds' scores for each taxonomy. Null odds being the chances that a question may be answered from the text provided - our questions concerned production of cement, aluminium etc and by using these scores we could determine with a high degree of certainty whether the company bio described a company that produces cement or steel or aluminium. The Electra model we used has the advantage that it's not tied to very specific keywords but picks up meaning from text (again see the Squad examples to see how this works).

We achieved satisfactory results for the small sample set that we used in our experiment. The results may be found in the first tab of the Building Sector Loan book spreadsheet, the Logit scores (negative being 'better') can be found in columns E though H. 

## Benchmarking and tracking climate mitigation and adaptation progress

The How banks can save the world paper cites the duty of the banks to "regularly assess the progress of climate adaptation and mitigation projects of their clients". We considered metrics by which a bank may assess this, and we had a secondary goal which was to make empirical side by side comparisons of cement manufacurers. We found that Co2 Per Tonne of Cementious Material was reasonably widely reported, sufficiently unambiguous in its' derivation and interpretation and was often reported over a period of time. Moreover Cement Manufacturers themselves use this measure as a means of reporting progress and setting targets. 

We used NLP notebooks very similar to the Taxonomy and Project Identification/Capex/Materiality to extract Co2 per Tonne of Cement from company literature. The numbers can be found in the first tab of the Building Sector Loan Book spreadsheet. 

## Extracting Qualifying Capex 

The problem of extracting qualifying Capex was much less tractable. We found that the required information was partially present in company reports however it was often spread across different sections of the reports and much of the supporting information we needed (such as project Capex, expected project duration and Co2 Per Tonne savings) were available but were spread across the internet in a variety of sources (industry news, analysis by green publications etc). 

Whilst we could use NLP direcly on company reports to obtain lists of possible candidate projects (see Evaluate_Project_Capex_And_Materiality.ipynb) we had to recourse to Google Search in order to find supporting documentation for each project (e.g. https://www.globalcement.com/news/itemlist/tag/Department%20of%20Energy in support of the LaFargeHolcim CO2MENT project).

## Next steps 

Given more time we'd expand our research on two areas here:
1. We'd automate the cycle of NLP analysis and search - starting with company reports we'd identify project names, perform specific searches for supporting documentation using those names, apply NLP to the results and repeat the cycle until the yield of useful information fell below some threshold.
2. We'd enhance the performance of our NLP model by pairing it with a second NLP model - specifically the model we currently use is tuned for Q&A - it's specialised at finding the sections within a body of text that answer some question. We'd run the results from this model through a second model that applies Named Entity Recognition - this would allow us for example to better identify likely project names (the NER model picks up proper nouns). 


## Summary

We believe there's huge scope to deliver environmental impact through measures such as GreenRWA. To get these we need to overcome a raft of challenges. Regulation and financial incentives are definately a significant components of this - and supplying the best available data to the models that underpin these regulations and incentives is a key challenge. 

Until such as time as firms provide consistent, audited and complete data on their environmental performance we need alternative methods to obtain such data. We believe that the application of NLP technology at scale can be part of the solution to this challenge. Our findings in this Hackathon, whilst modest, show that there's significant scope for this technology in this use case. 



