# Serene — System Flow Diagram (Simplified)

## High-Level User Journey & System Architecture

```mermaid
graph TD
    Start(["👤 User Opens<br/>Serene App"]) --> CheckSession{{"Is Logged In?"}}

    %% ── AUTHENTICATION ─────────────────────────────────────
    CheckSession -->|No| Auth["🔐 Login / Register<br/>POST /api/v1/auth/{login|register}"]
    CheckSession -->|Yes| LoadDashboard
    Auth -->|Success| StoreSession["💾 Store Session<br/>localStorage"]
    Auth -->|Failed| Auth
    StoreSession --> LoadDashboard["🏠 Dashboard Loaded"]

    %% ── DASHBOARD HYDRATION ────────────────────────────────
    LoadDashboard --> FetchData["📡 Fetch User Data<br/>Tasks · XP · Stats · Process Age<br/>GET /daily, /user, /stats"]
    FetchData --> MergeTasks["🔀 Merge Task Sources<br/>⚡ Default · 🤖 AI · ✏️ Custom"]
    MergeTasks --> RenderDashboard["🎨 Render Tabs<br/>Focus · Log · Stats · Chat · Blog"]

    %% ── TAB ROUTER ─────────────────────────────────────────
    RenderDashboard --> TabChoice{{"Active Tab?"}}

    TabChoice -->|Focus| FocusTab["🎯 Next Active Task<br/>POST /daily/{execute|complete}"]
    TabChoice -->|Log| LogTab["📅 Manage Custom Tasks<br/>POST/PUT/DELETE /tasks/manual"]
    TabChoice -->|Stats| StatsTab["📈 Mental Health Stats<br/>GET /stats/mental"]
    TabChoice -->|Chat| ChatFlow["💬 Serene AI Chat<br/>POST /sessions/{chat|voice|end|analyze}"]
    TabChoice -->|Blog| BlogPage["📝 Community Feed<br/>GET/POST/DELETE /blog/posts"]
    TabChoice -->|Logout| Auth

    %% ── FOCUS TAB ──────────────────────────────────────────
    FocusTab --> CompleteTask["✅ Execute & Complete Task<br/>XP Calc · Level Check · Refresh"]
    CompleteTask --> RenderDashboard

    %% ── LOG TAB ────────────────────────────────────────────
    LogTab --> TaskOps{{"Task Action?"}}
    TaskOps -->|Add/Edit/Delete| ManageTasks["🔧 Manage Tasks<br/>POST · PUT · DELETE<br/>/tasks/manual/{time}"]
    ManageTasks --> RenderDashboard

    %% ── BLOG TAB ───────────────────────────────────────────
    BlogPage --> BlogOps{{"Blog Action?"}}
    BlogOps -->|Browse/Search| BrowsePosts["🔍 View Posts<br/>GET /blog/posts + filter"]
    BlogOps -->|Create/Delete| CreatePost["✍️ Create/Delete Posts<br/>POST · DELETE /blog/posts"]
    BlogOps -->|Like/Comment| InteractPosts["❤️ Like/Comment<br/>POST · DELETE /blog/{posts|comments}"]
    BrowsePosts & CreatePost & InteractPosts --> RenderDashboard

    %% ── CHAT TAB ───────────────────────────────────────────
    ChatFlow --> SendMessage["💬 Send Message<br/>Text or Voice<br/>POST /sessions/{user}/chat"]
    SendMessage --> AiProcess["🤖 Serene AI (Gemini)<br/>oracle_service.py<br/>Save to session JSON"]
    AiProcess --> Reply["💻 AI Response<br/>Display to User"]
    Reply --> ChatChoice{{"Continue or End?"}}
    ChatChoice -->|Continue| SendMessage
    ChatChoice -->|End Session| EndSession["🔬 Analyze Session<br/>POST /sessions/{user}/analyze<br/>Update AI Tasks"]
    EndSession --> RenderDashboard

    %% ── STYLES ────────────────────────────────────────────
    style Start          fill:#222,stroke:#000,stroke-width:3px,color:#fff
    style CheckSession   fill:#444,stroke:#000,stroke-width:2px,color:#fff
    style Auth           fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style StoreSession   fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style LoadDashboard  fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style FetchData      fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style MergeTasks     fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style RenderDashboard fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style TabChoice      fill:#444,stroke:#000,stroke-width:2px,color:#fff
    style FocusTab       fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style LogTab         fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style StatsTab       fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style ChatFlow       fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style BlogPage       fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style CompleteTask   fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style TaskOps        fill:#444,stroke:#000,stroke-width:2px,color:#fff
    style ManageTasks    fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style BlogOps        fill:#444,stroke:#000,stroke-width:2px,color:#fff
    style BrowsePosts    fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style CreatePost     fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style InteractPosts  fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style SendMessage    fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style AiProcess      fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style Reply          fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style ChatChoice     fill:#444,stroke:#000,stroke-width:2px,color:#fff
    style EndSession     fill:#333,stroke:#000,stroke-width:2px,color:#fff
```


## Legend

- **Ovals**: Start/End points
- **Rectangles**: Operations (API calls + UI actions)
- **Diamonds**: Decision points
- **Merged Operations**: POST, PUT, DELETE combined into single nodes for clarity

## System Components

| Component | Purpose |
|-----------|---------|
| **Auth** | Login/Register — bcrypt hashing + localStorage session |
| **Dashboard** | Parallel data fetch + merge task sources |
| **Focus Tab** | Execute & complete active task → XP calculation |
| **Log Tab** | Create/Edit/Delete custom tasks |
| **Stats Tab** | Mental health metrics + energy tracking |
| **Chat Tab** | Serene AI (Gemini) + session analysis → AI task generation |
| **Blog Tab** | Community feed with posts, likes, comments |

## API Reference (Simplified)

| Feature | Method | Endpoint | Purpose |
|---------|--------|----------|---------|
| **Auth** | POST | `/api/v1/auth/{login\|register}` | Authenticate user |
| **Logout** | POST | `/api/v1/auth/logout` | Clear session |
| **Tasks** | GET/POST/PUT/DELETE | `/daily`, `/stats`, `/tasks/manual` | Fetch & manage tasks |
| **Blog** | GET/POST/DELETE | `/api/v1/blog/posts\|comments` | Posts + interactions |
| **Chat** | POST | `/sessions/{user}/{chat\|voice\|end\|analyze}` | AI chat + analysis |
---