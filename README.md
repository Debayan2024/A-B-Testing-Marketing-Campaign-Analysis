# Marketing Campaign Analysis – A/B Testing Project

## 📌 Problem Statement
The company has run **multiple marketing campaigns** to acquire customers.  
We need to determine whether the **Final Campaign performed significantly better** than the first campaign, and recommend if it should be scaled.

## 📂 Dataset
- Source: [Kaggle – Marketing Campaign Dataset](https://www.kaggle.com/datasets/rodsaldanha/arketing-campaign)
- Key columns:
  - `AcceptedCmp1` → Campaign A
  - `AcceptedCmp2` → Campaign B
  - `AcceptedCmp3` → Campaign C
  - `AcceptedCmp4` → Campaign D
  - `AcceptedCmp5` → Campaign E
  - `Response` → Final Campaign

## 🛠️ Tools & Methods
- Python (Pandas, SciPy, Seaborn, Matplotlib)
- Chi-Square Test for Independence (categorical hypothesis testing)

## 📊 Conversion Rates
| Campaign | Conversion Rate (%) |
|----------|----------------------|
| A (Cmp1) | 6.43 |
| B (Cmp2) | 1.34 |
| C (Cmp3) | 7.28 |
| D (Cmp4) | 7.46 |
| E (Cmp5) | 7.28 |
| Final    | 14.91 |

👉 Based on these rates, we focused on **Campaign A vs Final Campaign** for hypothesis testing.

## 🔬 Hypothesis Testing
- **Null Hypothesis (H₀):** There is no difference in conversion between Campaign A and Final Campaign.  
- **Alternative Hypothesis (H₁):** Final Campaign has a different conversion rate than Campaign A.  

**Test Used:** Chi-Square Test of Independence  

**Result:**  
- Chi2 = 82.93  
- p-value = 8.5e-20 (≈ 0)  

✅ Since **p < 0.05**, we reject H₀.  
The difference between Campaign A and Final Campaign is **statistically significant**.

## 📈 Visualization
![Conversion Rates Bar Chart](results/conversion_rates.png)  
![Hypothesis Testing Flowchart](results/hypothesis_testing_flow.png)

## 💡 Key Insights
1. **Final Campaign (14.9%)** more than doubled conversion compared to **Campaign A (6.4%)**.  
2. Campaign B performed worst (1.34%), suggesting poor targeting.  
3. Campaigns C, D, E showed moderate improvements (~7%).  
4. Statistical test confirmed the improvement from Campaign A → Final Campaign is **real, not random**.

## 📌 Business Impact
- The company significantly improved its marketing strategy across campaigns.  
- Final Campaign’s results are strong enough to justify **reallocation of budget** toward strategies modeled on it.  
- Future campaigns should replicate elements from the Final Campaign (better targeting, messaging, or incentives).

---
