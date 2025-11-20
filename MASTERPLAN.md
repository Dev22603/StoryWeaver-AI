# MASTERPLAN.md - StoryWeaver AI Development Roadmap

## Executive Summary

This document outlines the complete development plan for StoryWeaver AI, from initial setup to production launch. The project is divided into 6 phases spanning approximately 16-20 weeks for a solo developer or small team.

**Total Estimated Timeline**: 16-20 weeks
**MVP Target**: Phase 1-3 (8-10 weeks)
**Full Launch Target**: Phase 1-5 (14-17 weeks)

---

## Phase 0: Foundation & Setup
**Duration**: 3-5 days
**Goal**: Project infrastructure and development environment ready

### 0.1 Project Initialization

#### Tasks
- [ ] Create GitHub repository with proper .gitignore
- [ ] Initialize monorepo with Turborepo (optional) or single Next.js project
- [ ] Set up Next.js 14 project with App Router
- [ ] Configure TypeScript with strict mode
- [ ] Set up ESLint + Prettier with Airbnb config
- [ ] Install and configure Tailwind CSS
- [ ] Set up Husky for pre-commit hooks
- [ ] Create initial folder structure
- [ ] Write initial README.md

#### Commands
```bash
# Create Next.js project
npx create-next-app@latest storyweaver-ai --typescript --tailwind --eslint --app --src-dir=false
cd storyweaver-ai

# Install dev dependencies
npm install -D prettier eslint-config-prettier husky lint-staged
npm install -D @types/node @types/react

# Initialize Husky
npx husky init
```

### 0.2 Database & Prisma Setup

#### Tasks
- [ ] Install Prisma CLI
- [ ] Initialize Prisma
- [ ] Configure PostgreSQL database (local or cloud)
- [ ] Create initial Prisma schema
- [ ] Generate Prisma Client
- [ ] Create initial migration
- [ ] Set up database seeding
- [ ] Create Prisma client singleton
- [ ] Test database connection

#### Commands
```bash
# Install Prisma
npm install -D prisma
npm install @prisma/client

# Initialize Prisma
npx prisma init

# Create migration
npx prisma migrate dev --name init

# Generate client
npx prisma generate

# Seed database
npx prisma db seed
```

#### Initial Prisma Schema
```prisma
// prisma/schema.prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

// Base models (will expand in later phases)
model User {
  id                String   @id @default(uuid())
  email             String   @unique
  emailVerified     DateTime?
  displayName       String?
  avatarUrl         String?
  creditsBalance    Int      @default(100)
  subscriptionTier  String   @default("free")
  createdAt         DateTime @default(now())
  updatedAt         DateTime @updatedAt

  @@map("users")
}
```

### 0.3 External Services Setup

#### Tasks
- [ ] Set up PostgreSQL database (Supabase/Railway/Neon/local)
- [ ] Set up Redis instance (Upstash/Railway/local)
- [ ] Create Anthropic account and get API key
- [ ] Set up AWS S3 or Cloudinary account
- [ ] Set up Vercel account
- [ ] Configure Google OAuth credentials
- [ ] Configure environment variables locally
- [ ] Create .env.example file
- [ ] Test all service connections

#### Environment Variables
```bash
# .env
DATABASE_URL="postgresql://user:password@localhost:5432/storyweaver"
NEXTAUTH_URL="http://localhost:3000"
NEXTAUTH_SECRET="your-secret-here"
GOOGLE_CLIENT_ID="your-google-client-id"
GOOGLE_CLIENT_SECRET="your-google-client-secret"
ANTHROPIC_API_KEY="your-anthropic-key"
AWS_ACCESS_KEY_ID="your-aws-key"
AWS_SECRET_ACCESS_KEY="your-aws-secret"
AWS_S3_BUCKET="storyweaver-media"
REDIS_URL="redis://localhost:6379"
```

### 0.4 Design System Foundation

#### Tasks
- [ ] Install shadcn/ui
- [ ] Configure component aliases
- [ ] Add base components (Button, Input, Card, Dialog)
- [ ] Set up color palette and design tokens
- [ ] Create typography scale
- [ ] Build basic layout components

#### Commands
```bash
npx shadcn@latest init
npx shadcn@latest add button input card dialog
```

### 0.5 Authentication Setup

#### Tasks
- [ ] Install NextAuth.js (Auth.js)
- [ ] Configure NextAuth with Google provider
- [ ] Create auth API route
- [ ] Set up Prisma adapter for NextAuth
- [ ] Create auth utilities
- [ ] Test authentication flow

#### Commands
```bash
npm install next-auth @auth/prisma-adapter
```

