# Finding Pages That Do Not Get Clicks: A Model to Score Click-Through Rate Opportunities

**Author:** Jana Ghazy

**Lane:** CTR / Engagement Opportunity Scoring

**Date:** August 2026

---

## Abstract

I looked at pages that show up in search results but do not get clicks. My goal was to create something that helps content teams figure out which pages to fix first. I used search data from FlyRank to calculate the difference between how many clicks a page should get based on its position and how many it actually gets. I call this the CTR gap. I trained a model to predict this gap using things like position, impressions, page age and content type. The model does a decent job of finding patterns even with limited information. Position turned out to be the most important factor, followed by page age and impressions. The final result is a list of pages with the biggest gaps so teams know where to start.

---

## 1. Introduction

Sometimes pages rank well in search results but still do not get enough clicks. This is a problem because it means lost traffic and missed opportunities. I wanted to find out which pages content teams should look at first to improve click-through rates. The answer is simple: find pages where the gap between expected and actual clicks is the biggest. Then update the title or meta description. If you make a mistake you waste time. If you miss an opportunity the page keeps underperforming.

---

## 2. Data

I used data from the FlyRank internship warehouse. This data is completely anonymous so it does not include any client information or identifiable website details. I looked at two tables: one with page-level information and one with daily search metrics. I only used data from March 2026. I started with 100,000 rows and ended up with about 88,000 after cleaning. I removed pages with fewer than 100 impressions and got rid of extreme outliers. To keep things honest I did not use clicks as a feature because that would leak the answer.

---

## 3. Methodology

My idea is simple. For any page that shows up in search results you can guess roughly how many clicks it should get based on its position. A page in position 1 should do better than a page in position 10. I set up a rule for expected click-through rates based on position. Then I calculated the actual click-through rate from the data and defined the target as the difference between expected and actual. A positive difference means the page is getting fewer clicks than it should.

I used a Gradient Boosting model to predict this difference. I also created some extra features like position tiers and impression tiers. The final feature set included things like position, impressions, content age and content type. I split the data into training and testing sets.

---

## 4. Results

The model did a better job than a simple rule. It achieved an error rate of 0.003 and an R² of 0.16 which means it captured some meaningful patterns. When I looked at what factors were most important, position stood out clearly as the strongest signal. It made up about half of the model's decision-making. Content age mattered too, making up about a quarter combined. Impressions played a smaller role. The main takeaway is that position is the most useful thing to look at when trying to find pages that underperform on clicks. The model helps surface those pages more reliably than a fixed rule.

---

## 5. Limitations

I need to be honest about what this work does and does not prove. I can say that pages in lower positions tend to get lower click-through rates and that older content tends to do worse. But I cannot say that this is how search algorithms work or that updating a page will definitely improve its click-through rate. The main limitations are that I only used one month of data and the feature set is still fairly small. So the results are directional, not definitive.

---

## 6. Ranked Recommendations

The model flagged these as the top 10 pages with the biggest click-through rate gaps. Each one is a candidate for reviewing and updating the title or meta description.

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

The full ranked list is saved in `work/outputs/capstone_recommendations.csv`.

---

## 7. Reproducibility

All the code and analysis is available in the GitHub repository. The dataset is hosted on Hugging Face.

---

## 8. Acknowledgments

This work was built using the FlyRank ML Internship dataset. I thank the FlyRank team for making the data available and for their support.

---

*Built on the FlyRank ML Internship dataset*

*[FlyRank AI](https://flyrank.ai)*
