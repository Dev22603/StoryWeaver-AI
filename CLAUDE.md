# CLAUDE.md - StoryWeaver AI (AI Ghost Writer)

## Project Overview

StoryWeaver AI (internally: AI Ghost Writer / AIGW) is an AI-powered platform that transforms personal memories and anecdotes into professionally written books. The application serves as a structured journaling and book compilation tool, guiding users through memory capture, AI-enhanced refinement, and final manuscript generation.

### Core Value Proposition

Traditional ghostwriting is expensive and quality varies significantly. StoryWeaver AI democratizes book authorship by providing an intelligent system that interviews users about their memories, refines their stories through iterative AI processing, and compiles them into cohesive manuscripts with professional-grade narrative flow.

### Target Users

- Memoir writers capturing life stories
- Parents documenting family histories
- Professionals creating expertise-based books
- Anyone with stories to tell but lacking writing skills or time

---

## Technical Architecture

### Technology Stack

#### Frontend
- **Framework**: Next.js 14+ (App Router)
- **Language**: TypeScript (strict mode enabled)
- **Styling**: Tailwind CSS with custom design system
- **State Management**: Zustand for global state, React Query for server state
- **Forms**: React Hook Form with Zod validation
- **Rich Text Editor**: TipTap or Slate.js for anecdote editing
- **Audio Recording**: Web Audio API with MediaRecorder
- **File Upload**: react-dropzone with image optimization

#### Backend
- **Runtime**: Node.js 20+ (LTS)
- **Framework**: Express.js with TypeScript
- **Database**: PostgreSQL 15+
- **ORM**: Prisma (type-safe database access)
- **Authentication**: NextAuth.js (Auth.js) with Google OAuth
- **AI Integration**: Anthropic Claude API (claude-sonnet-4-20250514)
- **File Storage**: AWS S3 or Cloudinary for media files
- **Background Jobs**: Bull (Redis-based queue) for async processing
- **Validation**: Zod for request/response validation

#### Infrastructure
- **Hosting**:
  - Frontend: Vercel
  - Backend API: Railway / Render / DigitalOcean
  - Database: Supabase (PostgreSQL only) / Railway / Neon
- **CDN**: Vercel Edge Network
- **Cache**: Redis (for sessions and job queues)
- **File Storage**: AWS S3 / Cloudinary
- **Version Control**: Git with GitHub
- **CI/CD**: GitHub Actions
- **Monitoring**: Vercel Analytics + Sentry + Prometheus/Grafana

---

## Database Schema (Prisma)

### Prisma Schema