### Milestone 0: ✅ Development Environment Ready
- PostgreSQL database running
- Prisma configured and connected
- Next.js app building
- Design system configured
- Authentication setup
- All services connected

---

## Phase 1: Authentication & Core Navigation
**Duration**: 1 week
**Goal**: Users can sign up, log in, and navigate the app

### 1.1 Authentication Implementation

#### Tasks
- [ ] Configure NextAuth.js with Google OAuth
- [ ] Update Prisma schema with NextAuth models
- [ ] Create migration for auth tables
- [ ] Implement auth API route handlers
- [ ] Create login page with Google sign-in button
- [ ] Create signup flow (redirects to Google)
- [ ] Implement sign out functionality
- [ ] Create auth middleware for protected routes
- [ ] Handle auth state in client components
- [ ] Create user profile on first login
- [ ] Test auth flow end-to-end

#### Files to Create
```
app/(auth)/login/page.tsx
app/api/auth/[...nextauth]/route.ts
lib/auth.ts
lib/prisma.ts
middleware.ts
components/auth/login-button.tsx
components/auth/user-menu.tsx
```

#### Prisma Schema Addition
```prisma
model Account {
  id                String  @id @default(uuid())
  userId            String
  type              String
  provider          String
  providerAccountId String
  refresh_token     String?
  access_token      String?
  expires_at        Int?
  token_type        String?
  scope             String?
  id_token          String?
  session_state     String?

  user User @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@unique([provider, providerAccountId])
  @@map("accounts")
}

model Session {
  id           String   @id @default(uuid())
  sessionToken String   @unique
  userId       String
  expires      DateTime

  user User @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@map("sessions")
}
```

### 1.2 Base Layout & Navigation

#### Tasks
- [ ] Create app shell layout
- [ ] Build responsive sidebar navigation
- [ ] Create header with user menu
- [ ] Implement mobile navigation
- [ ] Add loading states
- [ ] Create 404 page
- [ ] Create error boundary
- [ ] Build breadcrumb component
- [ ] Set up page transitions

#### Files to Create
```
app/(dashboard)/layout.tsx
components/layout/sidebar.tsx
components/layout/header.tsx
components/layout/mobile-nav.tsx
components/shared/loading.tsx
app/not-found.tsx
app/error.tsx
```

### 1.3 User Settings Page

#### Tasks
- [ ] Create settings page layout
- [ ] Build profile section (name, avatar)
- [ ] Add account section (email, delete account)
- [ ] Create preferences section
- [ ] Implement profile update API
- [ ] Add avatar upload to S3/Cloudinary
- [ ] Handle form validation with Zod
- [ ] Show success/error toasts
- [ ] Implement avatar deletion

### Milestone 1: ✅ Authentication Complete
- Users can sign up/in with Google
- Protected routes working
- Navigation functional
- Settings page operational

---

## Phase 2: Projects Module
**Duration**: 1.5 weeks
**Goal**: Users can create, manage, and configure book projects

### 2.1 Database: Projects Schema

#### Tasks
- [ ] Add Project model to Prisma schema
- [ ] Create migration for projects table
- [ ] Generate Prisma client
- [ ] Create project service/repository
- [ ] Build project CRUD operations
- [ ] Add authorization checks
- [ ] Test project operations

#### Prisma Schema Addition
```prisma
model Project {
  id              String    @id @default(uuid())
  userId          String
  title           String
  description     String?
  genre           String?
  targetAudience  String?
  toneStyle       String    @default("conversational")
  questionRounds  Int       @default(3)
  status          String    @default("active")
  coverImageUrl   String?
  settings        Json      @default("{}")
  createdAt       DateTime  @default(now())
  updatedAt       DateTime  @updatedAt

  user User @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@index([userId])
  @@map("projects")
}
```

### 2.2 Projects Dashboard

#### Tasks
- [ ] Create projects list page
- [ ] Build project card component
- [ ] Implement empty state UI
- [ ] Add "Create Project" button/CTA
- [ ] Build project grid/list view toggle
- [ ] Add sorting (by date, name, status)
- [ ] Implement search/filter
- [ ] Add loading skeletons
- [ ] Handle pagination (if needed)

#### Files to Create
```
app/(dashboard)/projects/page.tsx
components/projects/project-card.tsx
components/projects/project-list.tsx
components/projects/empty-state.tsx
lib/hooks/use-projects.ts
lib/services/projects.ts
```

### 2.3 Create Project Flow

#### Tasks
- [ ] Create "New Project" dialog/page
- [ ] Build multi-step form
  - Step 1: Title & Description
  - Step 2: Genre & Audience
  - Step 3: Tone & Settings
