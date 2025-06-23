Let's walk through a detailed end-to-end example using a realistic Knowledge Graph (KG) scenario. We will explain how to:

1. Define the ontology and individuals.


2. Identify missing information.


3. Calculate each global metric: CIR, PIR, QF, Timeliness (T), and P-Score.


4. Apply these metrics at a subgraph level.


5. Interpret the results to identify gaps and guide KG improvement.




---

🔹 Step 1: Ontology Definition (Schema Layer)

Let’s assume a Research Knowledge Graph representing academic research. The ontology includes:

Classes:

Person

Paper

Organization

Venue

Topic


Properties:

authoredBy (Paper → Person)

affiliatedWith (Person → Organization)

publishedIn (Paper → Venue)

hasTopic (Paper → Topic)

cites (Paper → Paper)



---

🔹 Step 2: Individual Instances

Example KG Snapshot (simplified):

Subject	Predicate	Object

Paper123	authoredBy	Alice
Alice	affiliatedWith	MIT
Paper123	hasTopic	Artificial Intelligence
Paper123	publishedIn	NeurIPS2023
Paper123	cites	Paper99
Paper99	authoredBy	Bob
Bob	affiliatedWith	NULL
Paper99	publishedIn	NULL


Here, missing information is:

Bob's organization

Paper99's venue

Some papers/topics not cited/typed



---

🔹 Step 3: Calculating Global Metrics


---

(1) Class Instantiation Ratio (CIR)

Let’s assume:

Total instances: 1000

Instances of class Person: 250

Instances of class Paper: 500


CIR(Person) = 250 / 1000 = 0.25
CIR(Paper) = 500 / 1000 = 0.50
Interpretation: Papers dominate the KG, so structural maturity should emphasize them.


---

(2) Property Instantiation Ratio (PIR)

Let’s assume:

Total triples in KG: 5000

Triples using authoredBy: 700

Triples using publishedIn: 400


PIR(authoredBy) = 700 / 5000 = 0.14
PIR(publishedIn) = 400 / 5000 = 0.08
Interpretation: authoredBy is more structurally prevalent. Missing publishedIn data is concerning due to lower coverage.


---

(3) Query Frequency (QF)

Let’s assume:

Total queries for class Person: 1000

Queries for Alice: 100


QF(Alice) = 100 / 1000 = 0.10
Interpretation: Alice is a moderately popular individual in the Person class.


---

(4) Timeliness (T)

Let’s say:

Alice's affiliation last updated: 30 days ago

λ (decay constant) = 0.01


T(Alice) = exp(-0.01 × 30) ≈ 0.74
Interpretation: This data is moderately fresh.


---

(5) P-Score (Global)

Missing node: Bob (missing affiliatedWith)
Missing edge: Paper99 (missing publishedIn)

Let’s assign:

pi = 0.10 for Bob (based on frequency)

cn(Bob) = w1CIR(Person) + w2T(Bob) + w3*QF(Bob)


Assume:

CIR(Person) = 0.25, T(Bob) = 0.6, QF(Bob) = 0.08

w1 = 0.3, w2 = 0.3, w3 = 0.4


Then:

cn(Bob) = 0.3×0.25 + 0.3×0.6 + 0.4×0.08 = 0.075 + 0.18 + 0.032 = 0.287  
P-node(Bob) = 0.287 × 0.10 × log(1/0.10) ≈ 0.287 × 0.10 × 1 = 0.0287

Similarly compute for missing edge (publishedIn for Paper99):

Assume qj = 0.12, SPIR = 0.08, T = 0.5, QF = 0.05


ce(Paper99_publishedIn) = 0.3×0.08 + 0.3×0.5 + 0.4×0.05 = 0.024 + 0.15 + 0.02 = 0.194  
P-edge = 0.194 × 0.12 × log(1/0.12) ≈ 0.194 × 0.12 × 0.92 ≈ 0.0214

Global P-Score = 0.0287 (node) + 0.0214 (edge) = ~0.0501

Interpretation: Moderate level of uncertainty; improvements are needed.


---

🔹 Step 4: Subgraph Extraction

Let’s isolate the “AI Research” subgraph containing papers, people, topics under “Artificial Intelligence”.

Subgraph Composition:

100 instances

60 Paper, 30 Person, 10 Venue


300 triples

60 authoredBy, 30 publishedIn, 20 hasTopic, 10 cites




---

🔹 Step 5: Calculating Subgraph-Level Metrics


---

Subgraph CIR (SCIR)

SCIR(Paper) = 60 / 100 = 0.6
SCIR(Person) = 30 / 100 = 0.3
Indicates paper instances dominate—expected in a paper-centric domain.


---

Subgraph PIR (SPIR)

SPIR(authoredBy) = 60 / 300 = 0.20
SPIR(publishedIn) = 30 / 300 = 0.10
Shows authoredBy is twice as frequent, but publishedIn is still crucial—missing publishedIn triples signal concern.


---

Subgraph Query Frequency (SQF)

Total queries for Paper in subgraph: 1000

Queries for Paper123: 150
SQF(Paper123) = 150 / 1000 = 0.15
High—this paper matters.



---

Subgraph Timeliness (ST)

Let’s say:

Average Δt = 45 days, λ = 0.01
Average ST = exp(-0.01 × 45) = 0.64
Indicates moderate staleness.



---

Subgraph P-Score (Localized)

Missing:

Paper123: missing one cites link

Bob: missing affiliation


Use localized weights and local metrics:

SCIR, SPIR, SQF used in place of global counterparts


cn_local(Bob) = 0.3×SCIR(Person) + 0.3×T + 0.4×SQF = 0.3×0.3 + 0.3×0.6 + 0.4×0.08 = 0.09 + 0.18 + 0.032 = 0.302  
ce_local(Paper123_cites) = 0.3×SPIR + 0.3×T + 0.4×SQF = 0.3×0.06 + 0.3×0.6 + 0.4×0.15 = 0.018 + 0.18 + 0.06 = 0.258

P-Score(subgraph) = 0.302×0.10×log(10) + 0.258×0.12×log(1/0.12)
= 0.0302 + 0.0284 ≈ 0.0586


---

🔹 Step 6: Findings & Recommendations

Metric	Insight	Action

SCIR	Paper class is dominant, but Venue is underrepresented	Increase venue coverage to better normalize scholarly output
SPIR	Cites & publishedIn are sparse	Improve citation crawling; link paper to proper venues
ST	Subgraph is moderately stale	Trigger updates for high-query items
SQF	A few papers are heavily queried	Focus enrichment on these
P-Score	Moderate uncertainty tied to missing, important links	Fix missing affiliations, venues, and citations for top papers



---

✅ Conclusion

This step-by-step evaluation using global and localized metrics enables us to:

Diagnose structural, temporal, and usage gaps

Quantify uncertainty

Prioritize fixes in subgraphs where missing data is both relevant and frequently queried


Would you like to see this entire process implemented in a code-based simulation or generate a visual dashboard of the metrics?
