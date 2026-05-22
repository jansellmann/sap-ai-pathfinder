# Classic ML on SAP BTP

Three approaches for ML on SAP, ordered by preference for structured data:

## Approach 1: SAP-RPT-1 — Relational Foundation Model (start here for classification/regression)

SAP-RPT-1 (Relational Pre-trained Transformer) is a table-native foundation model. Uses
in-context learning — no model training required. Provide labeled historical rows as context
at inference time.

**When to use:**
- Classification or regression on tabular data (single table)
- Cold-start with limited historical training data
- Columns contain textual/semantic content (RPT-1 understands text natively)
- Rapid prototyping without training pipeline

**Current limitations:**
- Classification and regression ONLY — not time series, clustering, anomaly detection
- Context window: RPT-1-small 2048 rows, RPT-1-large 65,536 rows
- Latency: GPU-based, NOT suitable for < 200ms requirements
- Batch: current API is optimized for online (few-at-a-time) inference; batch on roadmap
- Explainability: model observability targeted July 2026, full column/row-level explainability H2 2026
- GPU availability: verify in target data center before committing

**Access:**
- Via AI Core Generative AI Hub (Extended plan required)
- Also callable via SQL stored procedure directly from HANA Cloud
- Playground (free, limited): [SAP RPT playground](https://www.sap.com/products/artificial-intelligence/sap-rpt.html)
  (max 25 rows, 4 columns, 50 target classes; rate limits apply)

**Important:** The SAP AI SDK for Python does NOT yet support RPT-1. Use the `requests`
library to call the deployment endpoint directly.

**Code example — RPT-1 inference (Python via requests):**
```python
import requests
import json

# Obtain token via OAuth2 client credentials flow beforehand
headers = {
    "Authorization": f"Bearer {token}",
    "ai-resource-group": "default",
    "Content-Type": "application/json"
}

payload = {
    "task": "classification",       # or "regression"
    "context": train_df.to_dict(orient="records"),   # labeled rows as few-shot context
    "test": test_df.to_dict(orient="records")
}

response = requests.post(
    f"{ai_core_base_url}/v2/inference/deployments/{deployment_id}/v1/predict",
    headers=headers,
    json=payload
)
predictions = response.json()  # list of predicted values + confidence scores
```

**Roadmap:** SAP-RPT-1.5 (generally available H2 2026) — verify current status before
committing to RPT-1 for new projects.

**Resources:**
- [SAP-RPT-1 Help Portal](https://help.sap.com/docs/sap-ai-core/sap-ai-core-service-guide/sap-rpt-1)
- [RPT-1 Hello World with AI Core (Blog)](https://community.sap.com/t5/artificial-intelligence-blogs-posts/building-an-sap-rpt-1-quot-hello-world-quot-with-ai-core/ba-p/14292100)
- [RPT-1 Getting Started Blog](https://community.sap.com/t5/artificial-intelligence-blogs-posts/sap-rpt-1-a-step-by-step-guide-on-getting-started/ba-p/14290171)
- [API Collection and Code Snippets](https://github.com/SAP-samples/sap-rpt-samples)
- [Open-source model (HuggingFace + GitHub)](https://github.com/SAP-samples/sap-rpt-1-oss)

---

## Approach 2: HANA Cloud PAL/APL — Embedded In-Database ML

Use for time series, anomaly detection, clustering, or when RPT-1 cannot meet operational requirements.

**When to use over RPT-1:**
- Time series forecasting, anomaly detection, clustering
- Latency < 200ms (PAL achieves sub-10ms in-database)
- Data must stay within HANA Cloud (governance, residency)
- Very high batch throughput needed
- Explainability required now
- SQL-native integration strongly preferred

**Development Workflow:**
1. Prepare training datasets using SQL or Python (hana-ml)
2. Select PAL (classical ML) or APL (AutoML) algorithm
3. Train models in-database
4. Evaluate with built-in metrics
5. Persist models as HANA objects for reuse

**Code example — PAL classification (Python):**
```python
from hana_ml import dataframe as hd
from hana_ml.algorithms.pal.trees import RDTClassifier

conn = hd.ConnectionContext(address='<hana-host>', port=443,
                             user='<user>', password='<password>')
hdf_train = conn.table('CUSTOMER_CHURN_TRAIN')

rfc = RDTClassifier(n_estimators=100, max_depth=10, random_state=42)
rfc.fit(data=hdf_train, key='CUSTOMER_ID', label='CHURN')

hdf_test = conn.table('CUSTOMER_CHURN_TEST')
predictions = rfc.predict(data=hdf_test, key='CUSTOMER_ID')
predictions.collect()  # Only pull from HANA when truly needed
```

**Code example — APL AutoML (SQL):**
```sql
CALL _SYS_AFL.APL_CREATE_MODEL_AND_TRAIN(
    CONFIG_TABLE, VAR_DESC_TABLE, 'TRAINING_DATA', 'APL_MODEL'
) WITH OVERVIEW;

CALL _SYS_AFL.APL_APPLY_MODEL('APL_MODEL', 'TEST_DATA', 'PREDICTIONS');
```

**Permissions required:** `AFL__SYS_AFL_AFLPAL_EXECUTE`, `AFL__SYS_AFL_APL_AREA_EXECUTE`

**Resources:**
- [ML Libraries Setup Guide](https://help.sap.com/docs/hana-cloud/sap-hana-cloud-getting-started-guide/using-machine-learning-libraries-apl-and-pal-in-sap-hana-database)
- [hana-ml-samples GitHub](https://github.com/SAP-samples/hana-ml-samples)
- [AutoML with APL Course](https://learning.sap.com/courses/hana_apl)
- [Python ML Client Course](https://learning.sap.com/courses/developing-ai-models-with-the-python-machine-learning-client-for-sap-hana-1)

---

## Approach 3: Custom ML with SAP AI Core

Use ONLY when custom frameworks or customer-specific model training is required and Approach
1 and 2 cannot meet the needs.

**Development Workflow:**
1. Build models (TensorFlow, PyTorch, scikit-learn, or any framework)
2. Containerize training + serving as Docker images
3. Define YAML templates for AI Core workflows
4. Execute training on AI Core's Kubernetes infrastructure
5. Register model in AI Core artifact store
6. Deploy as REST API endpoint
7. Consume from SAP or non-SAP applications

**When to choose:**
- Custom frameworks (TensorFlow, PyTorch) or specialized algorithms needed
- Customer-specific isolated model training required
- MLOps (versioning, A/B testing, CI/CD) critical
- Training data from multiple sources, not just HANA
- Generative AI or foundation model capabilities

**Resources:**
- [SAP AI Core Service Guide](https://help.sap.com/docs/sap-ai-core/sap-ai-core-service-guide/what-is-sap-ai-core)
- [AI Core Samples](https://github.com/SAP-samples/ai-core-samples)
- [Learning Journey: Using SAP AI Core](https://learning.sap.com/learning-journeys/learning-how-to-use-the-sap-ai-core-service-on-sap-business-technology-platform)
- [Training and Deploying Custom Models](https://developers.sap.com/tutorials/ai-core-custom-llm.html)

---

## Hybrid Pattern: HANA + AI Core

Best of both worlds:
```
HANA PAL → feature engineering and data preparation
RPT-1 or AI Core → inference on HANA-extracted features
HANA → store predictions for low-latency serving
HANA Vector Engine → semantic search with AI Core embeddings
```

**Semantic search example:**
```sql
CREATE TABLE PRODUCT_EMBEDDINGS (
    PRODUCT_ID NVARCHAR(50),
    EMBEDDING REAL_VECTOR(768),
    PRODUCT_NAME NVARCHAR(255)
);

SELECT TOP 10 PRODUCT_ID, PRODUCT_NAME,
    COSINE_SIMILARITY(EMBEDDING, :query_embedding) AS SIMILARITY
FROM PRODUCT_EMBEDDINGS
WHERE COSINE_SIMILARITY(EMBEDDING, :query_embedding) > 0.7
ORDER BY SIMILARITY DESC;
```
