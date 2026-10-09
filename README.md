# 📱 Mobile Phone Price Analysis: EDA + Machine Learning

An end-to-end data analysis project on a mobile phone dataset. It audits and cleans the data, explores what drives phone prices, and trains models that predict a phone's **price category** (Budget / Mid-range / Premium) from its specifications.

## 📌 Objectives
- Find and fix data-quality mistakes in the raw dataset
- Understand which features (brand, RAM, storage, etc.) affect price
- Predict the price category of a phone using machine learning

## 📂 Dataset
- **Raw file:** `mobile_phone_price_eda.csv` (1,505 rows × 12 columns)
- **Cleaned file:** `mobile_phone_price_eda_cleaned.csv` (1,500 rows × 12 columns)
- **Currency:** Indian Rupees (₹)

| Column | Description |
|--------|-------------|
| brand | Phone brand (10 brands, e.g. Apple, Samsung, Xiaomi) |
| model | Phone model name (50 models) |
| price | Price in ₹ |
| ram | RAM in GB |
| storage | Internal storage in GB |
| battery | Battery capacity in mAh |
| screen_size | Screen size in inches |
| camera | Main camera in MP |
| rating | User rating (1–5) |
| 5G (`is_5g` after cleaning) | Whether the phone supports 5G (Yes/No) |
| processor | Chipset type (6 types) |
| os | Operating system (Android/iOS) |

## 🧹 Data Quality Issues Found and Fixed
| Problem | Fix |
|---------|-----|
| 5 duplicate rows | Dropped |
| 15 missing values each in `battery`, `camera`, `rating` | Filled with the median of the same phone model |
| Column named `5G` (starts with a digit) | Renamed to `is_5g` |
| Apple phones not on iOS / other brands on iOS | iOS only for Apple, Android for everyone else |
| Brand-exclusive chips used by wrong brands (Apple A-series, Google Tensor, Exynos) | Reassigned to valid processors |
| Apple phones marked "No 5G" | Set to Yes |
| Apple phones with RAM above 8 GB | Capped at 8 GB |

After cleaning, a re-run audit shows **0** remaining errors.

## 🔍 Exploratory Data Analysis
- Univariate analysis: histograms and count plots
- Bivariate analysis: price vs brand, RAM, storage, camera, 5G, OS and processor
- Correlation heatmap
- Outlier detection (IQR method). Outliers such as 1 TB storage and 200 MP cameras are genuine values, so they were kept.

## 💡 Key Insights
1. **Apple is the costliest brand** (average ≈ ₹90,119), followed by Google (≈ ₹72,215) and OnePlus (≈ ₹64,414).
2. **Xiaomi is the cheapest brand** (average ≈ ₹49,393), closely followed by Realme (≈ ₹49,535).
3. **iOS phones cost far more than Android phones:** about ₹90,119 vs ₹55,826 on average.
4. **5G phones cost more than non-5G phones:** about ₹60,699 vs ₹53,647 on average.
5. **Storage (0.46) and RAM (0.36) are the features most correlated with price.** Camera has a weak positive correlation (0.13). Battery, screen size and rating have almost no correlation with price.

## 🤖 Machine Learning
Price was converted into 3 classes:

| Category | Price Range | Count |
|----------|-------------|-------|
| Budget | Below ₹45,000 | 328 |
| Mid-range | ₹45,000 – ₹70,000 | 820 |
| Premium | Above ₹70,000 | 352 |

`price` was **not** used as an input feature, to avoid data leakage. The data was split 80/20 (train/test) with stratification.

| Model | Accuracy | Precision | Recall | F1-Score |
|-------|----------|-----------|--------|----------|
| **Logistic Regression** | **0.8167** | **0.8178** | **0.8167** | **0.8145** |
| Decision Tree | 0.7933 | 0.7930 | 0.7933 | 0.7899 |
| Random Forest | 0.7733 | 0.7856 | 0.7733 | 0.7642 |

🏆 **Best model: Logistic Regression** (about 81.7% accuracy). Results may vary slightly with different library versions.

## 🛠️ Tools & Libraries
Python, Pandas, NumPy, Matplotlib, Seaborn, Scikit-learn, Jupyter Notebook

## 📁 Project Structure
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

## ▶️ How to Run
1. Clone the repository
   ```bash
   git clone https://github.com/YOUR-USERNAME/mobile-phone-price-eda.git
   cd mobile-phone-price-eda
   ```
2. Install the libraries
   ```bash
   pip install -r requirements.txt
   ```
3. Start Jupyter and open the notebook
   ```bash
   jupyter notebook
   ```
4. Run all cells (keep the CSV in the same folder as the notebook)

## 📄 Documentation
Read the full project documentation: [docs/project_documentation.md](docs/project_documentation.md)

## 🚀 Future Improvements
- Predict the exact price with regression models
- Hyperparameter tuning and cross-validation
- Build a simple web app (Streamlit) for live price prediction

## 👤 Author
**MENTA AKHIL**
[LinkedIn](https://www.linkedin.com/in/akhil-menta-777958371/) | [GitHub](https://github.com/AKHIL-MENTA) | akhilmenta73@gmail.com