- [ ] Implement form validation with Zod
- [ ] Handle form submission
- [ ] Create project via API
- [ ] Redirect to new project page
- [ ] Add form error handling
- [ ] Optional: Cover image upload

#### Files to Create
```
components/projects/create-project-dialog.tsx
components/projects/project-form.tsx
lib/validations/project.ts
app/api/projects/route.ts
```

### 2.4 Project Detail Page

#### Tasks
- [ ] Create project detail layout
- [ ] Build project header with title/actions
- [ ] Add project stats (anecdote count, word count)
- [ ] Create tabbed interface (Anecdotes, Compile, Settings)
- [ ] Implement project settings form
- [ ] Add delete project functionality (with confirmation)
- [ ] Build project context provider

#### Files to Create
```
app/(dashboard)/projects/[projectId]/page.tsx
app/(dashboard)/projects/[projectId]/layout.tsx
app/(dashboard)/projects/[projectId]/settings/page.tsx
components/projects/project-header.tsx
components/projects/project-stats.tsx
components/projects/project-tabs.tsx
lib/hooks/use-project.ts
```

### 2.5 API Routes for Projects

#### Tasks
- [ ] GET /api/projects - List user's projects
- [ ] POST /api/projects - Create project
- [ ] GET /api/projects/[id] - Get project
- [ ] PATCH /api/projects/[id] - Update project
- [ ] DELETE /api/projects/[id] - Delete project
- [ ] Add proper error handling
- [ ] Validate all inputs with Zod
- [ ] Add authorization checks
- [ ] Implement response caching

### Milestone 2: ✅ Projects Module Complete
- Users can create projects
- Project dashboard shows all projects
- Project settings configurable
- CRUD operations working

---

## Phase 3: Anecdotes Module (Core Feature)
**Duration**: 2.5 weeks
**Goal**: Users can write, enrich, and refine anecdotes with AI assistance

### 3.1 Database: Anecdotes Schema

#### Tasks
- [ ] Add Anecdote model to Prisma schema
- [ ] Add MediaAttachment model to Prisma schema
- [ ] Create migrations
- [ ] Generate Prisma client
- [ ] Create anecdote service/repository
- [ ] Build anecdote CRUD operations
- [ ] Set up S3/Cloudinary for media storage
- [ ] Configure storage bucket policies

#### Prisma Schema Addition
```prisma
model Anecdote {
  id                     String    @id @default(uuid())
  projectId              String
  userId                 String

  // Core content
  title                  String?
  rawContent             String
  timeFrameStart         DateTime?
  timeFrameEnd           DateTime?
  timeFrameApproximate   String?
  location               String?
  peopleInvolved         String[]
  themes                 String[]

  // Processing state
  status                 String    @default("draft")
  currentQuestionRound   Int       @default(0)

  // AI-generated content
  questionnaire          Json      @default("[]")
  aiRefinedContent       String?
  aiSummary500           String?
  aiSummary100           String?
  aiExtractedThemes      String[]
  aiSuggestedConnections String[]

  // User feedback on AI content
  userFeedback           String?
  refinementIterations   Int       @default(0)

  // Metadata
  wordCount              Int?
  estimatedReadingTime   Int?
  sortOrder              Int?
  createdAt              DateTime  @default(now())
  updatedAt              DateTime  @updatedAt
  lockedAt               DateTime?
  completedAt            DateTime?

  project          Project           @relation(fields: [projectId], references: [id], onDelete: Cascade)
  user             User              @relation(fields: [userId], references: [id], onDelete: Cascade)
  mediaAttachments MediaAttachment[]

  @@index([projectId])
  @@index([userId])
  @@index([status])
  @@map("anecdotes")
}

model MediaAttachment {
  id              String    @id @default(uuid())
  anecdoteId      String
  userId          String

  mediaType       String
  fileUrl         String
  fileName        String?
  fileSize        Int?
  mimeType        String?
  durationSeconds Int?

  // AI processing
  transcription   String?
  aiDescription   String?
  aiExtractedText String?

  // Metadata
  caption         String?
  sortOrder       Int       @default(0)
  createdAt       DateTime  @default(now())

  anecdote Anecdote @relation(fields: [anecdoteId], references: [id], onDelete: Cascade)
  user     User     @relation(fields: [userId], references: [id])

  @@index([anecdoteId])
  @@map("media_attachments")
}
```

### 3.2 Anecdotes List View

#### Tasks
- [ ] Create anecdotes list page (within project)
- [ ] Build anecdote card component
- [ ] Show status badges (draft, processing, complete)
- [ ] Display time frame and word count
- [ ] Implement drag-and-drop reordering
- [ ] Add empty state
- [ ] Create "Add Anecdote" button
- [ ] Add timeline view option

