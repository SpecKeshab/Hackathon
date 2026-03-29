# Serene — System Flow Diagram

## Complete User Journey & System Architecture

```mermaid
graph TD
    Start(["👤 User Opens<br/>Serene App"]) --> CheckSession{{"isLoggedIn()?<br/>localStorage"}}

    %% ── AUTH GATE ──────────────────────────────────────────
    CheckSession -->|No| AuthPage["🔐<br/>Auth Page<br/>/login  •  auth.jsx"]
    CheckSession -->|Yes| LoadDashboard

    AuthPage --> AuthTab{{"Tab Choice?"}}
    AuthTab -->|Login| LoginForm["🔑<br/>Login Form<br/>username + password"]
    AuthTab -->|Register| RegisterForm["🆕<br/>Register Form<br/>username + password"]

    LoginForm  --> PostLogin["🌐<br/>POST /api/v1/auth/login<br/>bcrypt.checkpw()"]
    RegisterForm --> PostRegister["🌐<br/>POST /api/v1/auth/register<br/>bcrypt.hashpw() + insert user"]

    PostLogin    --> AuthResult{{"Valid?"}}
    PostRegister --> AuthResult
    AuthResult -->|No — error toast| AuthPage
    AuthResult -->|Yes| StoreSession["💾<br/>setUser()<br/>localStorage  •  session.js"]
    StoreSession --> LoadDashboard["🏠<br/>Load Dashboard<br/>chat.jsx + home.jsx"]

    %% ── DASHBOARD HYDRATION ────────────────────────────────
    LoadDashboard --> FetchAll["📡<br/>Hydrate State<br/>Parallel API Calls"]

    FetchAll --> FetchPlan["📋<br/>GET /daily/{user}/plan<br/>Merged Task Schedule"]
    FetchAll --> FetchUser["👤<br/>GET /user/{user}<br/>XP + Level"]
    FetchAll --> FetchStats["📊<br/>GET /stats/mental/{user}<br/>Session History"]
    FetchAll --> FetchProcess["⏱️<br/>GET /stats/process/{user}<br/>Days since created_at"]

    FetchPlan --> MergeSources["🔀<br/>Merge Task Sources<br/>task_service.get_tasks()"]
    MergeSources --> DefaultTasks["⚡<br/>DEFAULT Tasks<br/>SQLite DB"]
    MergeSources --> AiTasks["🤖<br/>AI Tasks<br/>SQLite DB"]
    MergeSources --> CustomTasks["✏️<br/>CUSTOM Tasks<br/>SQLite DB + JSON"]

    DefaultTasks & AiTasks & CustomTasks --> RenderTabs["🎨<br/>Render Dashboard<br/>Focus / Log / Stats / Chat / Blog"]
    FetchUser    --> RenderTabs
    FetchStats   --> RenderTabs
    FetchProcess --> RenderTabs

    %% ── TAB ROUTER ─────────────────────────────────────────
    RenderTabs --> TabChoice{{"Active Tab?"}}

    TabChoice -->|Focus| FocusTab["🎯<br/>Active Quest View<br/>Next Scheduled Task"]
    TabChoice -->|Log|   LogTab["📅<br/>Daily Schedule<br/>All Tasks + Statuses"]
    TabChoice -->|Stats| StatsTab["📈<br/>Mental Health Stats<br/>Energy & Progress"]
    TabChoice -->|Chat|  ChatFlow["💬<br/>Open Serene Chat<br/>Start / Resume Session"]
    TabChoice -->|Blog|  BlogPage["📝<br/>Community Blog<br/>/blog  •  blog.jsx"]
    TabChoice -->|Logout| DoLogout["🚪<br/>clearUser() + POST /api/v1/auth/logout<br/>session.js"]
    DoLogout --> AuthPage

    %% ── FOCUS TAB ──────────────────────────────────────────
    FocusTab --> ExecuteBtn["▶️<br/>Start Task Button<br/>POST /daily/{user}/execute"]
    ExecuteBtn --> CompleteBtn["✅<br/>Complete Task<br/>POST /daily/{user}/complete"]
    CompleteBtn --> XpCalc["⚙️<br/>Calc XP<br/>Timing + Duration Bonus"]
    XpCalc --> UpdateXP["💎<br/>Update User XP<br/>SQLite users table"]
    UpdateXP --> LevelUp{{"Level Up?"}}
    LevelUp -->|Yes| ShowLevelUp["🎉<br/>Level Up Toast<br/>New Level Displayed"]
    LevelUp -->|No|  RefreshFocus["🔄<br/>Refresh Focus View"]
    ShowLevelUp --> RefreshFocus

    %% ── LOG TAB — TASK MANAGEMENT ──────────────────────────
    LogTab --> TaskAction{{"Task Action?"}}
    TaskAction -->|Add Custom| AddTaskModal["📝<br/>Task Form Modal<br/>activity, time, xp"]
    AddTaskModal --> PostTask["💾<br/>POST /tasks/{user}/manual<br/>DB + JSON Write"]
    PostTask --> RefreshLog["🔄<br/>Refresh Plan<br/>GET /daily/{user}/plan"]

    TaskAction -->|Edit Custom| EditModal["✏️<br/>Edit Task Modal<br/>Custom Only"]
    EditModal --> PutTask["🔁<br/>PUT /tasks/{user}/manual/{time}<br/>DB + JSON Update"]
    PutTask --> RefreshLog

    TaskAction -->|Delete Custom| DelTask["🗑️<br/>DELETE /tasks/{user}/manual/{time}<br/>DB + JSON Remove"]
    DelTask --> RefreshLog
    RefreshLog --> LogTab

    %% ── BLOG ───────────────────────────────────────────────
    BlogPage --> FetchPosts["📡<br/>GET /api/v1/blog/posts<br/>Community Feed"]
    FetchPosts --> BlogAction{{"Blog Action?"}}

    BlogAction -->|Search / Filter| SearchPosts["🔍<br/>Filter In Memory<br/>title · content · user · tags"]
    SearchPosts --> BlogAction

    BlogAction -->|Open Post| PostDetail["👁️<br/>GET /blog/posts/{id}<br/>Full Post + Comments"]
    PostDetail --> PostAction{{"Post Action?"}}
    PostAction -->|Like|           ToggleLike["❤️<br/>POST /blog/posts/{id}/like/{user}<br/>Toggle Like"]
    PostAction -->|Comment|        AddComment["💬<br/>POST /blog/posts/{id}/comments/{user}<br/>Add Comment"]
    PostAction -->|Delete Comment| DelComment["🗑️<br/>DELETE /blog/comments/{user}/{id}<br/>Own Comments Only"]
    ToggleLike & AddComment & DelComment --> PostDetail

    BlogAction -->|Write Post| NewPostForm["✍️<br/>New Post Form<br/>title · content · mood · tags"]
    NewPostForm --> SubmitPost["💾<br/>POST /api/v1/blog/posts/{user}<br/>Save to SQLite"]
    SubmitPost --> FetchPosts

    BlogAction -->|Delete Post| DelPost["🗑️<br/>DELETE /blog/posts/{user}/{id}<br/>Own Posts Only"]
    DelPost --> FetchPosts

    %% ── SERENE CHAT ────────────────────────────────────────
    ChatFlow --> InputType{{"Input Type?"}}
    InputType -->|Text|  TextMsg["⌨️<br/>Text Message<br/>User Types"]
    InputType -->|Voice| VoiceMsg["🎤<br/>Voice Message<br/>MediaRecorder API"]
    VoiceMsg --> UploadAudio["☁️<br/>Upload Audio<br/>POST /sessions/{user}/{id}/voice"]
    UploadAudio --> SereneCall
    TextMsg     --> SereneCall["🤖<br/>Serene AI Call<br/>POST /sessions/{user}/{id}/chat"]

    SereneCall --> GeminiChat["✨<br/>Gemini 2.0 Flash<br/>oracle_service.py"]
    GeminiChat --> SessionFile["💾<br/>Append to Session<br/>local_data/session/*.json"]
    SessionFile --> StreamReply["💬<br/>Serene Reply<br/>Structured JSON Response"]
    StreamReply --> UserChoice{{"User Action?"}}

    UserChoice -->|Continue|     InputType
    UserChoice -->|End Session| EndSession["🔚<br/>POST /sessions/{user}/{id}/end<br/>Close Session File"]

    EndSession --> Analyze["🔬<br/>POST /sessions/{user}/{id}/analyze<br/>Trigger Analysis"]
    Analyze --> GeminiAnalysis["🧠<br/>Gemini Analysis<br/>analysis_service.py"]
    GeminiAnalysis --> ParseResult["📊<br/>Parse JSON Result<br/>Feelings, Energy, Tasks"]
    ParseResult --> SaveAnalysis["💾<br/>Save to ConversationAnalysis<br/>SQLite DB"]
    ParseResult --> ReplaceAiTasks["♻️<br/>replace_ai_tasks()<br/>Atomic Swap in DB"]
    SaveAnalysis & ReplaceAiTasks --> RefreshAll["🔄<br/>Refresh Dashboard<br/>New AI Tasks Visible"]
    RefreshAll --> RenderTabs

    %% ── STYLES — decisions ─────────────────────────────────
    style Start        fill:#222,stroke:#000,stroke-width:3px,color:#fff
    style CheckSession fill:#444,stroke:#000,stroke-width:2px,color:#fff
    style AuthTab      fill:#444,stroke:#000,stroke-width:2px,color:#fff
    style AuthResult   fill:#444,stroke:#000,stroke-width:2px,color:#fff
    style TabChoice    fill:#444,stroke:#000,stroke-width:2px,color:#fff
    style LevelUp      fill:#444,stroke:#000,stroke-width:2px,color:#fff
    style TaskAction   fill:#444,stroke:#000,stroke-width:2px,color:#fff
    style BlogAction   fill:#444,stroke:#000,stroke-width:2px,color:#fff
    style PostAction   fill:#444,stroke:#000,stroke-width:2px,color:#fff
    style InputType    fill:#444,stroke:#000,stroke-width:2px,color:#fff
    style UserChoice   fill:#444,stroke:#000,stroke-width:2px,color:#fff

    %% ── STYLES — process nodes ─────────────────────────────
    style AuthPage       fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style LoginForm      fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style RegisterForm   fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style PostLogin      fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style PostRegister   fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style StoreSession   fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style DoLogout       fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style LoadDashboard  fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style FetchAll       fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style FetchPlan      fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style FetchUser      fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style FetchStats     fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style FetchProcess   fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style MergeSources   fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style DefaultTasks   fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style AiTasks        fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style CustomTasks    fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style RenderTabs     fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style FocusTab       fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style LogTab         fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style StatsTab       fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style ChatFlow       fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style BlogPage       fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style ExecuteBtn     fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style CompleteBtn    fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style XpCalc         fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style UpdateXP       fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style ShowLevelUp    fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style RefreshFocus   fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style AddTaskModal   fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style PostTask       fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style EditModal      fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style PutTask        fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style DelTask        fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style RefreshLog     fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style FetchPosts     fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style SearchPosts    fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style PostDetail     fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style ToggleLike     fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style AddComment     fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style DelComment     fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style NewPostForm    fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style SubmitPost     fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style DelPost        fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style TextMsg        fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style VoiceMsg       fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style UploadAudio    fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style SereneCall     fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style GeminiChat     fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style SessionFile    fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style StreamReply    fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style EndSession     fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style Analyze        fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style GeminiAnalysis fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style ParseResult    fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style SaveAnalysis   fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style ReplaceAiTasks fill:#333,stroke:#000,stroke-width:2px,color:#fff
    style RefreshAll     fill:#333,stroke:#000,stroke-width:2px,color:#fff
```