```prisma
// prisma/schema.prisma

generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id                String            @id @default(uuid())
  email             String            @unique
  emailVerified     DateTime?
  displayName       String?
  avatarUrl         String?
  creditsBalance    Int               @default(100)
  subscriptionTier  String            @default("free")
  createdAt         DateTime          @default(now())
  updatedAt         DateTime          @updatedAt

  // Relations
  accounts          Account[]
  sessions          Session[]
  projects          Project[]
  anecdotes         Anecdote[]
  mediaAttachments  MediaAttachment[]
  bookCompilations  BookCompilation[]
  aiInteractions    AIInteraction[]
  creditTransactions UserCredit[]

  @@map("users")
}

// NextAuth.js models
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

model VerificationToken {
  identifier String
  token      String   @unique
  expires    DateTime

  @@unique([identifier, token])
  @@map("verification_tokens")
}

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

  // Relations
  user             User              @relation(fields: [userId], references: [id], onDelete: Cascade)
  anecdotes        Anecdote[]
  bookCompilations BookCompilation[]

  @@index([userId])
  @@map("projects")
}

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
  status                 String    @default("draft") // 'draft', 'locked', 'questioning', 'refining', 'completed'
  currentQuestionRound   Int       @default(0)

  // AI-generated content
  questionnaire          Json      @default("[]")
  aiRefinedContent       String?
  aiSummary500           String?
  aiSummary100           String?
  aiExtractedThemes      String[]
  aiSuggestedConnections String[]  // Array of Anecdote IDs

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

  // Relations
  project         Project           @relation(fields: [projectId], references: [id], onDelete: Cascade)
  user            User              @relation(fields: [userId], references: [id], onDelete: Cascade)
  mediaAttachments MediaAttachment[]
  aiInteractions   AIInteraction[]

  @@index([projectId])
  @@index([userId])
  @@index([status])
  @@index([timeFrameStart, timeFrameEnd])
  @@map("anecdotes")
}

model MediaAttachment {
  id              String    @id @default(uuid())
  anecdoteId      String
  userId          String

  mediaType       String    // 'image', 'audio', 'video', 'document'
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

  // Relations
  anecdote Anecdote @relation(fields: [anecdoteId], references: [id], onDelete: Cascade)
  user     User     @relation(fields: [userId], references: [id])

  @@index([anecdoteId])
  @@map("media_attachments")
}

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
  status               String    @default("generating") // 'generating', 'draft', 'review', 'published'

  wordCount            Int?
  pageCount            Int?
  createdAt            DateTime  @default(now())
  publishedAt          DateTime?

  // Relations
  project        Project         @relation(fields: [projectId], references: [id], onDelete: Cascade)
  user           User            @relation(fields: [userId], references: [id])
  aiInteractions AIInteraction[]

  @@unique([projectId, versionNumber])
  @@index([projectId])
  @@map("book_compilations")
}

model AIInteraction {
  id               String    @id @default(uuid())
  userId           String
  anecdoteId       String?
  compilationId    String?

  interactionType  String    // 'question_generation', 'content_refinement', 'summarization', 'book_compilation', 'image_analysis'

  promptTokens     Int?
  completionTokens Int?
  totalTokens      Int?
  creditsUsed      Int?

  modelUsed        String?
  latencyMs        Int?

  createdAt        DateTime  @default(now())

  // Relations
  user        User             @relation(fields: [userId], references: [id])
  anecdote    Anecdote?        @relation(fields: [anecdoteId], references: [id])
  compilation BookCompilation? @relation(fields: [compilationId], references: [id])

  @@index([userId])
  @@index([createdAt])
  @@map("ai_interactions")
}

model UserCredit {
  id              String    @id @default(uuid())
  userId          String

  amount          Int
  transactionType String    // 'purchase', 'usage', 'bonus', 'refund'

  description     String?
  referenceId     String?
  balanceAfter    Int

  createdAt       DateTime  @default(now())

  // Relations
  user User @relation(fields: [userId], references: [id])

  @@index([userId])
  @@map("user_credits")
}
```

---

## API Endpoints

### Authentication (NextAuth.js)
- `GET /api/auth/[...nextauth]` - NextAuth.js handlers (signin, signout, callback, etc.)
- `GET /api/auth/session` - Get current session
- `POST /api/auth/signin` - Initiate Google OAuth signin

### Projects
- `GET /api/projects` - List user's projects
- `POST /api/projects` - Create new project
- `GET /api/projects/:id` - Get project details
- `PATCH /api/projects/:id` - Update project
- `DELETE /api/projects/:id` - Delete project
- `GET /api/projects/:id/stats` - Get project statistics

### Anecdotes
- `GET /api/projects/:id/anecdotes` - List anecdotes in project
- `POST /api/projects/:id/anecdotes` - Create anecdote
- `GET /api/anecdotes/:id` - Get anecdote details
- `PATCH /api/anecdotes/:id` - Update anecdote
- `DELETE /api/anecdotes/:id` - Delete anecdote
- `POST /api/anecdotes/:id/lock` - Lock anecdote and start AI processing
- `POST /api/anecdotes/:id/answer` - Submit answers to AI questions
- `POST /api/anecdotes/:id/refine` - Request AI refinement
- `POST /api/anecdotes/:id/complete` - Mark anecdote as complete
- `POST /api/anecdotes/:id/reorder` - Change anecdote sort order

### Media
- `POST /api/anecdotes/:id/media` - Upload media attachment
- `DELETE /api/media/:id` - Delete media attachment
- `POST /api/media/:id/transcribe` - Transcribe audio/video

### Book Compilation
- `POST /api/projects/:id/compile` - Start book compilation
- `GET /api/compilations/:id` - Get compilation details
- `GET /api/projects/:id/compilations` - List all versions
- `POST /api/compilations/:id/feedback` - Submit feedback on compilation
- `POST /api/compilations/:id/regenerate` - Regenerate with changes
- `POST /api/compilations/:id/export` - Export to DOCX/PDF/EPUB

### Credits
- `GET /api/credits/balance` - Get current balance
- `GET /api/credits/history` - Get transaction history
- `POST /api/credits/purchase` - Purchase credits

---

## AI Processing Pipeline

### Stage 1: Anecdote Analysis & Question Generation

When user locks an anecdote, the system:

