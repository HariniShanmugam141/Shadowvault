# 🔐 ShadowVault — Intelligent Adaptive Data Anonymization Platform

> NASSCOM Hackathon 2025 | Privacy & Compliance Track

---

## 🚀 Quick Setup (Windows)

### Step 1 — Install Python dependencies
```bash
pip install -r requirements.txt
```

### Step 2 — Download spaCy model
```bash
python -m spacy download en_core_web_sm
```

### Step 3 — Install Tesseract OCR
Download and install from:
https://github.com/UB-Mannheim/tesseract/wiki

Add to PATH: `C:\Program Files\Tesseract-OCR`

### Step 4 — Install Ollama (for LLM contextual detection)
Download from: https://ollama.com/download

Then run:
```bash
ollama pull mistral
```

Start Ollama server (runs in background):
```bash
ollama serve
```

### Step 5 — Run ShadowVault
```bash
python app.py
```

Open: http://127.0.0.1:5000

---

## 📁 Project Structure

```
shadowvault/
├── app.py                  ← Flask web server + all routes
├── shadowvault_core.py     ← Core engine (PII, vault, linking, scoring)
├── requirements.txt
├── templates/
│   ├── index.html          ← Dashboard + upload UI
│   ├── vault.html          ← Token vault + decryption
│   └── entities.html       ← Cross-document entity graph
├── vault/
│   ├── shadowvault.db      ← SQLite: tokens, entities, documents
│   └── master.key          ← AES-256 master encryption key (auto-generated)
├── uploads/                ← Uploaded documents
└── outputs/                ← Redacted output documents
```

---

## ✨ Features

### 1. 🔍 PII Detection (3 Layers)
- **Regex**: 20+ patterns — Aadhaar, PAN, passport, phone, email, addresses
- **spaCy NER**: Named entity recognition for names, locations, dates
- **Ollama LLM**: Contextual/implied PII detection (requires Ollama running)

### 2. 🔄 Reversible Anonymization
- PII replaced with encrypted tokens: `<<SV-TOKEN-A1B2C3D4>>`
- Real values stored in AES-256 encrypted SQLite vault
- Role-based decryption: admin > doctor > auditor > legal > analyst > viewer

### 3. 🔗 Cross-Document Entity Linking
- Detects same person across multiple documents
- Handles name variations: "Harini Shankar" = "H. Shankar" = "H.S."
- Assigns consistent pseudonyms (PERSON-0001) across all documents

### 4. 📊 Privacy Risk Score
- Weighted scoring based on PII type sensitivity
- k-anonymity risk estimation
- Levels: Low / Medium / High / Critical

### 5. 📋 Compliance Profiles
- **GDPR** (EU) — email, name, location, IP, cookies
- **HIPAA** (Healthcare) — MRN, diagnosis, insurance, patient data
- **DPDP Act 2023** (India) — Aadhaar, PAN, voter ID, bank account
- **PCI-DSS** (Financial) — card numbers, CVV, routing numbers

---

## 🔐 Role-Based Access

| Role    | Can Decrypt                                    |
|---------|------------------------------------------------|
| admin   | Everything                                     |
| doctor  | Name, DOB, MRN, Phone, Email, Insurance        |
| auditor | Aadhaar, PAN, SSN, Bank Account, Insurance     |
| legal   | Name, DOB, Address, Passport, Voter ID, SSN    |
| analyst | ZIP, Dates, Insurance ID only                  |
| viewer  | Nothing (can see tokens only)                  |

---

## ⚠️ If Ollama is not running
ShadowVault works without Ollama — it falls back to regex + spaCy.
The LLM layer adds contextual/implied PII detection on top.

---

## 🧪 Test with sample documents
Try uploading:
- A scanned ID card (JPG/PNG) — detects Aadhaar, name, DOB, photo
- A medical PDF — detects MRN, patient name, insurance ID
- Multiple documents with the same person — see entity linking in action
