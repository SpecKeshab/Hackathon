# LifeUp API Documentation

> Comprehensive API architecture for the LifeUp mental health platform

---

## 📡 API Strategy Overview

For the LifeUp platform, we recommend a **hybrid API approach** combining multiple technologies:

| API Type | Purpose | Use Case |
|----------|---------|----------|
| **REST API** | Standard CRUD operations | User profiles, posts, tasks, gamification data |
| **WebSocket API** | Real-time bidirectional communication | Live chat, instant notifications, typing indicators |
---

## REST API (Primary)

### Purpose
Handle all standard CRUD operations and business logic

### Key Endpoints

#### Authentication
```
POST   /api/auth/register          Create new user account
POST   /api/auth/login             Authenticate user
POST   /api/auth/refresh           Refresh access token
POST   /api/auth/logout            Logout user
```

#### User Management
```
GET    /api/users/me               Get current user profile
PUT    /api/users/me               Update user profile
GET    /api/users/{id}             Get user by ID (public info)
DELETE /api/users/me               Delete account
```

#### Gamification & Tracking
```
GET    /api/users/me/level         Get user level & progress
GET    /api/users/me/achievements  Get user achievements
POST   /api/activities             Log new activity with photo
GET    /api/activities             Get user's activity history
PUT    /api/activities/{id}        Update activity entry
```

#### Tasks & TODOs
```
GET    /api/tasks                  Get all tasks for user
POST   /api/tasks                  Create new task
PUT    /api/tasks/{id}             Update task
DELETE /api/tasks/{id}             Delete task
POST   /api/tasks/{id}/complete    Mark task as complete
```

#### Chat & Conversations
```
GET    /api/chat/history           Get chat history
POST   /api/chat/messages          Send message (REST fallback)
GET    /api/chat/messages          Retrieve past messages
```

#### RAG & AI
```
POST   /api/ai/query               Submit question to RAG system
GET    /api/ai/suggestions         Get wellness suggestions
POST   /api/ai/analyze             Analyze emotion/sentiment
```

#### Forum & Posts [**OPTIONAL**]
```
GET    /api/forum/posts            Get all posts (with filters)
POST   /api/forum/posts            Create new post
GET    /api/forum/posts/{id}       Get post with comments
PUT    /api/forum/posts/{id}       Update post
DELETE /api/forum/posts/{id}       Delete post
GET    /api/forum/posts/{id}/comments  Get post comments
POST   /api/forum/posts/{id}/comments  Add comment
```
#### Feedback & Analytics [**OPTIONAL**]
```
POST   /api/feedback               Submit user feedback
GET    /api/feedback/sentiment     Get mood/sentiment analysis
GET    /api/insights               Get personalized insights
```


### Response Format
```json
{
  "status": "success|error",
  "data": { /* response payload */ },
  "message": "Human-readable message",
  "timestamp": "2026-03-28T10:30:00Z"
}
```

---

## 🏗️ API Architecture Layers

```
Client (React/WebApp)
    ↓
    ├── REST API (HTTP/HTTPS)
    └── WebSocket API (ws/wss)
    ↓
API Gateway (nginx/load balancer)
    ↓
FastAPI Backend
    ├── chat/ (WebSocket handlers)
    ├── ai_engine/ (LLM + RAG)
    ├── voice/ (STT + TTS)
    ├── memory/ (Vector DB queries)
    └── tasks/ (Async jobs)
    ↓
Database Layer
    ├── PostgreSQL (data)
    └── PgVector (embeddings)
```

---

## 📊 Implementation Priority

### Phase 1 (MVP)
- ✅ REST API (Core endpoints)
- ✅ WebSocket Chat
- ✅ Basic RAG integration
- ✅ Voice streaming (STT)

### Phase 2
- Authentication/Authorization
- Advanced caching
- Analytics endpoints

---

## 📚 Related Resources

- FastAPI WebSocket Docs: https://fastapi.tiangolo.com/advanced/websockets/
- WebSocket Best Practices: https://www.rfc-editor.org/rfc/rfc6455
- OpenAPI/Swagger: Auto-generated at `/docs`