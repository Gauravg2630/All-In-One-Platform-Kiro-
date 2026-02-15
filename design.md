\# Design Document: AI-Powered Unified Learning and Developer Productivity Platform

\## Executive Summary

This design document details the technical architecture, component specifications, data models, and implementation strategies for an AI-powered unified learning platform. The platform consolidates learning, coding, AI assistance, project management, and portfolio showcasing into a single integrated experience, addressing the critical fragmentation problem in technical education.

The system employs a modern full-stack architecture with React.js frontend, Node.js/Express.js backend, PostgreSQL database, Redis caching, and integrated LLM services. Security is paramount with sandboxed code execution, JWT authentication, and comprehensive input validation. The design emphasizes scalability, maintainability, and exceptional user experience.

\## Overview

\### Problem Context

Students and developers face severe productivity losses from platform fragmentation:

- Average 5-7 platforms used daily for learning workflow
- 23 minutes lost per context switch (research: University of California, Irvine)
- Only 15% course completion rate due to friction
- 12+ months to build job-ready portfolio vs. potential 3 months

\### Solution Architecture

Our platform provides:

1. \*\*Single Integrated Environment\*\*: All tools in one browser tab
1. \*\*Context-Aware AI\*\*: Assistance that knows user's learning state and code
1. \*\*Zero-Setup Coding\*\*: Browser-based execution with instant feedback
1. \*\*Automatic Portfolio\*\*: Projects become portfolio items automatically
1. \*\*Structured Learning\*\*: Distraction-free, curated paths with clear progression

\### Design Principles

1. \*\*Developer Experience First\*\*: Minimize cognitive load, maximize flow state
1. \*\*Performance\*\*: Sub-2-second page loads, sub-5-second AI responses
1. \*\*Security by Design\*\*: Defense in depth with isolated execution environments
1. \*\*Scalable Architecture\*\*: Support 100K+ concurrent users without degradation
1. \*\*Data Integrity\*\*: ACID compliance, automatic backups, zero data loss
1. \*\*Privacy Respect\*\*: GDPR compliant, user data ownership, transparent policies

\## High-Level Architecture

\### System Architecture Diagram

