# Capstone Report

**Author:** Jana Ghazy  
**Lane:** CTR / Engagement Opportunity Scoring  
**Repo:** https://github.com/janaghazy/FlyRank-AI-Internship  
**Date:** August 2026

---

I looked at pages that show up in search results but do not get clicks. My goal was to create something that helps content teams figure out which pages to fix first. I used search data from FlyRank to train a model that predicts the gap between expected and actual click-through rates. The model does a decent job of finding patterns, with position and page age being the biggest drivers. The final result is a ranked list of pages with the biggest gaps so teams know exactly where to start.

---

## 1. Problem framing

Let's be real content teams don't have time to manually audit thousands of pages. The core question here is simple: out of all the pages we have, which ones are actually worth fixing?

When you dig into search data, you see it all the time. A page ranks really well, right there on page one or two, but for whatever reason, it is barely getting any clicks. That is a traffic leak. And because there are so many pages, a human almost never spot it.

So, what's the output? A ranked list. Nothing fancy. An editor looks at this list and asks: does this page need a better title? Should we rewrite the meta description? Or does the whole article need a refresh?

The cost of a bad call? If I flag a page that doesn't actually need work, the editor just burned an hour for no gain. But if I miss a real problem page, that page stays broken. We keep losing traffic.

---

## 2. Data safety

I used the data from the FlyRank internship warehouse. It is hosted on Hugging Face at https://huggingface.co/datasets/FlyRank/internship-lanes. For this analysis, I worked specifically with the "ai_opportunity" subset, filtering down to just March 2026.

I started with roughly 100,000 rows. After scrubbing out pages with super low impressions and clipping some obvious outliers, I landed on about 88,000 usable rows.

I was careful about leakage here. Raw clicks were never used as a feature that would be way too easy, and the model would just cheat its way to the answer. I also removed any columns that could tie back to a specific client. The client hash IDs were used purely for grouping during the train/test split, but never passed into the model itself. I skipped trend metrics like "trend_direction" too, since they're derived from click behavior.

---

## 3. Baseline

Before building the real model, I threw together a basic baseline. The logic was simple: assume your click-through rate depends entirely on your search ranking. The higher your average position, the more clicks you should get. 

When I ran it against the evaluation set, the numbers weren't great. The Mean Absolute Error sat around 0.005. The R²? Practically zero. It basically told me that position alone explains almost nothing about why pages underperform. That was a good sign, it meant there was actual work for a model to do.

---

## 4. Model / analysis

I went with XGBoost for this. It just works well with tabular data, and it's good at picking up those non-linear patterns that a linear model would totally miss. For instance, brand-new pages and years-old pages both tend to struggle with CTR, but a straight-line model would never catch that U-shape. Boosting handles it naturally.

The features I fed into it were: average position, total impressions, page age in days, content type (keyword article vs. feedly article), binned position tiers, and binned impression tiers.

I kept it clean—nothing derived from clicks made it into the feature set.

---

## 5. Evaluation

I split my data 80/20. But I was careful—I grouped by page ID first so that a single page's data never got split across both the train and test sets. If I hadn't done that, the model would essentially get to practice on the same page it was being tested on. That would be bias.

The model outperformed the baseline. MAE dropped to 0.003, which is roughly a 40% reduction. The R² landed at 0.16. That's not massive, but it's enough to prove the model is catching real signals.

Digging into the errors: it had a rough time with pages that had huge impression counts (above 10,000). Those outlier pages often had massive CTR gaps that were just hard to guess. It also slightly underestimated gaps for pages under 30 days old, which tells me "content_age" might need a tweak to handle new content better.

---

## 6. Interpretation

The model's priorities made a lot of sense. Position was the dominant factor, driving nearly half of the model's decision-making. Page age and impressions together accounted for about 25%.

But here's where it got interesting. Content type—whether a page was a "keyword article" or a "feedly article"—had almost zero impact. The model didn't care. That's a valuable negative result. It tells the us: don't overthink the article format when you're deciding what to fix. It all comes back to rank and how stale the content is.

---

## 7. Recommendation

First priority: The top 10 pages from the list below. These have the biggest CTR gaps. Editors should target these immediately for title and meta description updates. I'm pretty confident these pages are genuinely underperforming relative to their rank.

Second priority: For older pages with moderate gaps, a full content refresh might be better than just a title change—update the body copy and the publish date to make the page feel relevant again.

Third priority: For pages with tiny gaps, do nothing. Just monitor them. The rank might stabilize on its own.

Here's the top 10 the model flagged:

| Rank | Page ID | Position | Gap | Score |
|------|---------|----------|-----|-------|
| 1 | content_945d6ff91386... | 8.6 | 0.0031 | 139 |
| 2 | content_1642f339bd6e... | 4.1 | 0.0041 | 94 |
| 3 | content_60b99970e55b... | 3.9 | 0.0040 | 61 |
| 4 | content_0c5606abaaab... | 4.1 | 0.0041 | 52 |
| 5 | content_6a9c79f55413... | 1.6 | 0.0046 | 43 |
| 6 | content_0bca6d9a85a9... | 7.5 | 0.0031 | 31 |
| 7 | content_34a70fea29d1... | 3.2 | 0.0040 | 31 |
| 8 | content_0c5606abaaab... | 4.3 | 0.0041 | 26 |
| 9 | content_1ff623168718... | 6.7 | 0.0031 | 24 |
| 10 | content_bb2a9972810d... | 3.4 | 0.0040 | 24 |

Important caveat: I can't guarantee that rewriting a title will automatically spike CTR. That's ultimately down to human judgment and testing. The model points at the opportunity, it doesn't deliver the fix.

---

## 8. Reproducibility

Everything you need to reproduce this is up on GitHub.

The main repo is here: https://github.com/janaghazy/FlyRank-AI-Internship.  
The exact notebook for the final results is at: https://github.com/janaghazy/FlyRank-AI-Internship/blob/main/work/Capstone.ipynb.  
If you want to dig through the feature experiments and early exploration, they're in the notebooks folder: https://github.com/janaghazy/FlyRank-AI-Internship/tree/main/work/notebooks.

I locked the random seed to 42 so runs stay consistent. You'll just need Python 3.10, the usual data science stack, and the requirements.txt file in the repo.

---

## Acknowledgments

This project was built on the FlyRank ML Internship dataset, which is publicly available at https://huggingface.co/datasets/FlyRank/internship-lanes. I appreciate the FlyRank team for making real-world search data accessible for this kind of work.

*Built on the FlyRank ML Internship dataset*  
*[FlyRank AI](https://flyrank.ai)*