#### Files to Create
```
app/(dashboard)/projects/[projectId]/anecdotes/page.tsx
components/anecdotes/anecdote-card.tsx
components/anecdotes/anecdote-list.tsx
components/anecdotes/timeline-view.tsx
lib/hooks/use-anecdotes.ts
lib/services/anecdotes.ts
```

### 3.3 Anecdote Editor (Stage 1: Initial Entry)

#### Tasks
- [ ] Create anecdote detail page
- [ ] Build rich text editor component (TipTap/Slate)
- [ ] Add time frame picker (date range + approximate)
- [ ] Implement auto-save functionality
- [ ] Create "Save Draft" button
- [ ] Add word count display
- [ ] Build location input
- [ ] Add people involved tags
- [ ] Create themes/topics selector
- [ ] Implement "Lock" button to start AI processing

#### Files to Create
```
app/(dashboard)/projects/[projectId]/anecdotes/[anecdoteId]/page.tsx
components/anecdotes/anecdote-editor.tsx
components/anecdotes/time-frame-picker.tsx
components/anecdotes/tag-input.tsx
lib/hooks/use-anecdote.ts
stores/editor-store.ts
```

### 3.4 Media Upload System

#### Tasks
- [ ] Create media upload component
- [ ] Implement drag-and-drop upload
- [ ] Upload to S3/Cloudinary
- [ ] Add image preview with lightbox
- [ ] Build audio recorder component
- [ ] Create audio player component
- [ ] Handle multiple file uploads
- [ ] Implement upload progress
- [ ] Add file size validation
- [ ] Create media gallery view
- [ ] Handle media deletion

#### Files to Create
```
components/anecdotes/media-uploader.tsx
components/anecdotes/media-gallery.tsx
components/shared/audio-recorder.tsx
components/shared/audio-player.tsx
components/shared/image-lightbox.tsx
lib/storage/s3.ts (or cloudinary.ts)
lib/utils/media.ts
app/api/anecdotes/[id]/media/route.ts
```

### 3.5 AI Processing: Question Generation (Stage 2)

#### Tasks
- [ ] Create API route for anecdote processing
- [ ] Build AI prompt for anecdote analysis
- [ ] Implement image analysis with Claude Vision
- [ ] Generate follow-up questions
- [ ] Store questions in database
- [ ] Create background job for processing (Bull + Redis)
- [ ] Create questionnaire UI component
- [ ] Build answer input forms
- [ ] Handle multiple Q&A rounds
- [ ] Implement "No More Questions" option
- [ ] Track credit usage
- [ ] Handle errors gracefully

#### Files to Create
```
app/api/anecdotes/[id]/lock/route.ts
app/api/anecdotes/[id]/answer/route.ts
workers/process-anecdote.ts
lib/ai/client.ts
lib/ai/prompts.ts
lib/ai/processors.ts
lib/queue/jobs.ts
components/anecdotes/questionnaire.tsx
components/anecdotes/question-card.tsx
```

#### AI Prompt Structure
```typescript
const questionGenerationPrompt = `
You are helping a user write their memoir by enriching their anecdotes with more detail.

ANECDOTE:
${rawContent}

${images.length > 0 ? `IMAGES ATTACHED: ${imageDescriptions}` : ''}

TIME PERIOD: ${timeFrame || 'Not specified'}
LOCATION: ${location || 'Not specified'}
PEOPLE INVOLVED: ${people.join(', ') || 'Not specified'}

Generate ${questionCount} follow-up questions to help extract:
1. Sensory details (sights, sounds, smells, textures, tastes)
2. Emotional states and reactions
3. Dialogue or specific words spoken
4. Physical environment details
5. The significance or meaning of this memory
6. What happened before/after
7. How this experience affected them

Return as JSON: { "questions": [{ "question": "...", "focus_area": "..." }] }
`;
```

### 3.6 AI Processing: Content Refinement (Stage 3)

#### Tasks
- [ ] Create API route for refinement
- [ ] Build prompt for narrative generation
- [ ] Compile anecdote + Q&A into single narrative
- [ ] Create refinement review UI
- [ ] Allow user feedback on refined version
- [ ] Implement regeneration with feedback
- [ ] Track refinement iterations
- [ ] Create diff view (original vs refined)
- [ ] Queue refinement job

#### Files to Create
```
app/api/anecdotes/[id]/refine/route.ts
workers/refine-anecdote.ts
components/anecdotes/refinement-view.tsx
components/anecdotes/feedback-form.tsx
components/anecdotes/diff-viewer.tsx
```

