# BUILD-ORDER.md - Backend vs Frontend Development Strategy

## Recommendation: Backend-First with Strategic Frontend Scaffolding

Based on the complete project context for StoryWeaver AI, a **backend-first approach** is recommended. Here's the detailed reasoning:

---

## Why Backend First

### 1. AI Processing is Your Core Value & Highest Risk

The entire product hinges on the AI pipeline working well:
- Question generation quality
- Content refinement accuracy
- Summarization effectiveness
- Book compilation coherence

You need to **validate this works before investing in UI**. If the AI outputs are poor, the product fails regardless of how beautiful the frontend is.

### 2. Schema Drives Everything

Your database schema (projects, anecdotes, questionnaire, media, compilations) is complex. Changes to schema after frontend is built means:
- Rewriting API calls
- Updating TypeScript types
- Fixing broken UI components
- Adjusting state management

### 3. State Machine Complexity

The anecdote processing flow (draft → locked → questioning → refining → complete) needs to be rock-solid before the UI represents it. Backend-first lets you:
- Test all state transitions
- Handle edge cases
- Define clear API contracts

### 4. Credit System Integration

Every AI operation costs credits. You need the billing logic working before the frontend can properly:
- Show cost previews
- Block insufficient balance
- Track usage

---

## Recommended Build Order

### Phase 1: Foundation (Week 1)

| Task | Priority |
|------|----------|
| Database schema (all tables) | ✅ Critical |
| Authentication (Supabase Auth) | ✅ Critical |
| Basic API structure | ✅ Critical |
| Next.js project scaffold with routing | ✅ Critical |

### Phase 2: Core Backend (Weeks 2-3)

| Task | Priority |
|------|----------|
| Projects CRUD API | ✅ Critical |
| Anecdotes CRUD API | ✅ Critical |
| Media upload/storage | ✅ Critical |
| AI integration (Claude API) | ✅ Critical |
| Question generation endpoint | ✅ Critical |
| Content refinement endpoint | ✅ Critical |
| Summarization endpoint | ✅ Critical |
| Credit tracking | ✅ Critical |

**Test everything with Postman/Insomnia before moving to frontend**

### Phase 3: Frontend Shell (Week 3-4)

| Task | Priority |
|------|----------|
| Auth pages (login/callback) | ✅ Critical |
| Dashboard layout | ✅ Critical |
| Projects list & create | ✅ Critical |
| Basic anecdote editor | ✅ Critical |

### Phase 4: Full Integration (Weeks 4-6)

| Task | Priority |
|------|----------|
| Complete anecdote flow UI | ✅ Critical |
| AI processing states/feedback | ✅ Critical |
| Media gallery | ✅ Critical |
| Book compilation | ✅ Critical |

---

## Practical Tips

### Start With This Backend Spike

Before building anything else, prove the AI works:

```python
# Quick test script
from anthropic import Anthropic

client = Anthropic()

# Test question generation
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    messages=[{
        "role": "user",
        "content": """Analyze this anecdote and generate 3 follow-up questions:
        
        "I remember the day my mom took me to the science museum. 
        I was maybe 8 years old. There was this electricity exhibit 
        that changed everything for me."
        
        Return as JSON: {"questions": [...]}"""
    }]
)

print(response.content[0].text)
```

**Decision Point**: If the output quality is good, proceed. If not, iterate on prompts before building anything else.

### Use This Frontend Stub Pattern

While building backend, create minimal frontend stubs:

```typescript
// Temporary mock while backend is built
export async function getProjects(): Promise<Project[]> {
  // TODO: Replace with actual API call
  return [
    { id: '1', title: 'My Memoir', status: 'active' },
    { id: '2', title: 'Travel Stories', status: 'active' },
  ];
}
```

This lets you build UI components with realistic data shapes without waiting for backend completion.

---

## Exception: Build Frontend First If...

Consider frontend-first approach only if:

- You need to **pitch to investors** (visual demo matters more)
- You're **validating UX** with users first
- You're more comfortable with frontend and need momentum to start

In that case, use mock data and build the happy path UI, then backfill with real backend.

---

## Final Recommendation: Weekly Breakdown

| Week | Focus | Deliverable |
|------|-------|-------------|
| **Week 1** | Schema + Auth + Project CRUD | Backend foundation complete |
| **Week 2** | Anecdote CRUD + AI endpoints | Core AI pipeline working |
| **Week 3** | Frontend scaffold + Projects UI | Basic UI navigable |
| **Week 4** | Anecdote editor + AI integration UI | MVP feature complete |

### Benefits of This Approach

1. **Never blocked** - Always have something to work on
2. **Validate hard stuff early** - AI quality proven before UI investment
3. **Visible progress** - UI comes online mid-project for motivation
4. **Clean contracts** - API shapes finalized before frontend consumes them
5. **Easier debugging** - Know whether bugs are frontend or backend

---

## Quick Start Commands

### Day 1: Initialize Everything

```bash
# Create project
npx create-next-app@latest storyweaver-ai --typescript --tailwind --app

# Initialize Supabase
npx supabase init
npx supabase start

# Create first migration
npx supabase migration new initial_schema

# Test Anthropic connection
pip install anthropic
python test_ai.py
```

### Day 2: Build Schema

```bash
# Apply migrations
npx supabase db push

# Generate types
npx supabase gen types typescript --local > types/database.ts
```

### Day 3: First API Endpoints

```bash
# Create API route
mkdir -p app/api/projects
touch app/api/projects/route.ts

# Test with curl
curl http://localhost:3000/api/projects
```

---

## Risk Mitigation

| Risk | Mitigation |
|------|------------|
| AI quality is poor | Test prompts extensively in Week 2 before UI |
| Schema changes needed | Keep frontend on mocks until schema is stable |
| Auth complexity | Use Supabase Auth to avoid building from scratch |
| Scope creep | Stick to MVP features only in first 4 weeks |

---

## Success Criteria

### End of Week 2 (Backend Complete)
- [ ] Can create/read/update/delete projects via API
- [ ] Can create/read/update/delete anecdotes via API
- [ ] Can upload media files
- [ ] AI question generation returns quality questions
- [ ] AI refinement produces good narratives
- [ ] Credits are tracked per operation

### End of Week 4 (MVP Complete)
- [ ] User can sign up and log in
- [ ] User can create a project
- [ ] User can write and process an anecdote
- [ ] User sees AI-generated questions and can answer
- [ ] User sees refined anecdote
- [ ] Basic credit display works

---

*This build order optimizes for risk reduction and momentum. Adjust based on your specific constraints and comfort level.*

**Created**: November 2025