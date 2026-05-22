# Part 2: Market Basket Analysis and Product Recommendation

## Business Problem
The goal of this project is to improve product placement, cross-selling, upselling, and promotional bundling by understanding which products are frequently purchased together. This is accomplished using Market Basket Analysis to extract meaningful association rules from transaction data.

## Dataset Description
The dataset contains transaction records from a retail store. The key columns used are `TransactionID`, `ProductName`, and `Quantity`. Each row represents a single line item of a transaction.

## Data Cleaning Summary
1.  **Missing Product Names**: Entries with null product names were removed to ensure valid sets.
2.  **Invalid Quantities**: Any transaction with quantity $\le 0$ (e.g. cancelled/returned) was filtered out.
3.  **Duplicates**: Exact duplicates were dropped to avoid skewing frequency measures.

## Basket Preparation Method
The dataset was aggregated by `TransactionID`. For each transaction, a cross-tabular format was created where columns represent products. The quantity values were then encoded:
- 1 if the product was purchased (quantity $\ge 1$)
- 0 if the product was not purchased.

## Frequent Itemsets Summary
The `FP-Growth` algorithm (via MLxtend) was utilized because of its performance advantages over Apriori on larger datasets. A reasonable threshold for `min_support` (e.g., 2%) was set to ensure we capture widespread patterns without suffering from combinatorial explosion.

## Association Rules Summary
Association rules were generated from the frequent itemsets based on the `lift` metric, filtering by `min_lift >= 1.0`. The metrics calculated are:
- **Support**: Proportion of total baskets containing both antecedent and consequent.
- **Confidence**: Probability of seeing the consequent given the antecedent.
- **Lift**: Ratio of observed support to what would be expected if the antecedent and consequent were independent. Lift $> 1$ indicates a positive association.

## Top Rules with Interpretation
The rules are filtered based on confidence $\ge$ 0.2 and lift $\ge$ 1.2. The algorithm produced over 150 actionable correlations. Below is the interpretation of the strongest association group:

**Example Rule: Baby Diapers $\rightarrow$ Baby Wipes**
1. **Antecedent:** Baby Diapers
2. **Consequent:** Baby Wipes
3. **Support (0.026):** 2.6% of ALL checkouts contained both items.
4. **Confidence (0.922):** 92.2% of the customers who bought Baby Diapers went on to buy Baby Wipes.
5. **Lift (11.35):** Customers are 11.35 times more likely to buy Baby Wipes if they buy Baby Diapers.
6. **Business Meaning:** Diaper buyers almost inevitably need wipes. Ensure they are shelved in the same aisle physically or grouped via "Frequently Bought Together" prompts.

*(See `notebook.ipynb` for the full dataset rules, visualizations, and the 10 distinct extracted interpretations in the filtering phase).*

## Final Business Recommendations
1. **Bundling**: Products with high confidence should be sold together with a slight discount.
2. **Store Layout**: High lift items should be placed geographically close to boost organic cross-selling.
3. **Promotions**: If an item drives the sales of another (high lift), discount the antecedent to boost the sales of the consequent.
4. **Ignore Weak Rules**: Rules with Lift close to 1.0 or high Support but low Confidence should be ignored as they represent coincidentally popular items rather than truly associated products.

## How to Run the Project
1. Install requirements:
   ```bash
   pip install -r requirements.txt
   ```
2. Open and run `notebook.ipynb` in VS Code or Jupyter Notebook. 
