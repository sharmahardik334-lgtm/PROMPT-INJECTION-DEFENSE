# PDF Hidden-Text & Prompt-Injection Scanner

A local Streamlit application that scans text-based PDF files for potentially hidden or suspicious text and optionally uses an LLM to verify whether flagged text appears to be a prompt injection targeting an AI system.

This project is intended for **educational and security-research purposes**. Detection results are heuristic and should be reviewed manually rather than treated as definitive proof.

## Features

- Detects potentially hidden or suspicious PDF text using multiple signals:
  - Very small font size
  - Text positioned outside the visible page
  - Text colour that is very similar to the local background
  - Very low visible ink density
- Displays the suspicious text and the reasons it was flagged
- Optionally performs LLM-based prompt-injection verification
- Returns one of three LLM verdicts:
  - `YES` — likely prompt injection
  - `NO` — ordinary document content
  - `UNCERTAIN` — insufficient evidence
- Exports scan results as JSON or CSV
- Runs locally through a Streamlit web interface

## How It Works

```text
                PDF Upload
                    │
                    ▼
             Extract PDF Text
                    │
                    ▼
        Detect Suspicious / Hidden Text
                    │
        ┌───────────┴───────────┐
        ▼                       ▼
   Flagged Text            Normal Text
        │
        ▼
 Optional LLM Verification
        │
        ▼
   YES / NO / UNCERTAIN
        │
        ▼
   JSON / CSV Report
```

## Detection Approach

The rule-based detector checks each text span in the PDF for several suspicious characteristics.

### 1. Very Small Text

Text below a configurable font-size threshold is flagged because hidden instructions may be rendered using extremely small text.

### 2. Text Outside the Visible Page

Text whose bounding box lies outside the normal page boundaries is flagged.

### 3. Similar Text and Background Colours

Text whose colour is very close to the surrounding background may be visually difficult to notice.

### 4. Low Visible Ink Density

The rendered page is analysed to identify text that produces very little visible ink.

These signals are heuristic and can produce false positives. For example, logos, watermarks, decorative elements, and automatically generated PDFs may legitimately contain unusual text.

## LLM Verification

Flagged text can optionally be passed to an LLM for a second-stage classification.

The model is instructed to treat extracted PDF content as **untrusted data** and determine whether it appears to contain instructions attempting to manipulate or control an AI system.

The verifier returns only:

```text
YES
NO
UNCERTAIN
```

### API Key Configuration

The project does **not** contain an API key.

If LLM verification is required, configure the API key through the `OPENAI_API_KEY` environment variable rather than putting the key inside the source code.

For example, on Linux/macOS:

```bash
export OPENAI_API_KEY="your-api-key"
```

Do **not** commit API keys, passwords, tokens, or other credentials to GitHub.

If no API key is configured, the application reports:

```text
NOT CONFIGURED
```

## Project Structure

```text
PROMPT-INJECTION-DEFENSE/
│
├── app.py
├── detector.py
├── llm_verifier.py
├── test_openai.py
├── requirements.txt
├── README.md
└── .gitignore
```

### Main Files

| File | Purpose |
|---|---|
| `app.py` | Streamlit web application |
| `detector.py` | Rule-based hidden-text detection |
| `llm_verifier.py` | Optional LLM-based prompt-injection verification |
| `test_openai.py` | Simple API connection test |
| `requirements.txt` | Python dependencies |
| `.gitignore` | Prevents local environments and secrets from being committed |

## Requirements

- Python 3
- PyMuPDF
- NumPy
- Streamlit
- OpenAI Python SDK

All required Python packages are listed in `requirements.txt`.

## Running the Application Locally

Clone the repository and enter the project directory:

```bash
git clone https://github.com/sharmahardik334-lgtm/PROMPT-INJECTION-DEFENSE.git
cd PROMPT-INJECTION-DEFENSE
```

Create a virtual environment:

```bash
python3 -m venv .venv
```

Activate it:

```bash
source .venv/bin/activate
```

Install the dependencies:

```bash
python -m pip install -r requirements.txt
```

Start the Streamlit application:

```bash
python -m streamlit run app.py
```

Streamlit will start a local server and normally open the application in your browser at:

```text
http://localhost:8501
```

If it does not open automatically, copy the local URL shown in the terminal into your browser.

## Using the Application

1. Start the Streamlit application.
2. Upload a text-based PDF.
3. Configure detection thresholds if required.
4. Run the scan.
5. Review the suspicious text and detection reasons.
6. If an API key is configured, review the optional LLM verdict.
7. Export the results as JSON or CSV if required.

## Limitations

- The detector is heuristic and cannot guarantee that hidden or malicious content will be detected.
- False positives are possible.
- The application is primarily designed for text-based PDFs.
- LLM verification depends on API availability and configuration.
- LLM classifications should be treated as an additional signal, not as absolute ground truth.
- This tool should not be used as the sole basis for high-impact decisions such as hiring, financial decisions, or other consequential judgments.

## Disclaimer

This project is an educational security tool for exploring PDF hidden-text detection and prompt-injection risks in AI-assisted document processing.

It is not intended to replace manual security review or professional security tooling.