Here is an improved "Related Works" section

***

### **2. Related Works**

The evaluation of Knowledge Graphs (KGs) is a critical research area aimed at ensuring their usability, usability, and completeness across a wide range of applications. Prior works have explored this challenge from several perspectives, including ontology-aware structural metrics, data-level quality dimensions, task-based performance, and the information-theoretic measurement of knowledge itself.

#### **2.1 Ontology-aware and Structural Metrics**

A significant body of work focuses on evaluating KG quality by exploiting its underlying ontology and structural properties. For instance, Seo et al. propose a suite of structural metrics that measure the depth-aware and surface-level usage of classes and properties, demonstrating how schema exploitation varies across different KGs. Similarly, the K-SPEC framework, proposed by Zaveri et al. (2016), offers a more extensive implementation of this approach by defining 26 specific quality indicators. K-SPEC's contribution is its ability to generate a detailed, systematic audit of a KG's structural health. However, these methods, while providing a rich profile, primarily deliver a static and non-prioritized assessment. They identify structural weaknesses but do not inherently weigh the strategic value of fixing them from a user-centric perspective.

#### **2.2 Data-Level Quality Evaluation in Semantic Web**

Another research stream focuses on defining abstract quality dimensions at the data level. The comprehensive survey by G̈elis et al. (2022) provides a valuable taxonomy of such dimensions, including accuracy, completeness, consistency, and timeliness. This work is crucial for systematizing the field and understanding the multifaceted nature of KG quality. The primary limitation of such surveys, however, is that they present a menu of possible metrics rather than a unified, actionable methodology. The practitioner is left with the complex task of selecting, implementing, and synthesizing these disparate measures into a coherent strategy for KG improvement.

#### **2.3 Task-Based Evaluation**

A popular and pragmatic approach is to evaluate a KG based on its performance on a downstream task, such as question answering, entity linking, or link prediction. The contribution of this extrinsic evaluation method is its direct, application-oriented feedback. If a KG performs well on a task, it is deemed to be of high quality for that specific purpose. The significant drawback, however, is that this evaluation is narrow and often fails to reflect the KG's general intrinsic quality. A KG may be optimized for one task but perform poorly on others, and this approach provides few holistic or diagnostic insights for general improvement.

#### **2.4 Entropy-Based Metrics**

To provide a more holistic and intrinsic measure, several works have turned to information theory. Notably, Fetahu et al. (2022) introduce a comprehensive set of metrics based on principles from information theory and causal inference. The authors define the K-Score, which measures novelty as the fraction of unknown elements, and the I-Score (Information Score), which uses Shannon entropy to assess the informativeness of unknown nodes and edges. These metrics represent a significant advancement, offering a principled, unified score that accounts for both uncertainty and graph size. The key limitation, however, is that the I-Score is context-agnostic. Its formulation treats all uncertainty as equally significant, failing to differentiate between a knowledge gap concerning a highly relevant, timely entity and one concerning a dormant, obscure one.

#### **2.5 Hybrid and Dynamic Approach**

More recent work has begun to combine these approaches. The work of Fetahu et al. on measuring knowledge quantity is a critical step in this direction, inspiring our entropy-based formulation. However, most existing frameworks, from structural analyzers to entropy-based metrics, still lack a direct mechanism for incorporating **dynamic usage data** and **explicit personalization**.

**PRISM** differentiates itself by addressing these gaps directly. It builds upon the entropy-based foundation (2.4) but makes it dynamic by integrating metrics from structural and data-level analysis (2.1, 2.2), including timeliness and, crucially, user engagement patterns. By doing so, PRISM places these two concepts at the core of its design, viewing KG quality not as a static property but as a dynamic function of its data, its usage, and its strategic importance. Furthermore, our **SS-PRISM** framework provides the granular, diagnostic capabilities that surveys show are needed (2.2) while producing a prioritized, actionable output that is absent in both structural profiles (2.1) and task-based evaluations (2.3).
