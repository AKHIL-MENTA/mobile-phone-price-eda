# Project Documentation: Mobile Phone Price Analysis

## 1. Project Objective
To explore a mobile phone dataset, find and fix data-quality errors, understand which specifications drive the price of a phone, and build a machine learning model that predicts the **price category** (Budget / Mid-range / Premium).

## 2. Dataset Description
- **Raw file:** `mobile_phone_price_eda.csv`, 1,505 rows × 12 columns
- **Cleaned file:** `mobile_phone_price_eda_cleaned.csv`, 1,500 rows × 12 columns (created by the notebook)
- **Currency:** Indian Rupees (₹)
- **Coverage:** 10 brands, 50 phone models, 6 processor types, 2 operating systems

### 2.1 Column Details
| Column | Description | Type | Range / Values |
|--------|-------------|------|----------------|
| brand | Phone manufacturer | Text | Samsung, Xiaomi, Realme, Vivo, OnePlus, Apple, Oppo, Nothing, Google, Motorola |
| model | Phone model | Text | 50 unique models |
| price | Price in ₹ | Integer | 21,700 – 1,31,800 (mean ≈ 58,889) |
| ram | RAM in GB | Integer | 4 – 24 (median 8) |
| storage | Internal storage in GB | Integer | 64 – 1024 (median 256) |
| battery | Battery in mAh | Float | 3,500 – 6,458 (mean ≈ 4,982) |
| screen_size | Screen size in inches | Float | 6.1 – 7.0 |
| camera | Main camera in MP | Float | 12 – 200 (median 50) |
| rating | User rating | Float | 2.7 – 5.0 (mean ≈ 4.10) |
| 5G | 5G support | Text | Yes / No (renamed to `is_5g`) |
| processor | Chipset | Text | Snapdragon, MediaTek Dimensity, Unisoc, Exynos, Apple A-series, Google Tensor |
| os | Operating system | Text | Android (1,232 rows), iOS (273 rows) in the raw file |

## 3. Tools and Libraries
| Tool | Purpose |
|------|---------|
| Python 3 | Programming language |
| Pandas, NumPy | Data loading, cleaning, calculations |
| Matplotlib, Seaborn | Charts and visualisation |
| Scikit-learn | Machine learning models and evaluation metrics |
| Jupyter Notebook | Running and presenting the code |

## 4. Methodology
The notebook follows this workflow:

1. Load data → 2. Audit data quality → 3. Clean data → 4. EDA → 5. Feature engineering → 6. Model training and evaluation → 7. Prediction from user input

### 4.1 Data Loading
The CSV is read with `pd.read_csv()` and inspected using `head()`, `info()` and `describe()` to understand the shape, data types and value ranges.

### 4.2 Data Quality Audit
Before analysis, the notebook counts these problems:
- Duplicate rows (5 found)
- Missing values (15 each in `battery`, `camera`, `rating`)
- Logically impossible combinations, such as an Apple phone running Android, an Apple chip inside a non-Apple phone, an iPhone without 5G, or an iPhone with more than 8 GB RAM

### 4.3 Data Cleaning
| # | Problem | Fix | Reason |
|---|---------|-----|--------|
| 1 | Duplicate rows | Drop duplicates | Duplicates give extra weight to repeated records |
| 2 | Column `5G` starts with a digit | Rename to `is_5g` | Awkward to use in code |
| 3 | Missing battery, camera, rating | Median of the same model (fallback: overall median) | Phones of the same model have similar specs |
| 4 | OS does not match brand | iOS only for Apple, Android for others | iPhones run only iOS |
| 5 | Brand-exclusive chips on wrong brands | Apple→A-series, Google→Tensor, Exynos→Samsung only; others reassigned by price tier | These chips are not sold to other brands |
| 6 | Apple phones with no 5G | Set to Yes | iPhone 12 and later support 5G |
| 7 | Apple RAM above 8 GB | Capped at 8 GB | iPhones do not have more than 8 GB RAM |

After cleaning, the audit is repeated and every error count is **0**.

