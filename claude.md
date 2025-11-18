# StoryWeaver AI - Project Context

## Project Overview

**Product Name:** StoryWeaver AI
**Code Name:** AI Ghost Writer (AIGW)
**Purpose:** An AI-powered platform that helps users write books by collecting, processing, and compiling their anecdotes and memories into cohesive narratives.

## Problem Statement

Writing a book is both time-consuming and intellectually demanding. Not everyone has the skill or patience to complete an entire book on their own. Traditional ghostwriters vary in quality and the best ones are expensive. StoryWeaver AI democratizes book writing by providing an AI-powered ghostwriting assistant that helps users transform their memories and experiences into well-crafted books.

## Origin Story

The project was inspired by the creator's desire to write "Raising a Kid Scientist" - a book documenting scientific adventures, experiences, and choices that help parents nurture future scientists. This vision led to creating a tool that helps anyone bring their personal stories and knowledge to life through well-crafted books.

## Core Features

### 1. User Authentication & Project Management
- Google authentication for user sign-up and login
- Projects Dashboard displaying all book projects as cards
- Each project card shows title and short description
- Empty state prompts when no projects exist

### 2. Anecdote Collection System
- Chat-like interface for writing anecdotes (memories/stories)
- Support for rich media:
  - Text entries
  - Image uploads
  - Voice recordings (multiple per anecdote, chronologically arranged)
  - Video recordings
- Timeline tracking (flexible: specific dates, year ranges, or approximate periods)
- Functions as an anecdote journaling tool

### 3. AI Processing Pipeline

#### Stage 1: Initial Entry
- User writes and saves anecdote
- Data stored but not yet AI-processed

#### Stage 2: Locking & Analysis
- User presses "Lock" to initiate AI analysis
- AI analyzes text and uploaded media (images, audio, video)
- Generates contextual follow-up questions
- Iterative Q&A rounds (configurable number in project settings)
- User can stop early with "No More Questions" (to save credits)

#### Stage 3: Refinement
- User presses "Next" after Q&A completion
- AI compiles anecdote + questions + answers into cohesive narrative
- User reviews and provides feedback
- Iterative refinement until user is satisfied
- User marks "Complete Anecdote" when done

#### Stage 4: Summarization
- AI generates two summaries:
  - **500-word summary**: Full context, emotions, and setting
  - **100-word summary**: Quick overview for reference
- Summaries help maintain context during book compilation

### 4. Anecdote Editing & Regeneration
- Users can edit anecdotes at any time
- AI automatically regenerates processed version and summaries after edits

### 5. Book Compilation & Versioning
- "Compile Book" feature weaves anecdotes into structured manuscript
- Version control system (similar to Git commits)
- Each saved revision is timestamped and stored
- Users can:
  - Remove anecdotes
  - Add new ones
  - Recompile entire project
  - Review and proofread with comments
  - Edit copies directly
  - Rollback to previous versions

## Technical Architecture

### Database Schema

#### Anecdote Data Structure
```json
{
  "anecdote_id": "uuid",
  "date": "string",
  "raw_anecdote": "string",
  "questionnaire": [
    {
      "question": "string",
      "answer": "string"
    }
  ],
  "audio_recordings": ["list of URLs"],
  "video_recordings": ["list of URLs"],
  "images": ["list of URLs"],
  "ai_generated_final_anecdote": "string",
  "ai_generated_summary_500": "string",
  "ai_generated_summary_100": "string"
}
```

### Technology Stack (Planned)

#### Database Options (Under Consideration)
- **Current Preference:** Supabase (PostgreSQL-based)
  - Reasons: Affordability, simplicity, good for initial phase
- **Alternative:** MongoDB
- **Decision Status:** Leaning toward Supabase

#### Book Storage Strategy
- Consider storing compiled books as Git repositories
- Each chapter as separate text file
- Enables easy version tracking and rollback

### Authentication
- Google OAuth for user sign-up and login

## Screen Flow

1. **Projects Dashboard**
   - Displays project cards (title + description)
   - Empty state: "Create a Project" prompt

2. **Anecdotes List**
   - Accessed by clicking project card
   - Shows all anecdotes for the project
   - Empty state: "Create an Anecdote" prompt

3. **Anecdote Screen** (Write Your Anecdote)
   - Chat-like interface
   - Text input area
   - Media upload options (images, audio, video)
   - Timeline/date field (optional)
   - Action buttons: Save, Lock, Next, Complete

4. **Book Compilation View**
   - Full manuscript display
   - Comment/feedback functionality
   - Version history
   - Export options

## Future Enhancements

### Planned Features
- **Speech-to-Text Integration**: Automatic transcription of voice notes to text
- **Configurable Q&A Rounds**: Per-project settings for number of AI questioning rounds
- **Credit System**: Track and manage AI processing credits

### UX Improvements
- Real-time collaboration features
- Export formats (PDF, EPUB, DOCX)
- Template selection for different book genres
- AI writing style customization

## Development Guidelines

### Code Organization
- Follow modular architecture
- Separate concerns: authentication, anecdote management, AI processing, book compilation
- Design for scalability from the start

### AI Integration
- Use prompt engineering for:
  - Image analysis
  - Follow-up question generation
  - Anecdote refinement
  - Summary generation
  - Book compilation
- Implement error handling for AI service failures
- Design for credit/token management

### Data Management
- Ensure all media uploads are properly stored and referenced
- Implement efficient versioning system
- Design for data export and backup
- Consider GDPR compliance for user data

### Testing Strategy
- Test AI processing pipeline with various input types
- Validate version control system thoroughly
- Ensure media upload/retrieval works reliably
- Test edge cases (missing timelines, incomplete anecdotes, etc.)

## Project Status

**Current Phase:** Initial Setup
**Repository State:** New project, foundational planning complete

## Key User Journeys

### Journey 1: First-Time User
1. Sign up with Google
2. Create first project (e.g., "Raising a Kid Scientist")
3. Write first anecdote with details
4. Upload related images
5. Lock anecdote → Answer AI questions
6. Refine narrative → Complete anecdote
7. Repeat for more memories

### Journey 2: Book Compilation
1. Collect multiple anecdotes over time
2. Click "Compile Book"
3. Review AI-generated manuscript
4. Provide feedback and edits
5. Save version
6. Iterate until satisfied
7. Export final book

### Journey 3: Editing & Regeneration
1. Open existing anecdote
2. Make edits to text or media
3. System auto-regenerates AI content
4. Review new summaries
5. Recompile book if needed

## Credits & Cost Management

- Implement credit system for AI processing
- Allow users to stop Q&A rounds early to save credits
- Track usage per project and per user
- Consider pricing tiers based on usage

## Success Metrics

- Number of completed anecdotes per user
- Number of books compiled
- User retention and engagement
- Quality of AI-generated content (user satisfaction)
- Time saved compared to traditional ghostwriting

---

**Last Updated:** November 18, 2025
**Version:** 1.0
