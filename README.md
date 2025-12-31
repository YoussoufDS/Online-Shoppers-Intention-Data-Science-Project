# Online-Shoppers-Intention-Data-Science-Project

🛍️ Online Shoppers Purchasing Intention: End-to-End MLOps Pipeline

    Platform: Microsoft Fabric | Language: PySpark | Framework: MLflow

This project implements an industrial-grade AI prediction system. Its core strength lies in its self-healing architecture: the pipeline continuously monitors the quality of incoming data and autonomously decides whether to retrain the model or simply proceed with predictions.
🏗️ 1. System Architecture (MLOps Pipeline)

The project is built on a seamless orchestration between OneLake storage, Spark compute, and MLflow tracking.
The Lifecycle:

    Ingestion & Refinement: Data flows through Bronze, Silver, and Gold layers (Medallion Architecture).

    Health Check (Monitoring): Statistical analysis to detect Data Drift.

    Intelligent Decision: Conditional branching triggers either retraining or scoring.

    Feedback Loop: Predictions are stored for long-term performance analysis.

📊 2. The Dataset: Analyzing Customer Behavior

The [Dataset]((https://www.kaggle.com/datasets/imakash3011/online-shoppers-purchasing-intention-dataset))consists of 12,330 e-commerce sessions. 

    Browsing Variables: Number of pages visited (Administrative, Informational, ProductRelated) and associated durations.

    Google Analytics Indicators: BounceRates, ExitRates, and PageValues.

    Customer Context: Operating system, browser, region, traffic type, and visitor status (New/Returning).

    Temporal Variables: Month of the year and proximity to special days (Sales, Holidays).

🛠️ 3. Pipeline Development (Technical Justification)
🔵 Bronze & Silver Layers: Data Reliability

    Notebooks 01 & 02: We convert raw CSV files into Delta Tables.

    Why? The Delta format ensures ACID Transactional Integrity. If ingestion is interrupted, the table never contains partial or corrupt data. Furthermore, we enforce strict schema typing to reduce Spark's memory footprint by nearly 40%.

🟡 Gold Layer: Strategic Feature Engineering

    Notebook 03: Creation of high-value business variables.

    Key Indicators:

        IsReturningVisitor: Identifies customer loyalty.

        PageValue_ExitRate_Ratio: A high ratio indicates a page that converts well despite a high exit risk.

        is_peak_shopping_season: Captures the "Black Friday" or "Holiday Season" effect.

    Method: Utilizing VectorAssembler to group features into a format compatible with Spark ML algorithms.

🔴 Machine Learning & MLflow Governance

    Notebook 04: Training via Random Forest Classifier.

    <img width="1918" height="914" alt="image" src="https://github.com/user-attachments/assets/9aa434bc-9fa7-48b2-8865-854e12718152" />


    Why this choice? Unlike simpler models, Random Forest handles both categorical and numerical variables without requiring complex normalization. It is also highly robust against data noise.

    Tracking: Every execution logs Feature Importance. We discovered that PageValues is the #1 predictor, influencing the result by over 70%.

🕵️ 4. Data Drift Detection: The MLOps Heart

The major added value of this project is Notebook 04_data_drift_monitoring.
What is Data Drift?

It is the silent change in consumer habits. If users start navigating differently (e.g., increased mobile usage), a model trained on desktop data becomes obsolete without anyone noticing.
Our Statistical Solution:

We use the Kolmogorov-Smirnov (KS) Test.

<img width="1914" height="915" alt="image" src="https://github.com/user-attachments/assets/4b0da149-fd39-4289-be96-3efb240c0d1c" />

    Comparison: We compare the distribution of the "Reference" (training data) with the "Current" (production data).

    Alpha Threshold (0.05): If the probability that the two distributions are identical (p-value) is less than 5%, a critical_drift = True signal is sent to the pipeline.

🤖 5. Orchestration & Automation (Data Factory)

The entire pipeline is driven by "If/Else" logic within Microsoft Fabric:

    Execute Drift Check.

    Conditional Logic:

        If Drift Detected: The pipeline triggers retraining, registers a new model version in MLflow, marks it as latest, and then performs scoring.

        If Stable: The pipeline uses the existing model for immediate scoring.

    Outlook Notification: A success report is sent to the administrator, including the drift status.
    
<img width="1918" height="915" alt="image" src="https://github.com/user-attachments/assets/b55dbacb-f299-4558-a0d6-b9922d18d514" />


📈 6. Results & Perspectives
Metrics Interpretation:

    Recall: Maximized to avoid missing potential buyers (minimizing False Negatives).

    Feature Importance: PageValues, ExitRates, and TotalDuration are the pillars of the purchasing decision.

Future Evolutions:

    A/B Testing: Compare the new retrained model against the old one in real-time before a full rollout.

    Sentiment Analysis: Integrate textual customer reviews as a new Gold variable.

    Real-time Serving: Use a Fabric Endpoint for real-time inference during the user's live session.

📂 Repository Structure

    01_data_ingestion: Raw to Bronze (Delta).

    02_data_cleaning: Bronze to Silver (Cleaning).

    03_feature_engineering: Silver to Gold (Business Features).

    04_machine_learning: Model training & MLflow tracking.

    04_data_drift_monitoring: Statistical drift monitoring.

    05_scoring_production_prediction: Batch scoring and final export.

📥 How to use

    Import the .ipynb files into your Microsoft Fabric Workspace.

    Create a Lakehouse named Shopping_Lakehouse.

    Link the Data Pipeline to the notebooks and run!
