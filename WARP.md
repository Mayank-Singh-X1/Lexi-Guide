# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project Overview

LexiGuard is a multilingual legal document analyzer built as a hackathon project. It's a Flask-based web application that uses Google Gemini AI to analyze legal documents (rental agreements, loan contracts, terms of service) and provides summaries, risk analysis, and actionable insights in multiple languages.

## Architecture

### Backend (`backend/`)
- **Flask application** (`app.py`) - Single monolithic file containing all backend logic
- **AI Integration** - Google Gemini API for document analysis with sophisticated prompt engineering
- **Authentication** - Firebase Auth for user management
- **Database** - Firestore for storing user analyses and shared links
- **Document Processing** - Multi-format support (PDF, DOCX, images) with OCR fallback using Tesseract

### Frontend (`frontend/`)
- **Single-page application** (`index.html`) - Complete UI with embedded JavaScript modules
- **Firebase integration** - Client-side auth and Firestore queries
- **Responsive design** - CSS Grid/Flexbox with dark/light theme support
- **Interactive features** - Legal term explanations, document comparison, clause analysis

### Key Components

#### Document Analysis Pipeline (backend/app.py lines 153-240)
1. **Jurisdiction Detection** - Auto-detects legal jurisdiction from document content
2. **Document Classification** - Categorizes as rental, loan, or ToS with sentiment analysis  
3. **Multi-stage AI Analysis** - Structured extraction, risk analysis, missing clauses detection
4. **Multilingual Support** - All analyses delivered in user's preferred language

#### User Management & Data Persistence
- Firebase Auth integration with JWT token verification
- Firestore collections: `users/{uid}/documents/` for analysis history
- Shared analysis system with public links via `shared_analyses/` collection
- Document tagging and search functionality

#### Advanced Features
- **Interactive Legal Terms** - Clickable explanations for legal terminology
- **Document Comparison** - Side-by-side analysis of document versions
- **Clause Deep Dive** - Focused analysis of specific contract clauses
- **Export/Share** - PDF generation and shareable public links

## Common Development Tasks

### Running the Application
```bash
# Backend setup
cd backend
python -m venv venv
# Windows
venv\Scripts\activate
# macOS/Linux  
source venv/bin/activate
pip install -r requirements.txt

# Start the Flask server
python app.py
```

### Environment Setup
Create `backend/.env` with:
```
GEMINI_API_KEY="your_google_gemini_api_key"
```

**Required Firebase Setup:**
1. Firebase configuration is embedded in `frontend/index.html` (lines 126-134)
2. **CRITICAL**: You must create `backend/serviceAccountKey.json` with Firebase Admin SDK credentials
3. Without this file, the application will fail to start with: `FileNotFoundError: serviceAccountKey.json`

**Optional but Recommended:**
- Install Tesseract OCR system-wide for image text extraction (pytesseract is included in requirements)

### Testing Document Analysis
The application accepts these file formats:
- PDFs (text extraction with OCR fallback)
- DOCX/DOC files
- Images (PNG, JPG, JPEG) with OCR

### Debugging AI Responses
All AI interactions use structured prompts defined as constants (lines 75-148 in app.py). The system includes:
- Jurisdiction detection prompts
- Document classification prompts  
- Multi-part analysis prompts (key info extraction + summaries)
- Risk analysis and missing clause detection prompts

## Project-Specific Patterns

### Prompt Engineering Architecture
The codebase uses a sophisticated prompt template system where each analysis stage has dedicated prompts with specific formatting requirements. All prompts include jurisdiction context and language specification for localized responses.

### Error Handling Strategy
- Frontend: Try-catch blocks with user-friendly alerts
- Backend: Exception logging with generic error responses to avoid exposing system details
- Document processing: Graceful degradation (OCR fallback for scanned PDFs)

### Firebase Integration Pattern
- Client-side: Firebase v9 modular SDK for auth and Firestore
- Server-side: Firebase Admin SDK for user verification and database operations
- Security: JWT token verification on all protected endpoints

### Multi-language Support
The system delivers analysis in 6 languages: English, Hinglish, Hindi, Telugu, Tamil, and Gujarati. Language selection affects all AI-generated content but UI remains in English.

## Architecture Considerations

### Single-File Backend
The entire backend logic resides in one 324-line Flask file. While functional for a hackathon project, this monolithic approach means:
- All route handlers, AI prompts, and utility functions are in one file
- Database operations are mixed with business logic
- No separation of concerns between different analysis types

### Client-Side State Management  
The frontend uses vanilla JavaScript with global variables and function declarations attached to `window`. State management is handled through DOM manipulation and Firebase real-time updates.

### AI Response Processing
The system performs complex JSON extraction from AI responses using string manipulation (finding JSON boundaries) rather than structured parsing, which can be fragile with model output variations.

### Document Storage Strategy
Raw document text is not stored - only the analysis results are persisted. This reduces storage costs but prevents re-analysis with improved prompts without re-uploading.

## Troubleshooting

### Common Setup Issues
1. **Missing Firebase Service Account**: Create `backend/serviceAccountKey.json` from Firebase Console > Project Settings > Service Accounts
2. **Tesseract OCR**: If image processing fails, install Tesseract system-wide and ensure it's in PATH
3. **API Key Issues**: Verify Google Gemini API key is valid and has appropriate quotas
4. **Dependency Conflicts**: The requirements.txt uses specific versions - avoid upgrading without testing

### Verified Working Environment
- ✅ Python 3.13+ (tested)
- ✅ Windows 10/11 PowerShell (tested)
- ✅ All dependencies install successfully via pip
- ✅ Flask development server runs on port 5000
- ✅ Frontend serves from `/` route with static file serving
- ✅ Firebase integration working (with service account key)
- ✅ Google Gemini API integration functional
- ✅ Complete UI loads with authentication, document upload, and analysis features

### Test Results Summary
**Date**: September 12, 2025  
**Status**: ✅ FULLY OPERATIONAL  
**URL**: http://127.0.0.1:5000  
**Features Confirmed**:
- Authentication system (login/signup)
- Document upload (PDF, DOCX, images)
- Multi-language analysis (6 languages)
- Legal term explanations
- Document comparison
- Export/sharing functionality

<citations>
<document>
<document_type>WARP_DOCUMENTATION</document_type>
<document_id>getting-started/quickstart-guide/coding-in-warp</document_id>
</document>
</citations>
