# RetailRecX

## An Explainable and Ethical E-Commerce Recommendation System

RetailRecX is a machine-learning-based personalised e-commerce recommendation system designed around **personalisation, explainability, and ethical recommendation practices**.

The system uses a **Random Forest classifier** to estimate the likelihood that a customer will purchase a particular product. **SHAP** and **LIME** are used to explain individual recommendations, while popularity-bias analysis evaluates how recommendations are distributed across popular and less-popular products.

The project also evaluates two mitigation strategies:

- Popularity-Aware Re-ranking
- Long-Tail Re-ranking

The report describes the complete pipeline from the Online Retail dataset through preprocessing, feature engineering, model scoring, XAI, bias analysis, mitigation, and recommendation output. fileciteturn3file0L70-L79

---

## Objectives

1. Develop personalised product recommendations using historical transaction data.
2. Estimate customer-product purchase likelihood with machine learning.
3. Generate personalised Top-K recommendations.
4. Explain recommendations using SHAP.
5. Explain individual recommendations using LIME.
6. Compare SHAP and LIME explanations.
7. Identify popularity bias.
8. Evaluate popularity-aware and long-tail re-ranking.
9. Analyse trade-offs between relevance, diversity, catalogue coverage, and popularity concentration.
10. Explore responsible use of Generative AI for recommendation explanations.

---

## System Architecture

```text
Online Retail Dataset
        │
        ▼
Data Cleaning & Transformation
        │
        ▼
Feature Engineering
 ┌──────┼────────────────────┐
 ▼      ▼                    ▼
Customer Product   Customer-Product
Features Features     Interactions
        │
        ▼
Random Forest Model
        │
        ▼
Purchase Probability
        │
        ▼
Candidate Product Ranking
        │
        ├──────────────┐
        ▼              ▼
      SHAP            LIME
        │              │
        └──────┬───────┘
               ▼
      Explainable Recommendations
               │
               ▼
       Popularity Bias Analysis
          ┌────┴────┐
          ▼         ▼
  Popularity-     Long-Tail
  Aware Ranking   Re-ranking
          │         │
          └────┬────┘
               ▼
      Recommendation Evaluation
```

---

# Dataset

RetailRecX uses the **Online Retail dataset** containing historical e-commerce transaction records.

### Original Dataset

- **541,909 transactions**
- **8 attributes**

Attributes include:

| Attribute | Description |
|---|---|
| Invoice Number | Transaction identifier |
| Stock Code | Product identifier |
| Product Description | Product description |
| Quantity | Quantity purchased |
| Invoice Date | Transaction date/time |
| Unit Price | Product unit price |
| Customer ID | Customer identifier |
| Country | Customer country |

These fields provide customer purchasing and product-level transaction information. fileciteturn3file0L57-L62

### After Preprocessing

Records with missing customer IDs, cancelled transactions, invalid quantities/prices, and other unsuitable records were removed.

| Measure | Result |
|---|---:|
| Transactions | **391,283** |
| Customers | **4,334** |
| Products | **3,660** |

Invoice dates were transformed and customer-product interaction features were created. fileciteturn3file0L63-L67

---

# Feature Engineering

The recommendation pipeline creates three main feature groups.

### Customer Features

- Total purchases
- Total spending
- Purchase frequency
- Recency

### Product Features

- Product popularity
- Customer reach
- Product purchasing characteristics

### Customer-Product Interaction Features

- Customer-product recency
- Customer-product spending
- Previous purchase count
- Previous interaction

These features combine customer behaviour, product characteristics, and historical interactions. fileciteturn3file0L70-L75

---

# Recommendation Model

RetailRecX uses a **Random Forest classifier** to estimate the likelihood of a customer purchasing a particular product.

```text
Customer Features
       +
Product Features
       +
Customer-Product Features
       │
       ▼
Random Forest
       │
       ▼
Purchase Probability
       │
       ▼
Product Ranking
       │
       ▼
Personalised Top-K
Recommendations
```

Products are ranked according to predicted scores and the highest-ranked products are presented as personalised recommendations. fileciteturn3file0L89-L100

---

# Baseline Results

The evaluation set contained **1,317 customers**.

| Metric | Result |
|---|---:|
| ROC-AUC | **0.8862** |
| Precision@10 | **0.3251** |
| Recall@10 | **0.2046** |
| NDCG@10 | **0.3850** |
| Hit Rate@10 | **0.8671** |
| Catalogue Coverage | **29.93%** |
| Diversity | **0.0824** |

The Random Forest model achieved ROC-AUC 0.8862, while the baseline recommendation system achieved Hit Rate@10 of 0.8671. fileciteturn3file0L96-L110

---

# Explainable AI

RetailRecX uses:

- **SHAP**
- **LIME**

