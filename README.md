# Hackathon Decoder

A local Streamlit app that reads one hackathon page and reports eligibility with page evidence.

## Setup

1. Copy `.env.example` to `.env` only if `.env` does not already exist. Keep your real `GEMINI_API_KEY` local; never commit `.env`.
2. Set `GEMINI_API_KEY` in `.env`. The selected model is `gemini-3.8-flash` by default; `MODEL_NAME` can override it.
3. Install dependencies:

   ```powershell
   python -m pip install -r requirements.txt
   ```

4. Start the app:

   ```powershell
   streamlit run app.py
   ```

5. Open the local URL printed by Streamlit, usually `http://localhost:8501`.
