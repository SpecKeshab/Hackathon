# LifeUp: Mental Health & Wellness Platform

> A comprehensive, AI-powered mental health and wellness application designed to help users track mental health, receive personalized support, and engage with a supportive community.

## 👥 Team Members
- Avis Shrestha
- Sandesh Gyawali
- Alina Chauhan
- Bhaibhav Paudel
- Keshab Shrestha

---

## 📋 Project Overview

LifeUp is an intelligent mental health tracking and support platform that combines gamification, AI-driven insights, and community engagement to help users maintain and improve their mental wellbeing.

---

## 🎯 Key Features


### AI Intelligence(**Main Feature**)
- **Voice-to-Text Input** - Natural interaction via browser Web Speech API
- **Smart Summarization** - AI automatically summarizes voice notes and journal entries
- **RAG-Enhanced Chatbot** - Context-awa

### Activity Tracking(**Final Phase**)「OPTIONAL」
- **Gamified Wellness Tracking** - Level-based progression system with milestone rewards
- **Photo Evidence** - Record activities with multimedia proof for accountability
- **Interactive TODO Dashboard** - Gamified task management with real-time feedback

### Community & Support(**Final Phase**)「OPTIONAL」
- **Reddit-style Forum** - Users can share experiences and support each other
- **Intelligent Search & Filtering** - Discover relevant discussions and resources
- **AI-Powered Responses** - Get contextual support through intelligent chatbot
re responses using Retrieved Augmented Generation

---

## 🏗️ Architecture

### Frontend Stack
- **Framework:** React with Vite (fast build tool)
- **Language:** TypeScript (type safety)
- **Styling:** Tailwind CSS (utility-first design)
- **Voice Input:** 

### Backend Stack
- **Framework:** FastAPI (Python - async-first)
- **Database:** PostgreSQL with pgvector extension
- **Vector Storage:** PgVector for embedding management
- **Version Control:** Git

### Core Backend Modules
```
backend/
├── chat/              → Chat/conversation endpoints
├── ai_engine/         → LLM orchestration & integration
├── memory/            → Vector embeddings & semantic search
├── feedback/          → User feedback & analytics
├── voice/             → Speech-to-Text & Text-to-Speech handlers
└── tasks/             → Async job queue & background processing
```

---

## 🧠 RAG System Architecture

The Retrieval-Augmented Generation (RAG) system enables intelligent, context-aware responses:

```
User Input (Voice/Text)
    ↓
[Transcription & Preprocessing]
    ↓
[Query Embedding] → PgVector Search
    ↓
[Metadata Retrieval] → PostgreSQL
    ↓
[Context Assembly]
    ↓
[LLM Generation] → Informed Response
```

### RAG Components

| Component | Purpose | Storage |
|-----------|---------|---------|
| **User Question** | Query input from user | In-memory |
| **Summary Embeddings** | Vectorized content representations | PgVector |
| **Metadata** | Contextual information & source tracking | PostgreSQL |
| **Chunking Strategy** | Recursive splitting for optimal context | N/A |
| **Search Method** | Similarity-based retrieval | PgVector (Vector Search) |

---

## 🚀 Development Roadmap

### Phase 1: Backend Foundation
- FastAPI application setup
- Database & vector store configuration
- Core chat endpoints
- Voice handling pipeline

### Phase 2: Frontend Development
- React/Vite setup
- Voice input integration
- User authentication & profiles「OPTIONAL」
- Gamified dashboard「OPTIONAL」

### Phase 3: AI Integration
- LLM orchestration
- RAG implementation
- Smart summarization
- Feedback loops

### Phase 4: Community Features(**Final Phase**)「OPTIONAL」
- Forum implementation
- Filtering & search
- Analytics dashboard

---

## 🛠️ Tech Stack Summary

| Layer | Technology |
|-------|-----------|
| Frontend | React, TypeScript, Tailwind CSS, Vite |
| Backend | FastAPI, Python, PostgreSQL |
| AI/ML | LLM APIs, Vector Embeddings, RAG |
| Voice | --- |
| Database | PostgreSQL + PgVector |
| DevOps | Git, Docker (planned) |

---

## 📊 Evaluation Criteria

- **Problem Understanding** - Clear focus on mental health support
- **Innovation** - RAG chatbot + (Gamification + community features)
- **Technical Complexity** - Vector DBs, async processing, voice APIs
- **Impact** - Accessible mental health support for users
- **Presentation** - Well-structured, professional implementation