### 3.7 AI Processing: Summarization (Stage 4)

#### Tasks
- [ ] Create API route for summarization
- [ ] Generate 500-word comprehensive summary
- [ ] Generate 100-word quick summary
- [ ] Extract themes automatically
- [ ] Suggest connections to other anecdotes
- [ ] Display summaries in UI
- [ ] Mark anecdote as complete
- [ ] Update project stats

#### Files to Create
```
app/api/anecdotes/[id]/complete/route.ts
workers/summarize-anecdote.ts
components/anecdotes/summary-display.tsx
components/anecdotes/completion-dialog.tsx
```

### 3.8 Background Job System

#### Tasks
- [ ] Set up Bull queue with Redis
- [ ] Create job processors
- [ ] Implement job retry logic
- [ ] Add job status tracking
- [ ] Create admin job dashboard (optional)
- [ ] Test job processing
- [ ] Handle job failures

#### Files to Create
```
lib/queue/redis.ts
lib/queue/jobs.ts
workers/index.ts
workers/process-anecdote.ts
workers/refine-anecdote.ts
workers/summarize-anecdote.ts
```

### 3.9 Anecdote State Machine

#### Tasks
- [ ] Implement state transitions
- [ ] Handle edge cases (editing completed anecdote)
- [ ] Build progress indicator UI
- [ ] Add transition animations
- [ ] Create unlock/revert functionality
- [ ] Test full flow end-to-end

#### State Flow
```
draft → locked → questioning → refining → completed
                     ↑___________↓ (multiple rounds)
```

### Milestone 3: ✅ Anecdotes Module Complete (MVP)
- Full anecdote lifecycle working
- AI question generation functional
- Content refinement operational
- Summaries generated automatically
- This is the MINIMUM VIABLE PRODUCT

---

## Phase 4: Book Compilation Module
**Duration**: 2 weeks
**Goal**: Users can compile anecdotes into a structured book manuscript

### 4.1 Database: Compilations Schema

#### Tasks
- [ ] Add BookCompilation model to Prisma schema
- [ ] Create migration
- [ ] Generate Prisma client
- [ ] Create compilation service
- [ ] Test compilation operations

#### Prisma Schema Addition
```prisma
model BookCompilation {
  id                   String    @id @default(uuid())
  projectId            String
  userId               String

  // Version info
  versionNumber        Int
  versionName          String?
  commitMessage        String?

  // Content
  manuscriptContent    String?
  tableOfContents      Json?
  chapterStructure     Json?
  includedAnecdoteIds  String[]

  // AI generation metadata
  aiModelUsed          String?
  generationParams     Json?

  // File outputs
  docxUrl              String?
  pdfUrl               String?
  epubUrl              String?

  // Status
  status               String    @default("generating")

  wordCount            Int?
  pageCount            Int?
  createdAt            DateTime  @default(now())
  publishedAt          DateTime?

  project Project @relation(fields: [projectId], references: [id], onDelete: Cascade)
  user    User    @relation(fields: [userId], references: [id])

  @@unique([projectId, versionNumber])
  @@index([projectId])
  @@map("book_compilations")
}
```

### 4.2 Compilation Trigger UI

#### Tasks
- [ ] Create "Compile Book" tab in project
- [ ] Build anecdote selection interface
- [ ] Allow reordering for book structure
- [ ] Show word count estimate
- [ ] Add compilation settings (chapter grouping, etc.)
- [ ] Create "Start Compilation" button
- [ ] Show processing state
- [ ] Poll for completion status

#### Files to Create
```
app/(dashboard)/projects/[projectId]/compile/page.tsx
components/compilation/anecdote-selector.tsx
components/compilation/compilation-settings.tsx
components/compilation/compile-button.tsx
```

### 4.3 AI Book Compilation

#### Tasks
- [ ] Create API route for compilation
- [ ] Create background job for compilation
- [ ] Load all selected anecdotes with summaries
- [ ] Build comprehensive compilation prompt
- [ ] Generate chapter structure
- [ ] Write transitions between anecdotes
- [ ] Generate introduction and conclusion
- [ ] Create table of contents
- [ ] Stream response for long generation
- [ ] Handle credit deduction
- [ ] Store compiled manuscript

#### Files to Create
```
app/api/projects/[id]/compile/route.ts
workers/compile-book.ts
lib/ai/compilation-prompts.ts
```

#### Compilation Prompt Structure
```typescript
const compilationPrompt = `
You are a skilled ghostwriter compiling a memoir from collected anecdotes.

PROJECT: ${projectTitle}
DESCRIPTION: ${projectDescription}
GENRE: ${genre}
TARGET AUDIENCE: ${targetAudience}
TONE: ${toneStyle}