1. **Content Analysis**
   - Parse raw anecdote text
   - Identify key elements: people, places, emotions, events
   - Detect narrative gaps and unclear areas

2. **Image Analysis** (if images attached)
   - Send images to Claude with vision capabilities
   - Extract contextual details
   - Identify people, objects, settings
   - Note any text visible in images

3. **Question Generation**
   - Generate 3-5 targeted follow-up questions
   - Focus on sensory details, emotions, context
   - Avoid yes/no questions
   - Prioritize what will enrich the narrative

**Prompt Template:**
```
You are helping a user write their memoir. Analyze this anecdote and generate follow-up questions to extract more vivid details.

ANECDOTE:
{raw_content}

{if images: IMAGE DESCRIPTIONS: {ai_image_descriptions}}

Generate {question_count} questions that will help the user:
1. Add sensory details (sights, sounds, smells, textures)
2. Clarify the emotional journey
3. Provide context about people involved
4. Explain the significance of this memory
5. Fill any narrative gaps

Format as JSON array: [{"question": "...", "purpose": "..."}]
```

### Stage 2: Iterative Q&A Rounds

The system supports configurable question rounds (default: 3):

- After each answer submission, analyze new information
- Generate follow-up questions if more depth needed
- User can exit early with "No More Questions"
- Track all Q&A pairs for refinement stage

### Stage 3: Content Refinement

Compile all information into cohesive narrative:

**Prompt Template:**
```
You are a skilled ghostwriter helping compile a memoir. Transform this raw material into a polished narrative.

ORIGINAL ANECDOTE:
{raw_content}

Q&A SESSION:
{formatted_questionnaire}

ATTACHED MEDIA CONTEXT:
{media_descriptions}

Write a refined version that:
1. Maintains the author's voice and perspective
2. Incorporates all relevant details from Q&A
3. Uses vivid, sensory language
4. Has clear narrative flow
5. Preserves emotional authenticity

Target length: 500-1500 words depending on content richness.
```

### Stage 4: Summarization

Generate two summaries for context management:

**500-word summary**: Comprehensive overview preserving key details, emotions, and narrative arc. Used for book compilation context.

**100-word summary**: Quick reference capturing core event, significance, and timeframe. Used for timeline views and project overview.

### Stage 5: Book Compilation

When user triggers compilation:

1. **Gather Context**
   - Load all completed anecdotes
   - Retrieve 500-word summaries
   - Sort by timeframe
   - Identify thematic connections

2. **Structure Planning**
   - Propose chapter organization
   - Identify narrative throughlines
   - Suggest transitions between anecdotes

3. **Manuscript Generation**
   - Generate introduction
   - Weave anecdotes with transitions
   - Maintain consistent voice
   - Add chapter breaks
   - Generate conclusion

4. **Review Cycle**
   - Present draft to user
   - Accept feedback/comments
   - Regenerate sections as needed
   - Save as new version

---

## File Structure

