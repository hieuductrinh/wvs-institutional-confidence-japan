# 🇯🇵 Data Analytics on World Values Survey (WVS) 🇯🇵

This repository showcases my work analysing the **World Values Survey (Wave 7)**. The project explores how demographic and attitudinal variables can predict respondents’ confidence in various social organisations.

## 📌 At a Glance

* **Scope and sample:** Analysed **50 000 respondents** sampled from 65 countries (Wave 7) to study predictors of confidence in 17 social organisations (religious institutions, courts, elections, etc.).
* **Data wrangling:** Recoded negative survey codes to `NA`, profiled missingness via bar charts/heat maps, and standardised numeric variables with z‑scores to ensure comparability.
* **Descriptive insights:** Compared Japan against the rest of the world. Found that Japanese respondents place greater emphasis on **religion** but slightly less on **family**, report lower **perceived control over life**, and consume **newspapers/TV** more frequently than the global average.
* **Predictive modelling:** Built linear regression models for each confidence outcome with train–test splits and **stepwise selection**. Stepwise models improved test‑set R² (e.g. `CReligious` rose from **0.219 → 0.34**) and reduced error metrics.
* **Key drivers:** Identified high‑impact predictors such as **importance of religion**, **meeting frequency**, **neighbourhood trust**, **family importance**, **political knowledge**, **personal safety**, and **satisfaction with democracy**.
* **Clustering context:** Used the **World Governance Indicators (2022)** to perform **K‑Means clustering**, grouping countries by governance profiles. Japan clustered with Australia, Canada, Germany, Netherlands, New Zealand, Taiwan, the UK and Uruguay. Re‑running models on this “Japan‑cluster” highlighted regional nuances.
* **Tooling & ethics:** Encapsulated data prep, scaling and modelling in custom R functions; created heat maps, PCA biplots, and bar charts with **ggplot2**/**reshape2**; used Generative AI only to search for R function syntax and examples (documented in an appendix).

## 🚀 Skills demonstrated

* **Data wrangling:** Sampling, merging, recoding negative values to `NA`, profiling missingness, and performing train–test splits with reproducible seeds.
* **Statistical modelling:** Building and evaluating multiple linear regression models; calculating and interpreting MSE/MAE/RMSE/R²; implementing **stepwise feature selection** to reduce overfitting.
* **Custom functions:** Encapsulated scaling, transformation and modelling steps into R functions (`fit_transform`, `transform`, `lm_country`) to avoid repetition and improve readability.
* **Visualisation:** Produced heat maps comparing national averages, PCA biplots for cluster visualisation, bar charts for missing‑value counts, and multivariate plots using **ggplot2**, **GGally** and **reshape2**.
* **Unsupervised learning:** Applied **K‑Means clustering** on multivariate governance indicators and interpreted cluster memberships using **PCA** and domain knowledge.
* **Ethical AI use:** Generative AI (e.g. ChatGPT) was used only to search for R functions and examples; all code and analysis were written and validated manually. An appendix documents prompts and how suggestions were adapted.

## 🔧 Data preparation

* A random sample of **50 000 respondents** was taken from the WVS data (originally 46 variables); this subset represents **65 countries**. Sampling and seeding ensured reproducibility.
* Missing values were coded as negative numbers (−5 to −1) in the WVS instrument. These were converted to `NA` to simplify analysis. I quantified missingness per variable and generated bar charts/heatmaps to visualise which questions had the most non‑responses (e.g. confidence in environmental organisations had ~6.5 % missing).
* All numeric variables were **z‑score standardised** (mean 0, sd 1) before modelling so predictors were on comparable scales. Categorical variables (e.g. country codes) were kept intact.

## 📊 Descriptive insights

I began by comparing Japan with all other countries:

* **Imbalanced sample:** Canada had the most observations (2 085) while Cyprus had the least (510).
* **Cultural priorities:** Japanese respondents rated **religion** more important (VReligion) than the global average, but **family** somewhat less important (VFamily). They also reported lower **perceived control over life** (HChoice).
* **Media consumption:** Lower numbers on frequency questions mean higher consumption; the Japanese sample showed red shades for **newspaper** and **television** consumption, indicating they engage more often with these media than other countries.

These differences were summarised via heat maps of mean differences, making it easy to spot where Japan diverges from the global pattern.

## 🧮 Predictive modelling

To understand which variables best predict confidence in social organisations (e.g. confidence in religious institutions, courts, banks), I built **linear regression models** for each target. Key steps:

1. **Train–test split:** For each organisation, data for the focus group (Japan or cluster) were split 70/30 into training and testing sets. Missing values in numeric columns were imputed with the mean of the training set before scaling.
2. **Model fitting:** A full model used all available predictors. I then applied **stepwise selection** (both forward and backward) to find a parsimonious subset and reduce overfitting. Performance was evaluated via **MSE**, **MAE**, **RMSE**, **R²** on training and test sets, and a rounded‑prediction **accuracy** metric (to align continuous predictions with ordinal response scales). These metrics were tabulated for each organisation.
3. **Results:** For Japan, stepwise models improved generalisation: e.g. the `CReligious` model’s test‑set R² increased from 0.219 to 0.34 while error metrics decreased. Confidence in political parties (`CPParties`) and elections (`CElections`) also had moderate predictive power after feature selection.
4. **Important predictors:** Using p‑values from the final stepwise models, I identified the strongest predictors. For example, confidence in religious institutions (`CReligious`) was driven by **importance of religion**, **frequency of meeting people**, and **neighbourhood trust**. Confidence in courts depended on **family importance**, **knowledge of politics**, **personal safety**, **satisfaction with democracy**, and **overall satisfaction**.

### 🧠 Cluster analysis

To provide context beyond a single country, I enriched the study with **World Governance Indicators (WGI) 2022**. After replacing missing codes and scaling numeric columns, I applied **K‑Means clustering** (with clusters ≈ `n/5`) to group countries with similar governance profiles.

* The cluster containing **Japan** also included **Australia, Canada, Germany, Netherlands, New Zealand, Taiwan, the UK and Uruguay**. I visualised clusters using the first two principal components (PCA) to aid interpretation.
* Re‑running the predictive pipeline on this “Japan‑cluster” versus other countries highlighted which social organisations were better predicted at a regional level. Some models saw increased test‑set R² (e.g. for confidence in courts) but others weakened, underscoring the heterogeneity within and across clusters.

## 📈 Findings & outcomes

* **Predictive power varies by outcome:** Full models showed that confidence in religious institutions, elections and political parties were the most predictable outcomes for Japan. For example, the `CReligious` model explained **about 22%** of the variance, with `CPParties` and `CElections` close behind. Other organisations (e.g. universities, banks) had much lower R² values.
* **Stepwise regression boosts generalisation:** Applying forward/backward selection simplified the models, reduced overfitting and improved test‑set performance. For `CReligious` the R² increased from **0.219 to 0.34** and error metrics (MSE, MAE, RMSE) declined. Accuracy metrics also rose (e.g. `CPolice` achieved ~63% correct rounded predictions).
* **Important predictors:** Significant drivers across models included **importance of religion**, **meeting frequency**, **neighbourhood trust**, **family importance**, **political knowledge**, **personal safety**, **satisfaction with democracy**, and **overall life satisfaction**. These findings underscore the role of personal values and social trust in shaping institutional confidence.
* **Comparative insights:** When models were re‑estimated on the broader **Japan‑cluster** and on **other countries**, some outcomes became easier to predict while others weakened, illustrating regional heterogeneity. In the “other countries” models, stepwise selection substantially increased test‑set R² for several organisations (e.g. television companies reached ~13% explained variance) but at the cost of higher MSE, indicating a trade‑off between bias and variance.
* **Overall takeaway:** The analysis demonstrates how demographic factors, values and perceptions combine to influence trust in different social institutions. Tailoring models to specific countries or clusters reveals nuanced patterns that would be obscured in a global aggregate.
