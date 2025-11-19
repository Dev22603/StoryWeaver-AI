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
- **Platform**: Supabase (PostgreSQL + Auth + Storage + Edge Functions)
- **API Layer**: Supabase Edge Functions (Deno runtime)
- **AI Integration**: Anthropic Claude API (claude-sonnet-4-20250514)
- **File Storage**: Supabase Storage buckets for media
- **Real-time**: Supabase Realtime for collaborative features (future)

#### Infrastructure
- **Hosting**: Vercel (frontend) + Supabase (backend)
- **CDN**: Vercel Edge Network
- **Version Control**: Git with GitHub
- **CI/CD**: GitHub Actions
- **Monitoring**: Vercel Analytics + Supabase Dashboard

---

## Database Schema

### Tables

#### `users`
```sql
CREATE TABLE users (
  id UUID PRIMARY KEY REFERENCES auth.users(id),
  email TEXT NOT NULL UNIQUE,
  display_name TEXT,
  avatar_url TEXT,
  credits_balance INTEGER DEFAULT 100,
  subscription_tier TEXT DEFAULT 'free',
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);
```

#### `projects`
```sql
CREATE TABLE projects (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  title TEXT NOT NULL,
  description TEXT,
  genre TEXT,
  target_audience TEXT,
  tone_style TEXT DEFAULT 'conversational',
  question_rounds INTEGER DEFAULT 3,
  status TEXT DEFAULT 'active',
  cover_image_url TEXT,
  settings JSONB DEFAULT '{}',
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_projects_user_id ON projects(user_id);
```

#### `anecdotes`
```sql
CREATE TABLE anecdotes (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  project_id UUID NOT NULL REFERENCES projects(id) ON DELETE CASCADE,
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  
  -- Core content
  title TEXT,
  raw_content TEXT NOT NULL,
  time_frame_start DATE,
  time_frame_end DATE,
  time_frame_approximate TEXT,
  location TEXT,
  people_involved TEXT[],
  themes TEXT[],
  
  -- Processing state
  status TEXT DEFAULT 'draft',
  -- Possible values: 'draft', 'locked', 'questioning', 'refining', 'completed'
  current_question_round INTEGER DEFAULT 0,
  
  -- AI-generated content
  questionnaire JSONB DEFAULT '[]',
  ai_refined_content TEXT,
  ai_summary_500 TEXT,
  ai_summary_100 TEXT,
  ai_extracted_themes TEXT[],
  ai_suggested_connections UUID[],
  
  -- User feedback on AI content
  user_feedback TEXT,
  refinement_iterations INTEGER DEFAULT 0,
  
  -- Metadata
  word_count INTEGER,
  estimated_reading_time INTEGER,
  sort_order INTEGER,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW(),
  locked_at TIMESTAMPTZ,
  completed_at TIMESTAMPTZ
);

CREATE INDEX idx_anecdotes_project_id ON anecdotes(project_id);
CREATE INDEX idx_anecdotes_status ON anecdotes(status);
CREATE INDEX idx_anecdotes_time_frame ON anecdotes(time_frame_start, time_frame_end);
```

#### `media_attachments`
```sql
CREATE TABLE media_attachments (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  anecdote_id UUID NOT NULL REFERENCES anecdotes(id) ON DELETE CASCADE,
  user_id UUID NOT NULL REFERENCES users(id),
  
  media_type TEXT NOT NULL,
  -- Possible values: 'image', 'audio', 'video', 'document'
  file_url TEXT NOT NULL,
  file_name TEXT,
  file_size INTEGER,
  mime_type TEXT,
  duration_seconds INTEGER,
  
  -- AI processing
  transcription TEXT,
  ai_description TEXT,
  ai_extracted_text TEXT,
  
  -- Metadata
  caption TEXT,
  sort_order INTEGER DEFAULT 0,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_media_anecdote_id ON media_attachments(anecdote_id);
```

#### `book_compilations`
```sql
CREATE TABLE book_compilations (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  project_id UUID NOT NULL REFERENCES projects(id) ON DELETE CASCADE,
  user_id UUID NOT NULL REFERENCES users(id),
  
  -- Version info
  version_number INTEGER NOT NULL,
  version_name TEXT,
  commit_message TEXT,
  
  -- Content
  manuscript_content TEXT,
  table_of_contents JSONB,
  chapter_structure JSONB,
  included_anecdote_ids UUID[],
  
  -- AI generation metadata
  ai_model_used TEXT,
  generation_params JSONB,
  
  -- File outputs
  docx_url TEXT,
  pdf_url TEXT,
  epub_url TEXT,
  
  -- Status
  status TEXT DEFAULT 'generating',
  -- Possible values: 'generating', 'draft', 'review', 'published'
  
  word_count INTEGER,
  page_count INTEGER,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  published_at TIMESTAMPTZ
);

CREATE INDEX idx_compilations_project_id ON book_compilations(project_id);
CREATE UNIQUE INDEX idx_compilations_version ON book_compilations(project_id, version_number);
```

