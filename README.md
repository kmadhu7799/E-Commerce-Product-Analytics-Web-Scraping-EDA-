# E-commerce Product Analytics

![Python](https://img.shields.io/badge/Python-3.x-blue)
![Pandas](https://img.shields.io/badge/Pandas-Data%20Analysis-150458)
![Seaborn](https://img.shields.io/badge/Seaborn-Visualization-76B7B2)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange)

An end-to-end **E-commerce Product Analytics** project built around laptop listings collected from Flipkart. The project focuses on preparing product-level marketplace data, extracting useful attributes from semi-structured text, handling missing values and duplicates, and performing exploratory data analysis (EDA) to understand pricing, ratings, brands, processors, RAM, storage, discounts, and review activity.

> **Project Focus:** Laptop Product Analytics using Flipkart marketplace data
> **Dataset Size:** 984 product records across 41 pages

---

## 📌 Table of Contents

* [Overview](#overview)
* [Objectives](#objectives)
* [Dataset](#dataset)
* [Project Workflow](#project-workflow)
* [Data Cleaning and Feature Engineering](#data-cleaning-and-feature-engineering)
* [Exploratory Data Analysis](#exploratory-data-analysis)
* [Key Analytical Questions](#key-analytical-questions)
* [Technologies Used](#technologies-used)
* [Project Structure](#project-structure)
* [Getting Started](#getting-started)
* [Dataset Schema](#dataset-schema)
* [Important Notes](#important-notes)
* [Possible Extensions](#possible-extensions)
* [Skills Demonstrated](#skills-demonstrated)
* [Project Outcome](#project-outcome)
* [Author](#author)
* [License](#license)

---

## 📊 Overview

E-commerce marketplaces contain a large amount of product information in semi-structured form. Product names, specifications, prices, discounts, ratings, and reviews are valuable for understanding how products are positioned in the market.

This project converts raw Flipkart laptop-product data into a structured analytical dataset and investigates relationships between:

* Product price and original price
* Discounts and product positioning
* Brand and price
* Ratings and price
* Customer rating/review volume
* RAM and price
* Storage and price
* Processor family and product characteristics

The notebook demonstrates a practical data-analysis pipeline from raw CSV data to a cleaned dataset and exploratory visualizations.

---

## 🎯 Objectives

The main objectives of the project are to:

1. Load and inspect raw e-commerce product data.
2. Understand the structure and quality of the dataset.
3. Identify and handle missing values.
4. Check for duplicate records.
5. Extract useful fields from product names and feature descriptions.
6. Convert price and review-related fields into analysis-ready numerical values.
7. Create structured variables such as:

   * Brand
   * Processor
   * RAM
   * Storage
   * Number of ratings
   * Number of reviews
8. Save the cleaned dataset for further analysis.
9. Perform univariate, bivariate, and multivariate exploratory analysis.
10. Identify relationships and patterns that can support e-commerce product and pricing decisions.

---

## 📁 Dataset

The notebook loads the raw dataset from:

```text
455_61_68_flipkart.csv
```

The raw dataset contains **984 rows and 8 columns**.

### Raw Columns

| Column           | Description                                                         |
| ---------------- | ------------------------------------------------------------------- |
| `product name`   | Name/title of the laptop product                                    |
| `Price`          | Current selling price                                               |
| `rating`         | Product rating                                                      |
| `features`       | Product specification/features text                                 |
| `pagenum`        | Marketplace page number from which the product record was collected |
| `original_price` | Original/list price                                                 |
| `Discount`       | Displayed discount                                                  |
| `Review`         | Combined ratings and reviews text                                   |

The dataset contains product information from **41 pages**.

### Data Quality

Before cleaning:

* `rating`: 221 missing values
* `original_price`: 51 missing values
* `Discount`: 60 missing values
* `Review`: 221 missing values
* `product name`: no missing values
* `Price`: no missing values
* `features`: no missing values
* `pagenum`: no missing values
* Duplicate count: `0` at the initial duplicate check

The rating column had 763 non-null observations before missing-value treatment, with an average rating of approximately **4.25** among the available ratings.

---

## 🔄 Project Workflow

The project follows this general pipeline:

```text
Raw Flipkart CSV
       │
       ▼
Data Loading
       │
       ▼
Data Inspection
       │
       ├── Shape / Columns
       ├── Data Types
       ├── Missing Values
       └── Duplicate Check
       │
       ▼
Data Cleaning
       │
       ├── Missing-value treatment
       ├── Text parsing
       ├── Currency cleaning
       └── Data-type conversion
       │
       ▼
Feature Engineering
       │
       ├── Brand
       ├── Processor
       ├── Number of Ratings
       ├── Number of Reviews
       ├── RAM
       └── Storage
       │
       ▼
Cleaned Dataset
       │
       ▼
Exploratory Data Analysis
       │
       ├── Univariate Analysis
       ├── Bivariate Analysis
       └── Multivariate Analysis
       │
       ▼
Business-oriented Product Insights
```

---

## 🧹 Data Cleaning and Feature Engineering

### 1. Missing Value Treatment

The notebook first checks missing values using:

```python
df.isnull().sum()
```

Missing values in several categorical/text-derived fields are filled using the mode:

```python
df["rating"].fillna(df["rating"].mode()[0], inplace=True)
df["original_price"].fillna(df["original_price"].mode()[0], inplace=True)
df["Discount"].fillna(df["Discount"].mode()[0], inplace=True)
df["Review"].fillna(df["Review"].mode()[0], inplace=True)
```

The most frequent rating was **4.2**, which was used to fill missing ratings.

Storage values were also checked and missing storage values were filled using the mode.

> **Analytical Note:** Mode imputation is the approach implemented in the supplied notebook. For production analytics, alternative strategies such as domain-based rules or model-based imputation may be worth evaluating.

---

### 2. Duplicate Handling

The raw data was checked with:

```python
df.duplicated().sum()
```

The initial duplicate count was `0`.

After creating the cleaned dataset and reloading it, duplicate records were checked again and the notebook includes:

```python
df.drop_duplicates(inplace=True)
```

This provides an additional safeguard before analysis.

---

### 3. Brand Extraction

The brand is extracted from the beginning of the product name using a regular expression:

```python
df["Brand"] = df["product name"].apply(
    lambda x: re.findall(r"^\w+", x)[0]
)
```

Examples found in the dataset include:

* HP
* ZEBRONICS
* Lenovo
* Acer
* ASUS

---

### 4. Processor Extraction

The processor family is extracted from the `features` column:

```python
df["processor"] = df["features"].apply(
    lambda x: re.findall(r"^\w+", x)[0]
)
```

This produces processor-family information such as:

* Intel
* MediaTek

---

### 5. Price Conversion

The raw price contains the Indian Rupee symbol and comma separators.

For example:

```text
₹42,990
```

is converted into a numerical value using:

```python
df["Price"] = df["Price"].apply(
    lambda x: re.sub(r"[₹,]", "", x)
).astype("int")
```

The same transformation is applied to `original_price`.

This allows numerical price analysis, distributions, correlations, and outlier detection.

---

### 6. Ratings and Reviews Extraction

The raw `Review` field combines rating count and review count in a string such as:

```text
2,769 Ratings & 162 Reviews
```

The notebook separates this information into:

* `no_of_ratings`
* `no_of_reviews`

This turns unstructured text into numerical analytical variables.

---

### 7. Discount Cleaning

The discount percentage is extracted from the original discount text:

```python
df["Discount"] = df["Discount"].apply(
    lambda x: re.findall(r"[\d%]+", x)[0]
)
```

For example:

```text
15% off
```

becomes:

```text
15%
```

---

### 8. RAM and Storage Extraction

The notebook uses regular expressions to identify GB values from the `features` column:

```python
df["GB_List"] = df["features"].apply(
    lambda x: re.findall(r"(\d+)\s*GB", x)
)
```

These extracted values are then used to create:

* `RAM`
* `Storage`

For example, a specification containing:

```text
16 GB ... 512 GB
```

can be represented as:

```text
RAM = 16
Storage = 512
```

The notebook reports that `512` is the dominant storage value in the extracted storage data.

---

## 📈 Exploratory Data Analysis

The notebook performs three major levels of exploratory analysis:

1. Univariate Analysis
2. Bivariate Analysis
3. Multivariate Analysis

---

## 1. Univariate Analysis

The project examines individual variables to understand their distributions.

### Brand Distribution

The most frequent brands are visualized using:

```python
df["Brand"].value_counts().head(7).plot(kind="bar")
```

This helps identify which brands have the largest representation in the collected product listings.

### Price Distribution

A histogram is used to understand the distribution of product prices:

```python
sns.histplot(x=df["Price"], kde=True, data=df)
```

A box plot is also used:

```python
sns.boxplot(df["Price"])
```

These visualizations help identify:

* Typical price ranges
* Skewness
* High-priced products
* Potential price outliers

### Price Outlier Detection

The notebook calculates the Interquartile Range (IQR):

```python
q1 = df["Price"].quantile(0.25)
q3 = df["Price"].quantile(0.75)
iqr = q3 - q1

outliers = df[
    (df["Price"] < q1 - 1.5 * iqr) |
    (df["Price"] > q3 + 1.5 * iqr)
]
```

The brands associated with the most price outliers are then visualized.

The notebook also contains an optional **winsorization** approach for limiting extreme price values, but this section is commented out and is not part of the executed cleaning pipeline.

---

## 2. Bivariate Analysis

The project examines relationships between pairs of variables.

### Price vs Rating

```python
sns.scatterplot(
    x="Price",
    y="rating",
    data=df
)
```

This helps explore whether higher-priced laptops tend to receive different ratings from lower-priced products.

### RAM vs Price

```python
sns.violinplot(
    x="RAM",
    y="Price",
    data=df
)
```

This provides a distribution-based view of laptop prices across different RAM configurations.

---

## 3. Multivariate Analysis

The project also examines several variables simultaneously.

### Brand, Price, and RAM

```python
sns.scatterplot(
    x="Brand",
    y="Price",
    hue="RAM",
    data=df
)
```

This helps compare price positioning across brands while using RAM as an additional product specification.

### Pairwise Relationships

```python
sns.pairplot(df)
```

The pair plot provides a broad overview of relationships among numerical variables.

### Correlation Heatmap

```python
sns.heatmap(
    df.select_dtypes(include=["int64", "float64"]).corr(),
    annot=True,
    cmap="coolwarm"
)
```

This helps identify linear relationships between numerical product attributes such as:

* Price
* Rating
* Page number
* Original price
* Number of ratings
* Number of reviews
* RAM
* Storage

---

## 🔍 Key Analytical Questions

This project can be used to answer questions such as:

### Pricing

* What is the distribution of laptop prices?
* Which products fall into unusually high or low price ranges?
* How does current price compare with original price?
* Which brands occupy premium or budget price segments?

### Brand Analysis

* Which brands appear most frequently?
* Which brands have the highest-priced products?
* How does price vary across brands?
* Which brands have products with higher RAM configurations?

### Product Specifications

* How does RAM relate to price?
* How does storage capacity relate to price?
* Which processor families are most common?
* Do higher-specification laptops consistently command higher prices?

### Customer Engagement

* Which products have the highest number of ratings?
* Which products have the highest number of reviews?
* Is review volume associated with product price?
* Is rating associated with price?

### Discount Analysis

* Which products receive larger discounts?
* How does discount level relate to original price and selling price?
* Are heavily discounted products concentrated in particular brands or configurations?

---

## 🛠️ Technologies Used

### Programming Language

* Python

### Data Analysis

* NumPy
* Pandas

### Data Visualization

* Matplotlib
* Seaborn

### Text Processing

* Python Regular Expressions (`re`)

### Development Environment

* Jupyter Notebook

---

## 📂 Project Structure

A recommended repository structure is:

```text
ecommerce-product-analytics/
│
├── data/
│   ├── 455_61_68_flipkart.csv
│   └── cleaned_flipkart.csv
│
├── notebooks/
│   └── End-to-End_WebScraping(1).ipynb
│
├── README.md
│
└── requirements.txt
```

If the repository contains only the notebook and datasets, a simpler structure can be used:

```text
ecommerce-product-analytics/
│
├── End-to-End_WebScraping(1).ipynb
├── 455_61_68_flipkart.csv
├── cleaned_flipkart.csv
└── README.md
```

---

## 🚀 Getting Started

### 1. Clone the Repository

```bash
git clone <your-repository-url>
cd ecommerce-product-analytics
```

### 2. Create a Virtual Environment

#### Windows

```bash
python -m venv venv
venv\Scripts\activate
```

#### macOS / Linux

```bash
python3 -m venv venv
source venv/bin/activate
```

### 3. Install Dependencies

Create a `requirements.txt` containing:

```text
numpy
pandas
matplotlib
seaborn
jupyter
```

Then install:

```bash
pip install -r requirements.txt
```

### 4. Launch Jupyter Notebook

```bash
jupyter notebook
```

Open:

```text
End-to-End_WebScraping(1).ipynb
```

### 5. Verify the Input Dataset

The notebook expects the raw CSV:

```text
455_61_68_flipkart.csv
```

Place it in the working directory used by the notebook, or update the path in:

```python
df = pd.read_csv("455_61_68_flipkart.csv")
```

---

## 🔁 Reproducing the Analysis

The notebook can be executed sequentially.

### Step 1 — Load Libraries

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import re
import warnings

warnings.filterwarnings("ignore")
```

### Step 2 — Load Raw Data

```python
df = pd.read_csv("455_61_68_flipkart.csv")
```

### Step 3 — Inspect the Dataset

```python
df.head()
df.tail()
df.describe()
df.info()
df.isnull().sum()
df.duplicated().sum()
```

### Step 4 — Clean and Transform Fields

The notebook performs:

* Missing-value treatment
* Text parsing
* Price conversion
* Review extraction
* Discount cleaning
* Specification extraction

### Step 5 — Export the Cleaned Dataset

```python
df.to_csv("cleaned_flipkart.csv", index=False)
```

### Step 6 — Perform EDA

The notebook then performs:

* Brand frequency analysis
* Price distribution analysis
* Price outlier detection
* Price vs rating analysis
* RAM vs price analysis
* Brand vs price vs RAM analysis
* Pairwise numerical analysis
* Correlation analysis

---

## 📋 Dataset Schema

The cleaned analytical dataset contains the following fields:

| Column           | Type    | Description                                      |
| ---------------- | ------- | ------------------------------------------------ |
| `Price`          | Integer | Current selling price in INR                     |
| `rating`         | Float   | Product rating                                   |
| `pagenum`        | Integer | Source marketplace page number                   |
| `original_price` | Integer | Original/list price in INR                       |
| `Discount`       | String  | Discount percentage                              |
| `Brand`          | String  | Product brand extracted from product name        |
| `processor`      | String  | Processor family extracted from product features |
| `no_of_ratings`  | Integer | Number of product ratings                        |
| `no_of_reviews`  | Integer | Number of product reviews                        |
| `RAM`            | Integer | RAM capacity in GB                               |
| `Storage`        | Integer | Storage capacity in GB                           |

---

## ⚠️ Important Notes

### Product-Level Marketplace Analysis

The dataset represents **product listings**, not customer-level transaction data.

Therefore, metrics such as:

* Revenue
* Conversion rate
* Customer lifetime value
* Order count
* Cart abandonment
* Customer acquisition cost

are **not available in the supplied dataset**.

The project instead focuses on observable product marketplace attributes such as:

* Price
* Specifications
* Ratings
* Reviews
* Discounts
* Brands

### The Notebook Analyzes a Collected CSV

Despite the notebook filename containing `WebScraping`, the supplied notebook begins its analytical workflow by loading:

```text
455_61_68_flipkart.csv
```

The notebook itself does not contain an executed web-scraping implementation. It works from the already-collected CSV and performs data cleaning, feature engineering, and analytics.

### Missing-Value Strategy

The notebook uses mode-based imputation for several missing fields.

This is suitable as a learning/example workflow, but a production pipeline should validate whether mode imputation is appropriate for each business variable.

### Website Terms and Responsible Collection

If the raw data is recollected from an e-commerce website, make sure the collection process follows the site's terms of service, robots/access rules, applicable laws, and reasonable request-rate limits.

---

## 💡 Possible Extensions

This project can be extended into a more complete e-commerce analytics solution.

### 1. Automated Data Collection

Add a dedicated data-collection script that:

* Requests product pages
* Parses product information
* Handles pagination
* Stores raw records
* Logs collection errors
* Saves timestamped datasets

### 2. Advanced Price Analysis

Create price segments such as:

```text
Budget
Mid-range
Premium
Ultra-premium
```

Then compare brands, specifications, ratings, and discounts across segments.

### 3. Discount Effectiveness

Calculate:

```text
Discount Amount = Original Price - Selling Price
```

and investigate whether larger discounts are associated with greater review/rating activity.

### 4. Brand Benchmarking

Create a brand-level summary containing:

* Average price
* Median price
* Average rating
* Average discount
* Average RAM
* Average storage
* Average review count
* Product count

### 5. Product Ranking

Build a composite product score using factors such as:

* Rating
* Review volume
* Price
* Discount
* RAM
* Storage

This could support a recommendation or product-ranking system.

### 6. Dashboard

The cleaned dataset could be connected to:

* Power BI
* Tableau
* Streamlit
* Plotly Dash

A dashboard could provide interactive filters for:

* Brand
* Price range
* RAM
* Storage
* Rating
* Discount
* Processor

### 7. Predictive Analytics

Potential machine-learning extensions include:

* Predicting product price from specifications
* Estimating expected rating/review volume
* Product clustering
* Price-segment classification
* Similar-product recommendation

---

## 🎓 Skills Demonstrated

This project demonstrates practical skills in:

* Python
* Pandas
* NumPy
* Data Cleaning
* Missing-Value Handling
* Duplicate Detection
* Regular Expressions
* Feature Engineering
* Data Type Conversion
* Exploratory Data Analysis
* Statistical Summaries
* Outlier Detection using IQR
* Data Visualization
* Correlation Analysis
* E-commerce Product Analytics

---

## 🏆 Project Outcome

The project transforms raw, semi-structured Flipkart laptop listing data into a clean analytical dataset containing **984 product records and 11 structured variables** before the notebook's final duplicate-removal safeguard.

The resulting dataset is suitable for exploratory analysis of:

* Product pricing
* Brand positioning
* Product specifications
* Discounts
* Ratings
* Review activity
* Relationships among numerical product attributes

The workflow provides a foundation for taking raw marketplace data and turning it into actionable e-commerce analytics.

---

# 👨‍💻 Author

## Madhu Kurakula

**Associate Data Analyst | SQL | Python | Power BI | Data Analytics**

This project is part of my **data analytics portfolio** and demonstrates practical skills in **Python, Pandas, NumPy, data cleaning, feature engineering, exploratory data analysis (EDA), data visualization, and e-commerce product analytics**.

The project analyzes **Flipkart laptop product listings** to understand **product pricing, discounts, ratings, reviews, brands, processors, RAM, and storage configurations**. It also demonstrates the use of **data preprocessing, missing-value handling, regular expressions, outlier detection, correlation analysis, and univariate, bivariate, and multivariate analysis** to generate meaningful business insights from e-commerce product data.

---

## ⭐ If you found this project useful

If you found this project useful or interesting, consider giving the repository a ⭐.

Feel free to explore the **dataset, Jupyter Notebook, data-cleaning process, feature engineering, visualizations, exploratory analysis, and analytical methodology** used in this project.
