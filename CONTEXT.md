# CONTEXT.md - StoryWeaver AI Project Summary

## Product Overview

**StoryWeaver AI** (AI Ghost Writer) transforms personal anecdotes into professionally written books through AI-powered interviewing, refinement, and compilation.

**Core Value**: Democratize book authorship by replacing expensive ghostwriters with intelligent AI that captures memories, asks follow-up questions, refines narratives, and weaves them into cohesive manuscripts.

---

## Tech Stack

- **Frontend**: Next.js 14 (App Router), TypeScript, Tailwind CSS, Zustand, React Query
- **Backend**: Node.js + Express + Prisma ORM + PostgreSQL
- **Auth**: NextAuth.js (Auth.js) with Google OAuth
- **AI**: Anthropic Claude API (claude-sonnet-4-20250514)
- **Vector DB**: PostgreSQL with pgvector extension
- **Background Jobs**: Bull + Redis
- **File Storage**: AWS S3 or Cloudinary
- **Hosting**: Vercel (frontend) + Railway/Render (backend)

---

## Core Entities

```
User → Projects → Anecdotes → Media
                     ↓
              AI Processing
                     ↓
            Book Compilations → Versions
```

---

## User Flow

### 1. Project Creation
User creates project with: title, description, genre, audience, tone, AI settings (question rounds)

### 2. Anecdote Writing
User writes memory with: content, time frame, location, people, themes, media attachments

### 3. AI Processing Pipeline
```
Draft → Lock → Question Generation → Q&A Rounds → Refinement → Summarization → Complete
```

**Stage Details**:
- **Lock**: Submit for AI analysis
- **Questions**: AI generates 3-5 follow-up questions per round
- **Q&A**: User answers; repeats for configured rounds (1-5)
- **Refine**: AI compiles all into polished narrative
- **Summarize**: AI creates 500-word and 100-word summaries

### 4. Book Compilation
- Select completed anecdotes
- AI generates chapter structure
- AI writes transitions between anecdotes
- AI generates introduction/conclusion
- Version control for revisions
- Export to DOCX/PDF

---

## Database Schema (Key Tables)

### users
`id, email, display_name, credits_balance, subscription_tier`

### projects
`id, user_id, title, description, genre, target_audience, tone_style, question_rounds, status`

### anecdotes
`id, project_id, user_id, raw_content, status, questionnaire (JSONB), ai_refined_content, ai_summary_500, ai_summary_100, time_frame_start/end, people_involved, themes`

**Status values**: `draft, locked, questioning, refining, completed`

### media_attachments
`id, anecdote_id, media_type, file_url, transcription, ai_description`

### book_compilations
`id, project_id, version_number, manuscript_content, chapter_structure (JSONB), included_anecdote_ids, docx_url, pdf_url`

### anecdote_chunks (RAG)
`id, anecdote_id, project_id, chunk_text, chunk_type, embedding (vector), metadata`

---

## API Structure

### Auth
`/auth/signup, /auth/signin, /auth/signout`

### Projects
`GET/POST /api/projects`
`GET/PATCH/DELETE /api/projects/:id`

### Anecdotes
`GET/POST /api/projects/:id/anecdotes`
`GET/PATCH/DELETE /api/anecdotes/:id`
`POST /api/anecdotes/:id/lock` - Start AI processing
`POST /api/anecdotes/:id/answer` - Submit Q&A answers
`POST /api/anecdotes/:id/complete` - Finalize

### Compilation
`POST /api/projects/:id/compile` - Start compilation
`GET /api/compilations/:id` - Get result
`POST /api/compilations/:id/export` - Export to file

### RAG
`POST /api/rag/ingest` - Index anecdote
`POST /api/rag/search` - Semantic search

---

## AI Processing Details

### Question Generation
- Input: raw content, images, metadata
- Output: 3-5 targeted questions (sensory details, emotions, context)
- Credits: 2 per round

### Content Refinement
- Input: raw content + all Q&A
- Output: polished 500-2000 word narrative
- Credits: 5

### Summarization
- Output: 500-word (full context) + 100-word (quick reference)
- Credits: 2

### Book Compilation
- Uses RAG to retrieve relevant anecdotes
- Generates chapter structure, transitions, intro/conclusion
- Credits: 20

---

## RAG System

**Purpose**: Handle large projects exceeding context window limits

**Flow**:
1. **Ingest**: Chunk anecdotes → Generate embeddings → Store in pgvector
2. **Retrieve**: Embed query → Similarity search → Filter by metadata
3. **Generate**: Construct prompt with retrieved context → Claude generates

**Chunk Types**: raw, refined, summary_500, summary_100, qa

**Use Cases**:
- Chapter generation (retrieve relevant anecdotes)
- Transition writing (retrieve adjacent content)
- Consistency checking (retrieve entity mentions)
- Theme extraction (cluster by similarity)

---

## Credit System

| Operation | Credits |
|-----------|---------|
| Question generation | 2/round |
| Refinement | 5 |
| Summarization | 2 |
| Image analysis | 1 |
| Audio transcription | 3/min |
| Book compilation | 20 |

Free tier: 100 credits on signup

---

## File Structure

```
apps/web/
├── app/
│   ├── (auth)/login, callback
│   ├── (dashboard)/projects/[id]/anecdotes/[id]
│   └── api/projects, anecdotes, compile, rag
├── components/ui, features, shared
├── hooks/use-project, use-anecdote
├── lib/prisma, auth, ai, utils
├── stores/
└── types/

prisma/
├── schema.prisma
└── migrations/

workers/
├── process-anecdote.ts
└── compile-book.ts
```

---

## Key Features Summary

1. **Google OAuth** authentication
2. **Projects** with configurable AI settings
3. **Anecdotes** with rich text, media, metadata
4. **Multi-round AI questioning**
5. **AI narrative refinement**
6. **Automatic summarization**
7. **RAG-powered book compilation**
8. **Version control** for manuscripts
9. **Export** to DOCX/PDF
10. **Credit-based billing**

---

## Development Commands

```bash
npm run dev              # Start development
npm run build            # Production build
npm run lint             # Lint code
npm run test             # Run tests
npx prisma migrate dev   # Apply migrations
npx prisma generate      # Generate Prisma client
npx prisma studio        # Open Prisma Studio
```

---

## Environment Variables

```
DATABASE_URL
NEXTAUTH_URL
NEXTAUTH_SECRET
GOOGLE_CLIENT_ID
GOOGLE_CLIENT_SECRET
ANTHROPIC_API_KEY
OPENAI_API_KEY (for embeddings)
REDIS_URL
AWS_S3_BUCKET (or CLOUDINARY_URL)
```

---

## Naming Conventions

- **Files**: PascalCase components, camelCase utils, kebab-case folders
- **Variables**: camelCase
- **Types/Interfaces**: PascalCase
- **Database**: snake_case tables/columns
- **API**: REST, plural nouns, proper HTTP methods

---

## Current Status

MVP Phase - Building core anecdote lifecycle and AI processing pipeline.

**Priority Order**:
1. Database schema + Auth
2. Projects/Anecdotes CRUD
3. AI question generation
4. AI refinement
5. Book compilation with RAG
6. Export functionality