The XAI layer identifies factors influencing individual recommendations and improves transparency.

## SHAP

SHAP assigns feature contribution values showing whether individual features increase or decrease recommendation likelihood. fileciteturn3file0L114-L124

### Important SHAP Features

| Rank | Feature | Interpretation |
|---:|---|---|
| 1 | Customer-Product Recency | Recent interaction increases relevance |
| 2 | Customer-Product Spending | Higher historical spending indicates preference |
| 3 | Previous Purchase Count | Repeated purchases indicate interest |
| 4 | Previous Interaction | Existing interaction supports recommendation |
| 5 | Product Customer Reach | Wider customer reach influences recommendation |
| 6 | Total Quantity Purchased | Higher quantity indicates product preference |

fileciteturn3file0L120-L137

## LIME

LIME explains individual recommendations by approximating model behaviour around a selected customer-product instance and identifying influential feature conditions. fileciteturn3file0L138-L149

## SHAP vs LIME

| Aspect | SHAP | LIME |
|---|---|---|
| Explanation | Global and local | Mainly local |
| Approach | Feature contributions | Local surrogate model |
| Output | Contribution to score | Influential conditions/features |
| Agreement | — | 5 of top 6 features overlap |

For the same recommendation, **5 of the top 6 important features overlapped** between SHAP and LIME. fileciteturn3file0L150-L175

---

# Ethical Analysis: Popularity Bias

Popularity bias occurs when already-popular products receive disproportionately high recommendation exposure while less-popular products receive fewer opportunities.

In the baseline system, the top 20% of products represented only 20% of the catalogue but occupied **88.64% of recommendation slots**. fileciteturn3file0L185-L196

| Measure | Baseline |
|---|---:|
| Top 20% of Catalogue | 20% |
| Recommendation Slots from Top 20% | **88.64%** |
| Catalogue Coverage | **29.93%** |
| Diversity | **0.0824** |

---

# Popularity-Aware Re-ranking

A popularity penalty is introduced during ranking to reduce the excessive influence of highly popular products.

### Results

| Metric | Baseline | Popularity-Aware |
|---|---:|---:|
| Top-20% Popularity Share | 88.64% | **25.15%** |
| Precision@10 | 0.3251 | 0.2128 |
| NDCG@10 | 0.3850 | 0.2510 |
| Catalogue Coverage | 29.93% | **48.80%** |
| Diversity | 0.0824 | **0.1343** |

The mitigation substantially reduced popularity concentration and increased catalogue coverage and diversity, while Precision@10 and NDCG@10 decreased. fileciteturn3file0L197-L206

---

# Long-Tail Re-ranking

The long-tail strategy reserves recommendation positions for less popular products.

| Metric | Baseline | Long-Tail |
|---|---:|---:|
| Top-20% Popularity Share | 88.64% | **59.13%** |
| Precision@10 | 0.3251 | **0.3000** |
| NDCG@10 | 0.3850 | **0.3154** |
| Hit Rate@10 | 0.8671 | **0.8633** |
| Catalogue Coverage | 29.93% | **35.83%** |
| Diversity | 0.0824 | **0.0986** |

The report describes this strategy as increasing exposure outside the dominant popular group while preserving recommendation quality more effectively than the popularity-aware strategy. fileciteturn3file0L222-L232

---

# Full Mitigation Comparison

| Metric | Baseline | Popularity-Aware | Long-Tail |
|---|---:|---:|---:|
| Top-20% Popularity Share | 88.64% | 25.15% | 59.13% |
| Precision@10 | 0.3251 | 0.2128 | 0.3000 |
| NDCG@10 | 0.3850 | 0.2510 | 0.3154 |
| Hit Rate@10 | 0.8671 | — | 0.8633 |
| Catalogue Coverage | 29.93% | 48.80% | 35.83% |
| Diversity | 0.0824 | 0.1343 | 0.0986 |

The experiments show trade-offs between recommendation relevance, diversity, catalogue coverage, and popularity concentration. fileciteturn3file0L207-L218

---

# Generative AI

Generative AI is proposed as a **controlled explanation layer**, not as the component that independently chooses products.

```text
Recommendation Model
        │
        ▼
Recommended Products
        │
        ├──► SHAP Evidence
        │
        └──► LIME Evidence
                 │
                 ▼
        Verified Evidence
                 │
                 ▼
          Generative AI
                 │
                 ▼
       Natural-Language
          Explanation
```

The recommendation model first produces the recommendation and SHAP/LIME provide supporting evidence. Generative AI can then translate that evidence into natural-language explanations. fileciteturn3file0L233-L245

### Possible Applications

- Explain why a product was recommended.
- Explain how previous purchasing behaviour influenced a recommendation.
- Provide alternative products.
- Support conversational recommendation interfaces.

