# Extreme Multi-Label Classification for Appropriate Assignment of Diagnosis and DRG Codes from Billed Procedures

## Evaluation of Model Performance

Peformance will be measured using metrics that have been widely adopted for XML and ranking tasks. Precision at $k$ (p$@k$) is one such metric that counts the fraction of correct predictions in the top $k$ scoring labels in $\hat{y}$, and has been widely utilized. The ranking measure Normalized (Discounted) Cumlative Gain at $k$ (nDCG$@k$) as another evaluation metric. The p$@k$ and nDCG$@k$ metrics are defined for a predicted score vector $\hat{\mathbf y} \in {\mathbb{R}}^{L}$ and ground truth label vector $\mathbf y \in \left\lbrace 0, 1 \right\rbrace^L$ as:

$$
\begin{align}
        \text{P}@k := \frac{1}{k} \sum_{l\in \text{rank}_k (\hat{\mathbf y})} \mathbf y_l\\[1em]
        \text{DCG}@k := \sum_{l\in {\text{rank}}_k (\hat{\mathbf y})} \frac{\mathbf y_l}{\log(l+1)}\\[1em]
        \text{nDCG}@k := \frac{{\text{DCG}}@k}{\sum_{l=1}^{\min(k, \|\mathbf y\|_0)} \frac{1}{\log(l+1)}},\\[1em]
\end{align}
$$

where, $\text{rank}_k(\mathbf y)$ returns the $k$ largest indices of $\mathbf{y}$ ranked in descending order.

For datasets that contain excessively popular labels (often referred to as "head" labels) such as diagnosis codes, high p$@k$ may be achieved by simply predicting head labels repeatedly irrespective of their relevance to the data point. To check for such trivial behavior, it is recommended that XC methods also be evaluated with respect to propensity-scored counterparts of the p$@k$ and nDCG$@k$ metrics (PSP$@k$ and PSnDCG$@k$) described below.

$$
\begin{align}
        \text{PSP}@k := \frac{1}{k} \sum_{l\in \text{rank}_k (\hat{\mathbf y})} \frac{\mathbf y_l}{p_l}\\[1em]
        \text{PSDCG}@k := \sum_{l\in {\text{rank}}_k (\hat{\mathbf y})} \frac{\mathbf y_l}{p_l\log(l+1)}\\[1em]
        \text{PSnDCG}@k := \frac{{\text{PSDCG}}@k}{\sum_{l=1}^{k} \frac{1}{\log(l+1)}},\\[1em]
\end{align}
$$

where $p_l$ is the propensity score for label $l$ which helps in making metrics unbiased with respect to missing labels. Propensity-scored metrics place specific emphasis on performing well on tail labels and give reduced rewards for predicting popular or head labels. For this study we will use metrics$@k$ in $\{1,3,5\}$.

Given that as of the time of writing, no relevant paper on this topic exists, the overarching purpose of this project will be to establish a benchmark against which future work can be compared.

## Business Justification

Manual creation of data validation rulesets via SME logic is an incredibly time-consuming process. Current estimates are 6-12 weeks of 1 FTE per set (typically 2-4 weeks of 3 FTE). Using a conservative estimate of $58/hour based on an annualized salary of ~$120,000, each new feature costs between $14,000 and $28,000 at a minimum in initial development costs, plus ongoing maintenance and computation costs. Additionally, each medical concept that is not covered by extensive validations opens up the opportunity for adverse outcomes, including but not limited to unnoticed fraud, waste, and abuse, poor downstream model performance, and improper reporting.

Given the intractability of calculating the absolute number of potential validation rules necessary to capture the entire universe of procedure to diagnosis code relationships, we will instead use an idealized relationship where a single ruleset would be sufficient to capture all of the potential interactions between procedures codes and member demographics necessary to accurately assign a diagnosis code. Complete coverage of the more than 69,000 ICD-10 diagnosis codes would require between $960 Million and $1.9 Billion in pure development costs and between 2,600 and 5,200 FTE years of subject matter expert (SME) effort.

With the understanding that it is unlikely for any ruleset, regardless of complexity, to completely capture all of the possible factors that would lead to the assignment of a particular diagnosis code, we will consider our model to be a suitable replacement for a ruleset for any diagnosis code where P$@k$ exceeds some arbitrary threshold. Using the previously described estimates, we will be able to deduce the value of this model in terms of FTE weeks/years of development effort saved.
