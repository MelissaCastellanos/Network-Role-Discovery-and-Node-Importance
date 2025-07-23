
# Network Role Discovery and Node Importance in the Bitcoin OTC Trust Network
## Introduction
Bitcoin over-the-counter (OTC) trading is the process of buying and selling large quantities of Bitcoin directly between parties without public visibility [1][2].  It’s a favorable trading method among high-net worth individuals and institutional investors who are seeking discretion or avoiding market place disruptions [1]. Unlike typical exchange based trading, Bitcoin OTC trading offers increased liquidity, flexibility, and secrecy [2]. Due to the inherent ambiguity of Bitcoin OTC trading, it’s not without risks. One of the biggest challenges in OTC traders encounter is the counterparty risk, where one party fails to deliver after the other has already complied with the agreed-upon obligations. To combat this potential risk, it’s critical to identify reliable, trustworthy traders as well as unreliable, untrustworthy traders. Network embedding enables the detection of trusted users and bad actors by identifying meaningful node representations within a given network [3]. Through network role discovery and importance evaluation, it becomes possible to discern irregularities which might indicate untrustworthy traders [3]. 

This project aims to analyze the Bitcoin OTC trust network to distinguish key players and potentially risky users through the discovery of node roles and their relative importance. Low-dimensional feature vectors are generated using two different network embedding techniques, Node2vec and Struc2vec. Node2vec is mainly utilized for node classification, while Struc2vec aims to determine structural equivalence [4]. By utilizing these two network embedding methods, we get a better understanding of the network’s structure and behavior. These embeddings are then clustered to reveal structural roles within the network, which are evaluated using Eigenvector Centrality and PageRank. 

The following report outlines the development of the low-dimensional feature vectors, the role discovery process, and the evaluation of each identified role. Section II describes the data utilized for the network embedding. Section III addresses both embedding techniques, the clustering process, and the calculation of centrality and node importance measures. Section IV and V discuss results and conclusions. 

