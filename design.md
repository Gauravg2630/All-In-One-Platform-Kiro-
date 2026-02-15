# Design Document: AI-Powered Unified Learning and Developer Productivity Platform

## Executive Summary

This design document details the technical architecture, component specifications, data models, and implementation strategies for an AI-powered unified learning platform. The platform consolidates learning, coding, AI assistance, project management, and portfolio showcasing into a single integrated experience, addressing the critical fragmentation problem in technical education.

The system employs a modern full-stack architecture with React.js frontend, Node.js/Express.js backend, PostgreSQL database, Redis caching, and integrated LLM services. Security is paramount with sandboxed code execution, JWT authentication, and comprehensive input validation. The design emphasizes scalability, maintainability, and exceptional user experience.

## Overview

### Problem Context

Students and developers face severe productivity losses from platform fragmentation:
- Average 5-7 platforms used daily for learning workflow
- 23 minutes lost per context switch (research: University of California, Irvine)
- Only 15% course completion rate due to friction
- 12+ months to build job-ready portfolio vs. potential 3 months

### Solution Architecture

Our platform provides:
1. **Single Integrated Environment**: All tools in one browser tab
2. **Context-Aware AI**: Assistance that knows user's learning state and code
3. **Zero-Setup Coding**: Browser-based execution with instant feedback
4. **Automatic Portfolio**: Projects become portfolio items automatically
5. **Structured Learning**: Distraction-free, curated paths with clear progression

### Design Principles

1. **Developer Experience First**: Minimize cognitive load, maximize flow state
2. **Performance**: Sub-2-second page loads, sub-5-second AI responses
3. **Security by Design**: Defense in depth with isolated execution environments
4. **Scalable Architecture**: Support 100K+ concurrent users without degradation
5. **Data Integrity**: ACID compliance, automatic backups, zero data loss
6. **Privacy Respect**: GDPR compliant, user data ownership, transparent policies

## High-Level Architecture

### System Architecture Diagram

```
┌────────────────────────────────────────────────────────────────┐
│                     CLIENT LAYER (Browser)                     │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │              React.js Single Page Application            │  │
│  │                                                          │  │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────────┐   │  │
│  │  │  Learning   │  │    Code     │  │  AI Assistant   │   │  │
│  │  │  Dashboard  │  │   Editor    │  │   Interface     │   │  │
│  │  │             │  │  (Monaco)   │  │                 │   │  │
│  │  └─────────────┘  └─────────────┘  └─────────────────┘   │  │
│  │                                                          │  │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────────┐   │  │
│  │  │   Project   │  │   Profile   │  │    Progress     │   │  │
│  │  │  Manager    │  │  Showcase   │  │    Tracker      │   │  │
│  │  │             │  │             │  │                 │   │  │
│  │  └─────────────┘  └─────────────┘  └─────────────────┘   │  │
│  └──────────────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────────────┘
                             │  ▲
                             │  │  HTTPS/TLS 1.3
                             ▼  │
┌────────────────────────────────────────────────────────────────┐
│                    API GATEWAY LAYER                           │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │              Express.js Router & Middleware              │  │
│  │                                                          │  │
│  │  [CORS] → [Auth] → [Rate Limit] → [Validation] → [Routes]│  │
│  └──────────────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────────────┘
                             │  ▲
                             ▼  │
┌────────────────────────────────────────────────────────────────┐
│                     SERVICE LAYER                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────┐      │
│  │     User     │  │   Learning   │  │       AI         │      │
│  │   Service    │  │   Service    │  │  Assistant       │      │
│  │              │  │              │  │    Service       │      │
│  └──────────────┘  └──────────────┘  └──────────────────┘      │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────┐      │
│  │     Code     │  │   Project    │  │    Profile       │      │
│  │  Execution   │  │   Service    │  │    Service       │      │
│  │   Service    │  │              │  │                  │      │
│  └──────────────┘  └──────────────┘  └──────────────────┘      │
│  ┌──────────────┐  ┌──────────────┐                            │
│  │  Analytics   │  │   Feedback   │                            │
│  │   Service    │  │   Service    │                            │
│  │              │  │              │                            │
│  └──────────────┘  └──────────────┘                            │
└────────────────────────────────────────────────────────────────┘
         │                    │                    │
         ▼                    ▼                    ▼
┌──────────────┐  ┌─────────────────────┐  ┌────────────────┐
│   External   │  │   Code Execution    │  │  File Storage  │
│ LLM Provider │  │   Sandbox (Docker)  │  │   (AWS S3)     │
│(Claude/GPT)  │  │                     │  │                │
└──────────────┘  └─────────────────────┘  └────────────────┘
                             │
                             ▼
┌────────────────────────────────────────────────────────────────┐
│                       DATA LAYER                               │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │                                                          │  │
│  │  ┌─────────────────────┐      ┌─────────────────────┐    │  │
│  │  │    PostgreSQL       │      │       Redis         │    │  │
│  │  │  Primary Database   │      │   Cache & Queue     │    │  │
│  │  │                     │      │                     │    │  │
│  │  │ - Users             │      │ - Session Store     │    │  │
│  │  │ - Learning Content  │      │ - AI Cache          │    │  │
│  │  │ - Projects          │      │ - Execution Queue   │    │  │
│  │  │ - Conversations     │      │ - Rate Limit Data   │    │  │
│  │  │ - Progress          │      │                     │    │  │
│  │  └─────────────────────┘      └─────────────────────┘    │  │
│  │                                                          │  │
│  └──────────────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────────────┘
```

