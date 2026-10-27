# DataGuard-AI

A local data-profiling prototype for CSV and Excel files. It combines pandas and ydata-profiling reports with Ollama-generated suggestions for data-cleaning code. The repository contains a Streamlit interface and a separate FastAPI backend; it does not include the previously documented `index.html` frontend.

## Important safety limit

**Applying a fix executes Python on the host, without a sandbox.** The API's `apply_fix`, the Streamlit AI-fix handler, and the custom-code panel call `exec`. A copied DataFrame does not isolate that code from files, processes, or the network. AI suggestions are untrusted code, not validated repairs.

Use only disposable, non-sensitive data in an isolated development environment. Do not expose the application to untrusted users or use it as a production data-cleaning service. Profiling reports can contain source values; treat uploads, generated reports, and downloads accordingly.

## Local setup

Create a fresh environment rather than reusing the committed `.venv`:

```bash
git clone https://github.com/Zinga18018/DataGuard-AI.git
cd DataGuard-AI
python -m venv .venv-local
source .venv-local/bin/activate
python -m pip install -r requirements.txt
```

On Windows, activate with `.venv-local\Scripts\activate`. The requirements are unpinned. Excel `.xlsx` support also needs `openpyxl`, which is not listed in the requirements file.

For AI suggestions, provision Ollama and its `mistral` model separately. The code sends requests to `http://localhost:11434/api/generate`; provisioning a model requires a download and sufficient local resources. Neither interface is a security boundary around model-generated code.

Choose an interface from the repository root:

```bash
# Streamlit interface
python -m streamlit run app.py --server.address 127.0.0.1

# Or the separate FastAPI backend
python -m uvicorn api:app --host 127.0.0.1 --port 8000
```

The API documentation is at `http://127.0.0.1:8000/docs`. The Streamlit interface handles uploads, profiling, suggestions, and exports itself; it is not a frontend for that API.

## Source map and limits

- [app.py](app.py): Streamlit upload, profiling, custom-code, and download paths.
- [ai_integration.py](ai_integration.py): Ollama suggestions and the AI-fix execution handler.
- [api.py](api.py): upload, profile, recommendation, and fix endpoints.
- [requirements.txt](requirements.txt): declared Python dependencies.

HTML report export is implemented. PDF export additionally imports `pdfkit` (not declared) and uses a hard-coded Windows `wkhtmltopdf` executable path; installing the tool on `PATH` alone does not make this portable.

There is no committed project test suite or benchmark establishing large-dataset performance, correctness of suggested fixes, or secure isolation. Existing sample files and profiling reports are not documented as a vetted synthetic fixture. No project license file is included at the repository root.