## Data Description
The risks associated with Bitcoin OTC trading are mitigated by the maintenance of a user reputation record [5][6].  [The Bitcoin OTC trust weighted signed network dataset](https://snap.stanford.edu/data/soc-sign-bitcoin-otc.html) describes this record. The dataset contains data on how members of Bitcoin OTC rate each other’s trustworthiness on a scale of -10 to 10, with 10 being total trust. Attributes of the dataset are described in Table 1.  

#### Table 1. Bitcoin OTC Trust Weighted Signed Network Attributes
|Attribute    |Type             |Example Value   |Description|
|-------------|-----------------|----------------|-----------|
|Source       |Numeric (integer)|6               |Node id of source, i.e. rater|
|Target       |Numeric (integer)|2               |Node id of target i.e. ratee |
|Rating       |Numeric (integer)|4               |The source's rating for the target ranging from -10 to 10 in steps of 1|
|Time         |Numeric (float)  |1289241911.72836| Time of rating, measured as seconds since Epoch|

There are a total of 5,881 nodes, or ratings, in the dataset with 35,592 edges. Of the 35,592 edges in the network 89% are positive. A visualization of the network is available in Figure 1.  

<img width="900" height="600" alt="image" src="https://github.com/user-attachments/assets/91e49787-6137-4283-947d-f61408204f2a" />

*Figure 1. Bitcoin OTC trust weighted signed network. Top 50 nodes by centrality represented by blue points, where directed edges are shown as grey arrows.*

## Methodology 
Roles within a network can be identified through the process of role discovery, which detects nodes with similar functions, connections, or interactions [8][9]. Role discovery leverages network embedding methods to identify the particular role each node plays [9]. This project utilizes Struc2vec and Node2vec, network embedding, to convert each node to a lower-dimensional feature vector. These low-dimensional vectors were clustered using DBSCAN to discover functional roles in the network and potential abnormalities. Using Degree Centrality, Eigenvector Centrality, and Closeness Centrality, each node was then ranked on importance.

### A. Struc2vec 
The Struc2vec network embedding method is a framework utilized to derive vector representations that reflect each node’s structural position or role [10]. It’s implemented in this project using the Pandas, Numpy, NetworkX, and Scikit-Learn libraries. 
The first step in the application of Struc2vec was the creation of a directed graph utilizing the “Source”, “Target” and “Rating” attributes of the Bitcoin OTC Trust dataset. To capture structural characteristics, the degree signature of each node in the network 
was computed. This signature summarizes the sorted degrees of a node’s neighbors [11]. A cosine similarity matrix was then computed from the degree signatures, indicating which nodes were structurally similar to each other [11]. This was converted into a distance matrix, which was reduced into lower-dimensional embeddings using PCA. 

### B. Node2vec 

### C. Clustering and Role Discovery

### D. Node Evaluation
To quantify the importance of nodes in the Bitcoin OTC Trust network, we calculated three centrality measures, Degree Centrality, Eigenvector Centrality and Closeness Centrality. Degree Centrality measured the number of connections each node had, Eigenvector Centrality quantified the influence of each node in the network, and Closeness Centrality calculated the ‘closeness’ of each node to all other nodes. All three measures of centrality were normalized and averaged to compute node importance using Multi-Centrality Averaging. Node importance was defined as the sum of all normalized centrality measures, 
divided by the number of centrality measures calculated (1).  

(1)
***Importance  = (Degree Centrality + Eigenvector Centrality + Eigenvector Centrality) / 3***

All 5,881 nodes in the network were then ranked on importance to determine the most influential, and structurally important nodes in the Bitcoin OTC Trust network. Nodes with larger calculated importance values were considered to be key players in the 
network. While these rankings were done globally, determining the importance of nodes within each cluster could give more insight into the role each node plays in their given cluster. However, to calculate cluster level importance we would have to track 
nodes and their clustering position through the clustering process.  


## Results 
## Conclusions

## References
[1] “What is Bitcoin OTC Trading,” Bitcoin.com, 2025. https://www.bitcoin.com/get-started/what-is-bitcoin-otc-trading/ (accessed Jun. 26, 2025). 

[2] “What is OTC crypto trading, and how does it work?,” Cointelegraph, Mar. 2024. https://cointelegraph.com/explained/crypto-otc-trading (accessed Jun. 26, 2025) 

[3] Wang, Y., Yao, Y., Tong, H., Xu, F., and  Lu, J. “A Brief Review of Network Embedding” Big Data Mining and Analytics, vol. 2, no. 1, pp. 35–47, Mar. 2019, doi: https://doi.org/10.26599/bdma.2018.9020029  

[4] K. Huang, C. Xiao, L. M. Glass, M. Zitnik, and J. Sun, “SkipGNN: predicting molecular interactions with skip-graph networks,” Scientific Reports, vol. 10, no. 1, Dec. 2020, doi: https://doi.org/10.1038/s41598-020-77766-9.  

[5] S. Kumar, F. Spezzano, V.S. Subrahmanian, C. Faloutsos. Edge Weight Prediction in Weighted Signed Networks. IEEE International Conference on Data Mining (ICDM), 2016. 

[6] S. Kumar, B. Hooi, D. Makhija, M. Kumar, V.S. Subrahmanian, C. Faloutsos. REV2: Fraudulent User Prediction in Rating Platforms. 11th ACM International Conference on Web Searchand Data Mining (WSDM), 2018. 

[7] A. Grover and J. Leskovec, “node2vec: Scalable Feature Learning for Networks,” in Proc. 22nd ACM SIGKDD Int. Conf. Knowledge Discovery and Data Mining, 2016, pp. 855-864 

[8] Network Embedding for Role Discovery: Concepts, Tools, and Applications, 2022. https://markheimann.github.io/tutorials/NetworkRoleDiscovery (accessed Jul. 02, 2025). 

[9] P. Szczurek, “Role Discovery in Networks,” presented at: DATA-61000-00 Lecture, Lewis University, Romeville, IL, Jun. 2025. 

[10] L. Ribeiro, P. Saverese, and D. Figueiredo, “struc2vec: Learning Node Representations from Structural Identity,” doi: https://doi.org/10.1145/3097983.3098061.
 
[11] Figueiredo, Daniel & Ribeiro, Leonardo F. R. & Saverese, Pedro. (2017). struc2vec: Learning Node Representations from Structural Identity. 10.48550/arXiv.1704.03165.  
