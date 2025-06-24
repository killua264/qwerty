Of course. Crafting the Introduction and Methodology sections to be distinct yet complementary is crucial for a strong research paper. The Introduction sets the stage—the "why"—while the Methodology provides the technical blueprint—the "how."

Here are both sections, written in a style suitable for the AAAI conference.

***

### **1. Introduction**

Knowledge Graphs (KGs) have become cornerstone assets in modern AI, powering applications from semantic search engines and recommendation systems to complex question-answering. The utility of these KGs is fundamentally dependent on the quality, completeness, and timeliness of the knowledge they contain. As KGs grow to encompass billions of entities and relationships, their manual curation becomes intractable. Consequently, automated methods for assessing KG quality and directing maintenance efforts are of paramount importance.

However, existing approaches to KG evaluation are often insufficient. Many metrics are static, such as node count or edge density, offering little insight into the dynamic state of the graph. More advanced information-theoretic scores provide a global measure of uncertainty but suffer from a critical limitation: they treat all information as equally important. In practice, not all knowledge gaps are of equal value. An unknown fact about a trending topic or a frequently queried entity is more critical to resolve than an obscure, historical detail. This leads to inefficient resource allocation, where curation efforts are not directed towards the areas of the KG that matter most to its users and applications.

This paper addresses a significant gap in current research: the need for a KG evaluation framework that is not only dynamic and context-aware but also diagnostic at a granular level. We argue that an effective KG metric must move beyond simply quantifying what is known and instead prioritize what is *most valuable to discover*.

To this end, we introduce a novel, two-stage framework. First, we propose the **Personalized Relevance-weighted Information Score (PRISM)**, a metric that quantifies the total prioritized uncertainty within a KG. PRISM extends standard information-theoretic approaches by weighting each piece of unknown information with a relevance factor derived from its timeliness, structural importance, and direct user engagement. Second, we present the **Subgraph-Scoped PRISM (SS-PRISM)** framework, an analytical approach that applies these metrics at a subgraph level. Instead of a single global score, SS-PRISM generates a multi-faceted "health profile" for different domains within the KG, enabling targeted, actionable insights.

The contributions of this paper are threefold:
1.  We formalize the PRISM score, a new metric for quantifying prioritized uncertainty in KGs by integrating user engagement and data recency.
2.  We propose the SS-PRISM framework, a novel methodology for creating granular, diagnostic profiles of a KG's internal state.
3.  We demonstrate how these profiles enable data-driven strategies for KG lifecycle management, including readiness assessment, staleness detection, and strategic pruning.

The remainder of this paper details the methodology, presents empirical validation on real-world datasets, and discusses the implications for automated KG management.

### **3. Methodology**

Our methodology provides a formal framework for moving from a static, global assessment of a Knowledge Graph to a dynamic, localized, and actionable diagnostic model. This is realized in two primary stages: first, the formulation of the PRISM score from a set of foundational relevance metrics; and second, the application of this score within our Subgraph-Scoped PRISM (SS-PRISM) framework to analyze the internal health of the KG.

#### **3.1 Foundational Relevance Metrics**

The PRISM score is a composite metric built upon a set of foundational heuristics that measure the relevance, timeliness, and structural importance of entities within the KG. For any given node or edge, we define the following metrics:

**3.1.1 Timeliness (T)**
Measures the recency of an entity, giving higher weight to newer information. The timeliness `T` of a node `N_i` is calculated using an exponential decay function:
`T(N_i) = exp(-λ(t_c - t_d(N_i)))`
where `t_c` is the current time, `t_d(N_i)` is the timestamp of the node's creation or last modification, and `λ` is a decay-rate hyperparameter.

**3.1.2 Class Instantiation Ratio (CIR)**
Quantifies the dominance of a given class within the KG, serving as a proxy for its global importance. For a class `C`, the CIR is the ratio of its instances to the total number of instances in the KG:
`CIR(C) = N(IC) / N(I)`
where `N(IC)` is the number of instances of class `C` and `N(I)` is the total number of instances across all classes.

**3.1.3 Property Instantiation Ratio (PIR)**
Measures the structural significance of a property (i.e., an edge type) by its frequency of use. For a property `P`, the PIR is the ratio of triples using that property to the total number of triples in the KG:
`PIR(P) = N(IP) / N(T)`
where `N(IP)` is the number of triples with property `P` and `N(T)` is the total number of triples.

