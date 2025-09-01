# Network Role Discovery and Node Importance in the Bitcoin OTC Trust Network

## Introduction
Bitcoin over-the-counter (OTC) trading involves direct transactions between buyers and sellers without exchange visibility. While this type of trading offers liquidity, flexibility, and discretion, it carries a significant counterparty risk, where one party may fail to deliver after the other has already complied with the agreed-upon obligations.  

This project analyzes the **Bitcoin OTC Trust Network** to:  
- Discover **functional roles** of participants using **Node2Vec** (flow-based similarity) and **Struc2Vec** (structural similarity).  
- Detect clusters of **trusted vs. distrusted actors** via density-based clustering (DBSCAN).  
- Evaluate **node importance** with multi-centrality measures.  

Together, these methods provide insight into **community behaviors, structural equivalence, and influential actors** within the OTC network. 

> [!NOTE]
> This repository provides a summary of a group project I contributed to as part of the Advanced Data Analytics graduate course at Lewis University. The complete project report is included in the repository files for reference.

## Table of Contents  
- [Dataset](#Dataset)  
- [Methodology](#Methodology)  
- [Results](#Results)  
- [Conclusions](#Conclusions)  
- [References](#References)

## Dataset
We use the [Bitcoin OTC trust weighted signed network dataset](https://snap.stanford.edu/data/soc-sign-bitcoin-otc.html), which records user-assigned trust ratings on a scale of -10 (distrust) to +10 (total trust). The dataset consists of 5,881 users (nodes) connected by 35,592 trust ratings (edges) with roughly 89% of ratings being positive


**Dataset Attributes:** 
|Attribute    |Type             |Example Value   |Description|
|-------------|-----------------|----------------|-----------|
|Source       |Numeric (integer)|6               |Node id of source, i.e. rater|
|Target       |Numeric (integer)|2               |Node id of target i.e. ratee |
|Rating       |Numeric (integer)|4               |The source's rating for the target ranging from -10 to 10 in steps of 1|
|Time         |Numeric (float)  |1289241911.72836| Time of rating, measured as seconds since Epoch|

<p align="center">
  <img src="https://github.com/user-attachments/assets/91e49787-6137-4283-947d-f61408204f2a" width="800">
</p>
<p align="center"><i>Figure 1. Bitcoin OTC Trust Network. Blue points: top-50 nodes by centrality.</i></p>

## Methodology
Networks, like the Bitcoin OTC dataset, consist of nodes that play different roles depending on their connections and interactions. Role discovery identifies nodes with similar patterns of behavior or connectivity. In this project, each node (user) is first represented as a low-dimensional feature vector using network embedding techniques (Struct2Vec & Node2Vec), which capture both structural and relational information within the network. These embeddings are then clustered using DBSCAN to group together nodes with similar roles and highlight unusual or outlier behavior. Node importance is calculated using Degree, Eigenvector, and Closeness Centrality, providing a numerical assessment of each node’s influence and position within the network.

---

### **Network Embeddings**
**Struc2Vec**
- Captures **structural equivalence** (nodes in similar roles, even if far apart).  
- Built via degree signatures + biased random walks over structurally similar nodes.

**Goal:** Represent each node in a way that reflects its role in the overall network structure.

**Node2Vec**  
- Captures **flow/community similarity** (nodes that co-occur in random walks).  
- Config: 64-dim embeddings, 30-step walk length, 100 walks/node, window=10.
    
**Goal:** Represent each node to capture community and interaction patterns.

---

### **Clustering**
- Applied **DBSCAN**, **HDBSCAN**, and **Gaussian Mixture Models (GMM)** on embeddings.  
- Validated results with **silhouette score** and **Davies-Bouldin Index**.  
- Combined cluster profiles with network metrics such as in-degree, out-degree, and transaction volume, to identify different participant roles.
    - *distributors*
    - *absorbers*
    - *intermediaries*

**Goal:** Group nodes with similar behavior or roles and uncover functional communities and outliers.

---

### **Node Importance**
Utilized **multi-centrality averaging** to determine node importance:  

Importance = (Degree + Eigenvector + Closeness) / 3

- **Degree Centrality** → number of direct connections per node.  
- **Eigenvector Centrality** → influence on well-connected neighbors.  
- **Closeness Centrality** → efficiency of reaching all other nodes.

**Goal:** Quantify which nodes are most influential or central in the network. 

## Results
**Node2Vec** revealed communities of *everyday traders, optimistic raters, regulars, and distrusted cohorts*. 
<p align="center">
 <img width="370" height="353" alt="image" src="https://github.com/user-attachments/assets/f22a8e01-e89a-41d9-9faf-76a75f31f7dd" />
</p>
<p align="center"><i>Figure 2. Clusters obtained from Node2Vec embeddings, showing nodes grouped by community and interaction patterns. Nodes that frequently interact or co-occur in the network are placed closer together, revealing functional communities.</i></p>

**Roles**
| Cluster | Color (in your legend) | Size | avg\_out | avg\_in | Interpretation                                                                                                                                                                                    |
| :-----: | :--------------------: | ---: | -------: | ------: | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
|  **2**  |          Green         | 4200 |     1.52 |    0.89 | **Everyday traders.** This is the vast majority of nodes. They give modestly positive trust and receive modestly positive trust. Think casual users who both rate and get rated a bit above zero. |
|  **0**  |       Light blue       |  656 |     1.20 |    1.22 | **Well-liked regulars.** A mid-sized community with slightly higher incoming than outgoing trust—reliable counter-parties who tend to earn a bit more trust than they give.                       |
|  **4**  |         Purple         |  456 |     1.35 |    1.10 | **Optimistic raters.** They give out more trust than they get back, perhaps generous raters giving their first rating or connectors linking groups.                                                             |
|  **1**  |         Orange         |  292 |    –0.92 |   –4.17 | **Distrusted cluster.** Net negative both in giving and receiving: these are the accounts that get flagged and flag others. Possible scammers or users with poor reputations.                       |
|  **5**  |          Brown         |  125 |     0.80 |    1.34 | **Trust-recipients.** They receive substantially more trust than they give, perhaps new users who haven’t had many opportunities yet to rate others.                                               |
|  **3**  |           Red          |   49 |     2.02 |    1.75 | **Trusted core.** A small clique of highly active, highly trusted users. They give very high ratings and get very high ratings back.                                                              |
|  **10** |          Teal          |   37 |     1.81 |    1.77 | **Elite traders.** Similar to cluster 3 but even tighter—power users with excellent reputations on both sides.                                                                                    |
|  **8**  |          Gray          |   18 |     2.52 |    2.18 | **Super-trusted givers.** They almost always assign top marks (avg\_out >2) and earn top marks in return. Very small, very elite.                                                                 |
|  **9**  |         Yellow         |   15 |     2.12 |    1.87 | **High-trust hubs.** Another tiny, highly positive cluster, likely “hubs” in the network who both give and get excellent scores.                                                                   |
|  **6**  |       Light green      |   13 |     1.64 |    1.17 | **Moderate elites.** A micro-cluster of users with above-average outgoing trust but more modest incoming.                                                                                         |
|  **7**  |          Pink          |   12 |     2.36 |    1.56 | **Generous, selective.** They give very high trust but receive a bit less in return.                                                                                                              |
|  **11** |          Cyan          |    6 |     0.17 |    1.00 | **Quiet but trusted.** Very few ratings given (avg\_out ≈0.17) but what little they do they do right and they’re trusted by others.                                                               |
|  **–1** |      Navy (noise)      |    2 |     1.23 |    1.55 | **Outliers.** Only two nodes didn’t fit any dense region; they have small volumes of mostly positive interactions.                                                                                |

**Struc2Vec** uncovered *backbone participants, elite givers, and strong negatives* based on positional similarity.  
Centrality analysis highlighted the most influential hubs and measured how their impact compared to the rest of the network.

<p align="center">
 <img width="665" height="519" alt="image" src="https://github.com/user-attachments/assets/f5d75b20-cd53-478f-a6c5-1ca544a23a9b" />
</p>
<p align="center"><i>Figure 3. Clusters obtained from Struc2Vec embeddings, showing nodes grouped by structural similarity. Nodes with similar roles in the network are placed closer together, highlighting functional roles and structural patterns.</i></p>

**Roles**
|   Cluster | Color (in legend)   |   Size |   avg_out |       avg_in | Interpretation                                                                        |
|----------:|:--------------------|-------:|----------:|-------------:|:--------------------------------------------------------------------------------------|
|         0 | Blue                |   1164 | 1.1564    |  0.992494    | Everyday participants with modest positive trust.                                     |
|         5 | Brown               |   1045 | 2.07751   |  1.09143     | Optimistic givers with high outgoing trust.                                           |
|         8 | Olive               |    797 | 1.9147    |  1.05443     | Frequent raters with balanced trust.                                                  |
|         4 | Purple              |    703 | 1.72333   |  0.980907    | Connectors linking parts of the network.                                              |
|         1 | Orange              |    450 | 1.38963   |  1.08793     | Well-liked regulars with balanced trust.                                              |
|         9 | Cyan                |    291 | 1.31529   |  0.757291    | Cautious givers with moderate outgoing trust.                                         |
|         6 | Pink                |    227 | 1.44934   |  1.09977     | Balanced participants evenly matched.                                                 |
|        21 | Light Gray          |    172 | 0.0290698 | -2.20349     | Distrusted sinks with negative incoming trust.                                        |
|        16 | Medium Blue         |    133 | 0         |  0.482444    | Silent receivers with modest incoming trust.                                          |
|         2 | Green               |    131 | 0         |  1.48074     | Collectors trusted by others but no outgoing trust.                                   |
|        25 | Pastel Orange       |    105 | 0         | -0.0860544   | Inactive neutrals with no outgoing and near-zero incoming trust.                      |
|        18 | Light Olive         |     94 | 0.106383  | -0.18836     | Mildly distrusted with minimal outgoing and slightly negative incoming trust.         |
|        20 | White Smoke         |     77 | 0         | -5.0184      | Strongly distrusted with no outgoing and sharply negative incoming trust.             |
|        10 | Navy                |     74 | 0         |  0.827358    | Passive receivers who don’t rate others but receive modest positive trust.            |
|        12 | Dark Brown          |     65 | 0         | -2.00641     | Negative sinks with no outgoing and noticeably negative incoming trust.               |
|        14 | Dark Purple         |     54 | 0         | -0.348765    | Low engagement negatives with no outgoing and mildly negative feedback.               |
|        15 | Slate Blue          |     51 | 0.196078  | -0.741667    | Small negative participants with low outgoing and negative incoming trust.            |
|         3 | Red                 |     51 | 2.05882   |  1.68301     | Core influencers highly engaged with high outgoing and incoming trust.                |
|        22 | Salmon              |     38 | 2.44737   |  0.853509    | Active givers with high outgoing and positive incoming trust, likely opinion leaders. |
|        23 | Lavender            |     28 | 0         |  0.142857    | Occasional receivers with no outgoing activity but some incoming trust.               |
|        19 | Tan                 |     27 | 0         |  1.02469     | Quiet trust holders with no outgoing and moderate positive incoming trust.            |
|        11 | Dark Green          |     26 | 0         |  0.000518926 | Neutral participants with almost no outgoing or incoming trust activity.              |
|        13 | Dark Red            |     23 | 2.30435   |  0.126812    | Occasional givers with moderate outgoing and low incoming trust.                      |
|         7 | Gray                |     21 | 2.19048   |  1.69048     | Generous participants consistently active with high outgoing and incoming trust.      |
|        17 | Light Blue          |     19 | 0         | -1.64912     | Distrusted nodes with no outgoing and strongly negative incoming feedback.            |
|        24 | Dim Gray            |     14 | 0         | -1.09524     | Negative small group with no outgoing and moderately negative incoming trust.         |
|        -1 | Black (Noise)       |      1 | 1         |  1           | Noise/outliers, unclustered nodes with average trust values.                          |

## Conclusions
- **Complementary Insights:** Node2Vec captures transactional flows, while Struc2Vec surfaces role-based equivalence.  
- **Risk Management:** Embedding + clustering can flag suspicious or untrustworthy counterparties.  
- **Regulatory Use:** Clusters with negative/unreciprocated trust can guide oversight.  
- **Future Work:**  
  - Incorporate **temporal dynamics** to track role evolution.  
  - Test **alternative embeddings/clustering** for robustness.  
  - Enrich analysis with **transaction metadata (e.g., USD volume)**.  

## References
1. [What is Bitcoin OTC Trading – Bitcoin.com, 2025](https://www.bitcoin.com/get-started/what-is-bitcoin-otc-trading/)  
2. [What is OTC crypto trading – Cointelegraph, 2024](https://cointelegraph.com/explained/crypto-otc-trading)  
3. Wang, Y. et al. *A Brief Review of Network Embedding.* Big Data Mining and Analytics, 2019.  
4. Huang, K. et al. *SkipGNN: Predicting molecular interactions with skip-graph networks.* Sci Rep, 2020.  
5. Kumar, S. et al. *Edge Weight Prediction in Weighted Signed Networks.* ICDM, 2016.  
6. Kumar, S. et al. *REV2: Fraudulent User Prediction in Rating Platforms.* WSDM, 2018.  
7. Grover, A., Leskovec, J. *Node2Vec: Scalable Feature Learning for Networks.* KDD, 2016.  
8. [Network Embedding for Role Discovery Tutorial, 2022](https://markheimann.github.io/tutorials/NetworkRoleDiscovery)  
9. Szczurek, P. *Role Discovery in Networks.* Lecture, Lewis University, 2025.  
10. Ribeiro, L. et al. *Struc2Vec: Learning Node Representations from Structural Identity.* KDD, 2017.  
