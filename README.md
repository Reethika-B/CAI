# CycloneAI

AI-assisted tropical cyclone analysis using IBTrACS observations, satellite imagery, a local CNN image classifier, and NOAA NHC current-storm data.

## Structure

- `data/`: prepared datasets
- `models/`: trained models and scalers
- `satellite_images/`: satellite image archive
- `website/backend/`: FastAPI application, API routes, services, schemas, and ML loading
- `website/frontend/`: standalone HTML, CSS, and JavaScript dashboard

## Run locally

From the project root:

```powershell
.\.venv\Scripts\python.exe -m uvicorn website.backend.main:app --reload --host 127.0.0.1 --port 8000
```

In a second terminal:

```powershell
.\.venv\Scripts\python.exe -m http.server 5500 --directory website\frontend
```

Open the frontend at `http://127.0.0.1:5500/`.

Backend API documentation is available at `http://127.0.0.1:8000/docs`.

The frontend communicates with the backend through REST requests. The backend reads historical/local project assets and requests current-storm reports from the NOAA NHC feed; live monitoring therefore requires network access.

## Current model scope

The local CNN classifies satellite images as Low, Moderate, or High when the saved model loads. Track prediction loads `models/cyclone_track_lstm.keras.zip` with `feature_scaler.pkl` and `target_scaler.pkl`; these require scikit-learn 1.6.1/joblib. The LSTM expects five observations with `LAT`, `LON`, `STORM_SPEED`, and `STORM_DIR`, and returns the next sampled latitude/longitude. The API resolves the submitted UTC time and coordinates to the cleaned IBTrACS archive, then uses that fix and four preceding fixes. Inputs without a matching archive row or enough complete history return an explicit error and no coordinates. This is a research/demo one-step estimate, not an operational forecast. NOAA NHC supplies current storm fixes and movement fields, not forecast paths. Alert tiers are a simple project-side wind-threshold screen and are not official warnings.