**3.1.4 Subgraph Importance (SI)**
Captures the instance-level user engagement or "popularity" of a specific node. It is defined as the proportion of queries for a specific instance `I` relative to the total queries for its super-class `SC`:
`SI(I) = TQ(I) / TQ(SC)`
where `TQ(I)` is the total number of queries accessing instance `I`.

#### **3.2 Personalized Relevance-weighted Information Score (PRISM)**

The PRISM score extends the information-theoretic I-Score by integrating the above metrics into relevance-aware importance factors (`c_n`, `c_e`). It quantifies the total prioritized uncertainty in a KG. The score is computed as:
`PRISM = Σ [c_n(i) * p_i * log(1/p_i)] + Σ [c_e(j) * q_j * log(1/q_j)]`
where the summations are over the set of unknown nodes `S_n` and unknown edges `S_e`, respectively. `p_i` and `q_j` are the probabilities of a given node or edge being unknown. The key innovation lies in the distinct, context-aware calculation of the importance factors.

**3.2.1 Node Importance Factor (`c_n`)**
The importance of an unknown node is a function of its class, its specific popularity, and its recency. For each unknown node `i`, `c_n(i)` is the weighted sum of its normalized metrics:
`c_n = w_CIR * Norm(CIR) + w_SI * Norm(SI) + w_T * Norm(T)`

**3.2.2 Edge Importance Factor (`c_e`)**
The importance of an unknown edge is a function of its property type, the popularity of the nodes it connects, and its recency. For each unknown edge `j`, `c_e(j)` is calculated as:
`c_e = w_PIR * Norm(PIR) + w_SI * Norm(SI) + w_T * Norm(T)`

#### **3.3 Subgraph-Scoped PRISM (SS-PRISM): A Diagnostic Framework**

While the global PRISM score is effective for inter-KG comparison, it fails to provide insights into the internal state of the graph. We propose the Subgraph-Scoped PRISM (SS-PRISM) framework to address this limitation. This framework partitions the KG into meaningful subgraphs `G_s` (e.g., based on topic, class, or user context) and computes a "health profile" for each.

**3.3.1 Subgraph-Scoped PRISM Score**
For a given subgraph `G_s`, the SS-PRISM score is the PRISM score calculated exclusively on the set of unknown nodes and edges within `G_s`. It pinpoints the subdomains with the highest concentration of valuable, undiscovered knowledge.

**3.3.2 Subgraph Instantiation Ratio (SIR)**
We employ the SIR metric to assess the structural completeness of a subgraph. It measures the ratio of classes with at least one instance in `G_s` to the total number of classes defined for that subgraph's domain.

**3.3.3 Subgraph Timeliness (T_sub)**
To measure subgraph staleness, we define Subgraph Timeliness as the mean Timeliness (T) of all nodes and edges within `G_s`.

**3.3.4 Subgraph Importance (SI_sub)**
To measure the overall relevance of a subgraph, we define Subgraph Importance as the aggregate query activity for all instances within `G_s` relative to a parent domain or the entire KG.

#### **3.4 Synthesizing Actionable Insights**

The value of the SS-PRISM framework lies in synthesizing these metrics into a diagnostic dashboard for KG management. By analyzing the health profile of each subgraph, we can classify them into distinct, actionable states, as summarized in Table 1.

**Table 1: Subgraph Health Profiles for KG Diagnostics**
| Profile Name | High SS-PRISM | High SIR | High T_sub | High SI_sub | Implied Action |
| :--- | :---: | :---: | :---: | :---: | :--- |
| **Active Research Front** | ✅ | ❌/✅ | ✅ | ✅ | **Prioritize.** Allocate resources for data acquisition and curation. |
| **Incomplete Foundation** | ✅ | ❌ | ❌/✅ | ✅ | **Ingest.** Focus on filling missing classes and structural gaps. |
| **Mature & Stable Core** | ❌ | ✅ | ❌ | ✅ | **Monitor.** Core asset, requires low maintenance. |
| **Stale Archive** | ❌ | ✅ | ❌ | ❌ | **Prune.** Candidate for archiving to reduce complexity. |
| **Emerging Opportunity** | ✅ | ❌/✅ | ✅ | ❌ | **Nurture.** Monitor for growing importance; future research front. |

This methodology provides a comprehensive toolkit for KG lifecycle management, enabling data-driven decisions regarding KG readiness, staleness detection, stability analysis, and strategic pruning.