### 4.4 Exploratory Data Analysis
- **Univariate:** histograms (with KDE) of numeric columns; count plots of brand, 5G, processor and OS
- **Bivariate:** box plots of price by brand, RAM, storage, camera, 5G, OS, processor; scatter plots of price vs battery, screen size and rating
- **Correlation:** heatmap of numeric columns
- **Outliers:** IQR method (values beyond 1.5 × IQR). Extreme values such as 1 TB storage and 200 MP cameras are real flagship specs, so they were kept.

### 4.5 Feature Engineering
`price` was converted into a 3-class target:

| Category | Price Range | Count |
|----------|-------------|-------|
| Budget | Below ₹45,000 | 328 |
| Mid-range | ₹45,000 – ₹70,000 | 820 |
| Premium | Above ₹70,000 | 352 |

Categorical features (brand, is_5g, processor, os) were converted with one-hot encoding. `price` is **not** an input feature, because that would leak the answer to the model.

### 4.6 Model Building
- Train/test split: 80% / 20%, `random_state=42`, stratified by price category
- Models: Logistic Regression (with standard scaling), Decision Tree (max depth 8), Random Forest (300 trees)
- Metrics: Accuracy, Precision, Recall, F1-Score (weighted averages)

## 5. Results
| Model | Accuracy | Precision | Recall | F1-Score |
|-------|----------|-----------|--------|----------|
| **Logistic Regression** | **0.8167** | **0.8178** | **0.8167** | **0.8145** |
| Decision Tree | 0.7933 | 0.7930 | 0.7933 | 0.7899 |
| Random Forest | 0.7733 | 0.7856 | 0.7733 | 0.7642 |

Logistic Regression had the best F1-score and was selected as the best model. Exact values can differ slightly across library versions.

## 6. Key Findings
1. **Apple is the costliest brand** (average ≈ ₹90,119); **Xiaomi is the cheapest** (≈ ₹49,393).
2. Average brand prices: Apple 90,119 > Google 72,215 > OnePlus 64,414 > Nothing 60,670 > Oppo 56,505 > Samsung 53,216 > Vivo 52,551 > Motorola 51,844 > Realme 49,535 > Xiaomi 49,393.
3. **iOS phones average ≈ ₹90,119; Android phones ≈ ₹55,826.**
4. **5G phones average ≈ ₹60,699; non-5G phones ≈ ₹53,647.**
5. **Correlation with price:** storage 0.46, RAM 0.36, camera 0.13, battery 0.05, screen size −0.03, rating −0.01. Storage and RAM matter most.
6. A tree-based feature importance check showed numeric specs (RAM, storage, battery, screen size) contributing most to the Random Forest model.

## 7. Conclusion
Storage and RAM are the strongest numeric indicators of phone price, and brand (especially Apple) has a large effect. Rating, screen size and battery have little direct relationship with price. After fixing the data errors, a simple Logistic Regression model predicts the price category with about 82% accuracy.

## 8. Limitations
- The dataset appears synthetic or partly synthetic (for example, it needed many logical fixes), so findings may not match real market prices.
- Only 1,500 rows and 50 models; many rows share the same model.
- Some cleaning decisions (such as reassigning processors by price tier) are assumptions.
- Price categories use fixed thresholds that were chosen manually.
- No cross-validation or hyperparameter tuning was done for the final comparison.

## 9. Future Work
- Predict exact price using regression (Linear Regression, Random Forest Regressor, XGBoost)
- Use cross-validation and hyperparameter tuning
- Test on real market data
- Deploy a Streamlit app where users enter specs and get a predicted category

## 10. How to Run the Project
1. Clone or download the repository
2. Install libraries: `pip install -r requirements.txt`
3. Run `jupyter notebook` and open `Mobile_Phone_Price_Analysis_updated.ipynb`
4. Keep `mobile_phone_price_eda.csv` in the same folder as the notebook
5. Click **Kernel → Restart & Run All**

Running the notebook also creates `mobile_phone_price_eda_cleaned.csv`.

## 11. Folder Structure
```
mobile-phone-price-eda/
├── README.md
├── requirements.txt
├── .gitignore
├── mobile_phone_price_eda.csv
├── mobile_phone_price_eda_cleaned.csv
├── Mobile_Phone_Price_Analysis_updated.ipynb
└── docs/
    └── project_documentation.md
```
