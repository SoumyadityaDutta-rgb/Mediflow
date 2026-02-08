# Mediflow - AI Medical Health Assistant

**Mediflow** is an advanced AI-powered medical assistant designed to provide empathetic mental health support, analyze medical reports with OCR and Computer Vision, detect mental health crises, and immediately connect users to emergency services. It combines LangChain orchestration, Gemini AI, Groq LLMs, and external APIs (Twilio, Google Maps) to deliver a comprehensive health companion with safety-critical features.

## 🚀 Features

### 🧠 AI Mental Health Chatbot
- **Persona-Driven Support**: Chat with "Dr. Emily Hartman," a compassionate AI that provides warm, empathetic psychological support.
- **Dual LLM Architecture**: Uses **Gemini 2.5 Flash** (via Google Generative AI) for warm medical consultation responses and **Groq's Llama 3.1 8B** for agent orchestration.
- **Real-Time Crisis Detection**: Comprehensive regex-based detection of suicidal ideation, self-harm, hopelessness, and severe emotional collapse.
- **Immediate Emergency Response**: Automatically triggers an emergency phone call via **Twilio** when crisis keywords are detected—no delays or confirmation needed.


### 📄 Medical Report Analysis (RAG-Lite)
- **Chat with your Data**: Upload a report to inject its content into the chat context. Ask "What does this mean?" to get specific answers based on your unique data.
- **Multi-Format Support**: Process PDF, PNG, JPG medical documents.
- **Advanced OCR Pipeline**: 
  - **PaddleOCR** for high-accuracy text extraction
  - **Tesseract** (via pdf2image) for scanned documents
  - **PyPDF2** for PDF metadata extraction
- **Fuzzy Keyword Matching**: Uses RapidFuzz to identify medical terms (medications, dosages, conditions).
- **Patient-Friendly Summaries**: Converts complex medical jargon into simple explanations.

### 📈 Longitudinal Health Trend Analysis
- **Time-Series Tracking**: Upload multiple past reports (e.g., Blood Tests from Jan, Mar, Jun) simultaneously.
- **Auto-Extraction**: Gemini AI extracts dates and key biomarkers (Hemoglobin, Sugar, Cholesterol) from unstructured text.
- **Interactive Visualization**: Generates dynamic line charts (using Recharts) to visualize health trends over time.
- **Insight Generation**: Automatically detects if values are improving (⬇️ Bad Cholesterol) or worsening (⬆️ Blood Sugar).

### 🩺 Medical Imaging (MRI/DICOM/X-Ray Analysis)
- **Multimodal AI**: Accepts MRI, CT, X-Ray, Ultrasound images.
- **DICOM Support**: Native detection and parsing of DICOM files (.dcm) using **Pydicom**.
- **Medical Imaging Agent**: Specialized Agno-powered agent with Gemini 2.5 Flash for radiology interpretation.
- **Radiologist-Grade Output**: Structured findings with confidence levels and differential diagnoses.

### 📍 Intelligent Location Services
- **Specialist Matching**: Automatically maps disease keywords to relevant specialists (e.g., "diabetes" → endocrinologist, "depression" → psychiatrist).
- **Google Maps Integration**: Real-time geocoding and Places API searches.
- **Proximity Search**: Find doctors, therapists, clinics, and hospitals within 5km radius.
- **Smart Defaults**: Falls back to "doctor" if no specific specialty is detected.

## 🛠️ Tech Stack

