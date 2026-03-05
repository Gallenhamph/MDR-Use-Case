# 🛡️ Sophos MDR & Offensive Security Scenario Generator

A powerful, Streamlit-based web application designed for Cybersecurity Architects, Sales Engineers, and Consultants. This tool leverages the **Azure OpenAI Service (GPT-4o)** to dynamically generate highly tailored, highly technical cyberattack narratives, simulated MDR case logs, and targeted product recommendations based on a client's specific IT estate.

## ✨ Key Features

* **Azure OpenAI Powered:** Utilizes Microsoft's secure, enterprise-grade GPT-4o models to generate logical, highly technical attack paths and incident response logs.
* **Strict MDR Guardrails:** Prompt engineering ensures the AI never hallucinates response actions. All MDR interventions are strictly constrained to authorized Sophos capabilities (e.g., host isolation, M365 session disconnection, SHA256 blocking, and Active Threat Response).
* **Modular Architecture:** Cleanly separated codebase (`app.py`, `data.py`, `prompts.py`, `export.py`) for easy maintenance and scalability.
* **Massive Threat Intelligence Database:** Automatically maps the client's current technology stack to an expanded, built-in database of real-world CVEs and active threat trends.
* **Guaranteed Originality:** Randomly selects from over 25 modern Initial Access vectors (e.g., AiTM phishing, zero-day VPN exploits, NPM poisoning, MFA fatigue) to ensure every report is unique.
* **Executive-Ready Deliverables:** Automatically builds formatted PPTX decks and crash-proof PDF reports featuring an estate overview, graphical attack timelines, perfectly aligned monospaced MDR logs, and clickable MITRE/CVE hyperlinks.
* **Docker & CI/CD Ready:** Fully containerized for easy deployment, with support for automated updates via GitHub Container Registry (GHCR) and Watchtower.

---

## 🛠️ Prerequisites

* Python 3.10+ (If running locally)
* Access to **Azure OpenAI Service** (with a deployed `gpt-4o` model)
* **Docker Desktop** or Docker Engine (If containerizing)

## ⚙️ Configuration (Required for all setups)

Whether you are running the app locally or via Docker, you must provide your Azure API credentials. 

1. Create a hidden Streamlit secrets directory and file in the root of your project:
   ```bash
   mkdir .streamlit
   touch .streamlit/secrets.toml
   ```
2. Add your Azure OpenAI keys and endpoint to `secrets.toml`:
   ```toml
   AZURE_OPENAI_API_KEY = "your-azure-key-here"
   AZURE_OPENAI_ENDPOINT = "[https://your-resource-name.openai.azure.com/](https://your-resource-name.openai.azure.com/)"
   AZURE_OPENAI_DEPLOYMENT = "gpt-4o" # The specific name of your model deployment
   AZURE_OPENAI_API_VERSION = "2024-02-15-preview"
   ```
*(Note: `.streamlit/secrets.toml` is included in the `.gitignore` and `.dockerignore` files to prevent your keys from leaking to GitHub).*

---

## 🐳 Running with Docker (Recommended)

Dockerizing the application ensures it runs identically on any machine and avoids local Python version conflicts.

### 1. Build the Image Locally
Open your terminal in the project directory and build the Docker image:
```bash
docker build -t mdr-scenario-generator .
```

### 2. Run the Container
Run the container and mount your local `.streamlit` folder so the app can securely access your Azure API keys without baking them into the image:

**On Mac/Linux:**
```bash
docker run -d --name mdr-app -p 8501:8501 -v $(pwd)/.streamlit:/app/.streamlit mdr-scenario-generator
```

**On Windows (PowerShell):**
```powershell
docker run -d --name mdr-app -p 8501:8501 -v ${PWD}/.streamlit:/app/.streamlit mdr-scenario-generator
```
The app will now be live at `http://localhost:8501`.

### 3. Automated Updates (Watchtower + GHCR)
If you have configured GitHub Actions to build and push your Docker image to the GitHub Container Registry (GHCR), you can use Watchtower on your server to automatically update the live app whenever you push new code to the `main` branch.

Run this command on your server to start the Watchtower updater. Ensure you replace the placeholders with your GitHub username and Personal Access Token (PAT):

```bash
docker run -d \
  --name watchtower \
  -e DOCKER_API_VERSION="1.44" \
  -e REPO_USER="your-github-username" \
  -e REPO_PASS="ghp_YOUR_PERSONAL_ACCESS_TOKEN" \
  -v /var/run/docker.sock:/var/run/docker.sock \
  containrrr/watchtower \
  --interval 300 \
  mdr-app
```
*Watchtower will securely check GHCR every 5 minutes (300 seconds) for updates specifically for the `mdr-app` container and seamlessly restart it if a new image is found.*

---

## 💻 Running Locally (Standard Python)

If you prefer to run the application directly on your host machine without Docker:

1. **Install dependencies:**
   It is recommended to use a virtual environment.
   ```bash
   pip install -r requirements.txt
   ```
   *Required packages:*
   ```text
   streamlit==1.54.0
   requests==2.32.5
   fpdf2==2.8.7
   python-pptx==1.0.2
   openai==2.24.0
   ```

2. **Start the application:**
   ```bash
   streamlit run app.py
   ```

---

## 🏗️ File Structure

* `app.py`: The main Streamlit application. Handles the frontend UI, session state, and coordinates the backend logic.
* `data.py`: Houses the expansive `ATTACK_VECTORS` list and `SIMULATED_OSINT` dictionaries to keep the main app lightweight.
* `prompts.py`: Contains the `SYSTEM_PERSONA` and prompt-building instructions. Enforces the strict technical tone, MITRE ATT&CK hyperlinking rules, and authorized MDR response guardrails.
* `export.py`: The dedicated document generation engine. Contains the FPDF and Python-PPTX classes, graphic drawing logic, and text-cleaning regex functions.
* `Dockerfile`: Instructions for containerizing the application using a lightweight Python 3.10 slim environment.

## 📝 Tone & Formatting Notes

The underlying LLM is strictly instructed to act as a Principal Cybersecurity Architect. The output is sterile, objective, and highly technical. All MITRE T-codes, CVEs, and Sophos/Secureworks products generated in the text are automatically converted to clickable Markdown hyperlinks in the UI and PDF, and formatted beautifully for presentation slides.