```
storyweaver-ai/
├── .github/
│   └── workflows/
│       ├── ci.yml
│       └── deploy.yml
│
├── apps/
│   └── web/                      # Next.js frontend
│       ├── app/
│       │   ├── (auth)/
│       │   │   ├── login/
│       │   │   └── signup/
│       │   ├── (dashboard)/
│       │   │   ├── projects/
│       │   │   │   ├── page.tsx
│       │   │   │   └── [projectId]/
│       │   │   │       ├── page.tsx
│       │   │   │       ├── anecdotes/
│       │   │   │       │   └── [anecdoteId]/
│       │   │   │       ├── compile/
│       │   │   │       └── settings/
│       │   │   ├── credits/
│       │   │   └── settings/
│       │   ├── api/
│       │   │   ├── auth/
│       │   │   │   └── [...nextauth]/
│       │   │   ├── projects/
│       │   │   ├── anecdotes/
│       │   │   ├── media/
│       │   │   ├── compile/
│       │   │   └── webhooks/
│       │   ├── layout.tsx
│       │   └── page.tsx
│       ├── components/
│       │   ├── ui/
│       │   │   ├── button.tsx
│       │   │   ├── card.tsx
│       │   │   ├── dialog.tsx
│       │   │   ├── input.tsx
│       │   │   └── ...
│       │   ├── projects/
│       │   │   ├── project-card.tsx
│       │   │   ├── project-form.tsx
│       │   │   └── project-list.tsx
│       │   ├── anecdotes/
│       │   │   ├── anecdote-editor.tsx
│       │   │   ├── anecdote-card.tsx
│       │   │   ├── questionnaire.tsx
│       │   │   ├── refinement-view.tsx
│       │   │   └── media-uploader.tsx
│       │   ├── compilation/
│       │   │   ├── manuscript-viewer.tsx
│       │   │   ├── chapter-navigator.tsx
│       │   │   └── version-history.tsx
│       │   └── shared/
│       │       ├── audio-recorder.tsx
│       │       ├── image-gallery.tsx
│       │       └── loading-states.tsx
│       ├── lib/
│       │   ├── prisma.ts            # Prisma client singleton
│       │   ├── auth.ts              # NextAuth.js configuration
│       │   ├── api/
│       │   │   ├── client.ts        # API client for frontend
│       │   │   └── server.ts        # Server-side API helpers
│       │   ├── ai/
│       │   │   ├── client.ts
│       │   │   ├── prompts.ts
│       │   │   └── processors.ts
│       │   ├── storage/
│       │   │   ├── s3.ts           # S3 or Cloudinary client
│       │   │   └── upload.ts
│       │   ├── queue/
│       │   │   ├── redis.ts        # Redis client
│       │   │   └── jobs.ts         # Bull queue definitions
│       │   ├── utils/
│       │   │   ├── format.ts
│       │   │   ├── validation.ts
│       │   │   └── media.ts
│       │   └── hooks/
│       │       ├── use-project.ts
│       │       ├── use-anecdote.ts
│       │       └── use-credits.ts
│       ├── stores/
│       │   ├── editor-store.ts
│       │   └── ui-store.ts
│       ├── types/
│       │   ├── database.ts
│       │   ├── api.ts
│       │   └── ai.ts
│       ├── styles/
│       │   └── globals.css
│       ├── public/
│       │   └── assets/
│       ├── next.config.js
│       ├── tailwind.config.js
│       ├── tsconfig.json
│       └── package.json
│
├── packages/
│   └── shared/
│       ├── types/
│       └── utils/
│
├── prisma/
│   ├── schema.prisma              # Main Prisma schema
│   ├── migrations/                # Database migrations
│   │   └── ...
│   └── seed.ts                    # Database seeding
│
├── workers/                        # Background job processors
│   ├── process-anecdote.ts
│   ├── compile-book.ts
│   ├── transcribe-audio.ts
│   └── index.ts
│
├── docs/
│   ├── ARCHITECTURE.md
│   ├── API.md
│   └── DEPLOYMENT.md
│
├── CLAUDE.md
├── MASTERPLAN.md
├── README.md
├── package.json
├── tsconfig.json
└── turbo.json
```

---

## Environment Variables

### Frontend & Backend (.env)
```bash
# Database
DATABASE_URL="postgresql://user:password@localhost:5432/storyweaver?schema=public"

# NextAuth.js
NEXTAUTH_URL=http://localhost:3000
NEXTAUTH_SECRET=your_nextauth_secret

# Google OAuth
GOOGLE_CLIENT_ID=your_google_client_id
GOOGLE_CLIENT_SECRET=your_google_client_secret

# AI
ANTHROPIC_API_KEY=your_anthropic_key

# File Storage (AWS S3)
AWS_ACCESS_KEY_ID=your_aws_access_key
AWS_SECRET_ACCESS_KEY=your_aws_secret_key
AWS_REGION=us-east-1
AWS_S3_BUCKET=storyweaver-media

# Or Cloudinary
CLOUDINARY_CLOUD_NAME=your_cloud_name
CLOUDINARY_API_KEY=your_api_key
CLOUDINARY_API_SECRET=your_api_secret

# Redis (for Bull queue)
REDIS_URL=redis://localhost:6379

# External Services
ASSEMBLY_AI_KEY=your_assemblyai_key  # for transcription

# App
NEXT_PUBLIC_APP_URL=http://localhost:3000

# Analytics (optional)
NEXT_PUBLIC_POSTHOG_KEY=your_posthog_key
SENTRY_DSN=your_sentry_dsn
```

---

## Development Guidelines

### Code Style

- Use TypeScript strict mode
- Follow Airbnb style guide with Prettier
- Use absolute imports with `@/` prefix
- Prefer functional components with hooks
- Use server components by default, client only when needed
- Keep components small and focused
- Extract business logic to custom hooks or services
- Use Zod for all runtime validation
- Use Prisma for all database operations

### Prisma Best Practices