### Component Interaction Flow

**Example: User Executes Code with AI Help**

```
User writes code in Monaco Editor
         │
         ▼
Frontend sends execution request
         │
         ▼
API Gateway validates request & checks rate limit
         │
         ▼
Code Execution Service receives request
         │
         ├─> Checks Redis cache for identical code
         │
         ├─> Creates isolated Docker container
         │
         ├─> Executes code with 5s timeout, 512MB limit
         │
         ├─> Captures stdout, stderr, execution time
         │
         └─> Returns result to frontend
         
User encounters error
         │
         ▼
Clicks "Get AI Help" button
         │
         ▼
Frontend sends code + error + context to AI Service
         │
         ▼
AI Service constructs contextual prompt including:
  - User's current learning module
  - Code from editor
  - Error message
  - User's skill level
         │
         ▼
Calls LLM API (Claude/GPT)
         │
         ▼
Receives educational explanation
         │
         ▼
Stores conversation in database
         │
         ▼
Returns response to frontend
         │
         ▼
User sees helpful explanation with debugging steps
```

## Detailed Component Design

### Frontend Architecture

#### Technology Stack
- **Framework**: React 18+ with hooks
- **State Management**: React Context API + useReducer for complex state
- **Routing**: React Router v6
- **Styling**: Tailwind CSS for utility-first styling
- **Code Editor**: Monaco Editor (VS Code's editor)
- **HTTP Client**: Axios with interceptors
- **Build Tool**: Vite for fast development and optimized builds

#### Component Structure

```
src/
├── components/
│   ├── auth/
│   │   ├── LoginForm.jsx
│   │   ├── RegisterForm.jsx
│   │   ├── PasswordReset.jsx
│   │   └── ProfileSettings.jsx
│   ├── learning/
│   │   ├── LearningDashboard.jsx
│   │   ├── ContentViewer.jsx
│   │   ├── ModuleList.jsx
│   │   ├── ProgressBar.jsx
│   │   └── SearchInterface.jsx
│   ├── code/
│   │   ├── CodeEditor.jsx
│   │   ├── ExecutionPanel.jsx
│   │   ├── FileTree.jsx
│   │   ├── LanguageSelector.jsx
│   │   └── OutputConsole.jsx
│   ├── ai/
│   │   ├── ChatInterface.jsx
│   │   ├── MessageList.jsx
│   │   ├── InputArea.jsx
│   │   └── FeedbackRating.jsx
│   ├── project/
│   │   ├── ProjectDashboard.jsx
│   │   ├── ProjectEditor.jsx
│   │   ├── FileManager.jsx
│   │   └── VersionHistory.jsx
│   ├── profile/
│   │   ├── PublicProfile.jsx
│   │   ├── ProjectShowcase.jsx
│   │   ├── SkillsDisplay.jsx
│   │   └── AnalyticsView.jsx
│   └── common/
│       ├── Header.jsx
│       ├── Sidebar.jsx
│       ├── Loading.jsx
│       ├── ErrorBoundary.jsx
│       └── Toast.jsx
├── contexts/
│   ├── AuthContext.jsx
│   ├── ThemeContext.jsx
│   └── NotificationContext.jsx
├── hooks/
│   ├── useAuth.js
│   ├── useCodeExecution.js
│   ├── useAI.js
│   └── useLocalStorage.js
├── services/
│   ├── api.js
│   ├── auth.service.js
│   ├── learning.service.js
│   ├── code.service.js
│   ├── ai.service.js
│   ├── project.service.js
│   └── profile.service.js
├── utils/
│   ├── validators.js
│   ├── formatters.js
│   └── constants.js
└── App.jsx
```

#### Key Frontend Components

**1. CodeEditor Component**

```typescript
interface CodeEditorProps {
  initialCode: string;
  language: string;
  onCodeChange: (code: string) => void;
  onExecute: () => void;
  readOnly?: boolean;
}

// Features:
// - Monaco Editor integration
// - Syntax highlighting for multiple languages
// - Auto-save every 30 seconds
// - Keyboard shortcuts (Ctrl+S to save, Ctrl+Enter to execute)
// - Error highlighting from execution results
// - Auto-completion
// - Code formatting on demand
```

**2. AI ChatInterface Component**

```typescript
interface ChatInterfaceProps {
  projectId?: string;
  currentCode?: string;
  learningModule?: string;
}

// Features:
// - Real-time message streaming
// - Context-aware prompting (includes code, module)
// - Conversation history
// - Message rating system
// - Code snippet highlighting in responses
// - Copy code button for AI suggestions
// - Markdown rendering for formatted responses
```

**3. LearningDashboard Component**

```typescript
interface LearningDashboardProps {
  userId: string;
}

// Features:
// - Progress overview with visual charts
// - Current learning path display
// - Quick access to in-progress modules
// - Recommended next steps
// - Recent activity timeline
// - Achievement badges display
// - Learning streak counter
```

### Backend Architecture

#### Technology Stack
- **Runtime**: Node.js 18+ LTS
- **Framework**: Express.js 4.x
- **Database ORM**: Sequelize for PostgreSQL
- **Authentication**: JWT with bcrypt
- **Validation**: Joi for request validation
- **Logging**: Winston with structured logging
- **Testing**: Jest + Supertest

#### Service Layer Design

**1. User Service**

```typescript
interface UserService {
  // Authentication
  registerUser(data: UserRegistrationData): Promise<User>;
  authenticateUser(credentials: LoginCredentials): Promise<AuthToken>;
  verifyEmail(token: string): Promise<boolean>;
  resetPassword(email: string): Promise<void>;
  changePassword(userId: string, oldPassword: string, newPassword: string): Promise<void>;
  
  // Profile Management
  getUserProfile(userId: string): Promise<UserProfile>;
  updateUserProfile(userId: string, updates: ProfileUpdates): Promise<User>;
  uploadProfilePicture(userId: string, file: File): Promise<string>;
  
  // Session Management
  refreshToken(refreshToken: string): Promise<AuthToken>;
  logout(userId: string, sessionId: string): Promise<void>;
  getActiveSessions(userId: string): Promise<Session[]>;
}

// Implementation Details:
// - Password hashing: bcrypt with 12 rounds
// - JWT expiry: Access token 24h, Refresh token 7 days
// - Email verification: Time-limited tokens (24h)
// - Rate limiting: 5 failed login attempts = 15min lockout
```

**2. Learning Service**

```typescript
interface LearningService {
  // Content Management
  getAllContent(filters: ContentFilters): Promise<PaginatedContent>;
  getContentById(contentId: string): Promise<LearningContent>;
  searchContent(query: string, filters: SearchFilters): Promise<SearchResults>;
  getContentByCategory(category: string, difficulty?: string): Promise<LearningContent[]>;
  
  // Progress Tracking
  getProgressForUser(userId: string): Promise<LearningProgress[]>;
  trackProgress(userId: string, contentId: string, progress: number): Promise<void>;
  markContentComplete(userId: string, contentId: string): Promise<void>;
  getCompletionStats(userId: string): Promise<CompletionStats>;
  
  // Recommendations
  getRecommendations(userId: string): Promise<LearningContent[]>;
  getNextInPath(userId: string, currentContentId: string): Promise<LearningContent | null>;
  
  // Bookmarks
  bookmarkContent(userId: string, contentId: string): Promise<void>;
  getBookmarks(userId: string): Promise<LearningContent[]>;
}

// Implementation Details:
// - Full-text search using PostgreSQL tsvector
// - Caching: Popular content cached in Redis (15min TTL)
// - Recommendations: Collaborative filtering based on similar users
// - Progress auto-save: Debounced database writes
```

**3. Code Execution Service**

```typescript
interface CodeExecutionService {
  executeCode(request: ExecutionRequest): Promise<ExecutionResult>;
  validateCodeSafety(code: string, language: string): Promise<ValidationResult>;
  getExecutionHistory(userId: string, limit: number): Promise<ExecutionHistory[]>;
  getSupportedLanguages(): Promise<Language[]>;
}

interface ExecutionRequest {
  code: string;
  language: string;
  userId: string;
  projectId?: string;
  stdin?: string;
}

interface ExecutionResult {
  stdout: string;
  stderr: string;
  executionTime: number; // milliseconds
  memoryUsed: number; // bytes
  exitCode: number;
  error?: string;
}

// Implementation Details:
// - Docker-based sandboxing
// - Resource limits: 5s timeout, 512MB memory
// - Queue management: Redis-based job queue
// - Rate limiting: 10 executions/min per user
// - Security: No network access, limited file system
// - Language support: JavaScript (Node.js), Python 3, Java, C++
```

**4. AI Assistant Service**

```typescript
interface AIAssistantService {
  processQuery(request: AIRequest): Promise<AIResponse>;
  getConversationHistory(userId: string, conversationId?: string): Promise<Conversation>;
  rateResponse(responseId: string, rating: number, feedback?: string): Promise<void>;
  generatePersonalizedSuggestions(userId: string): Promise<Suggestion[]>;
}

interface AIRequest {
  query: string;
  userId: string;
  conversationId?: string;
  context: {
    currentCode?: string;
    currentModule?: string;
    errorMessage?: string;
    userSkillLevel?: string;
  };
}

interface AIResponse {
  response: string;
  responseId: string;
  conversationId: string;
  timestamp: Date;
  tokensUsed: number;
}

// Implementation Details:
// - LLM Provider: Anthropic Claude or OpenAI GPT-4
// - Context management: Last 10 messages + current state
// - Prompt engineering: Educational, Socratic method
// - Caching: Common questions cached in Redis
// - Rate limiting: 50 queries/day free, unlimited pro
// - Cost optimization: Cache hits reduce API calls by 40%
```

**5. Project Service**

```typescript
interface ProjectService {
  // Project CRUD
  createProject(userId: string, data: ProjectCreationData): Promise<Project>;
  getProject(projectId: string): Promise<Project>;
  getUserProjects(userId: string): Promise<Project[]>;
  updateProject(projectId: string, updates: ProjectUpdates): Promise<Project>;
  deleteProject(projectId: string): Promise<void>;
  
  // File Management
  getProjectFiles(projectId: string): Promise<FileNode[]>;
  createFile(projectId: string, path: string, content: string): Promise<ProjectFile>;
  updateFile(fileId: string, content: string): Promise<ProjectFile>;
  deleteFile(fileId: string): Promise<void>;
  renameFile(fileId: string, newName: string): Promise<ProjectFile>;
  
  // Version Control
  getFileVersions(fileId: string): Promise<FileVersion[]>;
  restoreFileVersion(fileId: string, versionId: string): Promise<ProjectFile>;
  
  // Sharing
  shareProject(projectId: string, settings: ShareSettings): Promise<ShareLink>;
  getSharedProject(shareToken: string): Promise<Project>;
}

// Implementation Details:
// - File storage: AWS S3 for large files, PostgreSQL for small
// - Version control: Snapshot on each save, retain 10 versions
// - Folder structure: Stored as path strings, rendered as tree
// - Soft deletes: 30-day recovery window
// - Templates: Predefined project structures for common stacks
```

### Database Schema Design

#### Entity Relationship Diagram

```
┌─────────────────────┐
│       USERS         │
│─────────────────────│
│ id (PK)             │
│ email (UNIQUE)      │
│ password_hash       │
│ first_name          │
│ last_name           │
│ profile_picture_url │
│ bio                 │
│ github_username     │
│ linkedin_url        │
│ is_email_verified   │
│ skill_level         │
│ created_at          │
│ updated_at          │
└─────────────────────┘
         │
         │ 1:N
         │
    ┌────┴────────────────────────────────────────┐
    │                                             │
    ▼                                             ▼
┌────────────────────────┐           ┌─────────────────────┐
│ LEARNING_PROGRESS      │           │      PROJECTS       │
│────────────────────────│           │─────────────────────│
│ id (PK)                │           │ id (PK)             │
│ user_id (FK)           │           │ user_id (FK)        │
│ content_id (FK)        │           │ title               │
│ progress_percentage    │           │ description         │
│ is_completed           │           │ technology_stack[]  │
│ time_spent             │           │ is_public           │
│ last_accessed          │           │ is_featured         │
│ completed_at           │           │ repository_url      │
│ UNIQUE(user, content)  │           │ live_demo_url       │
└────────────────────────┘           │ project_status      │
         │                           │ created_at          │
         │                           │ updated_at          │
         │ N:1                       └─────────────────────┘
         │                                     │
         ▼                                     │ 1:N
┌────────────────────────┐                     │
│   LEARNING_CONTENT     │                     ▼
│────────────────────────│           ┌─────────────────────┐
│ id (PK)                │           │   PROJECT_FILES     │
│ title                  │           │─────────────────────│
│ description            │           │ id (PK)             │
│ content_type           │           │ project_id (FK)     │
│ difficulty_level       │           │ file_path           │
│ category               │           │ file_name           │
│ tags[]                 │           │ file_content        │
│ content_body (JSONB)   │           │ file_type           │
│ estimated_duration     │           │ file_size           │
│ prerequisites[]        │           │ created_at          │
│ created_at             │           │ updated_at          │
│ updated_at             │           │ UNIQUE(project,path)│
└────────────────────────┘           └─────────────────────┘

         (USERS)
            │ 1:N
            ▼
┌────────────────────────┐
│  AI_CONVERSATIONS      │
│────────────────────────│
│ id (PK)                │
│ user_id (FK)           │
│ conversation_title     │
│ messages (JSONB)       │
│ context_data (JSONB)   │
│ created_at             │
│ updated_at             │
└────────────────────────┘

         (USERS)
            │ 1:N
            ▼
┌────────────────────────┐
│   CODE_EXECUTIONS      │
│────────────────────────│
│ id (PK)                │
│ user_id (FK)           │
│ project_id (FK, NULL)  │
│ code_content           │
│ programming_language   │
│ execution_output       │
│ execution_error        │
│ execution_time_ms      │
│ memory_used_bytes      │
│ executed_at            │
└────────────────────────┘

         (USERS)
            │ 1:N
            ▼
┌ ──────────────────────── ┐
│   USER_ACHIEVEMENTS      │
│ ──────────────────────── │
│ id (PK)                  │
│ user_id (FK)             │
│ achievement_type         │
│ achievement_data (JSONB) │
│ earned_at                │
└ ──────────────────────── ┘

         (USERS)
            │ 1:N
            ▼
┌────────────────────────┐
│   FEEDBACK_SUBMISSIONS │
│────────────────────────│
│ id (PK)                │
│ user_id (FK, NULL)     │
│ feedback_type          │
│ rating                 │
│ feedback_text          │
│ related_entity_id      │
│ related_entity_type    │
│ status                 │
│ created_at             │
└────────────────────────┘
```

#### Detailed Table Schemas

**Users Table**

```sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    first_name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100) NOT NULL,
    profile_picture_url TEXT,
    bio TEXT,
    github_username VARCHAR(100),
    linkedin_url TEXT,
    is_email_verified BOOLEAN DEFAULT FALSE,
    skill_level VARCHAR(20) DEFAULT 'beginner',
    learning_preferences JSONB DEFAULT '{}',
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_created_at ON users(created_at);
```

**Learning Content Table**

```sql
CREATE TABLE learning_content (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    title VARCHAR(255) NOT NULL,
    description TEXT,
    content_type VARCHAR(50) NOT NULL,
    difficulty_level VARCHAR(20) NOT NULL,
    category VARCHAR(100) NOT NULL,
    tags TEXT[] NOT NULL DEFAULT '{}',
    content_body JSONB NOT NULL,
    estimated_duration INTEGER,
    prerequisites TEXT[] DEFAULT '{}',
    author_id UUID,
    is_published BOOLEAN DEFAULT TRUE,
    view_count INTEGER DEFAULT 0,
    average_rating DECIMAL(3,2) DEFAULT 0.00,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_learning_content_category ON learning_content(category);
CREATE INDEX idx_learning_content_difficulty ON learning_content(difficulty_level);
CREATE INDEX idx_learning_content_published ON learning_content(is_published) WHERE is_published = TRUE;
CREATE INDEX idx_learning_content_tags ON learning_content USING GIN(tags);
CREATE INDEX idx_learning_content_search ON learning_content 
    USING GIN(to_tsvector('english', title || ' ' || description || ' ' || array_to_string(tags, ' ')));
```

**Projects Table**

```sql
CREATE TABLE projects (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    technology_stack TEXT[] NOT NULL DEFAULT '{}',
    is_public BOOLEAN DEFAULT FALSE,
    is_featured BOOLEAN DEFAULT FALSE,
    repository_url TEXT,
    live_demo_url TEXT,
    project_status VARCHAR(20) DEFAULT 'active',
    template_id UUID,
    total_files INTEGER DEFAULT 0,
    total_lines_of_code INTEGER DEFAULT 0,
    last_activity_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_projects_user_id ON projects(user_id);
CREATE INDEX idx_projects_public ON projects(is_public) WHERE is_public = TRUE;
CREATE INDEX idx_projects_featured ON projects(is_featured) WHERE is_featured = TRUE;
CREATE INDEX idx_projects_status ON projects(project_status);
CREATE INDEX idx_projects_last_activity ON projects(last_activity_at);
CREATE INDEX idx_projects_search ON projects 
    USING GIN(to_tsvector('english', title || ' ' || COALESCE(description, '') || ' ' || array_to_string(technology_stack, ' ')))
    WHERE is_public = TRUE;
```

**Project Files Table**

```sql
CREATE TABLE project_files (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    project_id UUID NOT NULL REFERENCES projects(id) ON DELETE CASCADE,
    file_path VARCHAR(500) NOT NULL,
    file_name VARCHAR(255) NOT NULL,
    file_content TEXT,
    file_type VARCHAR(50) NOT NULL,
    file_size INTEGER,
    parent_folder VARCHAR(500),
    is_deleted BOOLEAN DEFAULT FALSE,
    deleted_at TIMESTAMP WITH TIME ZONE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(project_id, file_path)
);

CREATE INDEX idx_project_files_project_id ON project_files(project_id);
CREATE INDEX idx_project_files_path ON project_files(file_path);
CREATE INDEX idx_project_files_active ON project_files(project_id, is_deleted) WHERE is_deleted = FALSE;
```

**File Versions Table**

```sql
CREATE TABLE file_versions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    file_id UUID NOT NULL REFERENCES project_files(id) ON DELETE CASCADE,
    version_number INTEGER NOT NULL,
    file_content TEXT NOT NULL,
    change_description TEXT,
    file_size INTEGER,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(file_id, version_number)
);

CREATE INDEX idx_file_versions_file_id ON file_versions(file_id, version_number DESC);
```

**Learning Progress Table**

```sql
CREATE TABLE learning_progress (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    content_id UUID NOT NULL REFERENCES learning_content(id) ON DELETE CASCADE,
    progress_percentage INTEGER DEFAULT 0 CHECK (progress_percentage >= 0 AND progress_percentage <= 100),
    is_completed BOOLEAN DEFAULT FALSE,
    time_spent INTEGER DEFAULT 0,
    last_accessed TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    completed_at TIMESTAMP WITH TIME ZONE,
    UNIQUE(user_id, content_id)
);

CREATE INDEX idx_learning_progress_user_id ON learning_progress(user_id);
CREATE INDEX idx_learning_progress_content_id ON learning_progress(content_id);
CREATE INDEX idx_learning_progress_completed ON learning_progress(is_completed) WHERE is_completed = TRUE;
```

**AI Conversations Table**

```sql
CREATE TABLE ai_conversations (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    conversation_title VARCHAR(255),
    messages JSONB NOT NULL,
    context_data JSONB,
    total_messages INTEGER DEFAULT 0,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_ai_conversations_user_id ON ai_conversations(user_id);
CREATE INDEX idx_ai_conversations_updated ON ai_conversations(updated_at DESC);
```

**Code Executions Table**

```sql
CREATE TABLE code_executions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    project_id UUID REFERENCES projects(id) ON DELETE SET NULL,
    code_content TEXT NOT NULL,
    programming_language VARCHAR(50) NOT NULL,
    execution_output TEXT,
    execution_error TEXT,
    execution_time_ms INTEGER,
    memory_used_bytes INTEGER,
    exit_code INTEGER,
    executed_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_code_executions_user_id ON code_executions(user_id);
CREATE INDEX idx_code_executions_executed_at ON code_executions(executed_at DESC);
CREATE INDEX idx_code_executions_language ON code_executions(programming_language);
```

**User Achievements Table**

```sql
CREATE TABLE user_achievements (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    achievement_type VARCHAR(100) NOT NULL,
    achievement_data JSONB,
    earned_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_user_achievements_user_id ON user_achievements(user_id);
CREATE INDEX idx_user_achievements_type ON user_achievements(achievement_type);
CREATE INDEX idx_user_achievements_earned ON user_achievements(earned_at DESC);
```

**Feedback Submissions Table**

```sql
CREATE TABLE feedback_submissions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) ON DELETE SET NULL,
    feedback_type VARCHAR(50) NOT NULL,
    rating INTEGER CHECK (rating >= 1 AND rating <= 5),
    feedback_text TEXT,
    related_entity_id UUID,
    related_entity_type VARCHAR(50),
    status VARCHAR(20) DEFAULT 'pending',
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_feedback_user_id ON feedback_submissions(user_id);
CREATE INDEX idx_feedback_type ON feedback_submissions(feedback_type);
CREATE INDEX idx_feedback_status ON feedback_submissions(status);
CREATE INDEX idx_feedback_created ON feedback_submissions(created_at DESC);
```

## Security Architecture

### Authentication & Authorization

**JWT Token Structure**

```typescript
interface JWTPayload {
  userId: string;
  email: string;
  role: 'user' | 'admin';
  iat: number;  // issued at
  exp: number;  // expiration
}
```

**Security Measures**:
- bcrypt password hashing (12 rounds)
- JWT access tokens (24h expiry)
- Refresh tokens (7-day expiry, rotated on use)
- HTTP-only cookies for token storage
- CSRF protection via SameSite cookies

### Code Execution Sandbox

**Docker Container Configuration**:

```yaml
security_opt:
  - no-new-privileges:true
cap_drop:
  - ALL
read_only: true
network_mode: none
tmpfs:
  - /tmp:size=50M,mode=1777
resources:
  limits:
    cpus: '0.5'
    memory: 512M
  reservations:
    cpus: '0.1'
    memory: 128M
```

**Execution Flow**:
1. Code validation (syntax check, forbidden patterns)
2. Container creation with strict resource limits
3. Code execution with 5-second timeout
4. Output capture (stdout/stderr)
5. Container destruction
6. Result sanitization and return

### Input Validation

**Validation Rules**:
- All user inputs validated server-side with Joi
- SQL injection prevention via parameterized queries
- XSS prevention via input sanitization and CSP headers
- File upload validation (type, size, content)
- Rate limiting on all endpoints

### Data Protection

**Encryption**:
- TLS 1.3 for all data in transit
- AES-256 encryption for sensitive data at rest
- Environment variables for secrets
- No plaintext passwords ever stored

**Privacy Compliance**:
- GDPR-compliant data handling
- User data export functionality
- Right to deletion (30-day soft delete)
- Minimal data collection principle
- Transparent privacy policy

## Performance Optimization

### Caching Strategy

**Redis Caching Layers**:

```typescript
// Layer 1: Session Store (15 min TTL)
cache.set(`session:${userId}`, sessionData, 900);

// Layer 2: AI Response Cache (1 hour TTL)
cache.set(`ai:response:${hash(query)}`, response, 3600);

// Layer 3: Popular Content (15 min TTL)
cache.set(`content:popular`, contentList, 900);

// Layer 4: User Progress (5 min TTL)
cache.set(`progress:${userId}`, progressData, 300);

// Layer 5: Rate Limiting (1 hour sliding window)
cache.incr(`ratelimit:${userId}:${endpoint}`, 3600);
```

### Database Optimization

**Query Performance**:
- Indexed all foreign keys
- Composite indexes for common query patterns
- Full-text search indexes (GIN)
- Connection pooling (10-50 connections)
- Query result caching for expensive operations

**N+1 Prevention**:
```typescript
// Bad: N+1 queries
const users = await User.findAll();
for (const user of users) {
  user.projects = await Project.findAll({ where: { userId: user.id } });
}

// Good: Single query with eager loading
const users = await User.findAll({
  include: [{ model: Project }]
});
```

### Frontend Optimization

**Code Splitting**:
```javascript
// Route-based code splitting
const Learning = lazy(() => import('./components/learning/LearningDashboard'));
const CodeEditor = lazy(() => import('./components/code/CodeEditor'));
const AIChat = lazy(() => import('./components/ai/ChatInterface'));
```

**Asset Optimization**:
- Image compression and lazy loading
- CDN delivery for static assets
- Gzip compression for text assets
- Service worker for offline capability
- Bundle size optimization (<250KB initial)

## Scalability Design

### Horizontal Scaling

**Load Balancing Strategy**:
```
                     ┌─────────────┐
                     │Load Balancer│
                     │  (AWS ELB)  │
                     └─────┬───────┘
                           │
          ┌────────────────┼────────────────┐
          │                │                │
    ┌─────▼─────┐    ┌─────▼─────┐    ┌─────▼─────┐
    │ Backend 1 │    │ Backend 2 │    │ Backend N │
    │(Stateless)│    │(Stateless)│    │(Stateless)│
    └───────────┘    └───────────┘    └───────────┘
```

**Service Scaling**:
- Stateless backend services (no session storage)
- Shared Redis for session management
- Database read replicas for read-heavy operations
- Queue-based code execution for burst handling
- Auto-scaling based on CPU/memory metrics

### Database Scaling

**Strategies**:
- Read replicas for scaling read operations
- Connection pooling to manage database load
- Partitioning large tables (>10M rows)
- Archive old data (executions >30 days)
- Materialized views for complex queries

## Error Handling & Monitoring

### Error Classification

```typescript
enum ErrorType {
  VALIDATION_ERROR = 400,
  AUTHENTICATION_ERROR = 401,
  AUTHORIZATION_ERROR = 403,
  NOT_FOUND_ERROR = 404,
  RATE_LIMIT_ERROR = 429,
  INTERNAL_SERVER_ERROR = 500,
  SERVICE_UNAVAILABLE = 503
}
```

### Logging Strategy

**Structured Logging**:
```typescript
logger.info('Code execution completed', {
  userId,
  language,
  executionTime: result.executionTime,
  exitCode: result.exitCode,
  timestamp: new Date().toISOString(),
  requestId: req.id
});
```

**Log Levels**:
- ERROR: Failures requiring immediate attention
- WARN: Potential issues or degraded performance
- INFO: Normal operational events
- DEBUG: Detailed diagnostic information (dev only)

### Monitoring & Alerting

**Metrics Tracked**:
- Request latency (p50, p95, p99)
- Error rates by endpoint
- Database query performance
- Cache hit/miss rates
- Code execution queue length
- Active user sessions
- AI API cost and latency

**Alerts**:
- Error rate >1% (warning), >5% (critical)
- Response time p95 >3s
- Database connections >80% capacity
- Redis memory >75% capacity
- Code execution queue >100 jobs

## Testing Strategy

### Testing Pyramid

```
          ┌──────────┐
          │   E2E    │  (5%)  - Critical user flows
          └──────────┘
        ┌────────────────┐
        │  Integration   │  (25%) - API endpoints, services
        └────────────────┘
    ┌──────────────────────┐
    │    Unit Tests        │  (70%) - Components, functions
    └──────────────────────┘
```

### Test Coverage Requirements

- **Unit Tests**: 80% code coverage minimum
- **Integration Tests**: All API endpoints
- **E2E Tests**: Critical user journeys
- **Security Tests**: Automated vulnerability scanning
- **Performance Tests**: Load testing before releases

### Property-Based Testing

Implementing property-based tests for critical correctness properties:

```typescript
// Example: User authentication round trip
test('Property 1: User registration and authentication', async () => {
  await fc.assert(
    fc.asyncProperty(
      fc.emailAddress(),
      fc.string({ minLength: 8, maxLength: 50 }),
      async (email, password) => {
        // Register user
        const user = await registerUser({ email, password });
        
        // Attempt authentication
        const token = await authenticateUser({ email, password });
        
        // Should be able to access dashboard
        const dashboard = await getDashboard(token);
        
        return dashboard !== null && dashboard.userId === user.id;
      }
    ),
    { numRuns: 100 }
  );
});
```

## Deployment Architecture

### Environment Setup

**Development**:
- Local PostgreSQL and Redis
- Docker Compose for services
- Hot reload enabled
- Debug logging

**Staging**:
- AWS RDS (PostgreSQL)
- AWS ElastiCache (Redis)
- Replica of production
- Performance profiling enabled

**Production**:
- AWS RDS Multi-AZ
- AWS ElastiCache cluster
- Auto-scaling EC2/ECS
- CloudWatch monitoring
- Automated backups

### CI/CD Pipeline

```
┌─────────────┐
│ Git Push    │
└──────┬──────┘
       │
       ▼
┌─────────────┐
│ Run Tests   │ ← Unit + Integration
└──────┬──────┘
       │
       ▼
┌─────────────┐
│ Build       │ ← Docker images
└──────┬──────┘
       │
       ▼
┌─────────────┐
│ Deploy      │ ← Staging
│ Staging     │
└──────┬──────┘
       │
       ▼
┌─────────────┐
│ E2E Tests   │ ← Automated
└──────┬──────┘
       │
       ▼
┌─────────────┐
│ Manual      │ ← QA Approval
│ Approval    │
└──────┬──────┘
       │
       ▼
┌─────────────┐
│ Deploy      │ ← Production
│ Production  │ (Blue-Green)
└─────────────┘
```

## Future Enhancements

### Phase 2 Features (3-6 months)
- Real-time collaboration on projects
- Live coding sessions with instructors
- Peer code review system
- Advanced analytics dashboard
- Mobile applications (iOS/Android)

### Phase 3 Features (6-12 months)
- Marketplace for user-created content
- Team workspaces for organizations
- Certification system
- Integration APIs for external tools
- Multi-language content support

### Technical Debt Management
- Quarterly code refactoring sprints
- Performance optimization reviews
- Security audit schedule
- Dependency update policy
- Documentation maintenance

## Conclusion

This design document provides a comprehensive blueprint for building a production-ready AI-powered unified learning platform. The architecture emphasizes security, scalability, and user experience while maintaining code quality and operational excellence. All technical decisions are justified by the requirements and aligned with industry best practices.

The system is designed to scale from initial launch (1K users) to enterprise-level deployment (100K+ users) while maintaining sub-2-second response times and 99.5% uptime. The modular architecture allows for iterative development and easy feature additions without major refactoring.

Success depends on rigorous testing, continuous monitoring, and iterative improvements based on user feedback and system metrics.
