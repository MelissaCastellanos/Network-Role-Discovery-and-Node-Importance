# Network Role Discovery and Node Importance in the Bitcoin OTC Trust Network

## Introduction
Bitcoin over-the-counter (OTC) trading involves direct transactions between buyers and sellers without exchange visibility. While it offers liquidity, flexibility, and discretion, it carries significant counterparty risk, where one party may fail to deliver after the other has already complied with the agreed-upon obligations.  

This project analyzes the **Bitcoin OTC Trust Network** to:  
- Discover **functional roles** of participants using **Node2Vec** (flow-based similarity) and **Struc2Vec** (structural similarity).  
- Detect clusters of **trusted vs. distrusted actors** via density-based clustering.  
- Evaluate **node importance** with multi-centrality measures.  

Together, these methods provide insight into **community behaviors, structural equivalence, and influential actors** within the OTC network.  

This repository provides a summary of a group project I contributed to as part of the Advanced Data Analytics graduate course at Lewis University. The complete project report is included in the repository files for reference.
---

## Dataset
We use the [Bitcoin OTC trust weighted signed network dataset](https://snap.stanford.edu/data/soc-sign-bitcoin-otc.html), which records user-assigned trust ratings on a scale of -10 (distrust) to +10 (total trust).  

**Dataset Summary**:  
- **Nodes (users):** 5,881  
- **Edges (ratings):** 35,592  
- **Positive edges:** 89%  
- **Attributes:**  

| Attribute | Type  | Example | Description |
|-----------|-------|---------|-------------|
| Source    | int   | 6       | Node ID of rater |
| Target    | int   | 2       | Node ID of ratee |
| Rating    | int   | 4       | Trust score (-10 to +10) |
| Time      | float | 1289241911.7 | Timestamp (Unix epoch) |

<p align="center">
  <img src="https://github.com/user-attachments/assets/91e49787-6137-4283-947d-f61408204f2a" width="800">
</p>
<p align="center"><i>Figure 1. Bitcoin OTC Trust Network. Blue points: top-50 nodes by centrality.</i></p>

---

## Methodology

### 1. **Embeddings**
- **Struc2Vec**  
  - Captures **structural equivalence** (nodes in similar roles, even if far apart).  
  - Built via degree signatures + biased random walks over structurally similar nodes.  
- **Node2Vec**  
  - Captures **flow/community similarity** (nodes that co-occur in random walks).  
  - Config: 64-dim embeddings, 30-step walk length, 100 walks/node, window=10.  

### 2. **Clustering**
- Applied **DBSCAN**, **HDBSCAN**, and **Gaussian Mixture Models (GMM)** on embeddings.  
- Validated results with **silhouette score** and **Davies-Bouldin Index**.  
- Combined cluster profiles with network metrics such as in-degree, out-degree, and transaction volume, to identify different participant roles → *distributors*, *absorbers*, and *intermediaries*.

### 3. **Node Importance**
Utilized **multi-centrality averaging** to determine node importance:  

\[
\text{Importance} = \frac{\text{Degree} + \text{Eigenvector} + \text{Closeness}}{3}
\]

- **Degree Centrality** → number of direct connections per node.  
- **Eigenvector Centrality** → influence on well-connected neighbors.  
- **Closeness Centrality** → efficiency of reaching all other nodes.  

---

## Results
- **Node2Vec** revealed communities of *everyday traders, optimistic raters, regulars, and distrusted cohorts*.  
- **Struc2Vec** uncovered *backbone participants, elite givers, and strong negatives* based on positional similarity.  
- Centrality analysis highlighted the most influential hubs and measured how their impact compared to the rest of the network.

---

## Conclusions
- **Complementary Insights:** Node2Vec captures transactional flows, while Struc2Vec surfaces role-based equivalence.  
- **Risk Management:** Embedding + clustering can flag suspicious or untrustworthy counterparties.  
- **Regulatory Use:** Clusters with negative/unreciprocated trust can guide oversight.  
- **Future Work:**  
  - Incorporate **temporal dynamics** to track role evolution.  
  - Test **alternative embeddings/clustering** for robustness.  
  - Enrich analysis with **transaction metadata (e.g., USD volume)**.  

---

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
