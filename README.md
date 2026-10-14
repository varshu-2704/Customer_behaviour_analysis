<a id="top"></a>

# Customer Shopping Behavior Analysis

An end-to-end data analytics project that explores how customers shop: what they buy, how much they spend, how they respond to discounts and subscriptions, and which segments drive revenue. The workflow runs from raw CSV to cleaned data in **[Python](https://www.python.org/)**, business analysis in **[MySQL](https://www.mysql.com/)**, and an interactive **[Power BI](https://powerbi.microsoft.com/)** dashboard.

**Quick links:** [Notebook](CUSTOMER_SHOPPING_ANALYSIS.IPYNB) · [SQL queries](Customer_behaviour_SQLqueries.sql) · [Dataset](customer_shopping_behavior.csv) · [Dashboard](customer_behavior_dashboard.pbix) · [Report (PDF)](Customer_Shopping_Behavior_Analysis.pdf) · [Slides (PDF)](Customer-Shopping-Behavior-Analysis..pdf)

---

## Table of Contents

- [Project Overview](#project-overview)
- [Dataset](#dataset)
- [Tools and Technologies](#tools-and-technologies)
- [Methodology](#methodology)
- [Business Questions Answered (SQL)](#business-questions-answered-sql)
- [Key Insights](#key-insights)
- [Recommendations](#recommendations)
- [Repository Structure](#repository-structure)
- [How to Run](#how-to-run)
- [Limitations](#limitations)
- [License](#license)
- [Author](#author)

---

## Project Overview

Retail teams need to know *who* their customers are and *what* influences their spending. This project analyzes 3,900 purchase records to answer questions about:

- Revenue by gender, age group and product category
- The effect of discounts, subscriptions and shipping type on spending
- Customer segmentation by purchase history (New / Returning / Loyal)
- Best-rated, most-purchased and most-discounted products

The goal is to turn transaction data into insights that can guide marketing, pricing and loyalty strategy.

[Back to top](#top)

---

## Dataset

| Property | Details |
|---|---|
| File | [`customer_shopping_behavior.csv`](customer_shopping_behavior.csv) |
| Records | 3,900 purchases |
| Columns | 18 |
| Customers' age range | 18 – 70 |
| Locations | 50 US states |
| Missing values | 37 in `Review Rating` |

**Columns:** Customer ID, Age, Gender, Item Purchased, Category, Purchase Amount (USD), Location, Size, Color, Season, Review Rating, Subscription Status, Shipping Type, Discount Applied, Promo Code Used, Previous Purchases, Payment Method, Frequency of Purchases.

**Categories:** Clothing, Accessories, Footwear, Outerwear.

[Back to top](#top)

---

## Tools and Technologies

| Tool | Purpose |
|---|---|
| [Python](https://www.python.org/) · [pandas](https://pandas.pydata.org/) | Data cleaning and feature engineering |
| [SQLAlchemy](https://www.sqlalchemy.org/) · [PyMySQL](https://pymysql.readthedocs.io/) | Loading the cleaned data into MySQL |
| [Jupyter Notebook](https://jupyter.org/) | Exploratory analysis and data preparation |
| [MySQL](https://www.mysql.com/) | Business analysis using aggregations, subqueries, CTEs and window functions |
| [Power BI](https://powerbi.microsoft.com/) | Interactive dashboard (`.pbix`) |

[Back to top](#top)

---

## Methodology

### 1. Data Preparation and EDA (Python)
Code: [`CUSTOMER_SHOPPING_ANALYSIS.IPYNB`](CUSTOMER_SHOPPING_ANALYSIS.IPYNB)

- Loaded the CSV with pandas and inspected structure with `df.info()` and `df.describe()`
- **Missing values:** imputed the 37 missing `Review Rating` values with the **median rating of each product category**
- **Standardized column names** to snake_case (e.g. `Purchase Amount (USD)` becomes `purchase_amount`)
- **Feature engineering:**
  - `age_group`: customers split into four quartile-based groups: Young Adult (18–31), Adult (32–44), Middle-aged (45–57), Senior (58–70)
  - `purchase_frequency_days`: purchase frequency text (Weekly, Monthly, Quarterly, etc.) mapped to a number of days
- **Redundancy check:** `discount_applied` and `promo_code_used` were identical in every row, so `promo_code_used` was dropped

### 2. Database Integration
- Connected Python to a local MySQL database (`customer_behaviour`) and wrote the cleaned DataFrame to a `customer` table

### 3. SQL Analysis
- Answered 10 business questions (listed below) in [`Customer_behaviour_SQLqueries.sql`](Customer_behaviour_SQLqueries.sql)

### 4. Visualization
- Built an interactive dashboard in Power BI: [`customer_behavior_dashboard.pbix`](customer_behavior_dashboard.pbix)

[Back to top](#top)

---

## Business Questions Answered (SQL)

All queries are in [`Customer_behaviour_SQLqueries.sql`](Customer_behaviour_SQLqueries.sql).

1. What is the total revenue from male vs. female customers?
2. Which customers used a discount but still spent more than the average purchase amount?
3. Which 5 products have the highest average review rating?
4. How do average purchase amounts compare between Standard and Express shipping?
5. Do subscribed customers spend more than non-subscribers?
6. Which 5 products have the highest share of discounted purchases?
7. How many customers fall into the New, Returning and Loyal segments?
8. What are the top 3 most purchased products within each category?
9. Are repeat buyers (more than 5 previous purchases) also likely to subscribe?
10. What is the revenue contribution of each age group?

[Back to top](#top)

---

## Key Insights

Overall: **$233,081** total revenue, **$59.76** average purchase, **3.75** average review rating.

| Area | Finding |
|---|---|
| **Gender** | Male customers generated **$157,890** vs. **$75,191** from female customers. This follows the customer mix (68% of purchases are by men); average spend per purchase is almost the same (~$59.5 vs ~$60.3). |
| **Category** | Clothing leads with **$104,264** (about 45% of revenue), followed by Accessories ($74,200), Footwear ($36,093) and Outerwear ($18,524). |
| **Discounts** | 43% of purchases used a discount. **839** discounted purchases were still above the average spend. Hats (50%), Sneakers (49.7%) and Coats (49.1%) have the highest discount rates. |
| **Top-rated products** | Gloves (3.86), Sandals (3.84), Boots (3.82), Hat (3.80), Handbag (3.78). |
| **Shipping** | Express shipping averages **$60.48** vs. **$58.46** for Standard, a small difference of about 3%. |
| **Subscriptions** | 1,053 subscribers (27% of purchases) average **$59.49** vs. **$59.87** for non-subscribers, so subscribing does not make customers spend more per purchase. Subscribers contribute about 27% of revenue. |
| **Segments** | Based on previous purchases: **Loyal 79.9%** (3,116), Returning 18.0% (701), New 2.1% (83). |
| **Repeat buyers** | Of 3,476 customers with more than 5 previous purchases, 958 (28%) are subscribers, about the same as the overall subscription rate. |
| **Age groups** | Young Adults (18–31) contribute the most at $62,143, then Middle-aged $59,197, Adult $55,978 and Senior $55,763. Differences are modest. |
| **Top products per category** | Accessories: Jewelry, Belt, Sunglasses · Clothing: Blouse, Pants, Shirt · Footwear: Sandals, Shoes, Sneakers · Outerwear: Jacket, Coat |

> Figures were recomputed from the dataset using the same logic as the SQL queries and the cleaning steps in the notebook.

[Back to top](#top)

---

## Recommendations

- **Rework the subscription offer.** Subscribers don't spend more per purchase, so add real benefits (early access, member pricing, free shipping) that justify subscribing and lift spend.
- **Review the discount policy.** Nearly half of Hat, Sneaker and Coat purchases are discounted. Test whether those discounts drive sales or just give away margin.
- **Grow the female customer base.** Spend per purchase is equal across genders, so more female customers would directly increase revenue.
- **Feature top-rated products** (Gloves, Sandals, Boots) in campaigns and invest in Clothing and Accessories, the biggest revenue drivers.
- **Revisit segment definitions.** With the current thresholds almost 80% of customers are "Loyal"; tighter cut-offs would give more useful segments.
- **Use shipping as a small upsell lever**, since Express customers spend slightly more.

[Back to top](#top)

---

## Repository Structure

```
Customer_behaviour_analysis/
├── CUSTOMER_SHOPPING_ANALYSIS.IPYNB
├── Customer_behaviour_SQLqueries.sql
├── customer_shopping_behavior.csv
├── customer_behavior_dashboard.pbix
├── Customer_Shopping_Behavior_Analysis.pdf
├── Customer-Shopping-Behavior-Analysis..pdf
├── LICENSE
└── README.md
```

| File | Description |
|---|---|
| [`CUSTOMER_SHOPPING_ANALYSIS.IPYNB`](CUSTOMER_SHOPPING_ANALYSIS.IPYNB) | Python: cleaning, feature engineering, MySQL load |
| [`Customer_behaviour_SQLqueries.sql`](Customer_behaviour_SQLqueries.sql) | 10 SQL business-analysis queries |
| [`customer_shopping_behavior.csv`](customer_shopping_behavior.csv) | Raw dataset (3,900 rows, 18 columns) |
| [`customer_behavior_dashboard.pbix`](customer_behavior_dashboard.pbix) | Power BI dashboard |
| [`Customer_Shopping_Behavior_Analysis.pdf`](Customer_Shopping_Behavior_Analysis.pdf) | Written project report |
| [`Customer-Shopping-Behavior-Analysis..pdf`](Customer-Shopping-Behavior-Analysis..pdf) | Presentation slides |
| [`LICENSE`](LICENSE) | GNU GPL v3.0 |

[Back to top](#top)

---

## How to Run

**Prerequisites:** [Python 3.8+](https://www.python.org/downloads/), [MySQL Server](https://dev.mysql.com/downloads/mysql/), [Power BI Desktop](https://powerbi.microsoft.com/desktop/), [Jupyter](https://jupyter.org/install).

1. **Clone the repository**
   ```bash
   git clone https://github.com/varshu-2704/Customer_behaviour_analysis.git
   cd Customer_behaviour_analysis
   ```

2. **Install dependencies**
   ```bash
   pip install pandas sqlalchemy pymysql jupyter
   ```

3. **Create the database** in MySQL:
   ```sql
   CREATE DATABASE customer_behaviour;
   ```

4. **Run the notebook** [`CUSTOMER_SHOPPING_ANALYSIS.IPYNB`](CUSTOMER_SHOPPING_ANALYSIS.IPYNB). Before running, set your own MySQL username, password, host and port in the connection cell (preferably via environment variables, never hard-coded). The notebook cleans the data and writes it to a `customer` table.

5. **Run the SQL queries** in [`Customer_behaviour_SQLqueries.sql`](Customer_behaviour_SQLqueries.sql) using MySQL Workbench or the command line.

6. **Open the dashboard:** open [`customer_behavior_dashboard.pbix`](customer_behavior_dashboard.pbix) in Power BI Desktop (it may need its data source pointed to your local database or the [CSV](customer_shopping_behavior.csv)).

[Back to top](#top)

---

## Limitations

- Review-rating imputation uses category medians, which can smooth over differences between products.
- The dataset is a single snapshot with no purchase dates, so trends over time can't be analyzed.
- Age groups are quartile-based, not fixed marketing age brackets.

[Back to top](#top)

---

## License

This project is licensed under the **[GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.html)**. See the [LICENSE](LICENSE) file for details.

[Back to top](#top)

---

## Author

**varshu-2704** · [GitHub profile](https://github.com/varshu-2704) · [Repository](https://github.com/varshu-2704/Customer_behaviour_analysis)