#### `ai_interactions`
```sql
CREATE TABLE ai_interactions (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES users(id),
  anecdote_id UUID REFERENCES anecdotes(id),
  compilation_id UUID REFERENCES book_compilations(id),
  
  interaction_type TEXT NOT NULL,
  -- Possible values: 'question_generation', 'content_refinement', 'summarization', 'book_compilation', 'image_analysis'
  
  prompt_tokens INTEGER,
  completion_tokens INTEGER,
  total_tokens INTEGER,
  credits_used INTEGER,
  
  model_used TEXT,
  latency_ms INTEGER,
  
  created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_ai_interactions_user_id ON ai_interactions(user_id);
CREATE INDEX idx_ai_interactions_created_at ON ai_interactions(created_at);
```

#### `user_credits`
```sql
CREATE TABLE user_credits (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES users(id),
  
  amount INTEGER NOT NULL,
  transaction_type TEXT NOT NULL,
  -- Possible values: 'purchase', 'usage', 'bonus', 'refund'
  
  description TEXT,
  reference_id UUID,
  balance_after INTEGER,
  
  created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_credits_user_id ON user_credits(user_id);
```

---

## API Endpoints

### Authentication
- `POST /auth/signup` - Register with Google OAuth
- `POST /auth/signin` - Sign in with Google OAuth
- `POST /auth/signout` - Sign out
- `GET /auth/session` - Get current session

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
├── apps/
│   └── web/
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
│       │   ├── supabase/
│       │   │   ├── client.ts
│       │   │   ├── server.ts
│       │   │   └── middleware.ts
│       │   ├── ai/
│       │   │   ├── client.ts
│       │   │   ├── prompts.ts
│       │   │   └── processors.ts
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
├── packages/
│   └── shared/
│       ├── types/
│       └── utils/
├── supabase/
│   ├── functions/
│   │   ├── process-anecdote/
│   │   ├── compile-book/
│   │   └── transcribe-audio/
│   ├── migrations/
│   │   ├── 001_initial_schema.sql
│   │   ├── 002_add_compilations.sql
│   │   └── ...
│   └── seed.sql
├── docs/
│   ├── ARCHITECTURE.md
│   ├── API.md
│   └── DEPLOYMENT.md
├── CLAUDE.md
├── MASTERPLAN.md
├── README.md
├── package.json
└── turbo.json
```

---

## Environment Variables

### Frontend (.env.local)
```bash
# Supabase
NEXT_PUBLIC_SUPABASE_URL=your_supabase_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_anon_key

# App
NEXT_PUBLIC_APP_URL=http://localhost:3000

# Analytics (optional)
NEXT_PUBLIC_POSTHOG_KEY=your_posthog_key
```

### Backend (Supabase Edge Functions)
```bash
# AI
ANTHROPIC_API_KEY=your_anthropic_key

# Storage
SUPABASE_SERVICE_ROLE_KEY=your_service_role_key

# External Services
ASSEMBLY_AI_KEY=your_assemblyai_key  # for transcription
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
- Extract business logic to custom hooks
- Use Zod for all runtime validation

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
- API tests: Supertest for edge functions

### Performance Targets

- Lighthouse score > 90
- First Contentful Paint < 1.5s
- Time to Interactive < 3s
- Core Web Vitals in green

---

## Security Considerations

### Authentication
- Google OAuth only (no password management)
- JWT tokens with short expiry
- Refresh token rotation
- Session invalidation on logout

### Data Protection
- Row Level Security (RLS) on all tables
- User can only access own data
- Encrypted storage for sensitive content
- HTTPS everywhere

### API Security
- Rate limiting on all endpoints
- Input validation with Zod
- CORS configuration
- CSRF protection

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
- Vercel account
- Supabase project
- Anthropic API key

### Deployment Steps

1. **Supabase Setup**
   ```bash
   supabase init
   supabase db push
   supabase functions deploy
   ```

2. **Vercel Deployment**
   - Connect GitHub repository
   - Configure environment variables
   - Deploy

3. **Post-Deployment**
   - Configure custom domain
   - Set up monitoring
   - Enable analytics

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

### Tools
- Vercel Analytics for web vitals
- Supabase Dashboard for database metrics
- Custom dashboard for business metrics
- Error tracking with Sentry (optional)

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