ANECDOTES (in chronological order):
${anecdotes.map((a, i) => `
### Anecdote ${i + 1}: ${a.title || 'Untitled'}
TIME: ${a.time_frame}
SUMMARY (500 words): ${a.aiSummary500}
FULL TEXT: ${a.aiRefinedContent}
`).join('\n---\n')}

Create a complete book manuscript that:
1. Opens with an engaging introduction
2. Organizes anecdotes into logical chapters
3. Writes smooth transitions between stories
4. Maintains consistent voice throughout
5. Closes with a meaningful conclusion
6. Includes chapter titles and breaks

Format the output as:
{
  "title": "...",
  "introduction": "...",
  "chapters": [
    {
      "title": "Chapter 1: ...",
      "content": "..."
    }
  ],
  "conclusion": "...",
  "total_word_count": ...
}
`;
```

### 4.4 Manuscript Viewer

#### Tasks
- [ ] Create manuscript reading interface
- [ ] Build chapter navigation sidebar
- [ ] Implement scroll sync with chapters
- [ ] Add reading progress indicator
- [ ] Create comment/feedback system
- [ ] Highlight sections for feedback
- [ ] Build inline editing capability

#### Files to Create
```
app/(dashboard)/projects/[projectId]/compilations/[compilationId]/page.tsx
components/compilation/manuscript-viewer.tsx
components/compilation/chapter-nav.tsx
components/compilation/feedback-sidebar.tsx
components/compilation/inline-editor.tsx
```

### 4.5 Revision & Versioning

#### Tasks
- [ ] Create version history UI
- [ ] Implement version comparison
- [ ] Allow reverting to previous versions
- [ ] Create "Save as New Version" functionality
- [ ] Build commit message input
- [ ] Show version timeline
- [ ] Handle version branching

#### Files to Create
```
components/compilation/version-history.tsx
components/compilation/version-compare.tsx
components/compilation/save-version-dialog.tsx
app/api/compilations/[id]/feedback/route.ts
app/api/compilations/[id]/regenerate/route.ts
```

### 4.6 Export Functionality

#### Tasks
- [ ] Install document generation libraries (docx, pdfkit)
- [ ] Create export API route
- [ ] Implement DOCX export
- [ ] Implement PDF export
- [ ] Implement EPUB export (stretch)
- [ ] Create export options UI
- [ ] Handle export processing as background job
- [ ] Upload exports to S3/Cloudinary
- [ ] Provide download links
- [ ] Track exports

#### Files to Create
```
app/api/compilations/[id]/export/route.ts
workers/export-book.ts
lib/utils/export-docx.ts
lib/utils/export-pdf.ts
components/compilation/export-dialog.tsx
```

### Milestone 4: ✅ Book Compilation Complete
- Users can compile anecdotes into book
- Manuscript viewer functional
- Version history working
- Export to DOCX/PDF operational

---

## Phase 5: Credits & Monetization
**Duration**: 1 week
**Goal**: Credit system operational for sustainable business model

### 5.1 Database: Credits Schema

#### Tasks
- [ ] Add UserCredit model to Prisma schema
- [ ] Add AIInteraction model to Prisma schema
- [ ] Update User model with credits_balance
- [ ] Create migrations
- [ ] Create credit service
- [ ] Build credit transaction functions

#### Prisma Schema Addition
```prisma
model AIInteraction {
  id               String    @id @default(uuid())
  userId           String
  anecdoteId       String?
  compilationId    String?

  interactionType  String
  promptTokens     Int?
  completionTokens Int?
  totalTokens      Int?
  creditsUsed      Int?
  modelUsed        String?
  latencyMs        Int?

  createdAt        DateTime  @default(now())

  user User @relation(fields: [userId], references: [id])

  @@index([userId])
  @@index([createdAt])
  @@map("ai_interactions")
}

model UserCredit {
  id              String    @id @default(uuid())
  userId          String
  amount          Int
  transactionType String
  description     String?
  referenceId     String?
  balanceAfter    Int
  createdAt       DateTime  @default(now())

  user User @relation(fields: [userId], references: [id])

  @@index([userId])
  @@map("user_credits")
}
```

### 5.2 Credits Display & Management

#### Tasks
- [ ] Add credits display to header
- [ ] Create credits page
- [ ] Build transaction history
- [ ] Show usage breakdown by type
- [ ] Create low balance warning
- [ ] Build credits purchase UI
- [ ] Add usage analytics charts

#### Files to Create
```
app/(dashboard)/credits/page.tsx
components/credits/balance-display.tsx
components/credits/transaction-history.tsx
components/credits/usage-chart.tsx
components/credits/purchase-dialog.tsx
lib/hooks/use-credits.ts
```