## Legend

- **Ovals**: Start and End points
- **Rectangles**: Processing steps and actions
- **Diamonds**: Decision points
- **Black Theme**: Consistent with LifeUp Flowchart Classic style

## What Changed from v1

| Area | v1 (old) | v2 (current) |
|------|----------|--------------|
| **Auth** | Auto-created user on first visit (no password) | Full Login / Register with `bcrypt` hashing |
| **Session** | Hardcoded `"incri"` username everywhere | `localStorage` session via `session.js` — `getUsername()` / `isLoggedIn()` |
| **Route guard** | No protection — any URL was accessible | `ProtectedRoute` in `App.jsx` — unauthenticated users go to `/login` |
| **Logout** | Not available | Sidebar + mobile header button — `clearUser()` + `POST /api/v1/auth/logout` |
| **Blog** | Not available | Community feed with search, likes, comments, create/delete posts |
| **Process Age** | Global `2024-01-01` constant — same for all users | Per-user `created_at` from SQLite — resets to 0 on registration |

## API Reference

### Auth  (`/api/v1/auth`)
| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/register` | Create account — bcrypt hash stored |
| `POST` | `/login` | Verify password — returns user object |
| `POST` | `/logout` | Stateless — frontend calls `clearUser()` |

### Blog  (`/api/v1/blog`)
| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/posts` | List all posts, newest first |
| `GET` | `/posts/{id}` | Single post with all comments |
| `POST` | `/posts/{user}` | Create new post |
| `PUT` | `/posts/{user}/{id}` | Edit own post |
| `DELETE` | `/posts/{user}/{id}` | Delete own post |
| `POST` | `/posts/{id}/like/{user}` | Toggle like |
| `POST` | `/posts/{id}/comments/{user}` | Add comment |
| `DELETE` | `/comments/{user}/{id}` | Delete own comment |

## Task Source Key

| Badge | Source | Storage | Editable? |
|-------|--------|---------|-----------|
| ⚡ DEFAULT | Constitution tasks seeded at registration | SQLite DB | ❌ No |
| 🤖 AI | Generated by Serene after each session analysis | SQLite DB (replaced each session) | ❌ No |
| ✏️ CUSTOM | Created by the user | SQLite DB + `local_data/custom_tasks/{user}.json` | ✅ Yes |

