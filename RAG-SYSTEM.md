# RAG-SYSTEM.md - Complete Guide to Building a RAG System for Story Weaving

## Table of Contents

1. [Introduction: What is RAG?](#1-introduction-what-is-rag)
2. [Why RAG for Story Weaving?](#2-why-rag-for-story-weaving)
3. [Prerequisites: Topics You Need to Learn](#3-prerequisites-topics-you-need-to-learn)
4. [System Architecture Overview](#4-system-architecture-overview)
5. [Component Deep Dive](#5-component-deep-dive)
6. [Step-by-Step Implementation](#6-step-by-step-implementation)
7. [Testing and Evaluation](#7-testing-and-evaluation)
8. [Advanced Optimizations](#8-advanced-optimizations)
9. [Troubleshooting Common Issues](#9-troubleshooting-common-issues)
10. [Resources and Further Learning](#10-resources-and-further-learning)

---

## 1. Introduction: What is RAG?

### 1.1 The Problem RAG Solves

Large Language Models (LLMs) like Claude have two fundamental limitations:

1. **Knowledge Cutoff**: They only know information up to their training date
2. **Context Window Limits**: They can only process a limited amount of text at once (e.g., 200K tokens)

When you're compiling a book from 50+ anecdotes, each with 1000+ words, plus Q&A sessions, summaries, and metadata, you quickly exceed what fits in a single prompt.

### 1.2 What is RAG?

**RAG = Retrieval-Augmented Generation**

Instead of stuffing everything into the prompt, RAG:

1. **Stores** your content in a searchable database
2. **Retrieves** only the relevant pieces when needed
3. **Augments** the LLM prompt with this retrieved context
4. **Generates** output using this focused, relevant information

Think of it like this:
- **Without RAG**: Trying to write a book while holding all 50 chapters in your head
- **With RAG**: Having an assistant who fetches exactly the chapters you need for each section

### 1.3 RAG Components

```
┌─────────────────────────────────────────────────────────────┐
│                      RAG SYSTEM                              │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐               │
│  │  Content │───▶│ Chunking │───▶│Embedding │               │
│  │  (Docs)  │    │          │    │  Model   │               │
│  └──────────┘    └──────────┘    └────┬─────┘               │
│                                       │                      │
│                                       ▼                      │
│                               ┌──────────────┐               │
│                               │   Vector     │               │
│                               │   Database   │               │
│                               └──────┬───────┘               │
│                                      │                       │
│  ┌──────────┐    ┌──────────┐       │                       │
│  │  Query   │───▶│ Embed    │───────┤                       │
│  │          │    │ Query    │       │                       │
│  └──────────┘    └──────────┘       │                       │
│                                      ▼                       │
│                               ┌──────────────┐               │
│                               │  Retrieval   │               │
│                               │  (Search)    │               │
│                               └──────┬───────┘               │
│                                      │                       │
│                                      ▼                       │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐               │
│  │  Final   │◀───│   LLM    │◀───│ Retrieved│               │
│  │  Output  │    │ (Claude) │    │  Context │               │
│  └──────────┘    └──────────┘    └──────────┘               │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### 1.4 Key Terminology

| Term | Definition | Analogy |
|------|------------|---------|
| **Embedding** | A numerical representation of text as a vector (list of numbers) | Converting words to coordinates on a map |
| **Vector** | A list of numbers representing text in multi-dimensional space | GPS coordinates, but with 1000+ dimensions |
| **Vector Database** | A database optimized for storing and searching vectors | A library organized by meaning, not alphabet |
| **Chunk** | A piece of your content split for processing | A paragraph or section of your anecdote |
| **Semantic Search** | Finding content by meaning, not keywords | Finding "happy moments" even if the word "happy" isn't used |
| **Retrieval** | The process of finding relevant chunks | The librarian fetching relevant books |
| **Context Window** | Maximum text an LLM can process at once | Your working memory capacity |

---

## 2. Why RAG for Story Weaving?

### 2.1 The Story Weaving Challenge

When compiling anecdotes into a book, you need to:

1. **Maintain Consistency**: Same people, places, and facts across chapters
2. **Create Narrative Flow**: Smooth transitions between different time periods
3. **Identify Themes**: Recognize patterns across all anecdotes
4. **Preserve Voice**: Keep the author's unique style throughout
5. **Handle References**: When Chapter 5 references something from Chapter 2
6. **Generate Transitions**: Connect unrelated anecdotes meaningfully

### 2.2 Why Standard LLM Calls Fail

**Scenario**: User has 40 anecdotes, each with:
- 800 words raw content
- 500 words Q&A
- 500 words refined content
- 500 words summary

**Total**: 40 × 2,300 = 92,000 words ≈ 120,000 tokens

Claude's context window is ~200K tokens, but:
- You need room for the system prompt (~2K tokens)
- You need room for the output (~4K tokens)
- Performance degrades with very long contexts
- Cost increases with token count

### 2.3 How RAG Helps Story Weaving

| Challenge | RAG Solution |
|-----------|--------------|
| Too much content | Store all anecdotes, retrieve only relevant ones |
| Finding connections | Semantic search finds thematically similar anecdotes |
| Maintaining consistency | Retrieve all mentions of a person/place when writing about them |
| Writing transitions | Retrieve the end of previous anecdote and start of next |
| Identifying themes | Cluster similar anecdotes by embedding proximity |

### 2.4 Your RAG Use Cases

1. **Chapter Generation**: Retrieve anecdotes for a specific chapter theme
2. **Transition Writing**: Retrieve adjacent anecdotes to write bridges
3. **Consistency Checking**: Retrieve all mentions of an entity to verify facts
4. **Theme Extraction**: Cluster anecdotes by semantic similarity
5. **Timeline Construction**: Retrieve anecdotes by time period
6. **Character Development**: Retrieve all anecdotes featuring a person

---

## 3. Prerequisites: Topics You Need to Learn

### 3.1 Foundational Concepts

#### A. Vector Mathematics (Basic Level)

**What to Learn**:
- What is a vector (list of numbers)
- Vector dimensions
- Cosine similarity (how vectors are compared)
- Distance metrics (Euclidean, dot product)

**Why It Matters**: Embeddings are vectors. Understanding how they're compared helps you tune retrieval.

**Time to Learn**: 2-3 hours

**Resources**:
- 3Blue1Brown "Essence of Linear Algebra" (YouTube) - Chapters 1-3
- https://www.mathsisfun.com/algebra/vectors.html

**Key Concepts**:
```
Vector: [0.1, 0.3, -0.2, 0.8, ...]  (1536 numbers for OpenAI embeddings)

Cosine Similarity: Measures angle between vectors
- 1.0 = Identical meaning
- 0.0 = Unrelated
- -1.0 = Opposite meaning

Example:
"happy child" embedding ≈ "joyful kid" embedding (similarity ~0.9)
"happy child" embedding ≠ "sad adult" embedding (similarity ~0.2)
```

---

#### B. Text Embeddings

**What to Learn**:
- What embeddings represent
- How embedding models work (conceptually)
- Different embedding models and their trade-offs
- Embedding dimensions and their meaning

**Why It Matters**: Choosing the right embedding model affects retrieval quality.

**Time to Learn**: 3-4 hours

**Key Embedding Models**:

| Model | Dimensions | Best For | Provider |
|-------|------------|----------|----------|
| text-embedding-3-small | 1536 | General purpose, cost-effective | OpenAI |
| text-embedding-3-large | 3072 | Higher accuracy, more expensive | OpenAI |
| voyage-2 | 1024 | Code and technical content | Voyage AI |
| embed-english-v3.0 | 1024 | English text, good quality | Cohere |
| all-MiniLM-L6-v2 | 384 | Free, runs locally | Hugging Face |

**Resources**:
- OpenAI Embeddings Guide: https://platform.openai.com/docs/guides/embeddings
- Hugging Face Course Chapter on Embeddings

---

#### C. Vector Databases

**What to Learn**:
- How vector databases differ from traditional databases
- Indexing strategies (HNSW, IVF)
- Approximate nearest neighbor (ANN) search
- Metadata filtering

**Why It Matters**: Vector DB choice affects speed, accuracy, and cost.

**Time to Learn**: 4-5 hours

**Vector Database Options**:

| Database | Type | Best For | Pricing |
|----------|------|----------|---------|
| **Pinecone** | Managed cloud | Production, easy setup | Free tier, then paid |
| **PostgreSQL pgvector** | Self-hosted/managed | Already using PostgreSQL | Included with PostgreSQL |
| **Weaviate** | Self-hosted/cloud | Complex queries | Free self-hosted |
| **Chroma** | Local/embedded | Development, prototyping | Free |
| **Qdrant** | Self-hosted/cloud | High performance | Free self-hosted |
| **Milvus** | Self-hosted | Large scale | Free |

**Recommendation for StoryWeaver**: Start with **PostgreSQL pgvector** (you're already using PostgreSQL with Prisma) or **Pinecone** (easiest to start).

---

#### D. Chunking Strategies

**What to Learn**:
- Why chunking matters
- Different chunking methods
- Chunk size trade-offs
- Overlap strategies

**Why It Matters**: Bad chunking = bad retrieval = bad output.

**Time to Learn**: 2-3 hours

**Chunking Methods**:

| Method | Description | Best For |
|--------|-------------|----------|
| **Fixed Size** | Split every N characters | Simple, predictable |
| **Sentence** | Split at sentence boundaries | Natural breaks |
| **Paragraph** | Split at paragraph breaks | Preserves context |
| **Semantic** | Split by meaning/topic change | Best quality, complex |
| **Recursive** | Try multiple strategies | Balanced approach |

**Chunk Size Trade-offs**:

```
Small Chunks (100-200 tokens):
  ✅ More precise retrieval
  ✅ Less noise in results
  ❌ May lose context
  ❌ More chunks to search

Large Chunks (500-1000 tokens):
  ✅ More context preserved
  ✅ Fewer chunks
  ❌ May retrieve irrelevant content
  ❌ Uses more context window
```

**For Story Weaving**: Use **paragraph-based chunking** with **100-200 token overlap**. Anecdotes have natural paragraph breaks that preserve meaning.

---

#### E. Retrieval Strategies

**What to Learn**:
- Similarity search
- Hybrid search (keyword + semantic)
- Reranking
- Filtering and metadata

**Why It Matters**: Better retrieval = better generated content.

**Time to Learn**: 3-4 hours

**Retrieval Methods**:

| Method | How It Works | When to Use |
|--------|--------------|-------------|
| **Similarity Search** | Find nearest vectors | General use |
| **MMR (Maximal Marginal Relevance)** | Balance relevance and diversity | Avoid duplicate content |
| **Hybrid Search** | Combine keyword + semantic | When exact terms matter |
| **Filtered Search** | Apply metadata filters first | When you know constraints |
| **Reranking** | Re-score results with another model | Improve precision |

---

#### F. Prompt Engineering for RAG

**What to Learn**:
- How to structure prompts with retrieved context
- Handling multiple retrieved chunks
- Instructing the LLM to use (and not hallucinate beyond) context
- Citation and source tracking

**Why It Matters**: The prompt determines how well the LLM uses retrieved information.

**Time to Learn**: 3-4 hours

---

### 3.2 Technical Skills Required

#### A. Python Fundamentals
- Variables, functions, classes
- Async/await
- Working with APIs
- JSON handling
- File I/O

**Time to Learn** (if new): 2-3 weeks

#### B. Database Basics
- SQL queries
- CRUD operations
- Indexing concepts
- Joins and relationships

**Time to Learn** (if new): 1 week

#### C. API Integration
- REST APIs
- Authentication
- Error handling
- Rate limiting

**Time to Learn** (if new): 3-5 days

---

### 3.3 Learning Path Summary

| Week | Topics | Hours |
|------|--------|-------|
| 1 | Vector basics, Embeddings concept | 6-8 |
| 2 | Vector databases, Chunking strategies | 8-10 |
| 3 | Retrieval methods, Prompt engineering | 6-8 |
| 4 | Hands-on: Build simple RAG prototype | 10-15 |

**Total**: ~30-40 hours of learning before building

---

## 4. System Architecture Overview

### 4.1 High-Level Architecture for StoryWeaver

```
┌─────────────────────────────────────────────────────────────────┐
│                    STORYWEAVER RAG SYSTEM                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌─────────────┐                                                │
│  │   Anecdote  │                                                │
│  │   Created/  │                                                │
│  │   Updated   │                                                │
│  └──────┬──────┘                                                │
│         │                                                        │
│         ▼                                                        │
│  ┌─────────────────────────────────────────┐                    │
│  │         INGESTION PIPELINE              │                    │
│  ├─────────────────────────────────────────┤                    │
│  │  1. Extract content from anecdote       │                    │
│  │  2. Create chunks with metadata         │                    │
│  │  3. Generate embeddings                 │                    │
│  │  4. Store in vector database            │                    │
│  └──────────────────┬──────────────────────┘                    │
│                     │                                            │
│                     ▼                                            │
│  ┌─────────────────────────────────────────┐                    │
│  │         VECTOR DATABASE                 │                    │
│  │         (PostgreSQL pgvector)           │                    │
│  ├─────────────────────────────────────────┤                    │
│  │  • Chunk text                           │                    │
│  │  • Embedding vector                     │                    │
│  │  • Metadata (anecdote_id, project_id,   │                    │
│  │    time_frame, people, themes, etc.)    │                    │
│  └──────────────────┬──────────────────────┘                    │
│                     │                                            │
│                     ▼                                            │
│  ┌─────────────────────────────────────────┐                    │
│  │         RETRIEVAL PIPELINE              │                    │
│  ├─────────────────────────────────────────┤                    │
│  │  1. Receive query/task                  │                    │
│  │  2. Generate query embedding            │                    │
│  │  3. Search vector database              │                    │
│  │  4. Apply filters (project, time, etc.) │                    │
│  │  5. Rerank results                      │                    │
│  │  6. Return top K chunks                 │                    │
│  └──────────────────┬──────────────────────┘                    │
│                     │                                            │
│                     ▼                                            │
│  ┌─────────────────────────────────────────┐                    │
│  │         GENERATION PIPELINE             │                    │
│  ├─────────────────────────────────────────┤                    │
│  │  1. Construct prompt with context       │                    │
│  │  2. Send to Claude                      │                    │
│  │  3. Generate chapter/transition/etc.    │                    │
│  │  4. Post-process output                 │                    │
│  └─────────────────────────────────────────┘                    │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 4.2 Data Flow for Book Compilation

```
User clicks "Compile Book"
         │
         ▼
┌─────────────────────┐
│ 1. PLANNING PHASE   │
├─────────────────────┤
│ • Retrieve all      │
│   anecdote summaries│
│ • Identify themes   │
│ • Create chapter    │
│   outline           │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ 2. CHAPTER LOOP     │
├─────────────────────┤
│ For each chapter:   │
│ • Retrieve relevant │
│   anecdotes         │
│ • Retrieve context  │
│   from adjacent     │
│   chapters          │
│ • Generate chapter  │
│   content           │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ 3. TRANSITION PHASE │
├─────────────────────┤
│ Between chapters:   │
│ • Retrieve end of   │
│   previous chapter  │
│ • Retrieve start of │
│   next chapter      │
│ • Generate smooth   │
│   transition        │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ 4. CONSISTENCY CHECK│
├─────────────────────┤
│ • Retrieve all      │
│   mentions of key   │
│   entities          │
│ • Verify facts      │
│   match             │
│ • Flag conflicts    │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ 5. FINAL ASSEMBLY   │
├─────────────────────┤
│ • Generate intro    │
│ • Assemble chapters │
│ • Generate outro    │
│ • Create TOC        │
└─────────────────────┘
```

### 4.3 Database Schema for RAG

```sql
-- Chunks table for storing embedded content
CREATE TABLE anecdote_chunks (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  
  -- Source reference
  anecdote_id UUID NOT NULL REFERENCES anecdotes(id) ON DELETE CASCADE,
  project_id UUID NOT NULL REFERENCES projects(id) ON DELETE CASCADE,
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  
  -- Chunk content
  chunk_text TEXT NOT NULL,
  chunk_index INTEGER NOT NULL,  -- Position in original document
  chunk_type TEXT NOT NULL,      -- 'raw', 'refined', 'summary', 'qa'
  
  -- Vector embedding (using pgvector)
  embedding vector(1536),        -- Dimension depends on model
  
  -- Metadata for filtering
  time_frame_start DATE,
  time_frame_end DATE,
  people_mentioned TEXT[],
  themes TEXT[],
  locations TEXT[],
  
  -- Token count for context budgeting
  token_count INTEGER,
  
  -- Timestamps
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Index for vector similarity search
CREATE INDEX ON anecdote_chunks 
USING ivfflat (embedding vector_cosine_ops)
WITH (lists = 100);

-- Index for metadata filtering
CREATE INDEX idx_chunks_project ON anecdote_chunks(project_id);
CREATE INDEX idx_chunks_anecdote ON anecdote_chunks(anecdote_id);
CREATE INDEX idx_chunks_type ON anecdote_chunks(chunk_type);
CREATE INDEX idx_chunks_time ON anecdote_chunks(time_frame_start, time_frame_end);

-- GIN index for array searches
CREATE INDEX idx_chunks_people ON anecdote_chunks USING GIN(people_mentioned);
CREATE INDEX idx_chunks_themes ON anecdote_chunks USING GIN(themes);
```

---

## 5. Component Deep Dive

### 5.1 Embedding Service

**Purpose**: Convert text to vectors

**Implementation Options**:

| Option | Pros | Cons |
|--------|------|------|
| OpenAI API | High quality, easy | Costs money, external dependency |
| Voyage AI | Great for narrative | Less common |
| Local model | Free, private | Slower, lower quality |

**Recommended**: OpenAI `text-embedding-3-small` for balance of quality and cost

**Code Structure**:
```typescript
// services/embedding-service.ts
export class EmbeddingService {
  async embedText(text: string): Promise<number[]>
  async embedBatch(texts: string[]): Promise<number[][]>
  async embedQuery(query: string): Promise<number[]>
}
```

---

### 5.2 Chunking Service

**Purpose**: Split anecdotes into optimal pieces

**Strategy for Anecdotes**:

```
For each anecdote, create chunks from:
1. Raw content (paragraph chunks)
2. Refined content (paragraph chunks)
3. 500-word summary (single chunk)
4. 100-word summary (single chunk)
5. Q&A pairs (each Q&A as separate chunk)
```

**Chunk Metadata**:
```typescript
interface ChunkMetadata {
  anecdoteId: string;
  projectId: string;
  chunkType: 'raw' | 'refined' | 'summary_500' | 'summary_100' | 'qa';
  chunkIndex: number;
  timeFrameStart?: Date;
  timeFrameEnd?: Date;
  peopleMentioned: string[];
  themes: string[];
  locations: string[];
  tokenCount: number;
}
```

---

### 5.3 Vector Store Service

**Purpose**: Store and search embeddings

**Key Operations**:
```typescript
// services/vector-store-service.ts
export class VectorStoreService {
  // Store chunks
  async upsertChunks(chunks: Chunk[]): Promise<void>
  
  // Delete chunks (when anecdote updated)
  async deleteByAnecdoteId(anecdoteId: string): Promise<void>
  
  // Similarity search
  async similaritySearch(
    embedding: number[],
    options: {
      projectId: string;
      topK: number;
      threshold?: number;
      filters?: {
        chunkTypes?: string[];
        timeRange?: { start: Date; end: Date };
        people?: string[];
        themes?: string[];
      };
    }
  ): Promise<ChunkResult[]>
  
  // Hybrid search (if supported)
  async hybridSearch(
    query: string,
    embedding: number[],
    options: SearchOptions
  ): Promise<ChunkResult[]>
}
```

---

### 5.4 Retrieval Service

**Purpose**: Orchestrate retrieval for different use cases

**Retrieval Strategies**:

```typescript
// services/retrieval-service.ts
export class RetrievalService {
  // Get context for writing a specific chapter
  async getChapterContext(
    projectId: string,
    chapterTheme: string,
    includedAnecdoteIds: string[]
  ): Promise<RetrievedContext>
  
  // Get context for writing transitions
  async getTransitionContext(
    projectId: string,
    previousAnecdoteId: string,
    nextAnecdoteId: string
  ): Promise<RetrievedContext>
  
  // Get all mentions of a person for consistency
  async getPersonMentions(
    projectId: string,
    personName: string
  ): Promise<RetrievedContext>
  
  // Get anecdotes by time period
  async getByTimePeriod(
    projectId: string,
    startDate: Date,
    endDate: Date
  ): Promise<RetrievedContext>
  
  // Find thematically similar anecdotes
  async getSimilarAnecdotes(
    anecdoteId: string,
    topK: number
  ): Promise<RetrievedContext>
}
```

---

### 5.5 Generation Service

**Purpose**: Generate content using retrieved context

**Key Prompts**:

```typescript
// services/generation-service.ts
export class GenerationService {
  // Generate chapter outline
  async generateOutline(
    projectContext: ProjectContext,
    allSummaries: string[]
  ): Promise<ChapterOutline[]>
  
  // Generate chapter content
  async generateChapter(
    chapterOutline: ChapterOutline,
    retrievedAnecdotes: RetrievedContext,
    previousChapterEnd?: string
  ): Promise<string>
  
  // Generate transition
  async generateTransition(
    previousChapterEnd: string,
    nextChapterStart: string,
    narrativeContext: string
  ): Promise<string>
  
  // Generate introduction
  async generateIntroduction(
    projectContext: ProjectContext,
    bookOutline: ChapterOutline[]
  ): Promise<string>
  
  // Generate conclusion
  async generateConclusion(
    projectContext: ProjectContext,
    bookOutline: ChapterOutline[],
    keyThemes: string[]
  ): Promise<string>
}
```

---

## 6. Step-by-Step Implementation

### Step 1: Set Up Vector Database (PostgreSQL pgvector)

#### 1.1 Enable pgvector Extension

**Via Prisma Migration or SQL**:
```sql
CREATE EXTENSION IF NOT EXISTS vector;
```

#### 1.2 Create Chunks Table

**Claude Code Prompt**:
```
Create a Prisma migration for the anecdote_chunks table that will store RAG chunks for the StoryWeaver AI project.

Requirements:
1. Table name: anecdote_chunks
2. Fields:
   - id (UUID, primary key)
   - anecdote_id (UUID, foreign key to anecdotes)
   - project_id (UUID, foreign key to projects)
   - user_id (UUID, foreign key to auth.users)
   - chunk_text (TEXT, the actual content)
   - chunk_index (INTEGER, position in source)
   - chunk_type (TEXT: 'raw', 'refined', 'summary_500', 'summary_100', 'qa')
   - embedding (vector(1536) for OpenAI embeddings)
   - time_frame_start (DATE, nullable)
   - time_frame_end (DATE, nullable)
   - people_mentioned (TEXT array)
   - themes (TEXT array)
   - locations (TEXT array)
   - token_count (INTEGER)
   - created_at, updated_at timestamps

3. Indexes:
   - IVFFlat index on embedding for vector search
   - Regular indexes on project_id, anecdote_id, chunk_type
   - GIN indexes on array columns

4. Access control:
   - Implement user-based filtering in API layer
   - Use Prisma middleware for access control

5. Add a PostgreSQL function for similarity search that:
   - Takes an embedding vector, project_id, limit, and optional filters
   - Returns chunks ordered by cosine similarity
   - Supports filtering by chunk_type, time range, people, themes

Generate the complete migration SQL file.
```

---

### Step 2: Create Embedding Service

#### 2.1 Install Dependencies

```bash
npm install openai
# or for Python
pip install openai
```

#### 2.2 Implement Embedding Service

**Claude Code Prompt**:
```
Create a TypeScript embedding service for the StoryWeaver AI project that uses OpenAI's text-embedding-3-small model.

Requirements:

1. File: lib/services/embedding-service.ts

2. Class: EmbeddingService
   
3. Methods:
   - embedText(text: string): Promise<number[]>
     - Embeds a single text string
     - Returns 1536-dimensional vector
   
   - embedBatch(texts: string[]): Promise<number[][]>
     - Embeds multiple texts efficiently
     - Handles OpenAI's batch limits (max 2048 inputs)
     - Returns array of embeddings in same order
   
   - embedQuery(query: string): Promise<number[]>
     - Optimized for search queries
     - May use different parameters than document embedding

4. Error handling:
   - Retry on rate limits with exponential backoff
   - Handle API errors gracefully
   - Log errors with context

5. Configuration:
   - Use OPENAI_API_KEY from environment
   - Model: text-embedding-3-small
   - Configurable dimensions (default 1536)

6. Optimizations:
   - Cache embeddings for repeated texts (optional)
   - Track token usage for billing

7. Include TypeScript types for all inputs/outputs

8. Add JSDoc documentation for all methods

Generate the complete implementation with error handling and types.
```

---

### Step 3: Create Chunking Service

**Claude Code Prompt**:
```
Create a TypeScript chunking service for the StoryWeaver AI project that splits anecdotes into optimal chunks for RAG.

Requirements:

1. File: lib/services/chunking-service.ts

2. Class: ChunkingService

3. Types needed:
   ```typescript
   interface Anecdote {
     id: string;
     projectId: string;
     userId: string;
     rawContent: string;
     refinedContent?: string;
     summary500?: string;
     summary100?: string;
     questionnaire?: Array<{ question: string; answer: string }>;
     timeFrameStart?: Date;
     timeFrameEnd?: Date;
     peopleMentioned?: string[];
     themes?: string[];
     locations?: string[];
   }
   
   interface Chunk {
     anecdoteId: string;
     projectId: string;
     userId: string;
     chunkText: string;
     chunkIndex: number;
     chunkType: 'raw' | 'refined' | 'summary_500' | 'summary_100' | 'qa';
     metadata: ChunkMetadata;
     tokenCount: number;
   }
   ```

4. Methods:
   - chunkAnecdote(anecdote: Anecdote): Chunk[]
     - Creates all chunks from an anecdote
     - Handles: raw content, refined content, summaries, Q&A
   
   - chunkText(text: string, options: ChunkOptions): string[]
     - Splits text into chunks
     - Options: maxTokens, overlap, splitOn ('paragraph' | 'sentence')
   
   - countTokens(text: string): number
     - Estimates token count (use tiktoken or simple heuristic)

5. Chunking strategy:
   - For raw/refined content: Split by paragraphs, max 500 tokens, 50 token overlap
   - For summaries: Keep as single chunk
   - For Q&A: Each Q&A pair as separate chunk
   
6. Each chunk should include metadata:
   - Source anecdote info
   - Time frame
   - People, themes, locations
   - Token count

7. Handle edge cases:
   - Very long paragraphs (split further)
   - Empty content
   - Missing optional fields

Generate the complete implementation with all edge case handling.
```

---

### Step 4: Create Vector Store Service

**Claude Code Prompt**:
```
Create a TypeScript vector store service for the StoryWeaver AI project using PostgreSQL pgvector with Prisma.

Requirements:

1. File: lib/services/vector-store-service.ts

2. Class: VectorStoreService

3. Dependencies:
   - Use existing Prisma client from lib/prisma
   - Import types from previous services

4. Types:
   ```typescript
   interface ChunkWithEmbedding {
     chunk: Chunk;
     embedding: number[];
   }
   
   interface SearchOptions {
     projectId: string;
     topK: number;
     threshold?: number;  // Minimum similarity score (0-1)
     filters?: {
       chunkTypes?: string[];
       timeRange?: { start: Date; end: Date };
       people?: string[];
       themes?: string[];
       anecdoteIds?: string[];  // Include or exclude specific anecdotes
     };
   }
   
   interface ChunkResult {
     chunk: Chunk;
     score: number;  // Similarity score
     anecdoteId: string;
   }
   ```

5. Methods:
   - upsertChunks(chunks: ChunkWithEmbedding[]): Promise<void>
     - Insert or update chunks with embeddings
     - Batch operations for efficiency
   
   - deleteByAnecdoteId(anecdoteId: string): Promise<void>
     - Delete all chunks for an anecdote (for re-indexing)
   
   - deleteByProjectId(projectId: string): Promise<void>
     - Delete all chunks for a project
   
   - similaritySearch(embedding: number[], options: SearchOptions): Promise<ChunkResult[]>
     - Vector similarity search with filters
     - Use the Supabase RPC function created in Step 1
   
   - getChunksByAnecdoteId(anecdoteId: string): Promise<Chunk[]>
     - Get all chunks for a specific anecdote

6. Use raw SQL queries via Prisma.$queryRaw for vector similarity search

7. Handle errors:
   - Database connection errors
   - Invalid embeddings
   - Missing required filters

8. Add logging for debugging

Generate the complete implementation including the RPC function call.
```

---

### Step 5: Create Retrieval Service

**Claude Code Prompt**:
```
Create a TypeScript retrieval service for the StoryWeaver AI project that orchestrates retrieval for different story weaving use cases.

Requirements:

1. File: lib/services/retrieval-service.ts

2. Class: RetrievalService

3. Dependencies:
   - EmbeddingService
   - VectorStoreService
   - Database client for fetching full anecdotes

4. Types:
   ```typescript
   interface RetrievedContext {
     chunks: ChunkResult[];
     totalTokens: number;
     anecdoteIds: string[];  // Unique anecdotes represented
   }
   
   interface ChapterContext {
     mainAnecdotes: RetrievedContext;  // Primary anecdotes for the chapter
     supportingContext: RetrievedContext;  // Related context
     previousChapterEnd?: string;  // For continuity
   }
   ```

5. Methods:

   a. getChapterContext(
        projectId: string,
        chapterTheme: string,
        anecdoteIds: string[],
        maxTokens: number = 8000
      ): Promise<ChapterContext>
      - Retrieves context for generating a specific chapter
      - Gets full refined content for specified anecdotes
      - Gets supporting context from similar anecdotes
      - Respects token budget
   
   b. getTransitionContext(
        projectId: string,
        fromAnecdoteId: string,
        toAnecdoteId: string
      ): Promise<{ fromEnd: string; toStart: string; sharedThemes: string[] }>
      - Gets the end of the previous anecdote
      - Gets the start of the next anecdote
      - Identifies shared themes/people for smooth transition
   
   c. getPersonMentions(
        projectId: string,
        personName: string,
        maxChunks: number = 20
      ): Promise<RetrievedContext>
      - Retrieves all chunks mentioning a person
      - For consistency checking
   
   d. getByTimePeriod(
        projectId: string,
        startDate: Date,
        endDate: Date
      ): Promise<RetrievedContext>
      - Retrieves anecdotes from a time period
      - For chronological chapters
   
   e. getSimilarAnecdotes(
        anecdoteId: string,
        topK: number = 5
      ): Promise<RetrievedContext>
      - Finds thematically similar anecdotes
      - For suggesting connections
   
   f. getAllSummaries(
        projectId: string
      ): Promise<{ anecdoteId: string; summary: string }[]>
      - Gets 100-word summaries for all anecdotes
      - For planning and outlining

6. Helper methods:
   - budgetTokens(chunks: ChunkResult[], maxTokens: number): ChunkResult[]
     - Trims chunks to fit token budget
     - Prioritizes by relevance score
   
   - deduplicateChunks(chunks: ChunkResult[]): ChunkResult[]
     - Removes overlapping chunks from same anecdote
     - Keeps highest scoring

7. Each method should:
   - Log what it's retrieving (for debugging)
   - Track token usage
   - Handle empty results gracefully

Generate the complete implementation with all methods and helpers.
```

---

### Step 6: Create Ingestion Pipeline

**Claude Code Prompt**:
```
Create a TypeScript ingestion pipeline for the StoryWeaver AI project that processes anecdotes into the vector store when they are created or updated.

Requirements:

1. File: lib/services/ingestion-service.ts

2. Class: IngestionService

3. Dependencies:
   - ChunkingService
   - EmbeddingService
   - VectorStoreService
   - Database client

4. Methods:

   a. ingestAnecdote(anecdoteId: string): Promise<IngestResult>
      - Fetches anecdote from database
      - Creates chunks
      - Generates embeddings
      - Stores in vector database
      - Returns stats (chunks created, tokens used)
   
   b. reingestAnecdote(anecdoteId: string): Promise<IngestResult>
      - Deletes existing chunks
      - Re-ingests with current content
      - Used when anecdote is updated
   
   c. ingestProject(projectId: string): Promise<IngestResult>
      - Ingests all anecdotes in a project
      - Batch processing for efficiency
      - Progress tracking
   
   d. deleteAnecdoteChunks(anecdoteId: string): Promise<void>
      - Removes all chunks for an anecdote
      - Used when anecdote is deleted

5. Types:
   ```typescript
   interface IngestResult {
     anecdoteId: string;
     chunksCreated: number;
     tokensProcessed: number;
     embeddingsGenerated: number;
     timeMs: number;
   }
   ```

6. Optimization:
   - Batch embedding calls (not one per chunk)
   - Progress callbacks for UI updates
   - Handle partial failures (some chunks fail)

7. Error handling:
   - Log failures with context
   - Return partial results on error
   - Retry logic for transient failures

8. Integration points:
   - Call from anecdote.complete webhook/event
   - Call from anecdote.update event
   - Call from anecdote.delete event

Generate the complete implementation with batching and error handling.
```

---

### Step 7: Create Generation Service

**Claude Code Prompt**:
```
Create a TypeScript generation service for the StoryWeaver AI project that generates book content using retrieved context.

Requirements:

1. File: lib/services/generation-service.ts

2. Class: GenerationService

3. Dependencies:
   - Anthropic Claude client
   - RetrievalService
   - Project and anecdote types

4. Types:
   ```typescript
   interface ChapterOutline {
     number: number;
     title: string;
     theme: string;
     anecdoteIds: string[];
     estimatedWords: number;
   }
   
   interface BookOutline {
     title: string;
     chapters: ChapterOutline[];
     estimatedTotalWords: number;
   }
   
   interface GenerationOptions {
     tone: string;
     targetAudience: string;
     maxWords?: number;
   }
   ```

5. Methods:

   a. generateBookOutline(
        projectId: string,
        anecdoteSummaries: { id: string; summary: string; timeFrame?: string }[],
        options: GenerationOptions
      ): Promise<BookOutline>
      
      Prompt strategy:
      - Provide all summaries
      - Ask Claude to identify themes
      - Group anecdotes into chapters
      - Suggest chapter order and titles
   
   b. generateChapter(
        projectId: string,
        chapterOutline: ChapterOutline,
        retrievedContext: ChapterContext,
        options: GenerationOptions
      ): Promise<string>
      
      Prompt strategy:
      - Provide chapter theme and goals
      - Include full anecdote content
      - Include supporting context
      - Include end of previous chapter
      - Instruct to weave anecdotes together
      - Maintain author's voice
   
   c. generateTransition(
        fromText: string,
        toText: string,
        sharedElements: string[],
        options: GenerationOptions
      ): Promise<string>
      
      Prompt strategy:
      - Show end of previous section
      - Show start of next section
      - List shared themes/people
      - Request smooth bridge
   
   d. generateIntroduction(
        projectContext: { title: string; description: string },
        bookOutline: BookOutline,
        options: GenerationOptions
      ): Promise<string>
      
      Prompt strategy:
      - Book overview
      - Hook the reader
      - Set expectations
   
   e. generateConclusion(
        projectContext: { title: string; description: string },
        bookOutline: BookOutline,
        keyThemes: string[],
        options: GenerationOptions
      ): Promise<string>
      
      Prompt strategy:
      - Summarize journey
      - Reflect on themes
      - Meaningful ending

6. Include detailed prompts for each method
7. Handle streaming for long generations
8. Track token usage
9. Add retry logic for API failures

Generate the complete implementation with all prompts fully written out.
```

---

### Step 8: Create Book Compilation Orchestrator

**Claude Code Prompt**:
```
Create a TypeScript book compilation orchestrator for the StoryWeaver AI project that coordinates the entire RAG-powered book generation process.

Requirements:

1. File: lib/services/book-compiler-service.ts

2. Class: BookCompilerService

3. Dependencies:
   - RetrievalService
   - GenerationService
   - IngestionService
   - Database client

4. Types:
   ```typescript
   interface CompilationRequest {
     projectId: string;
     userId: string;
     selectedAnecdoteIds: string[];
     options: {
       tone: string;
       targetAudience: string;
       includeIntro: boolean;
       includeConclusion: boolean;
     };
   }
   
   interface CompilationResult {
     success: boolean;
     compilationId: string;
     manuscript: {
       title: string;
       introduction?: string;
       chapters: {
         number: number;
         title: string;
         content: string;
         wordCount: number;
       }[];
       conclusion?: string;
     };
     stats: {
       totalWords: number;
       totalChapters: number;
       anecdotesIncluded: number;
       tokensUsed: number;
       generationTimeMs: number;
     };
   }
   
   interface CompilationProgress {
     stage: 'preparing' | 'outlining' | 'generating' | 'finalizing';
     currentStep: string;
     progress: number;  // 0-100
     estimatedTimeRemaining?: number;
   }
   ```

5. Main method:
   
   compileBook(
     request: CompilationRequest,
     onProgress?: (progress: CompilationProgress) => void
   ): Promise<CompilationResult>

6. Implementation steps:

   Step 1: Preparation
   - Verify all anecdotes are complete
   - Ensure all anecdotes are ingested in vector store
   - Load project settings
   
   Step 2: Outline Generation
   - Get all summaries for selected anecdotes
   - Generate book outline with chapters
   - Save outline for user review (optional)
   
   Step 3: Chapter Generation (loop)
   - For each chapter:
     a. Retrieve context for chapter
     b. Generate chapter content
     c. Store chapter
     d. Update progress
   
   Step 4: Transition Generation (loop)
   - For each chapter boundary:
     a. Get transition context
     b. Generate transition
     c. Insert into manuscript
   
   Step 5: Introduction & Conclusion
   - Generate introduction (if requested)
   - Generate conclusion (if requested)
   
   Step 6: Assembly
   - Combine all pieces
   - Generate table of contents
   - Calculate statistics
   - Save compilation to database

7. Error handling:
   - Save progress on failure
   - Allow resuming from last successful step
   - Detailed error reporting

8. Progress tracking:
   - Call onProgress callback at each step
   - Estimate remaining time
   - Support cancellation

9. Optimization:
   - Parallel generation where possible
   - Cache retrieved context
   - Reuse embeddings

Generate the complete implementation with detailed progress tracking and error recovery.
```

---

### Step 9: Create API Endpoints

**Claude Code Prompt**:
```
Create Next.js API routes for the StoryWeaver RAG system.

Requirements:

1. Files to create:
   - app/api/rag/ingest/route.ts
   - app/api/rag/search/route.ts
   - app/api/compile/route.ts
   - app/api/compile/[id]/status/route.ts

2. Endpoints:

   a. POST /api/rag/ingest
      - Body: { anecdoteId: string }
      - Triggers ingestion of an anecdote
      - Returns: { success: boolean, stats: IngestResult }
   
   b. POST /api/rag/search
      - Body: { 
          projectId: string,
          query: string,
          filters?: SearchOptions
        }
      - Performs semantic search
      - Returns: { results: ChunkResult[] }
   
   c. POST /api/compile
      - Body: CompilationRequest
      - Starts book compilation (async)
      - Returns: { compilationId: string }
   
   d. GET /api/compile/[id]/status
      - Returns current compilation progress
      - Returns: CompilationProgress | CompilationResult

3. Each endpoint should:
   - Validate authentication
   - Validate input with Zod
   - Handle errors gracefully
   - Return appropriate status codes
   - Check credits before expensive operations

4. For long-running operations (compile):
   - Use background processing (Edge Functions or queue)
   - Return immediately with job ID
   - Support polling for status

5. Include rate limiting for search endpoint

Generate all API routes with full implementation.
```

---

### Step 10: Create Background Job Processor

**Claude Code Prompt**:
```
Create a Bull queue worker for processing book compilations in the background for the StoryWeaver AI project.

Requirements:

1. File: workers/compile-book.ts

2. Purpose: Handle long-running book compilation asynchronously

3. Trigger: Job added to Bull queue from API when compilation is requested

4. Implementation:

   a. Receive compilation request
   b. Update status to 'processing'
   c. Run BookCompilerService.compileBook()
   d. Update progress in database periodically
   e. Save final result
   f. Handle errors and save failure state

5. Status tracking:
   ```typescript
   // Table: book_compilation_jobs
   interface CompilationJob {
     id: string;
     projectId: string;
     userId: string;
     status: 'pending' | 'processing' | 'completed' | 'failed';
     progress: number;
     currentStep: string;
     result?: CompilationResult;
     error?: string;
     createdAt: Date;
     updatedAt: Date;
   }
   ```

6. Error handling:
   - Catch all errors
   - Save error state to database via Prisma
   - User can see what went wrong

7. Timeouts:
   - Configure Bull job timeout limits
   - Save progress before timeout
   - Support resuming with Bull job events

8. Notifications:
   - Could trigger email when complete (optional)
   - Update via WebSocket or polling

Generate the complete Bull worker implementation.
```

---

### Step 11: Integration with Anecdote Lifecycle

**Claude Code Prompt**:
```
Create hooks and triggers to integrate the RAG system with the anecdote lifecycle in StoryWeaver AI.

Requirements:

1. Files:
   - lib/hooks/use-anecdote-ingest.ts
   - workers/on-anecdote-complete.ts

2. Scenarios to handle:

   a. Anecdote Completed
      - When anecdote status changes to 'completed'
      - Automatically ingest into vector store
      - Use database trigger or webhook
   
   b. Anecdote Updated
      - When completed anecdote is unlocked and re-completed
      - Delete old chunks
      - Re-ingest with new content
   
   c. Anecdote Deleted
      - Remove all chunks from vector store
   
   d. Project Deleted
      - Remove all chunks for project

3. Implementation approach using Prisma middleware or API hooks:
   ```typescript
   // In API route when anecdote status changes to 'completed'
   if (updatedAnecdote.status === 'completed' && previousStatus !== 'completed') {
     // Add job to Bull queue for ingestion
     await ingestionQueue.add('ingest-anecdote', { anecdoteId: updatedAnecdote.id });
   }
   ```

4. React hook for manual triggering:
   ```typescript
   function useAnecdoteIngest() {
     const ingest = async (anecdoteId: string) => { ... }
     const reingest = async (anecdoteId: string) => { ... }
     return { ingest, reingest, isIngesting, error }
   }
   ```

5. Error handling:
   - Log failures
   - Retry logic
   - User notification of ingest status

Generate all triggers, functions, and hooks for complete integration.
```

---

## 7. Testing and Evaluation

### 7.1 Unit Tests

**Claude Code Prompt**:
```
Create comprehensive unit tests for the RAG system services in StoryWeaver AI.

Requirements:

1. Test files:
   - lib/services/__tests__/chunking-service.test.ts
   - lib/services/__tests__/embedding-service.test.ts
   - lib/services/__tests__/retrieval-service.test.ts

2. ChunkingService tests:
   - Chunks text correctly by paragraphs
   - Respects max token limit
   - Creates correct chunk types
   - Handles empty content
   - Preserves metadata
   - Calculates token count accurately

3. EmbeddingService tests:
   - Returns correct dimension vectors
   - Handles batch requests
   - Retries on rate limit
   - Throws on API error

4. RetrievalService tests:
   - Retrieves relevant chunks
   - Respects token budget
   - Applies filters correctly
   - Deduplicates results
   - Handles empty results

5. Use mocks for:
   - OpenAI API
   - Supabase client
   - Database queries

6. Test data:
   - Create realistic anecdote fixtures
   - Create mock embeddings

Generate complete test suites with good coverage.
```

### 7.2 Integration Tests

**Claude Code Prompt**:
```
Create integration tests for the RAG system that test the full pipeline.

Requirements:

1. File: tests/integration/rag-pipeline.test.ts

2. Setup:
   - Use test database
   - Create test project and anecdotes
   - Real embedding calls (or very good mocks)

3. Test scenarios:

   a. Full ingestion pipeline
      - Create anecdote
      - Ingest it
      - Verify chunks in database
      - Verify embeddings stored
   
   b. Retrieval accuracy
      - Ingest multiple anecdotes
      - Search for specific theme
      - Verify relevant anecdotes returned
      - Verify irrelevant anecdotes excluded
   
   c. Book compilation
      - Ingest 5 test anecdotes
      - Run compilation
      - Verify output includes all anecdotes
      - Verify chapters are coherent
   
   d. Update/delete handling
      - Ingest anecdote
      - Update anecdote
      - Re-ingest
      - Verify old chunks deleted
      - Verify new chunks present

4. Cleanup after tests

Generate complete integration test suite.
```

### 7.3 Evaluation Metrics

**Claude Code Prompt**:
```
Create an evaluation framework to measure RAG system quality for StoryWeaver AI.

Requirements:

1. File: lib/evaluation/rag-evaluator.ts

2. Metrics to measure:

   a. Retrieval Quality
      - Precision: % of retrieved chunks that are relevant
      - Recall: % of relevant chunks that were retrieved
      - MRR (Mean Reciprocal Rank): Position of first relevant result
   
   b. Generation Quality
      - Coherence: Does the chapter flow well?
      - Completeness: Are all anecdotes included?
      - Consistency: Are facts consistent?
      - Voice preservation: Does it sound like the author?
   
   c. System Performance
      - Ingestion time per anecdote
      - Retrieval latency
      - Generation time per chapter
      - Token efficiency

3. Evaluation methods:

   a. Automated metrics:
      - Embedding similarity scores
      - Token usage
      - Time measurements
   
   b. LLM-as-judge:
      - Use Claude to rate coherence
      - Use Claude to check consistency
   
   c. Human evaluation (manual):
      - Rating interface
      - A/B comparisons

4. Create test dataset:
   - 10 diverse anecdotes
   - Known themes and connections
   - Expected retrieval results

5. Reporting:
   - Generate evaluation report
   - Track metrics over time
   - Identify improvement areas

Generate the evaluation framework with all metrics.
```

---

## 8. Advanced Optimizations

### 8.1 Hybrid Search

Combine keyword and semantic search for better results.

**Claude Code Prompt**:
```
Implement hybrid search for the StoryWeaver RAG system that combines BM25 keyword search with vector similarity search.

Requirements:

1. Add full-text search to chunks table:
   ```sql
   ALTER TABLE anecdote_chunks 
   ADD COLUMN fts tsvector 
   GENERATED ALWAYS AS (to_tsvector('english', chunk_text)) STORED;
   
   CREATE INDEX idx_chunks_fts ON anecdote_chunks USING GIN(fts);
   ```

2. Implement hybrid search function that:
   - Runs both BM25 and vector search
   - Normalizes scores
   - Combines with weighted average (e.g., 0.7 semantic + 0.3 keyword)
   - Returns merged, deduplicated results

3. Use cases:
   - When searching for specific names or places
   - When semantic search misses exact matches

Generate the hybrid search implementation.
```

### 8.2 Reranking

Use a reranker model to improve retrieval precision.

**Claude Code Prompt**:
```
Add reranking capability to the StoryWeaver RAG retrieval pipeline.

Requirements:

1. Options:
   - Cohere Rerank API
   - Cross-encoder model

2. Implementation:
   - Retrieve top 20 results with vector search
   - Rerank with query-document pairs
   - Return top 10 after reranking

3. When to use:
   - For chapter generation (high precision needed)
   - Not for quick searches (adds latency)

Generate reranking service and integration.
```

### 8.3 Query Expansion

Improve retrieval by expanding the query.

**Claude Code Prompt**:
```
Implement query expansion for the StoryWeaver RAG system.

Requirements:

1. Methods:
   - Synonym expansion
   - LLM-generated related terms
   - Hypothetical document embedding (HyDE)

2. HyDE approach:
   - Take user query
   - Ask LLM to write a hypothetical passage that would answer it
   - Embed that passage instead of the query
   - Better matches document style

3. Integration:
   - Add to RetrievalService
   - Make it optional (adds latency and cost)

Generate query expansion implementation.
```

### 8.4 Caching

Cache frequently accessed data for performance.

**Claude Code Prompt**:
```
Implement caching for the StoryWeaver RAG system.

Requirements:

1. What to cache:
   - Embeddings (they don't change)
   - Frequently accessed chunks
   - Search results (short TTL)

2. Cache implementation:
   - Use Redis or Upstash
   - In-memory cache for embeddings during ingestion

3. Cache invalidation:
   - When anecdote is updated
   - When chunks are deleted

4. Metrics:
   - Cache hit rate
   - Cache size
   - Memory usage

Generate caching layer implementation.
```

---

## 9. Troubleshooting Common Issues

### 9.1 Poor Retrieval Quality

**Symptoms**: Irrelevant chunks retrieved, missing relevant content

**Causes and Solutions**:

| Cause | Solution |
|-------|----------|
| Chunks too large | Reduce chunk size to 300-500 tokens |
| Chunks too small | Increase overlap, use larger chunks |
| Bad embeddings | Try different embedding model |
| Missing metadata | Add filters for time/people/themes |
| Query too vague | Use query expansion |

### 9.2 Incoherent Generation

**Symptoms**: Generated chapters don't flow well, feel disjointed

**Causes and Solutions**:

| Cause | Solution |
|-------|----------|
| Too many chunks | Reduce context, increase relevance threshold |
| Missing context | Retrieve more supporting context |
| Poor prompts | Improve prompt with explicit instructions |
| Wrong chunk types | Use refined content instead of raw |

### 9.3 Slow Performance

**Symptoms**: Ingestion or retrieval takes too long

**Causes and Solutions**:

| Cause | Solution |
|-------|----------|
| No batch embedding | Batch embed all chunks together |
| Missing indexes | Add vector and metadata indexes |
| Too many chunks | Increase chunk size |
| No caching | Add embedding cache |

### 9.4 Inconsistent Facts

**Symptoms**: Same person/place described differently

**Causes and Solutions**:

| Cause | Solution |
|-------|----------|
| Chunks lack context | Include more surrounding text |
| No consistency check | Add retrieval step to verify facts |
| Wrong chunks retrieved | Improve metadata filtering |

---

## 10. Resources and Further Learning

### 10.1 Tutorials and Courses

- **LangChain RAG Tutorial**: https://python.langchain.com/docs/tutorials/rag/
- **LlamaIndex Guide**: https://docs.llamaindex.ai/
- **Pinecone Learning Center**: https://www.pinecone.io/learn/
- **DeepLearning.AI RAG Course**: https://www.deeplearning.ai/short-courses/

### 10.2 Documentation

- **OpenAI Embeddings**: https://platform.openai.com/docs/guides/embeddings
- **PostgreSQL pgvector**: https://github.com/pgvector/pgvector
- **Anthropic Claude**: https://docs.anthropic.com/

### 10.3 Research Papers

- "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks" (Lewis et al., 2020)
- "Dense Passage Retrieval for Open-Domain Question Answering" (Karpukhin et al., 2020)

### 10.4 Tools to Explore

- **LangChain**: Framework for RAG applications
- **LlamaIndex**: Data framework for LLM applications
- **Haystack**: End-to-end NLP framework
- **txtai**: Semantic search platform

### 10.5 Community

- **r/LocalLLaMA**: Reddit community
- **LangChain Discord**: Active help community
- **Hugging Face Forums**: ML community

---

## Summary Checklist

### Learning Checklist

- [ ] Understand vector mathematics basics (2-3 hours)
- [ ] Learn about embeddings and models (3-4 hours)
- [ ] Study vector databases (4-5 hours)
- [ ] Master chunking strategies (2-3 hours)
- [ ] Learn retrieval methods (3-4 hours)
- [ ] Practice RAG prompt engineering (3-4 hours)
- [ ] Build a simple prototype (10-15 hours)

### Implementation Checklist

- [ ] Step 1: Set up pgvector in Supabase
- [ ] Step 2: Create embedding service
- [ ] Step 3: Create chunking service
- [ ] Step 4: Create vector store service
- [ ] Step 5: Create retrieval service
- [ ] Step 6: Create ingestion pipeline
- [ ] Step 7: Create generation service
- [ ] Step 8: Create book compiler orchestrator
- [ ] Step 9: Create API endpoints
- [ ] Step 10: Create background job processor
- [ ] Step 11: Integrate with anecdote lifecycle

### Testing Checklist

- [ ] Unit tests for all services
- [ ] Integration tests for pipeline
- [ ] Evaluation metrics implemented
- [ ] Performance benchmarks run

### Optimization Checklist

- [ ] Hybrid search (if needed)
- [ ] Reranking (if needed)
- [ ] Query expansion (if needed)
- [ ] Caching layer

---

## Estimated Timeline

| Week | Focus | Hours |
|------|-------|-------|
| 1 | Learning fundamentals | 15-20 |
| 2 | Learning advanced topics + simple prototype | 15-20 |
| 3 | Implementation Steps 1-5 | 20-25 |
| 4 | Implementation Steps 6-8 | 20-25 |
| 5 | Implementation Steps 9-11 + Testing | 20-25 |
| 6 | Optimization and polish | 15-20 |

**Total**: ~6 weeks, 100-135 hours

---

*This guide provides everything you need to build a production-quality RAG system for StoryWeaver AI. Start with the learning prerequisites, then work through the implementation steps systematically. Use the Claude Code prompts to generate each component, then integrate and test.*

**Version**: 1.0  
**Last Updated**: November 2025