### 5.3 Credit Deduction System

#### Tasks
- [ ] Create credit check middleware
- [ ] Implement atomic credit deduction
- [ ] Log all AI interactions
- [ ] Handle insufficient credits gracefully
- [ ] Create cost preview before operations
- [ ] Implement refund for failed operations
- [ ] Add credit consumption to all AI operations

#### Files to Create
```
lib/credits/check-balance.ts
lib/credits/deduct-credits.ts
lib/credits/costs.ts
lib/credits/transactions.ts
```

### 5.4 Payment Integration (Optional for MVP)

#### Tasks
- [ ] Set up Stripe account
- [ ] Install Stripe SDK
- [ ] Create Stripe webhook handler
- [ ] Build credit purchase flow
- [ ] Implement subscription tiers (future)
- [ ] Handle payment success/failure
- [ ] Send purchase receipts via email

#### Files to Create
```
app/api/payments/checkout/route.ts
app/api/webhooks/stripe/route.ts
lib/stripe/client.ts
```

### Milestone 5: ✅ Monetization Ready
- Credits displayed and tracked
- AI operations deduct credits
- Transaction history visible
- Purchase flow functional (or manual for MVP)

---

## Phase 6: Polish, Testing & Launch
**Duration**: 2-3 weeks
**Goal**: Production-ready application

### 6.1 Testing Suite

#### Tasks
- [ ] Set up Vitest for unit tests
- [ ] Write unit tests for utilities
- [ ] Write tests for services/repositories
- [ ] Set up React Testing Library
- [ ] Write component tests for key components
- [ ] Set up Playwright for E2E tests
- [ ] Create E2E tests for critical flows
  - User signup/login
  - Create project
  - Create and process anecdote
  - Compile book
  - Export manuscript
- [ ] Test error states
- [ ] Test loading states
- [ ] Cross-browser testing
- [ ] Mobile responsiveness testing

### 6.2 Performance Optimization

#### Tasks
- [ ] Run Lighthouse audits
- [ ] Optimize images (next/image)
- [ ] Implement code splitting
- [ ] Add caching strategies (React Query)
- [ ] Optimize database queries (Prisma)
- [ ] Add database indexes
- [ ] Implement request deduplication
- [ ] Lazy load heavy components
- [ ] Optimize bundle size

### 6.3 Error Handling & Logging

#### Tasks
- [ ] Implement global error boundary
- [ ] Add Sentry error tracking
- [ ] Create user-friendly error messages
- [ ] Build retry mechanisms for AI calls
- [ ] Log critical errors to database
- [ ] Create admin error dashboard (future)
- [ ] Add monitoring alerts

### 6.4 SEO & Meta

#### Tasks
- [ ] Add meta tags to all pages
- [ ] Create OpenGraph images
- [ ] Implement sitemap
- [ ] Add robots.txt
- [ ] Create landing page (if not done)
- [ ] Write compelling copy
- [ ] Optimize for Core Web Vitals

### 6.5 Documentation

#### Tasks
- [ ] Write user documentation
- [ ] Create FAQ page
- [ ] Build help tooltips throughout app
- [ ] Create onboarding flow/tutorial
- [ ] Write API documentation
- [ ] Create contributor guidelines
- [ ] Document deployment process

### 6.6 Deployment

#### Tasks
- [ ] Set up production PostgreSQL database
- [ ] Configure production Redis instance
- [ ] Set production environment variables
- [ ] Run database migrations in production
- [ ] Deploy to Vercel
- [ ] Deploy background workers (Railway/Render)
- [ ] Configure custom domain
- [ ] Set up SSL certificate
- [ ] Configure CDN
- [ ] Set up monitoring/alerts
- [ ] Configure backup strategy

### 6.7 Launch Checklist

- [ ] All critical E2E tests passing
- [ ] No console errors in production build
- [ ] All environment variables configured
- [ ] Database migrations applied
- [ ] S3/Cloudinary buckets configured
- [ ] Background workers running
- [ ] Credits system tested
- [ ] Export functionality working
- [ ] Email notifications working (if any)
- [ ] Analytics configured
- [ ] Error tracking configured
- [ ] Backup strategy in place
- [ ] Privacy policy published
- [ ] Terms of service published

### Milestone 6: ✅ LAUNCH
- Production deployment complete
- All systems operational
- Ready for users!

---

## Post-Launch Roadmap

### Immediate (Weeks 1-2)
- Monitor error rates
- Gather user feedback
- Fix critical bugs
- Optimize performance bottlenecks