### Risks

- Hallucinated explanations
- Bias reproduction
- Privacy risks
- Overly persuasive explanations

The report therefore recommends keeping the underlying recommendation and XAI mechanisms as the evidence source and grounding generated explanations in verified SHAP/LIME outputs. fileciteturn3file0L246-L262

---

# Evaluation Framework

### Recommendation Performance

- ROC-AUC
- Precision@10
- Recall@10
- NDCG@10
- Hit Rate@10

### Recommendation Quality

- Catalogue Coverage
- Diversity

### Explainability

- SHAP feature contributions
- LIME local explanations
- SHAP/LIME agreement

### Ethical Evaluation

- Top-20% popularity share
- Catalogue coverage
- Diversity
- Recommendation relevance
- Popularity concentration

---

# Key Findings

- Random Forest achieved **ROC-AUC = 0.8862**.
- Baseline **Hit Rate@10 = 0.8671**.
- SHAP and LIME had **5/6 important features in common** for the selected recommendation.
- The baseline top 20% of products occupied **88.64% of recommendation slots**.
- Popularity-aware re-ranking reduced this to **25.15%**.
- Long-tail re-ranking reduced it to **59.13%**.
- Both mitigation strategies increased catalogue coverage and diversity, with different effects on recommendation relevance. fileciteturn3file0L263-L280

---

# Limitations

1. The Online Retail dataset contains historical transaction records and does not include demographic or detailed preference information.
2. The system may face cold-start problems for new customers and products.
3. Customer-product interactions can be sparse.
4. Evaluation uses historical offline data rather than real-time user interactions.
5. Offline metrics may not fully represent real-world user responses.
6. Ethical findings depend on the characteristics of the selected dataset.
7. SHAP and LIME do not guarantee that users will correctly understand or trust explanations.
8. Generative AI could introduce hallucination or misleading explanations if not grounded in verified evidence. fileciteturn3file0L281-L301

---

# Project Workflow

```text
1. Load Online Retail Dataset
        ↓
2. Clean Transaction Data
        ↓
3. Transform Invoice Dates
        ↓
4. Create Customer Features
        ↓
5. Create Product Features
        ↓
6. Create Customer-Product Features
        ↓
7. Train Random Forest
        ↓
8. Generate Purchase Scores
        ↓
9. Rank Candidate Products
        ↓
10. Generate Top-K Recommendations
        ↓
11. Generate SHAP Explanations
        ↓
12. Generate LIME Explanations
        ↓
13. Analyse Popularity Bias
        ↓
14. Apply Re-ranking Strategies
        ↓
15. Compare Performance, Diversity and Coverage
```

---

# Suggested Repository Structure

```text
RetailRecX/
│
├── README.md
├── data/
├── notebooks/
│   ├── preprocessing.ipynb
│   ├── recommendation_model.ipynb
│   ├── shap_analysis.ipynb
│   ├── lime_analysis.ipynb
│   └── bias_mitigation.ipynb
├── src/
│   ├── preprocessing/
│   ├── features/
│   ├── recommendation/
│   ├── explainability/
│   └── mitigation/
├── models/
├── results/
│   ├── shap/
│   ├── lime/
│   └── bias_analysis/
├── requirements.txt
└── docs/
    └── project_report.pdf
```

This is a recommended GitHub organisation and is not presented as an exact folder structure from the report.

---

# Conclusion

RetailRecX demonstrates a practical approach to building a personalised e-commerce recommendation system with a stronger focus on **explainability and ethical recommendation practices**.

The Random Forest model achieved a ROC-AUC of **0.8862**, while the baseline recommendation system achieved a Hit Rate@10 of **0.8671**.

SHAP and LIME provided complementary explanations, with substantial agreement between the two methods. The ethical analysis identified significant popularity bias, and both re-ranking strategies reduced popularity concentration while improving catalogue coverage and diversity, with different relevance trade-offs.

The project also proposes Generative AI as a natural-language explanation layer. The report emphasises that generated explanations should remain grounded in verified recommendation evidence to reduce hallucination, bias, privacy, and misleading-explanation risks. fileciteturn3file0L302-L324

---

# Project Information

| Field | Information |
|---|---|
| Project | RetailRecX |
| Title | An Explainable and Ethical E-Commerce Recommendation System |
| Assessment | CA2 |
| Student | Taimoor Ahmad |
| Student Number | 20054191 |
| Teacher | Nitya Govindaraju |
| Institution | Dublin Business School |
| Date | August 2025 |

The report cover identifies the project as CA2 and provides these project details. fileciteturn3file0L1-L3

---

## Academic Areas

- E-Commerce
- Analytics
- AI & Machine Learning
- Ethics & Fairness
- Recommendation Systems

