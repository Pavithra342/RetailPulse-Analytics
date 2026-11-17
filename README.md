# RetailPulse — AI-Powered Customer Analytics & Demand Forecasting

A local Windows/VS Code implementation using the prepared RetailPulse datasets. It includes an interactive Streamlit dashboard, customer segmentation, a clearly labelled churn-risk prototype, demand forecasting, inventory recommendations, a FastAPI service, basic tests, and an optional MLflow/LSTM expansion path.

## Included modules

- Executive dashboard and sales analytics
- Customer behaviour analytics
- RFM-style customer segmentation using K-Means
- Recency-based churn-risk prototype
- Monthly demand forecasting (Prophet when installed; linear trend fallback otherwise)
- Demand-based inventory planning estimates
- FastAPI endpoints for summary, monthly sales, segments, churn risk and forecast
- Reproducible pipeline script and tests

## Data and interpretation notes

The project uses the UCI Online Retail dataset supplied with the project. The cleaned transaction dataset contains 536,641 rows and the customer feature dataset contains 4,339 customers.

**Positive-sales measure:** transaction lines with Quantity > 0, non-cancellation invoices and UnitPrice >= 0. This is an analytical measure, not audited accounting revenue.

**Churn limitation:** there are no verified churn labels in the source dataset. The Churn Risk page uses recency thresholds as a transparent proxy. Any model trained on that proxy is a demonstration, not a validated production churn model.

**Inventory limitation:** the source data does not include stock-on-hand, supplier lead time or replenishment cost. Lead time and safety-stock days are assumptions entered by the user; recommendations are demand-based estimates.

**Currency:** displayed amounts are in the source dataset's currency and are not converted.

## 1. Requirements

Recommended: Windows 10/11, Python 3.11, VS Code.

Check Python in PowerShell:

```powershell
py -3.11 --version
```

If Python 3.11 is not installed, install it from the official Python website and enable the Python Launcher. Open this folder in VS Code.

## 2. Install dependencies

Double-click `setup.bat`, or run these commands in the VS Code terminal from the project root:

```powershell
py -3.11 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
```

If PowerShell blocks activation, use Command Prompt in VS Code and run:

```bat
.venv\Scripts\activate.bat
```

## 3. Run the dashboard

```powershell
streamlit run app.py
```

The browser should open automatically. If it does not, open the local URL printed in the terminal, usually `http://localhost:8501`.

## 4. Run the API (optional)

Open a second terminal:

```powershell
.\.venv\Scripts\Activate.ps1
uvicorn api.main:app --reload
```

- Health: `http://127.0.0.1:8000/health`
- API docs: `http://127.0.0.1:8000/docs`

## 5. Run the pipeline and tests

```powershell
python scripts/run_pipeline.py
python -m pytest -q
```

To train and save the proxy churn demonstration model:

```powershell
python scripts/train_churn.py
```

Generated CSV outputs are written to `data/processed/`.

## 6. MLflow

The `mlflow` dependency is included for experiment tracking. Start the local MLflow UI with:

```powershell
mlflow ui
```

It normally runs at `http://127.0.0.1:5000`. The current baseline app does not automatically log every model run; add tracking around experiments when model evaluation is finalized.

## 7. Optional LSTM

Install only if required and supported by your Python environment:

```powershell
pip install -r requirements-lstm.txt
```

TensorFlow is a large dependency and can be difficult to install on some Windows/Python combinations. Prophet is used for the first forecasting implementation; the fallback is clearly identified if Prophet is not installed.

## Project structure

```text
RetailPulse_Project/
├── app.py
├── api/
│   └── main.py
├── src/
│   ├── analytics.py
│   ├── churn.py
│   ├── config.py
│   ├── data_loader.py
│   ├── forecasting.py
│   ├── inventory.py
│   ├── monitoring.py
│   └── segmentation.py
├── scripts/
│   ├── run_pipeline.py
│   └── train_churn.py
├── tests/
│   └── test_analytics.py
├── data/
│   └── processed/
│       ├── cleaned_retail.csv
│       └── customer_features.csv
├── docs/
└── models/
```

## Troubleshooting

- `ModuleNotFoundError`: activate `.venv` and run `pip install -r requirements.txt`.
- Missing CSV: check the two CSV files are in `data/processed/`.
- Prophet install error: update pip and retry. The dashboard has a linear trend fallback if Prophet is unavailable.
- Slow first launch: loading the 536k-row transaction CSV takes a little time; subsequent Streamlit reads are cached.
- If a command fails, copy the complete terminal error and share it so it can be debugged.

## Attribution

Dataset: UCI Online Retail, CC BY 4.0. Preserve the source attribution when redistributing the dataset or derived outputs.