### Short-term (Month 1-2)
- Audio transcription feature
- Speech-to-text for anecdotes
- Enhanced export formatting
- Additional AI refinement options
- User testimonials and case studies

### Medium-term (Month 3-6)
- Mobile app (React Native)
- Collaborative editing
- Custom AI personas
- Template library
- Print-on-demand integration

### Long-term (Month 6+)
- AI-generated illustrations
- Audiobook generation
- Translation services
- Community features
- Professional editor marketplace

---

## Risk Mitigation

### Technical Risks

| Risk | Mitigation |
|------|------------|
| AI API rate limits | Implement queuing, caching, backoff |
| High AI costs | Optimize prompts, set user limits |
| Data loss | Regular backups, version control |
| Security breach | Input validation, encryption, auditing |
| Performance issues | Load testing, monitoring, CDN |
| Database connection limits | Connection pooling (Prisma) |
| Background job failures | Retry logic, dead letter queues |

### Business Risks

| Risk | Mitigation |
|------|------------|
| Low adoption | Focus on niche marketing, testimonials |
| High churn | Improve onboarding, add engagement features |
| Credit system gaming | Usage limits, abuse detection |
| Competition | Focus on UX, unique features |

---

## Resource Requirements

### Development Team
- **MVP**: 1 full-stack developer (16-20 weeks)
- **Accelerated**: 2-3 developers (8-10 weeks)
- **With QA**: Add 1 part-time QA (Phase 6)

### Infrastructure Costs (Monthly)

| Service | Free Tier | Growth |
|---------|-----------|--------|
| PostgreSQL (Supabase/Neon) | $0 | $25+ |
| Redis (Upstash) | $0 | $10+ |
| Vercel | $0 | $20+ |
| S3 / Cloudinary | $0-5 | $10-50+ |
| Anthropic | Pay-per-use | $50-500+ |
| Domain | $12/year | $12/year |
| **Total** | ~$5/month | $120-600/month |

### Time Investment (Solo Developer)
- Phase 0: 3-5 days
- Phase 1: 5-7 days
- Phase 2: 7-10 days
- Phase 3: 12-18 days
- Phase 4: 10-14 days
- Phase 5: 5-7 days
- Phase 6: 10-15 days
- **Total**: 52-76 days (11-15 weeks)

---

## Success Metrics

### MVP Success Criteria
- [ ] 10 users complete full flow (signup → book export)
- [ ] Average 3+ anecdotes per project
- [ ] < 5% error rate on AI operations
- [ ] < 3s page load times
- [ ] Positive user feedback on book quality

### Launch Success Criteria
- [ ] 100 registered users in first month
- [ ] 25 books compiled in first month
- [ ] Net Promoter Score > 30
- [ ] < 1% churn in first month
- [ ] $500+ revenue in first month

---

## Daily/Weekly Workflow

### Daily Standup (Solo)
1. What did I complete yesterday?
2. What will I complete today?
3. Any blockers?

### Weekly Review
1. Compare progress to plan
2. Update task estimates
3. Adjust priorities
4. Document learnings
5. Plan next week

### Code Review Checklist
- [ ] Types are correct
- [ ] Error handling present
- [ ] Loading states handled
- [ ] Mobile responsive
- [ ] Accessibility checked
- [ ] No console warnings
- [ ] Authorization checks in place
- [ ] Database queries optimized

---

## Getting Started

### Day 1 Tasks
1. Read through entire MASTERPLAN.md
2. Set up development environment (Phase 0)
3. Create GitHub repository
4. Initialize project structure
5. Configure PostgreSQL and Prisma
6. Set up NextAuth.js
7. Start Phase 1

### First Week Goals
- Complete Phase 0 and Phase 1
- Have authentication working
- Navigation functional
- Ready to start Projects module

---

## Appendix

### Useful Resources
- [Next.js Documentation](https://nextjs.org/docs)
- [Prisma Documentation](https://www.prisma.io/docs)
- [NextAuth.js Documentation](https://next-auth.js.org)
- [Anthropic API Reference](https://docs.anthropic.com)
- [Tailwind CSS](https://tailwindcss.com/docs)
- [shadcn/ui](https://ui.shadcn.com)
- [Bull Documentation](https://docs.bullmq.io/)

### Inspiration & References
- Notion (UI patterns)
- Linear (design system)
- Grammarly (writing assistance)
- Scrivener (book writing)

---

*This masterplan is a living document. Update it as you learn and as requirements evolve. Good luck building StoryWeaver AI!*

---

**Created**: November 2025
**Last Updated**: November 2025
**Version**: 2.0 (Node.js + Prisma + PostgreSQL)