- **Frontend**: [Next.js](https://nextjs.org/) (React-based web interface with Tailwind CSS)
- **Backend**: [FastAPI](https://fastapi.tiangolo.com/) (High-performance async API with CORS support)
- **Agent Orchestration**: [LangGraph](https://langchain-ai.github.io/langgraph/) (State-machine based agent flow with 'IsEmergency' and 'IsLocation' nodes)
- **LLMs**:
  - [Google Gemini 2.5 Flash](https://ai.google.dev/) (Medical consultation, trend extraction & image analysis)
  - [Groq Llama 3.1 8B](https://groq.com/) (Fast inference for legacy summarization task)

- **Medical Imaging & OCR**:
  - [Agno Framework](https://github.com/phidatahq/agno) (Specialized medical imaging agent)
  - [PaddleOCR](https://github.com/PaddlePaddle/PaddleOCR) (High-accuracy text extraction)
  - [Pydicom](https://pydicom.github.io/) (DICOM file parsing)
  - [PyPDF2](https://pypdf.readthedocs.io/) (PDF metadata extraction)
  - [pdf2image](https://github.com/Belval/pdf2image) (PDF to image conversion)
  - [OpenCV](https://opencv.org/) (Image processing)
  - [Pillow](https://python-pillow.org/) (Image manipulation)
- **Fuzzy Matching**: [RapidFuzz](https://github.com/maxbachmann/RapidFuzz) (Medical keyword matching)
- **External APIs**:
  - **Google Maps API** (Geocoding, Places, Specialist search)
  - **Twilio API** (Emergency voice calls)
  - **DuckDuckGo Search** (Medical literature integration)

## 📂 Project Structure

```
Aimedicalanalyzer/
├── backend/
│   ├── main.py              # FastAPI server with /ask, /analyze_report endpoints
│   ├── aiagent.py           # LangChain agent, tools, SYSTEM_PROMPT, and response parsing
│   ├── tools.py             # Low-level integrations (Gemini queries, Twilio calls)
│   ├── config.py            # API keys & configuration (⚠️ REQUIRES SECURITY AUDIT)
│   ├── medical_pipeline.py  # OCR pipeline, DICOM parsing, medical image analysis
│   ├── medical_agent.py     # Agno-based medical imaging agent for MRI/X-Ray analysis
│   └── __pycache__/         # Python cache
├── frontend-web/          # Next.js Frontend (React, Tailwind, Lucide)
├── requirements.txt         # Python dependencies
├── LICENSE                  # MIT License
└── README.md                # This file
```

## ⚙️ Installation & Setup

### Prerequisites
- Python 3.9+
- API Keys:
  - **Google Gemini API Key** (from [Google AI Studio](https://aistudio.google.com/app/apikey))
  - **Groq API Key** (from [console.groq.com](https://console.groq.com/))
  - **Google Maps API Key** (Geocoding + Places enabled)
  - **Twilio Account** (ACCOUNT_SID, AUTH_TOKEN, FROM_NUMBER)
  - **Emergency Contact Number** (E.164 format, e.g., +1234567890)
- Optional:

  - `poppler-utils` (system package for PDF processing on Windows)

### 1. Clone the Repository
```bash
git clone <repository-url>
cd Aimedicalanalyzer
```

### 2. Install Dependencies
```bash
pip install -r requirements.txt
```

### 3. Configure API Keys (⚠️ SECURITY CRITICAL)
Edit `backend/config.py` and add your credentials:
```python
GROQ_API_KEY = "gsk_..."
GOOGLE_MAPS_API_KEY = "AIzaSy..."
GEMINI_API_KEY = "AIzaSy..."
TWILIO_ACCOUNT_SID = "AC..."
TWILIO_AUTH_TOKEN = "..."
TWILIO_FROM_NUMBER = "+1..."
EMERGENCY_CONTACT_NUMBER = "+91..."
```

**⚠️ IMPORTANT**: Replace hardcoded keys with environment variables:
```bash
# .env or system environment
export GROQ_API_KEY="..."
export GOOGLE_MAPS_API_KEY="..."
export GEMINI_API_KEY="..."
export TWILIO_ACCOUNT_SID="..."
export TWILIO_AUTH_TOKEN="..."
export TWILIO_FROM_NUMBER="..."
export EMERGENCY_CONTACT_NUMBER="..."
```



## 🚀 Running the Application

### Prerequisites Check
Before starting, verify:
1. **All API keys configured** in `backend/config.py`
2. **Backend dependencies installed**: `pip install -r requirements.txt`


### Step 1: Start the Backend Server
From the project root:

**Option A - Using Python Module (Recommended):**
```bash
python -m backend.main
```

**Option B - Using Uvicorn CLI:**
```bash
uvicorn backend.main:app --host 0.0.0.0 --port 8000 --reload
```

**Expected Output:**
```
INFO:     Uvicorn running on http://0.0.0.0:8000
INFO:     Application startup complete
```

The backend is now ready at `http://localhost:8000`.

### Step 2: Start the Frontend Interface
1. Navigate to the web frontend directory:
   ```bash
   cd frontend-web
   ```
2. Install dependencies (first time only):
   ```bash
   npm install
   ```
3. Start the development server:
   ```bash
   npm run dev
   ```
4. Open your browser to `http://localhost:3000`

## 🔄 API Endpoints

### POST `/ask` - Chat Endpoint
Send a message to the AI assistant with automatic crisis detection.

**Request:**
```json
{
  "message": "I'm feeling depressed and lonely"
}
```

**Response:** Plain text response from Dr. Emily Hartman or emergency call trigger.

**Crisis Detection:** Messages containing keywords like "suicide," "kill myself," "self-harm," etc. automatically trigger emergency calls.

**Location Requests:** Queries like "find psychiatrists in Delhi" automatically invoke Google Maps search.

### POST `/analyze_report` - Medical Report Analysis
Upload and analyze medical documents.

**Request:** `multipart/form-data` with file field
- Accepts: PDF, PNG, JPG, JPEG
- File size: Recommended <20MB

**Response:** Extracted medical data, summaries, and key findings

**Example:**
```bash
curl -X POST "http://localhost:8000/analyze_report" \
  -F "file=@medical_report.pdf"
```

## 🧠 Application Flow

1. **User Input** → Message sent via Next.js frontend or `/ask` API
2. **Emergency Detection** → Regex patterns scan for crisis keywords
3. **If Crisis Detected** → Immediate Twilio call to EMERGENCY_CONTACT_NUMBER (no delay)
4. **If Location Request** → Google Maps API finds nearby specialists
5. **Default Behavior** → LangChain agent processes request
6. **Tool Selection** → Agent picks best tool:
   - `call_emergency` - For suicidal ideation or self-harm
   - `ask_mental_health_specialist` - For health advice using Gemini
   - `find_nearby_therapists_by_location` - For location-based specialist search
7. **Response Generation** → Final answer returned to frontend

## 💬 Conversation Features

### Mental Health Chat
- Empathetic, persona-driven responses from "Dr. Emily Hartman"
- Supports follow-up questions and multi-turn conversations
- Chat history preserved in session

### Medical File Upload
- Supports PDF, PNG, JPG medical documents
- Automatic OCR extraction using PaddleOCR
- Fuzzy matching for medical keywords
- Summarization in patient-friendly language

### Medical Image Analysis
- Automatic DICOM detection for .dcm files
- MRI, CT, X-Ray analysis using Gemini + Agno framework
- Structured radiology reports with findings and recommendations
- Research context from medical literature

## 🛡️ Disclaimer & Safety Information

⚠️ **CRITICAL: Mediflow is an AI Assistant and does NOT replace professional medical advice.**

### Medical Use Only
- The AI's analysis of medical reports, images, and health conditions is for **informational purposes only**.
- All AI recommendations **must be verified by a certified medical professional** before acting.
- **Do not rely on this application for diagnosis or treatment decisions.**

### Mental Health Crisis
- In case of a **mental health emergency, always contact local emergency services immediately**:
  - **USA**: 911 or National Suicide Prevention Lifeline: 988
  - **India**: Emergency: 112; AASRA: +91-22-2754-6669
  - **UK**: 999 or Samaritans: 116 123
- While Mediflow can detect and initiate emergency calls, this is **not a substitute for professional crisis intervention**.

### Liability
- The developers are **not liable** for misdiagnosis, delayed treatment, or adverse outcomes from using this application.
- Users assume all responsibility for medical decisions made based on AI analysis.

### Data Privacy
- Medical files uploaded are processed on your local instance.
- Ensure compliance with HIPAA, GDPR, and other healthcare regulations when using with patient data.
- Do not upload real patient information without proper anonymization.

## ⚠️ Troubleshooting

### Backend Won't Start
**Error**: `ModuleNotFoundError: No module named 'backend'`
- **Solution**: Run from project root and use `python -m backend.main` instead of direct script execution.

**Error**: `Connection refused on port 8000`
- **Solution**: Check if another service is using port 8000. Use `netstat -ano | findstr :8000` (Windows) or `lsof -i :8000` (macOS/Linux).

### Missing/Invalid API Keys
**Error**: `KeyError: 'GROQ_API_KEY'` or similar
- **Solution**: Verify all keys are set in `backend/config.py`.
- **Solution**: Check environment variables: `echo %GROQ_API_KEY%` (Windows) or `echo $GROQ_API_KEY` (Unix).

**Error**: `401 Unauthorized` from Gemini/Groq/Google Maps
- **Solution**: Verify API keys are correct and have appropriate permissions enabled.
- **Solution**: Check API quotas and billing status in respective dashboards.


### PDF Processing Fails
**Error**: `DLL load failed` or `poppler not found` (Windows)
- **Solution**: Install poppler-utils:
  ```bash
  choco install poppler  # via Chocolatey
  # OR manually download from: https://github.com/oschwartz10612/poppler-windows/releases/
  ```
- **Solution**: Set poppler path in code if installed manually.

### Medical Image Analysis Errors
**Error**: `AgnoImage not found` or `agno import error`
- **Solution**: Ensure Agno SDK is installed: `pip install agno`
- **Solution**: Verify Gemini API key is correctly configured for Agno.

### High Memory Usage
- **Issue**: App uses >2GB RAM during PDF processing
- **Solution**: Process smaller files individually; PaddleOCR is memory-intensive.
- **Solution**: Consider using a GPU-enabled environment for faster processing.

## 🤝 Contributing

Contributions are welcome! To contribute:

1. **Fork the repository** and create a feature branch:
   ```bash
   git checkout -b feature/your-feature-name
   ```

2. **Make changes** and test thoroughly:
   ```bash
   python -m pytest tests/  # If tests exist
   python -m backend.main   # Manual testing
   cd frontend-web && npm run dev
   ```

3. **Follow code style** and add docstrings.

4. **Submit a pull request** with a clear description of changes.

### Areas for Contribution
- [ ] Database integration for chat history persistence
- [ ] Multi-language support (medical terminology)
- [ ] Enhanced DICOM parsing and 3D visualization
- [ ] Mobile app version (React Native/Flutter)
- [ ] Improved emergency detection with NLP models
- [ ] Unit and integration tests
- [ ] Docker containerization
- [ ] Performance optimization for large documents

## 📞 Support & Contact

For questions, bug reports, or feature requests:
- Open an [Issue](https://github.com/your-repo/issues)
- Contact: [your-email@example.com](mailto:your-email@example.com)

## 📄 License

This project is licensed under the **MIT License** - see the [LICENSE](LICENSE) file for details.

---

**Built with ❤️ for accessible, empathetic, and effective healthcare support.**

*Last Updated: December 2025*