\```

┌────────────────────────────────────────────────────────────────┐

│                     CLIENT LAYER (Browser)                      │

│  ┌──────────────────────────────────────────────────────────┐  │

│  │              React.js Single Page Application             │  │

│  │                                                            │  │

│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────────┐  │  │

│  │  │  Learning   │  │    Code     │  │  AI Assistant   │  │  │

│  │  │  Dashboard  │  │   Editor    │  │   Interface     │  │  │

│  │  │             │  │  (Monaco)   │  │                 │  │  │

│  │  └─────────────┘  └─────────────┘  └─────────────────┘  │  │

│  │                                                            │  │

│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────────┐  │  │

│  │  │   Project   │  │   Profile   │  │    Progress     │  │  │

│  │  │  Manager    │  │  Showcase   │  │    Tracker      │  │  │

│  │  │             │  │             │  │                 │  │  │

│  │  └─────────────┘  └─────────────┘  └─────────────────┘  │  │

│  └──────────────────────────────────────────────────────────┘  │

└────────────────────────────────────────────────────────────────┘

│  ▲

│  │  HTTPS/TLS 1.3

▼  │

┌────────────────────────────────────────────────────────────────┐

│                    API GATEWAY LAYER                            │

│  ┌──────────────────────────────────────────────────────────┐  │

│  │              Express.js Router & Middleware               │  │

│  │                                                            │  │

│  │  [CORS] → [Auth] → [Rate Limit] → [Validation] → [Routes]│  │

│  └──────────────────────────────────────────────────────────┘  │

└────────────────────────────────────────────────────────────────┘

│  ▲

▼  │

┌────────────────────────────────────────────────────────────────┐

│                     SERVICE LAYER                               │

│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────┐     │

│  │     User     │  │   Learning   │  │       AI         │     │

│  │   Service    │  │   Service    │  │  Assistant       │     │

│  │              │  │              │  │    Service       │     │

│  └──────────────┘  └──────────────┘  └──────────────────┘     │

│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────┐     │

│  │     Code     │  │   Project    │  │    Profile       │     │

│  │  Execution   │  │   Service    │  │    Service       │     │

│  │   Service    │  │              │  │                  │     │

│  └──────────────┘  └──────────────┘  └──────────────────┘     │

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

│                       DATA LAYER                                │

│  ┌──────────────────────────────────────────────────────────┐  │

│  │                                                            │  │

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

│  │                                                            │  │

│  └──────────────────────────────────────────────────────────┘  │

└────────────────────────────────────────────────────────────────┘

\```

\### Component Interaction Flow

\*\*Example: User Executes Code with AI Help\*\*

\```

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

\```

\## Detailed Component Design

\### Frontend Architecture

\#### Technology Stack

- \*\*Framework\*\*: React 18+ with hooks
- \*\*State Management\*\*: React Context API + useReducer for complex state
- \*\*Routing\*\*: React Router v6
- \*\*Styling\*\*: Tailwind CSS for utility-first styling
- \*\*Code Editor\*\*: Monaco Editor (VS Code's editor)
- \*\*HTTP Client\*\*: Axios with interceptors
- \*\*Build Tool\*\*: Vite for fast development and optimized builds

\#### Component Structure

\```

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

\```

\#### Key Frontend Components

\*\*1. CodeEditor Component\*\*

\```typescript

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

\```

\*\*2. AI ChatInterface Component\*\*

\```typescript

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

\```

\*\*3. LearningDashboard Component\*\*

\```typescript

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

\```

\### Backend Architecture

\#### Technology Stack

- \*\*Runtime\*\*: Node.js 18+ LTS
- \*\*Framework\*\*: Express.js 4.x
- \*\*Database ORM\*\*: Sequelize for PostgreSQL
- \*\*Authentication\*\*: JWT with bcrypt
- \*\*Validation\*\*: Joi for request validation
- \*\*Logging\*\*: Winston with structured logging
- \*\*Testing\*\*: Jest + Supertest

\#### Service Layer Design

\*\*1. User Service\*\*

\```typescript

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

\```

\*\*2. Learning Service\*\*

\```typescript

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

\```

\*\*3. Code Execution Service\*\*

\```typescript

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

\```

\*\*4. AI Assistant Service\*\*

\```typescript

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

\```

\*\*5. Project Service\*\*

\```typescript

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

\```

\### Database Schema Design

\#### Entity Relationship Diagram

\```

┌─────────────────────┐

│       USERS         │

│─────────────────────│

│ id (PK)             │

│ email (UNIQUE)      │

│ password\_hash       │

│ first\_name          │

│ last\_name           │

│ profile\_picture\_url │

│ bio                 │

│ github\_username     │

│ linkedin\_url        │

│ is\_email\_verified   │

│ skill\_level         │

│ created\_at          │

│ updated\_at          │

└─────────────────────┘

│

│ 1:N

│

┌────┴────────────────────────────────────────┐

│                                             │

▼                                             ▼

┌────────────────────────┐           ┌─────────────────────┐

│ LEARNING\_PROGRESS      │           │      PROJECTS       │

│────────────────────────│           │─────────────────────│

│ id (PK)                │           │ id (PK)             │

│ user\_id (FK)           │           │ user\_id (FK)        │

│ content\_id (FK)        │           │ title               │

│ progress\_percentage    │           │ description         │

│ is\_completed           │           │ technology\_stack[]  │

│ time\_spent             │           │ is\_public           │

│ last\_accessed          │           │ is\_featured         │

│ completed\_at           │           │ repository\_url      │

│ UNIQUE(user, content)  │           │ live\_demo\_url       │

└────────────────────────┘           │ project\_status      │

│                            │ created\_at          │

│                            │ updated\_at          │

│ N:1                        └─────────────────────┘

│                                     │

▼                                     │ 1:N

┌────────────────────────┐                    │

│   LEARNING\_CONTENT     │                    ▼

│────────────────────────│           ┌─────────────────────┐

│ id (PK)                │           │   PROJECT\_FILES     │

│ title                  │           │─────────────────────│

│ description            │           │ id (PK)             │

│ content\_type           │           │ project\_id (FK)     │

│ difficulty\_level       │           │ file\_path           │

│ category               │           │ file\_name           │

│ tags[]                 │           │ file\_content        │

│ content\_body (JSONB)   │           │ file\_type           │

│ estimated\_duration     │           │ file\_size           │

│ prerequisites[]        │           │ created\_at          │

│ created\_at             │           │ updated\_at          │

│ updated\_at             │           │ UNIQUE(project,path)│

└────────────────────────┘           └─────────────────────┘

(USERS)

│ 1:N

▼

┌────────────────────────┐

│  AI\_CONVERSATIONS      │

│────────────────────────│

│ id (PK)                │

│ user\_id (FK)           │

│ conversation\_title     │

│ messages (JSONB)       │

│ context\_data (JSONB)   │

│ created\_at             │

│ updated\_at             │

└────────────────────────┘

(USERS)

│ 1:N

▼

┌────────────────────────┐

│   CODE\_EXECUTIONS      │

│────────────────────────│

│ id (PK)                │

│ user\_id (FK)           │

│ project\_id (FK, NULL)  │

│ code\_content           │

│ programming\_language   │

│ execution\_output       │

│ execution\_error        │

│ execution\_time\_ms      │

│ memory\_used\_bytes      │

│ executed\_at            │

└────────────────────────┘

(USERS)

│ 1:N

▼

┌────────────────────────┐

│   USER\_ACHIEVEMENTS    │

│────────────────────────│

│ id (PK)                │

│ user\_id (FK)           │

│ achievement\_type       │

│ achievement\_data (JSONB)│

│ earned\_at              │

└────────────────────────┘

(USERS)

│ 1:N

▼

┌────────────────────────┐

│   FEEDBACK\_SUBMISSIONS │

│────────────────────────│

│ id (PK)                │

│ user\_id (FK, NULL)     │

│ feedback\_type          │

│ rating                 │

│ feedback\_text          │

│ related\_entity\_id      │

│ related\_entity\_type    │

│ status                 │

│ created\_at             │

└────────────────────────┘

\```

\#### Detailed Table Schemas

\*\*Users Table\*\*

\```sql

CREATE TABLE users (

id UUID PRIMARY KEY DEFAULT gen\_random\_uuid(),

email VARCHAR(255) UNIQUE NOT NULL,

password\_hash VARCHAR(255) NOT NULL,

first\_name VARCHAR(100) NOT NULL,

last\_name VARCHAR(100) NOT NULL,

profile\_picture\_url TEXT,

bio TEXT,

github\_username VARCHAR(100),

linkedin\_url TEXT,

is\_email\_verified BOOLEAN DEFAULT FALSE,

skill\_level VARCHAR(20) DEFAULT 'beginner', -- 'beginner', 'intermediate', 'advanced'

learning\_preferences JSONB DEFAULT '{}', -- User preferences for learning style

created\_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT\_TIMESTAMP,

updated\_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT\_TIMESTAMP

);

-- Indexes

CREATE INDEX idx\_users\_email ON users(email);

CREATE INDEX idx\_users\_created\_at ON users(created\_at);

\```

\*\*Learning Content Table\*\*

\```sql

CREATE TABLE learning\_content (

id UUID PRIMARY KEY DEFAULT gen\_random\_uuid(),

title VARCHAR(255) NOT NULL,

description TEXT,

content\_type VARCHAR(50) NOT NULL, -- 'tutorial', 'exercise', 'project', 'article'

difficulty\_level VARCHAR(20) NOT NULL, -- 'beginner', 'intermediate', 'advanced'

category VARCHAR(100) NOT NULL, -- 'web\_development', 'data\_structures', etc.

tags TEXT[] NOT NULL DEFAULT '{}', -- Searchable tags

content\_body JSONB NOT NULL, -- Structured content with sections, code examples, etc.

estimated\_duration INTEGER, -- Duration in minutes

prerequisites TEXT[] DEFAULT '{}', -- Array of content IDs

author\_id UUID, -- Internal content creator

is\_published BOOLEAN DEFAULT TRUE,

view\_count INTEGER DEFAULT 0,

average\_rating DECIMAL(3,2) DEFAULT 0.00,

created\_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT\_TIMESTAMP,

updated\_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT\_TIMESTAMP

);

-- Indexes

CREATE INDEX idx\_learning\_content\_category ON learning\_content(category);

CREATE INDEX idx\_learning\_content\_difficulty ON learning\_content(difficulty\_level);

CREATE INDEX idx\_learning\_content\_published ON learning\_content(is\_published) WHERE is\_published = TRUE;

CREATE INDEX idx\_learning\_content\_tags ON learning\_content USING GIN(tags);

-- Full-text search index

CREATE INDEX idx\_learning\_content\_search ON learning\_content

USING GIN(to\_tsvector('english', title || ' ' || description || ' ' || array\_to\_string(tags, ' ')));

\```

\*\*Projects Table\*\*

\```sql

CREATE TABLE projects (

id UUID PRIMARY KEY DEFAULT gen\_random\_uuid(),

user\_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,

title VARCHAR(255) NOT NULL,

description TEXT,

technology\_stack TEXT[] NOT NULL DEFAULT '{}', -- ['React', 'Node.js', 'PostgreSQL']

is\_public BOOLEAN DEFAULT FALSE,

is\_featured BOOLEAN DEFAULT FALSE,

repository\_url TEXT,

live\_demo\_url TEXT,

project\_status VARCHAR(20) DEFAULT 'active', -- 'active', 'completed', 'archived'

template\_id UUID, -- Reference to project template if used

total\_files INTEGER DEFAULT 0,

total\_lines\_of\_code INTEGER DEFAULT 0,

last\_activity\_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT\_TIMESTAMP,

created\_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT\_TIMESTAMP,

updated\_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT\_TIMESTAMP

);

-- Indexes

CREATE INDEX idx\_projects\_user\_id ON projects(user\_id);

CREATE INDEX idx\_projects\_public ON projects(is\_public) WHERE is\_public = TRUE;

CREATE INDEX idx\_projects\_featured ON projects(is\_featured) WHERE is\_featured = TRUE;

CREATE INDEX idx\_projects\_status ON projects(project\_status);

CREATE INDEX idx\_projects\_last\_activity ON projects(last\_activity\_at);

-- Full-text search for public projects

CREATE INDEX idx\_projects\_search ON projects

USING GIN(to\_tsvector('english', title || ' ' || COALESCE(description, '') || ' ' || array\_to\_string(technology\_stack, ' ')))

WHERE is\_public = TRUE;

\```

\*\*Project Files Table\*\*

\```sql

CREATE TABLE project\_files (

id UUID PRIMARY KEY DEFAULT gen\_random\_uuid(),

project\_id UUID NOT NULL REFERENCES projects(id) ON DELETE CASCADE,

file\_path VARCHAR(500) NOT NULL, -- 'src/components/Header.jsx'

file\_name VARCHAR(255) NOT NULL, -- 'Header.jsx'

file\_content TEXT, -- Actual code content

file\_type VARCHAR(50) NOT NULL, -- 'javascript', 'python', 'html', 'css'

file\_size INTEGER, -- Size in bytes

parent\_folder VARCHAR(500), -- 'src/components' for folder structure

is\_deleted BOOLEAN DEFAULT FALSE, -- Soft delete

deleted\_at TIMESTAMP WITH TIME ZONE,

created\_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT\_TIMESTAMP,

updated\_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT\_TIMESTAMP,

UNIQUE(project\_id, file\_path)

);

-- Indexes

CREATE INDEX idx\_project\_files\_project\_id ON project\_files(project\_id);

CREATE INDEX idx\_project\_files\_path ON project\_files(file\_path);

CREATE INDEX idx\_project\_files\_active ON project\_files(project\_id, is\_deleted) WHERE is\_deleted = FALSE;

\```

\*\*File Versions Table (for version control)\*\*

\```sql

CREATE TABLE file\_versions (

id UUID PRIMARY KEY DEFAULT gen\_random\_uuid(),

file\_id UUID NOT NULL REFERENCES project\_files(id) ON DELETE CASCADE,

version\_number INTEGER NOT NULL,

file\_content TEXT NOT NULL,

change\_description TEXT,

file\_size INTEGER,

created\_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT\_TIMESTAMP,

UNIQUE(file\_id, version\_number)

);

-- Index

CREATE INDEX idx\_file\_versions\_file\_id ON file\_versions(file\_id, version\_number DESC);

\```

\*\*Learning Progress Table\*\*

\```sql

CREATE TABLE learning\_progress (

id UUID PRIMARY KEY DEFAULT gen\_random\_uuid(),

user\_id UUID NOT NULL REFERENCES users(id)