- Always use transactions for multi-step operations
- Use `include` and `select` to optimize queries
- Leverage Prisma middleware for logging and auditing
- Keep migrations in version control
- Use `prisma generate` after schema changes
- Run `prisma migrate dev` in development
- Run `prisma migrate deploy` in production

### Git Workflow

- Main branch: `main` (protected)
- Feature branches: `feature/feature-name`
- Bug fixes: `fix/bug-description`
- Commit format: Conventional Commits
  - `feat:` new feature
  - `fix:` bug fix
  - `docs:` documentation
  - `refactor:` code refactoring
  - `test:` tests
  - `chore:` maintenance

### Testing Strategy

- Unit tests: Vitest for utilities and hooks
- Component tests: React Testing Library
- E2E tests: Playwright for critical flows
- API tests: Jest/Supertest for API routes
- Database tests: Use separate test database

### Performance Targets

- Lighthouse score > 90
- First Contentful Paint < 1.5s
- Time to Interactive < 3s
- Core Web Vitals in green
- API response time < 200ms (p95)

---

## Security Considerations

### Authentication
- Google OAuth via NextAuth.js
- JWT tokens with short expiry (configurable)
- Refresh token rotation
- Session invalidation on logout
- CSRF protection via NextAuth.js

### Data Protection
- Database-level constraints via Prisma
- Row-level access control in API layer
- User can only access own data
- Encrypted storage for sensitive content
- HTTPS everywhere
- Environment variables for secrets

### API Security
- Rate limiting on all endpoints (express-rate-limit)
- Input validation with Zod
- CORS configuration
- Helmet.js for security headers
- SQL injection protection via Prisma

### Content Safety
- AI content moderation for generated text
- Image scanning for inappropriate content
- User reporting mechanism

---

## Credit System

### Credit Costs (Draft Pricing)
- Question generation: 2 credits
- Content refinement: 5 credits
- Summarization: 2 credits
- Book compilation: 20 credits
- Image analysis: 1 credit
- Audio transcription: 3 credits per minute

### Free Tier
- 100 credits on signup
- Good for ~10 anecdotes or 1 small book

### Paid Plans (Future)
- Starter: $9.99/month - 500 credits
- Writer: $24.99/month - 1500 credits
- Author: $49.99/month - 4000 credits
- Credit packs available for one-time purchase

---

## Deployment

### Prerequisites
- Vercel account (frontend)
- Railway/Render account (backend) or use Vercel for full-stack
- PostgreSQL database (Supabase/Railway/Neon)
- Redis instance (Upstash/Railway)
- AWS S3 or Cloudinary account
- Anthropic API key

### Deployment Steps

1. **Database Setup**
   ```bash
   # Set DATABASE_URL in environment
   npx prisma migrate deploy
   npx prisma generate
   npx prisma db seed
   ```

2. **Vercel Deployment (Full-stack)**
   - Connect GitHub repository
   - Configure environment variables
   - Deploy
   - Vercel will handle both frontend and API routes

3. **Background Workers**
   - Deploy workers separately to Railway/Render
   - Or use Vercel Cron Jobs for simpler tasks
   - Configure Redis connection

4. **Post-Deployment**
   - Configure custom domain
   - Set up monitoring (Sentry)
   - Enable analytics
   - Test all integrations

---

## Monitoring & Analytics

### Key Metrics
- Daily/Monthly Active Users
- Projects created per user
- Anecdotes per project
- Completion rate (draft → completed)
- Book compilations generated
- Credit usage patterns
- AI processing latency
- Error rates
- Database query performance

### Tools
- Vercel Analytics for web vitals
- Sentry for error tracking
- Prisma logging for slow queries
- Custom dashboard for business metrics
- Prometheus + Grafana (optional, for advanced monitoring)

---

## Future Enhancements

### Phase 2 Features
- Collaborative editing (co-authors)
- Professional editor marketplace
- Custom AI personas/voices
- Advanced formatting templates
- Print-on-demand integration
- Public book sharing/publishing

### Phase 3 Features
- Mobile apps (iOS/Android)
- Offline support
- AI-powered illustrations
- Audiobook generation
- Translation to other languages
- Community features

---

## Support & Resources

### Documentation
- User guide: `/docs/user-guide`
- API reference: `/docs/api`
- FAQ: `/docs/faq`

### Contact
- Support email: support@storyweaver.ai
- Discord community: (future)
- GitHub issues for bugs

---

## License

Proprietary - All rights reserved

---

*This document should be updated as the project evolves. Last updated: November 2025*
