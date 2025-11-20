# StoryWeaver AI - System Architecture

This document describes the technical architecture, design decisions, and system components of StoryWeaver AI.

---

## Table of Contents

- [Overview](#overview)
- [System Architecture](#system-architecture)
- [Tech Stack](#tech-stack)
- [Data Flow](#data-flow)
- [Database Design](#database-design)
- [AI Processing Pipeline](#ai-processing-pipeline)
- [Background Jobs](#background-jobs)
- [File Storage](#file-storage)
- [Authentication & Authorization](#authentication--authorization)
- [API Design](#api-design)
- [Frontend Architecture](#frontend-architecture)
- [Performance Considerations](#performance-considerations)
- [Security](#security)
- [Scalability](#scalability)
- [Monitoring & Observability](#monitoring--observability)

---

## Overview

StoryWeaver AI is a full-stack web application built with Next.js 14+ using the App Router. The application follows a modern serverless architecture with background job processing for long-running AI operations.

### Key Characteristics

- **Monolithic Frontend**: Single Next.js application handling both UI and API routes
- **Type-Safe**: End-to-end TypeScript with Prisma for database access
- **Serverless**: API routes run as serverless functions on Vercel
- **Async Processing**: Background jobs handled by Bull + Redis
- **AI-Powered**: Anthropic Claude API for intelligent content processing
- **Cloud-Native**: Designed for deployment on modern cloud platforms

---

## System Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                          Client Browser                          │
│                       (React + Next.js)                          │
└───────────────────────────────┬─────────────────────────────────┘
                                │ HTTPS
                    ┌───────────▼───────────┐
                    │   Vercel Edge Network  │
                    │        (CDN)           │
                    └───────────┬───────────┘
                                │
        ┌───────────────────────┼───────────────────────┐
        │                       │                       │
┌───────▼────────┐    ┌────────▼────────┐    ┌────────▼────────┐
│  Next.js Pages │    │  API Routes     │    │  Static Assets  │
│  (SSR/SSG)     │    │  (Serverless)   │    │  (Images, etc)  │
└────────────────┘    └────────┬────────┘    └─────────────────┘
                               │
        ┌──────────────────────┼──────────────────────┐
        │                      │                      │
┌───────▼────────┐   ┌─────────▼────────┐   ┌───────▼──────────┐
│  PostgreSQL    │   │   Redis Cache    │   │  Anthropic API   │
│  (Prisma)      │   │   + Job Queue    │   │  (Claude)        │
└────────────────┘   └──────────────────┘   └──────────────────┘
                               │
                     ┌─────────▼─────────┐
                     │  Background       │
                     │  Workers (Bull)   │
                     │  - Process        │
                     │  - Refine         │
                     │  - Compile        │
                     └───────────────────┘
                               │
                     ┌─────────▼─────────┐
                     │  S3 / Cloudinary  │
                     │  (File Storage)   │
                     └───────────────────┘
```

### Component Breakdown

1. **Frontend (Next.js)**
   - Server-rendered pages for SEO
   - Client-side routing for SPA experience
   - React components with TypeScript
   - State management with Zustand
   - Server state with React Query

2. **API Layer (Next.js API Routes)**
   - RESTful endpoints
   - Serverless functions
   - Input validation with Zod
   - Authorization middleware
   - Database access via Prisma

3. **Database (PostgreSQL + Prisma)**
   - Relational data store
   - Type-safe queries
   - Migration system
   - Connection pooling

4. **Cache & Queue (Redis)**
   - Session storage
   - Job queue (Bull)
   - Rate limiting
   - Caching layer

5. **Background Workers**
   - Process long-running AI operations
   - Handle retries and failures
   - Can scale independently

6. **File Storage (S3/Cloudinary)**
   - User-uploaded media
   - Generated documents
   - CDN integration

7. **AI Provider (Anthropic)**
   - Question generation
   - Content refinement
   - Summarization
   - Book compilation
   - Image analysis

---

## Tech Stack

### Frontend

```typescript
{
  "framework": "Next.js 14+",
  "language": "TypeScript",
  "styling": "Tailwind CSS",
  "components": "shadcn/ui",
  "stateManagement": {
    "global": "Zustand",
    "server": "React Query / TanStack Query"
  },
  "forms": "React Hook Form",
  "validation": "Zod",
  "richText": "TipTap / Slate.js"
}
```

### Backend

```typescript
{
  "runtime": "Node.js 20+",
  "apiFramework": "Next.js API Routes",
  "database": "PostgreSQL 15+",
  "orm": "Prisma",
  "authentication": "NextAuth.js (Auth.js)",
  "cache": "Redis",
  "jobQueue": "Bull",
  "fileStorage": "AWS S3 / Cloudinary",
  "ai": "Anthropic Claude API"
}
```

### Infrastructure

```typescript
{
  "hosting": {
    "frontend": "Vercel",
    "workers": "Railway / Render / Vercel Cron"
  },
  "database": "Supabase / Railway / Neon",
  "redis": "Upstash / Railway",
  "cdn": "Vercel Edge Network",
  "monitoring": "Sentry + Vercel Analytics"
}
```

---

## Data Flow

### User Story Creation Flow

```
1. User writes anecdote in editor
   ↓
2. Auto-save to database (debounced)
   ↓
3. User clicks "Lock and Process"
   ↓
4. API validates and checks credits
   ↓
5. Job queued in Redis (Bull)
   ↓
6. Worker picks up job
   ↓
7. Worker calls Claude API
   ↓
8. Questions saved to database
   ↓
9. UI polls for status
   ↓
10. Questions displayed to user
```

### Book Compilation Flow

```
1. User selects anecdotes
   ↓
2. User configures compilation settings
   ↓
3. User triggers compilation
   ↓
4. API creates compilation record
   ↓
5. Job queued with all anecdote data
   ↓
6. Worker loads summaries from DB
   ↓
7. Worker streams Claude API response
   ↓
8. Manuscript saved to database
   ↓
9. Export jobs queued (DOCX, PDF)
   ↓
10. Files uploaded to S3
   ↓
11. Download links available
```

---

## Database Design

### Schema Overview

The database follows a relational model with the following key entities:

- **User**: Authentication and profile data
- **Project**: Container for related anecdotes
- **Anecdote**: User's memory/story
- **MediaAttachment**: Photos, audio, video
- **BookCompilation**: Generated manuscripts
- **AIInteraction**: Logging and analytics
- **UserCredit**: Transaction history

### Key Relationships

```
User
 ├─ Projects (1:N)
 │   ├─ Anecdotes (1:N)
 │   │   └─ MediaAttachments (1:N)
 │   └─ BookCompilations (1:N)
 ├─ AIInteractions (1:N)
 └─ UserCredits (1:N)
```

### Indexing Strategy

Critical indexes for performance:

```prisma
// User lookups
@@index([userId]) on Projects
@@index([userId]) on Anecdotes

// Project anecdotes
@@index([projectId]) on Anecdotes
@@index([projectId, status]) on Anecdotes

// Compilation lookups
@@index([projectId]) on BookCompilations
@@unique([projectId, versionNumber]) on BookCompilations

// Analytics
@@index([userId, createdAt]) on AIInteractions
@@index([createdAt]) on AIInteractions
```

### Data Consistency

- **Cascading Deletes**: When a user deletes a project, all anecdotes and compilations are deleted
- **Soft Deletes**: Future feature for data recovery
- **Transactions**: Multi-step operations use Prisma transactions
- **Optimistic Updates**: UI updates optimistically for better UX

---

## AI Processing Pipeline

### Stage 1: Question Generation

```typescript
// Triggered when user locks anecdote

async function processAnecdote(anecdoteId: string) {
  // 1. Load anecdote with media
  const anecdote = await prisma.anecdote.findUnique({
    where: { id: anecdoteId },
    include: { mediaAttachments: true }
  });

  // 2. Analyze images (if any)
  const imageDescriptions = await analyzeImages(
    anecdote.mediaAttachments.filter(m => m.mediaType === 'image')
  );

  // 3. Generate questions
  const questions = await claude.messages.create({
    model: 'claude-sonnet-4-20250514',
    messages: [{
      role: 'user',
      content: buildQuestionPrompt(anecdote, imageDescriptions)
    }]
  });

  // 4. Save questions to DB
  await prisma.anecdote.update({
    where: { id: anecdoteId },
    data: {
      questionnaire: questions,
      status: 'questioning'
    }
  });

  // 5. Deduct credits
  await deductCredits(anecdote.userId, 2);
}
```

### Stage 2: Content Refinement

```typescript
async function refineAnecdote(anecdoteId: string) {
  // 1. Load anecdote with Q&A
  const anecdote = await prisma.anecdote.findUnique({
    where: { id: anecdoteId }
  });

  // 2. Build comprehensive prompt
  const prompt = buildRefinementPrompt(
    anecdote.rawContent,
    anecdote.questionnaire,
    anecdote.userFeedback
  );

  // 3. Generate refined narrative
  const refined = await claude.messages.create({
    model: 'claude-sonnet-4-20250514',
    max_tokens: 4000,
    messages: [{ role: 'user', content: prompt }]
  });

  // 4. Save refined content
  await prisma.anecdote.update({
    where: { id: anecdoteId },
    data: {
      aiRefinedContent: refined.content[0].text,
      status: 'refining',
      refinementIterations: anecdote.refinementIterations + 1
    }
  });

  // 5. Deduct credits
  await deductCredits(anecdote.userId, 5);
}
```

### Stage 3: Summarization

```typescript
async function summarizeAnecdote(anecdoteId: string) {
  const anecdote = await prisma.anecdote.findUnique({
    where: { id: anecdoteId }
  });

  // Generate both summaries in parallel
  const [summary500, summary100, themes] = await Promise.all([
    generateSummary(anecdote.aiRefinedContent, 500),
    generateSummary(anecdote.aiRefinedContent, 100),
    extractThemes(anecdote.aiRefinedContent)
  ]);

  await prisma.anecdote.update({
    where: { id: anecdoteId },
    data: {
      aiSummary500: summary500,
      aiSummary100: summary100,
      aiExtractedThemes: themes,
      status: 'completed',
      completedAt: new Date()
    }
  });

  await deductCredits(anecdote.userId, 2);
}
```

### Stage 4: Book Compilation

```typescript
async function compileBook(projectId: string, anecdoteIds: string[]) {
  // 1. Load project and anecdotes
  const project = await prisma.project.findUnique({
    where: { id: projectId }
  });

  const anecdotes = await prisma.anecdote.findMany({
    where: { id: { in: anecdoteIds } },
    orderBy: { timeFrameStart: 'asc' }
  });

  // 2. Build compilation prompt
  const prompt = buildCompilationPrompt(project, anecdotes);

  // 3. Stream response for long generation
  const stream = await claude.messages.stream({
    model: 'claude-sonnet-4-20250514',
    max_tokens: 16000,
    messages: [{ role: 'user', content: prompt }]
  });

  let manuscript = '';
  for await (const chunk of stream) {
    manuscript += chunk.content[0].text;
    // Update progress in DB
  }

  // 4. Parse and save manuscript
  const parsed = parseManuscript(manuscript);

  const compilation = await prisma.bookCompilation.create({
    data: {
      projectId,
      userId: project.userId,
      versionNumber: await getNextVersionNumber(projectId),
      manuscriptContent: manuscript,
      chapterStructure: parsed.chapters,
      tableOfContents: parsed.toc,
      includedAnecdoteIds: anecdoteIds,
      status: 'draft'
    }
  });

  await deductCredits(project.userId, 20);

  return compilation;
}
```

---

## Background Jobs

### Job Queue Architecture

Using **Bull** for reliable background job processing:

```typescript
// lib/queue/jobs.ts

import Queue from 'bull';
import { redisClient } from './redis';

export const anecdoteQueue = new Queue('anecdote-processing', {
  redis: redisClient,
  defaultJobOptions: {
    attempts: 3,
    backoff: {
      type: 'exponential',
      delay: 2000
    },
    removeOnComplete: 100,
    removeOnFail: 500
  }
});

export const compilationQueue = new Queue('book-compilation', {
  redis: redisClient,
  defaultJobOptions: {
    attempts: 2,
    backoff: {
      type: 'exponential',
      delay: 5000
    },
    timeout: 300000 // 5 minutes
  }
});

export const exportQueue = new Queue('document-export', {
  redis: redisClient
});
```

### Job Types

1. **process-anecdote**
   - Analyze anecdote
   - Generate questions
   - Timeout: 60s
   - Retries: 3

2. **refine-anecdote**
   - Generate refined narrative
   - Timeout: 90s
   - Retries: 3

3. **summarize-anecdote**
   - Generate summaries
   - Extract themes
   - Timeout: 45s
   - Retries: 3

4. **compile-book**
   - Generate manuscript
   - Timeout: 5 minutes
   - Retries: 2

5. **export-document**
   - Generate DOCX/PDF
   - Upload to S3
   - Timeout: 2 minutes
   - Retries: 3

### Job Status Tracking

```typescript
// API endpoint to check job status
export async function GET(req: Request, { params }: { params: { jobId: string } }) {
  const job = await anecdoteQueue.getJob(params.jobId);

  if (!job) {
    return NextResponse.json({ error: 'Job not found' }, { status: 404 });
  }

  const state = await job.getState();
  const progress = job.progress();
  const failedReason = job.failedReason;

  return NextResponse.json({
    id: job.id,
    state,
    progress,
    failedReason,
    finishedOn: job.finishedOn,
    processedOn: job.processedOn
  });
}
```

---

## File Storage

### Storage Strategy

**Primary: AWS S3**
**Alternative: Cloudinary**

### Bucket Structure

```
storyweaver-media/
├── users/
│   └── {userId}/
│       └── avatars/
│           └── {filename}
├── anecdotes/
│   └── {anecdoteId}/
│       ├── images/
│       ├── audio/
│       └── documents/
└── compilations/
    └── {compilationId}/
        ├── manuscript.docx
        ├── manuscript.pdf
        └── manuscript.epub
```

### Upload Flow

```typescript
// 1. Generate presigned URL
const url = await s3.getSignedUrl('putObject', {
  Bucket: process.env.AWS_S3_BUCKET,
  Key: `anecdotes/${anecdoteId}/images/${filename}`,
  Expires: 300, // 5 minutes
  ContentType: mimeType
});

// 2. Client uploads directly to S3
await fetch(url, {
  method: 'PUT',
  body: file,
  headers: { 'Content-Type': mimeType }
});

// 3. Save metadata to database
await prisma.mediaAttachment.create({
  data: {
    anecdoteId,
    userId,
    mediaType: 'image',
    fileUrl: publicUrl,
    fileName: filename,
    fileSize: file.size,
    mimeType
  }
});
```

---

## Authentication & Authorization

### NextAuth.js Configuration

```typescript
// lib/auth.ts

import NextAuth from 'next-auth';
import GoogleProvider from 'next-auth/providers/google';
import { PrismaAdapter } from '@auth/prisma-adapter';
import { prisma } from './prisma';

export const { auth, signIn, signOut, handlers } = NextAuth({
  adapter: PrismaAdapter(prisma),
  providers: [
    GoogleProvider({
      clientId: process.env.GOOGLE_CLIENT_ID!,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET!,
      authorization: {
        params: {
          prompt: 'consent',
          access_type: 'offline',
          response_type: 'code'
        }
      }
    })
  ],
  callbacks: {
    async session({ session, user }) {
      if (session.user) {
        session.user.id = user.id;
        session.user.creditsBalance = user.creditsBalance;
        session.user.subscriptionTier = user.subscriptionTier;
      }
      return session;
    }
  },
  pages: {
    signIn: '/login',
    error: '/login'
  }
});
```

### Authorization Middleware

```typescript
// lib/auth/authorize.ts

export async function authorizeProjectAccess(
  userId: string,
  projectId: string
): Promise<boolean> {
  const project = await prisma.project.findUnique({
    where: { id: projectId },
    select: { userId: true }
  });

  return project?.userId === userId;
}

export async function authorizeAnecdoteAccess(
  userId: string,
  anecdoteId: string
): Promise<boolean> {
  const anecdote = await prisma.anecdote.findUnique({
    where: { id: anecdoteId },
    select: { userId: true }
  });

  return anecdote?.userId === userId;
}
```

### Protected API Routes

```typescript
// app/api/projects/[id]/route.ts

export async function GET(
  req: Request,
  { params }: { params: { id: string } }
) {
  const session = await auth();

  if (!session?.user) {
    return NextResponse.json(
      { error: 'Unauthorized' },
      { status: 401 }
    );
  }

  const hasAccess = await authorizeProjectAccess(
    session.user.id,
    params.id
  );

  if (!hasAccess) {
    return NextResponse.json(
      { error: 'Forbidden' },
      { status: 403 }
    );
  }

  // ... rest of handler
}
```

---

## API Design

### REST Principles

- **Resource-based URLs**: `/api/projects/{id}/anecdotes`
- **HTTP Methods**: GET, POST, PATCH, DELETE
- **Status Codes**: 200, 201, 400, 401, 403, 404, 500
- **JSON Responses**: Consistent error format

### Request/Response Format

```typescript
// Request
POST /api/projects
{
  "title": "My Memoir",
  "description": "A collection of memories",
  "genre": "memoir",
  "toneStyle": "conversational"
}

// Success Response (201)
{
  "project": {
    "id": "uuid",
    "title": "My Memoir",
    "createdAt": "2025-01-01T00:00:00Z",
    ...
  }
}

// Error Response (400)
{
  "error": "Validation failed",
  "details": [
    {
      "field": "title",
      "message": "Title is required"
    }
  ]
}
```

### Input Validation

All inputs validated with **Zod**:

```typescript
import { z } from 'zod';

const createProjectSchema = z.object({
  title: z.string().min(1).max(200),
  description: z.string().max(1000).optional(),
  genre: z.string().optional(),
  targetAudience: z.string().optional(),
  toneStyle: z.enum(['conversational', 'formal', 'literary']).default('conversational'),
  questionRounds: z.number().min(1).max(5).default(3)
});

export async function POST(req: Request) {
  const body = await req.json();
  const result = createProjectSchema.safeParse(body);

  if (!result.success) {
    return NextResponse.json(
      { error: 'Validation failed', details: result.error.issues },
      { status: 400 }
    );
  }

  // ... create project
}
```

---

## Frontend Architecture

### Component Structure

```
components/
├── ui/                    # shadcn/ui primitives
│   ├── button.tsx
│   ├── card.tsx
│   └── dialog.tsx
├── layout/                # Shell components
│   ├── sidebar.tsx
│   ├── header.tsx
│   └── mobile-nav.tsx
├── projects/              # Domain components
│   ├── project-card.tsx
│   ├── project-form.tsx
│   └── project-list.tsx
├── anecdotes/
│   ├── anecdote-editor.tsx
│   ├── questionnaire.tsx
│   └── refinement-view.tsx
└── shared/                # Reusable components
    ├── audio-recorder.tsx
    └── image-gallery.tsx
```

### State Management

**Global State (Zustand)**:
```typescript
// stores/editor-store.ts

import { create } from 'zustand';

interface EditorState {
  content: string;
  isDirty: boolean;
  isSaving: boolean;
  lastSaved: Date | null;
  setContent: (content: string) => void;
  save: () => Promise<void>;
}

export const useEditorStore = create<EditorState>((set, get) => ({
  content: '',
  isDirty: false,
  isSaving: false,
  lastSaved: null,
  setContent: (content) => set({ content, isDirty: true }),
  save: async () => {
    set({ isSaving: true });
    // ... save logic
    set({ isSaving: false, isDirty: false, lastSaved: new Date() });
  }
}));
```

**Server State (React Query)**:
```typescript
// lib/hooks/use-project.ts

import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';

export function useProject(projectId: string) {
  return useQuery({
    queryKey: ['project', projectId],
    queryFn: () => fetchProject(projectId),
    staleTime: 5 * 60 * 1000 // 5 minutes
  });
}

export function useUpdateProject() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: updateProject,
    onSuccess: (data) => {
      queryClient.invalidateQueries({ queryKey: ['project', data.id] });
    }
  });
}
```

---

## Performance Considerations

### Database Optimization

- **Connection Pooling**: Prisma manages connection pool
- **Query Optimization**: Use `select` and `include` strategically
- **Indexes**: All foreign keys and frequent queries indexed
- **Pagination**: Cursor-based pagination for large lists

### Caching Strategy

1. **React Query**: Client-side cache (5 min stale time)
2. **Next.js**: Static generation for marketing pages
3. **Redis**: Session storage and rate limiting
4. **CDN**: Static assets via Vercel Edge

### Code Splitting

```typescript
// Dynamic imports for heavy components
const AnecdoteEditor = dynamic(() => import('@/components/anecdotes/anecdote-editor'), {
  loading: () => <EditorSkeleton />
});

const ManuscriptViewer = dynamic(() => import('@/components/compilation/manuscript-viewer'));
```

---

## Security

### Key Measures

1. **Authentication**: OAuth only (no passwords)
2. **Authorization**: Row-level checks in API
3. **Input Validation**: Zod schemas on all inputs
4. **SQL Injection**: Prevented by Prisma
5. **XSS**: React escapes by default
6. **CSRF**: NextAuth.js protection
7. **Rate Limiting**: Per-user API limits
8. **Secrets**: Environment variables only
9. **HTTPS**: Enforced everywhere
10. **Content Security Policy**: Configured headers

---

## Scalability

### Horizontal Scaling

- **API Routes**: Automatically scaled by Vercel
- **Workers**: Can deploy multiple instances
- **Database**: Connection pooling + read replicas
- **Redis**: Cluster mode for high traffic

### Vertical Scaling

- **Database**: Upgrade to larger instance
- **Redis**: Increase memory
- **Workers**: More CPU/memory per instance

### Cost Optimization

- **Serverless**: Pay per invocation
- **Database**: Connection pooling reduces costs
- **CDN**: Reduce origin requests
- **AI**: Optimize prompts for fewer tokens

---

## Monitoring & Observability

### Error Tracking

```typescript
// Sentry integration
import * as Sentry from '@sentry/nextjs';

Sentry.init({
  dsn: process.env.SENTRY_DSN,
  tracesSampleRate: 0.1,
  beforeSend(event) {
    // Filter sensitive data
    return event;
  }
});
```

### Performance Monitoring

- **Vercel Analytics**: Web Vitals, page load times
- **Prisma**: Query performance monitoring
- **Custom Metrics**: AI processing latency, queue times

### Logging

```typescript
// Structured logging
import { logger } from '@/lib/logger';

logger.info('Anecdote processed', {
  anecdoteId,
  userId,
  duration: elapsedMs,
  creditsUsed: 2
});
```

---

## Design Decisions

### Why Next.js?

- **Full-stack**: Single codebase for frontend + API
- **Performance**: Excellent optimization out-of-the-box
- **Developer Experience**: Fast refresh, TypeScript support
- **Deployment**: Seamless Vercel integration
- **SEO**: Server-side rendering for marketing pages

### Why Prisma?

- **Type Safety**: Generated types from schema
- **Migrations**: Version-controlled schema changes
- **Developer Experience**: Excellent tooling (Prisma Studio)
- **Performance**: Efficient queries with connection pooling

### Why NextAuth.js?

- **OAuth Support**: Built-in Google provider
- **Database Sessions**: Persisted in PostgreSQL
- **Security**: CSRF protection, secure cookies
- **Flexibility**: Easy to extend

### Why Bull + Redis?

- **Reliability**: Job persistence and retries
- **Monitoring**: Built-in UI for queue inspection
- **Performance**: Fast, in-memory operations
- **Scalability**: Multiple workers can process jobs

### Why Anthropic Claude?

- **Quality**: Best-in-class text generation
- **Context**: Large context window (200K tokens)
- **Vision**: Image analysis capabilities
- **Safety**: Built-in content moderation
- **API**: Clean, well-documented API

---

**Last Updated**: November 2025
**Version**: 2.0 (Node.js + Prisma + PostgreSQL)
