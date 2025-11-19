# FEATURES.md - StoryWeaver AI Complete Feature Documentation

## Table of Contents

1. [Authentication & User Management](#1-authentication--user-management)
2. [User Settings & Preferences](#2-user-settings--preferences)
3. [Projects Management](#3-projects-management)
4. [Anecdotes Core System](#4-anecdotes-core-system)
5. [Media Management](#5-media-management)
6. [AI Processing Pipeline](#6-ai-processing-pipeline)
7. [Book Compilation System](#7-book-compilation-system)
8. [Version Control & History](#8-version-control--history)
9. [Export & Publishing](#9-export--publishing)
10. [Credits & Billing System](#10-credits--billing-system)
11. [Dashboard & Analytics](#11-dashboard--analytics)
12. [Search & Discovery](#12-search--discovery)
13. [Notifications System](#13-notifications-system)
14. [Collaboration Features](#14-collaboration-features)
15. [Accessibility Features](#15-accessibility-features)
16. [Mobile & Responsive Features](#16-mobile--responsive-features)
17. [Keyboard Shortcuts](#17-keyboard-shortcuts)
18. [Data Management](#18-data-management)
19. [Help & Support](#19-help--support)
20. [Admin Features](#20-admin-features)

---

## 1. Authentication & User Management

### 1.1 Google OAuth Sign-Up

**Description**: New users can create an account using their Google credentials, eliminating the need for password management.

**User Flow**:
1. User clicks "Sign Up with Google" button
2. Redirected to Google OAuth consent screen
3. User grants permission to access email and profile
4. Redirected back to StoryWeaver AI
5. User profile created automatically
6. Redirected to onboarding or dashboard

**Technical Details**:
- Uses Supabase Auth with Google provider
- Extracts email, name, and avatar from Google profile
- Creates entry in `users` table on first login
- Issues JWT token for session management
- Stores refresh token securely

**Data Captured**:
- Email address
- Display name
- Profile avatar URL
- Google user ID (for linking)

---

### 1.2 Google OAuth Sign-In

**Description**: Existing users can sign in using their Google account with one click.

**User Flow**:
1. User clicks "Sign In with Google"
2. Redirected to Google OAuth
3. If already authorized, auto-redirects back
4. Session created, user lands on dashboard

**Features**:
- Remember me functionality (extended session)
- Automatic session refresh
- Multiple device support
- Session invalidation on sign out

---

### 1.3 Sign Out

**Description**: Users can securely sign out from any device.

**User Flow**:
1. User clicks avatar/profile menu
2. Selects "Sign Out"
3. Confirmation dialog appears
4. Session terminated
5. Redirected to landing page

**Options**:
- Sign out from current device only
- Sign out from all devices
- Sign out and clear local data

---

### 1.4 Session Management

**Description**: Automatic handling of user sessions with security and convenience.

**Features**:
- JWT token with 1-hour expiry
- Automatic token refresh
- Session persistence across browser tabs
- Secure cookie storage
- CSRF protection
- Activity-based session extension

**Security**:
- Tokens invalidated on password change (if added later)
- IP-based session validation (optional)
- Suspicious activity detection

---

### 1.5 Account Deletion

**Description**: Users can permanently delete their account and all associated data.

**User Flow**:
1. Navigate to Settings → Account
2. Click "Delete Account"
3. Enter confirmation text
4. Review data to be deleted
5. Confirm deletion
6. Account and all data permanently removed

**Data Deleted**:
- User profile
- All projects
- All anecdotes
- All media files
- Credit history
- AI interaction logs

**Safeguards**:
- 7-day grace period before permanent deletion
- Email notification sent
- Option to cancel deletion within grace period
- Data export option before deletion

---

## 2. User Settings & Preferences

### 2.1 Profile Management

**Description**: Users can view and edit their profile information.

**Editable Fields**:
- Display name
- Profile avatar (upload or change)
- Bio/description (optional)
- Location (optional)
- Website/social links (optional)

**Features**:
- Image cropping for avatar
- Preview changes before saving
- Validation for all fields
- Success/error notifications

---

### 2.2 Account Settings

**Description**: Core account configuration options.

**Features**:
- View connected email address
- View account creation date
- View last login time
- Download account data
- Delete account
- View active sessions

---

### 2.3 Notification Preferences

**Description**: Control how and when notifications are received.

**Notification Types**:
- Email notifications
- In-app notifications
- Browser push notifications (optional)

**Configurable Events**:
- AI processing complete
- Book compilation ready
- Low credit balance warning
- Weekly progress summary
- Tips and feature updates
- Security alerts

---

### 2.4 Writing Preferences

**Description**: Default settings for writing and AI interactions.

**Configurable Options**:
- Default tone style (conversational, formal, literary, etc.)
- Preferred question count per round
- Auto-save interval
- Editor font and size
- Dark/light mode
- Spell check on/off
- Grammar suggestions on/off

---

### 2.5 Privacy Settings

**Description**: Control data usage and visibility.

**Options**:
- Allow anonymous usage analytics
- Allow AI training on content (opt-in)
- Public profile visibility (for future social features)
- Search engine indexing (for published books)

---

### 2.6 Language & Localization

**Description**: Set preferred language and regional settings.

**Options**:
- Interface language
- Date format (MM/DD/YYYY, DD/MM/YYYY, etc.)
- Time format (12-hour, 24-hour)
- First day of week
- Number formatting

---

### 2.7 Accessibility Settings

**Description**: Customize the interface for accessibility needs.

**Options**:
- High contrast mode
- Reduced motion
- Screen reader optimizations
- Keyboard navigation hints
- Font size scaling
- Color blind friendly palettes

---

## 3. Projects Management

### 3.1 Projects Dashboard

**Description**: Central hub displaying all user's book projects.

**Features**:
- Grid view of project cards
- List view option
- Project thumbnail/cover image
- Project title and description preview
- Status indicator (active, archived, completed)
- Anecdote count badge
- Word count display
- Last modified date
- Quick actions menu

**Interactions**:
- Click to open project
- Right-click context menu
- Drag to reorder (if sort by custom)
- Bulk selection for actions

---

### 3.2 Create New Project

**Description**: Multi-step wizard for creating a new book project.

**Step 1 - Basic Information**:
- Project title (required)
- Short description
- Cover image upload (optional)

**Step 2 - Book Details**:
- Genre selection (memoir, biography, self-help, etc.)
- Target audience (children, young adult, adult, etc.)
- Estimated length (short, medium, long)

**Step 3 - Writing Style**:
- Tone selection (conversational, formal, humorous, dramatic, etc.)
- Point of view (first person, third person)
- Tense preference (past, present)

**Step 4 - AI Settings**:
- Question rounds (1-5)
- Question depth (basic, detailed, comprehensive)
- Auto-summarization on/off

**Validation**:
- Title required, 3-100 characters
- Description max 500 characters
- At least one setting configured

---

### 3.3 Project Detail View

**Description**: Main workspace for a specific book project.

**Layout Components**:
- Project header with title and actions
- Tab navigation (Anecdotes, Compile, Timeline, Settings)
- Statistics sidebar
- Quick actions bar

**Statistics Displayed**:
- Total anecdotes
- Completed anecdotes
- Total word count
- Estimated pages
- Time span covered
- Days since last edit

---

### 3.4 Project Settings

**Description**: Configure project-specific options.

**Editable Settings**:
- Title and description
- Cover image
- Genre and audience
- Tone and style
- AI processing settings
- Collaborator access (future)
- Archive project
- Delete project

---

### 3.5 Project Search & Filter

**Description**: Find projects quickly with search and filters.

**Search**:
- Search by project title
- Search by description content
- Instant results as you type

**Filters**:
- Status (all, active, archived, completed)
- Genre
- Date created
- Date modified
- Word count range

**Sort Options**:
- Alphabetical (A-Z, Z-A)
- Date created (newest, oldest)
- Date modified (recent first)
- Word count (highest, lowest)
- Custom order

---

### 3.6 Project Duplication

**Description**: Create a copy of an existing project.

**Options**:
- Copy project settings only
- Copy settings and anecdotes
- Copy everything including compilations

**Use Cases**:
- Create variation of a book
- Start new project with same settings
- Backup before major changes

---

### 3.7 Project Archive

**Description**: Archive completed or inactive projects.

**Features**:
- Archived projects hidden from main view
- Accessible via "Archived" filter
- Can be unarchived anytime
- No deletion of data
- Archived projects don't count toward limits (if any)

---

### 3.8 Project Deletion

**Description**: Permanently delete a project and all its content.

**Safeguards**:
- Confirmation dialog with project name
- List of content to be deleted
- Type project name to confirm
- 30-day recovery period (soft delete)
- Email confirmation sent

**What Gets Deleted**:
- Project metadata
- All anecdotes
- All media files
- All compilations
- All versions

---

### 3.9 Project Export

**Description**: Export entire project data.

**Export Formats**:
- JSON (complete data backup)
- ZIP (with all media files)
- Markdown (anecdotes only)

**Includes**:
- Project settings
- All anecdotes (raw and refined)
- Q&A history
- Summaries
- Media files
- Compilation history

---

### 3.10 Project Import

**Description**: Import a previously exported project.

**Supported Formats**:
- StoryWeaver JSON export
- Plain text files (creates anecdotes)
- Markdown files

**Options**:
- Import as new project
- Merge with existing project
- Replace existing project

---

## 4. Anecdotes Core System

### 4.1 Anecdotes List View

**Description**: View all anecdotes within a project.

**Display Options**:
- Card grid view
- Compact list view
- Timeline view (chronological)

**Card Information**:
- Anecdote title (or first line preview)
- Time frame
- Status badge
- Word count
- Thumbnail (if image attached)
- Last edited date

**Interactions**:
- Click to open editor
- Drag to reorder
- Multi-select for bulk actions
- Quick status change

---

### 4.2 Create New Anecdote

**Description**: Start a new memory entry.

**Creation Methods**:
- Click "New Anecdote" button
- Keyboard shortcut (Ctrl/Cmd + N)
- Quick add from timeline view
- Voice recording start

**Initial Fields**:
- Title (optional, auto-generated if blank)
- Opens directly to editor

---

### 4.3 Anecdote Editor

**Description**: Rich text editor for writing anecdotes.

**Editor Features**:
- Rich text formatting (bold, italic, underline)
- Headings (H1, H2, H3)
- Bullet and numbered lists
- Block quotes
- Links
- Horizontal dividers
- Undo/redo
- Word count
- Character count
- Reading time estimate

**Editor Modes**:
- Standard mode (default)
- Focus mode (distraction-free)
- Markdown mode (for power users)

**Toolbar**:
- Formatting buttons
- Media insert button
- Voice record button
- Save button
- Preview button
- Settings menu

---

### 4.4 Auto-Save

**Description**: Automatic saving of anecdote content.

**Behavior**:
- Saves every 30 seconds (configurable)
- Saves on significant pause in typing
- Saves when switching tabs/windows
- Visual indicator when saving
- Confirmation when saved
- Conflict resolution if edited elsewhere

**Manual Save**:
- Ctrl/Cmd + S
- Save button in toolbar
- Save and close option

---

### 4.5 Time Frame Picker

**Description**: Set when the anecdote took place.

**Input Options**:
- Specific date
- Date range (start to end)
- Month and year only
- Year only
- Decade (e.g., "early 1990s")
- Approximate (e.g., "around age 10")
- Season + year (e.g., "Summer 1985")
- Leave blank if unknown

**Features**:
- Calendar picker
- Quick presets (last year, childhood, etc.)
- Age calculator (converts age to year based on birth year)
- Validates logical ranges

---

### 4.6 Location Tagging

**Description**: Add location context to anecdotes.

**Features**:
- Free text location input
- Location autocomplete (via API)
- Map pin selection (optional)
- Multiple locations per anecdote
- Location history for quick selection

---

### 4.7 People Tagging

**Description**: Tag people mentioned in the anecdote.

**Features**:
- Add names as tags
- Relationship labels (mother, friend, teacher)
- Auto-suggest from previous anecdotes
- People index across project
- Link to other anecdotes featuring same person

---

### 4.8 Theme/Topic Tagging

**Description**: Categorize anecdotes by theme.

**Features**:
- Predefined theme suggestions
- Custom theme creation
- Multiple themes per anecdote
- Color coding for themes
- Filter anecdotes by theme
- AI-suggested themes

**Common Themes**:
- Childhood
- Education
- Career
- Family
- Travel
- Achievement
- Challenge
- Relationship
- Discovery
- Loss

---

### 4.9 Anecdote Preview

**Description**: Preview anecdote in reading format.

**Features**:
- Clean reading view
- Displays all metadata
- Shows attached media
- Estimated reading time
- Print-friendly view
- Share preview link (future)

---

### 4.10 Anecdote Status System

**Description**: Track anecdote through processing stages.

**Statuses**:

| Status | Description | Icon | Color |
|--------|-------------|------|-------|
| Draft | Initial writing, not yet processed | ✏️ | Gray |
| Locked | Submitted for AI processing | 🔒 | Blue |
| Questioning | AI questions generated, awaiting answers | ❓ | Yellow |
| Refining | AI generating refined version | ⚙️ | Orange |
| Review | Refined version ready for review | 👁️ | Purple |
| Complete | Finalized and ready for compilation | ✅ | Green |

**Transitions**:
- Draft → Locked (user clicks Lock)
- Locked → Questioning (AI generates questions)
- Questioning → Refining (user completes Q&A)
- Refining → Review (AI generates refined content)
- Review → Complete (user approves)
- Any → Draft (user unlocks for editing)

---

### 4.11 Lock Anecdote

**Description**: Submit anecdote for AI processing.

**Pre-Lock Checklist**:
- Content has minimum word count (50 words)
- User confirms ready for processing
- Credit cost displayed
- Estimated processing time shown

**What Happens**:
- Anecdote becomes read-only
- Sent to AI processing queue
- User notified when questions ready
- Credits deducted

---

### 4.12 Unlock Anecdote

**Description**: Revert anecdote to draft for editing.

**Behavior**:
- Returns to draft status
- Preserves AI-generated content
- Allows editing original content
- Q&A history preserved
- Can re-lock when ready

**Warning**:
- Any pending AI processing canceled
- Credits not refunded for completed steps

---

### 4.13 Anecdote Duplication

**Description**: Create a copy of an anecdote.

**Options**:
- Copy to same project
- Copy to different project
- Include media attachments
- Include Q&A history
- Reset status to draft

---

### 4.14 Anecdote Deletion

**Description**: Delete an anecdote permanently.

**Safeguards**:
- Confirmation dialog
- Shows what will be deleted
- Cannot delete if included in active compilation
- Soft delete with 30-day recovery

---

### 4.15 Anecdote Reordering

**Description**: Change the order of anecdotes in the project.

**Methods**:
- Drag and drop in list view
- Set sort order number manually
- Auto-sort by time frame
- Auto-sort by status
- Custom sort and save

---

### 4.16 Anecdote Connections

**Description**: Link related anecdotes together.

**Features**:
- Manually link anecdotes
- AI-suggested connections
- View connection graph
- Navigate between connected anecdotes
- Connection labels (sequel, related, contrast)

---

### 4.17 Anecdote Word Goals

**Description**: Set and track word count goals.

**Features**:
- Set target word count
- Progress bar visualization
- Notifications when goal reached
- Project-wide goal tracking

---

## 5. Media Management

### 5.1 Image Upload

**Description**: Attach images to anecdotes.

**Supported Formats**:
- JPEG
- PNG
- GIF
- WebP
- HEIC (converted to JPEG)

**Upload Methods**:
- Drag and drop
- Click to browse
- Paste from clipboard
- Mobile camera capture

**Limits**:
- Max file size: 10MB
- Max images per anecdote: 20
- Total storage per user: varies by plan

**Processing**:
- Auto-rotation based on EXIF
- Thumbnail generation
- Compression for web
- Original preserved

---

### 5.2 Image Gallery

**Description**: View and manage attached images.

**Features**:
- Thumbnail grid
- Full-size lightbox
- Image captions
- Reorder images
- Delete images
- Download original
- Zoom and pan

---

### 5.3 Image Analysis (AI)

**Description**: AI extracts information from images.

**Analysis Includes**:
- Scene description
- People detection
- Text extraction (OCR)
- Object identification
- Emotional tone
- Historical context clues
- Location identification

**Usage**:
- Informs question generation
- Adds context for refinement
- User can view AI descriptions
- User can correct/edit descriptions

---

### 5.4 Audio Recording

**Description**: Record voice notes within anecdotes.

**Features**:
- In-browser recording
- Record/pause/resume/stop
- Waveform visualization
- Recording timer
- Multiple recordings per anecdote
- Playback before saving

**Technical**:
- WebM/Opus format
- Automatic gain control
- Noise suppression
- Echo cancellation

---

### 5.5 Audio Upload

**Description**: Upload pre-recorded audio files.

**Supported Formats**:
- MP3
- WAV
- M4A
- OGG
- WebM

**Limits**:
- Max file size: 100MB
- Max duration: 30 minutes per file
- Max files per anecdote: 10

---

### 5.6 Audio Player

**Description**: Playback audio recordings.

**Features**:
- Play/pause
- Seek bar
- Playback speed (0.5x, 1x, 1.5x, 2x)
- Volume control
- Download audio
- Timestamp markers

---

### 5.7 Audio Transcription (AI)

**Description**: Convert audio to text automatically.

**Features**:
- Automatic transcription on upload
- Speaker diarization (multiple speakers)
- Timestamp alignment
- Punctuation and formatting
- Edit transcription
- Use transcription in anecdote

**Credits**: Charged per minute of audio

---

### 5.8 Video Upload

**Description**: Attach video files to anecdotes.

**Supported Formats**:
- MP4
- MOV
- WebM
- AVI

**Limits**:
- Max file size: 500MB
- Max duration: 10 minutes
- Max videos per anecdote: 5

---

### 5.9 Video Player

**Description**: Playback video attachments.

**Features**:
- Standard video controls
- Full-screen mode
- Picture-in-picture
- Playback speed control
- Thumbnail preview on hover
- Subtitle support (if provided)

---

### 5.10 Video Transcription (AI)

**Description**: Extract and transcribe audio from video.

**Features**:
- Audio extraction
- Full transcription
- Visual scene descriptions
- Key moment detection
- Edit and use transcription

---

### 5.11 Document Upload

**Description**: Attach documents for reference.

**Supported Formats**:
- PDF
- DOC/DOCX
- TXT
- RTF

**Features**:
- Document preview
- Text extraction
- Download original
- AI analysis of content

---

### 5.12 Media Organization

**Description**: Organize and manage all media.

**Features**:
- Sort by date, type, size
- Filter by media type
- Search media by caption
- Bulk delete
- Move media between anecdotes
- Storage usage display

---

### 5.13 Media Captions

**Description**: Add descriptions to media files.

**Features**:
- Free text caption
- AI-generated caption suggestion
- Caption displayed in gallery
- Captions searchable
- Captions included in export

---

## 6. AI Processing Pipeline

### 6.1 Question Generation

**Description**: AI analyzes anecdote and generates follow-up questions.

**Inputs**:
- Raw anecdote text
- Image descriptions
- Audio transcriptions
- Metadata (time, location, people)

**Output**:
- 3-5 targeted questions per round
- Questions aim to extract:
  - Sensory details
  - Emotional context
  - Dialogue and specifics
  - Significance and meaning
  - Narrative gaps

**Question Types**:
- Open-ended (describe, explain)
- Specific (what exactly, who specifically)
- Sensory (what did you see/hear/feel)
- Emotional (how did you feel)
- Reflective (why was this important)

**Credits**: 2 credits per question round

---

### 6.2 Question Display & Answering

**Description**: Interface for viewing and answering AI questions.

**Features**:
- Questions displayed one at a time or all at once
- Rich text answer input
- Voice recording for answers
- Skip question option
- Mark question as not applicable
- Save progress
- Submit when complete

**Guidance**:
- Tips for good answers
- Example answers
- Minimum/suggested answer length
- Progress indicator

---

### 6.3 Multiple Question Rounds

**Description**: Iterative questioning for deeper detail.

**Behavior**:
- After answering, AI analyzes responses
- Generates follow-up questions based on answers
- Continues for configured number of rounds
- Each round digs deeper

**Configuration**:
- 1-5 rounds (default: 3)
- Can stop early with "No More Questions"
- Fewer rounds = lower credit cost

---

### 6.4 Stop Questioning

**Description**: User can end Q&A early.

**Trigger**:
- "No More Questions" button
- Skip remaining rounds

**Behavior**:
- Proceeds to refinement with collected answers
- No charge for unused rounds
- Can always add more later by unlocking

---

### 6.5 Content Refinement

**Description**: AI compiles all information into polished narrative.

**Inputs**:
- Original anecdote
- All questions and answers
- Media descriptions and transcriptions
- Project tone settings

**Output**:
- Polished narrative (500-2000 words)
- Maintains author's voice
- Incorporates all details
- Smooth narrative flow
- Vivid sensory language

**Credits**: 5 credits per refinement

---

### 6.6 Refinement Review

**Description**: Review and approve AI-refined content.

**Features**:
- Side-by-side view (original vs refined)
- Unified diff view
- Inline highlighting of additions
- Accept/reject sections
- Edit refined version directly

---

### 6.7 Refinement Feedback

**Description**: Provide feedback for re-generation.

**Feedback Options**:
- Free text feedback
- Specific section feedback
- Tone adjustment requests
- Length adjustment (shorter/longer)
- Style preferences

**Example Feedback**:
- "Make the opening more dramatic"
- "Include more dialogue"
- "Tone down the emotional language"
- "Expand the part about my grandmother"

---

### 6.8 Regenerate Refinement

**Description**: Generate new version based on feedback.

**Behavior**:
- Incorporates user feedback
- Maintains previous good elements
- Generates fresh refined version
- Iteration count tracked
- Credits charged per regeneration

---

### 6.9 Summarization

**Description**: AI generates summaries of finalized anecdote.

**Outputs**:

**500-Word Summary**:
- Comprehensive overview
- Preserves key details
- Maintains emotional tone
- Used for book compilation context

**100-Word Summary**:
- Quick reference
- Core event only
- Key significance
- Used for timeline view and search

**Credits**: 2 credits for both summaries

---

### 6.10 Theme Extraction

**Description**: AI identifies themes in anecdote.

**Output**:
- 3-5 suggested themes
- Confidence score per theme
- User can accept/reject/add themes
- Themes used for organization

---

### 6.11 Connection Suggestions

**Description**: AI identifies related anecdotes.

**Analysis**:
- Shared people
- Similar time periods
- Common themes
- Narrative continuity
- Contrasting perspectives

**Output**:
- Suggested connections with reasons
- User can accept/reject
- Forms connection graph

---

### 6.12 AI Processing Queue

**Description**: Background processing system.

**Features**:
- Queue management
- Priority processing (paid users)
- Progress indicators
- Estimated wait time
- Notifications on completion
- Retry on failure

---

### 6.13 AI Error Handling

**Description**: Graceful handling of AI failures.

**Scenarios**:
- API timeout
- Rate limiting
- Content moderation flags
- Invalid responses

**User Experience**:
- Clear error messages
- Automatic retry option
- Manual retry button
- Credit refund for failures
- Support contact for persistent issues

---

### 6.14 AI Model Selection (Future)

**Description**: Choose AI model for processing.

**Options**:
- Standard (balanced speed/quality)
- Premium (highest quality)
- Fast (quick but less detailed)

**Trade-offs**:
- Different credit costs
- Different processing times
- Different output quality

---

## 7. Book Compilation System

### 7.1 Compilation Dashboard

**Description**: Main interface for book compilation.

**Displays**:
- Compilation status
- Included anecdotes
- Word count
- Page estimate
- Chapter structure preview
- Previous versions

---

### 7.2 Anecdote Selection

**Description**: Choose which anecdotes to include.

**Features**:
- Checkbox selection
- Select all / deselect all
- Filter by status (show only completed)
- Filter by theme
- Filter by time period
- Preview anecdote before selecting
- Drag to reorder selection

**Validation**:
- Minimum anecdotes required (3)
- All selected must be complete
- Warning for missing time periods

---

### 7.3 Chapter Organization

**Description**: Structure anecdotes into chapters.

**Methods**:
- Automatic (AI suggests structure)
- Chronological (by time frame)
- Thematic (by theme)
- Manual (user defines)

**Features**:
- Create chapter titles
- Assign anecdotes to chapters
- Reorder chapters
- Reorder within chapters
- Chapter descriptions

---

### 7.4 Compilation Settings

**Description**: Configure how book is generated.

**Options**:
- Include introduction (yes/no)
- Include conclusion (yes/no)
- Transition style (seamless, chapter breaks, dated entries)
- Include anecdote titles as headers
- Include time stamps
- Include location tags
- Include photos (future)

---

### 7.5 Start Compilation

**Description**: Trigger AI book generation.

**Pre-Compilation**:
- Review settings
- Confirm anecdote selection
- See credit cost
- Estimated processing time

**Processing**:
- Progress indicator
- Stage updates (structuring, writing, finalizing)
- Cancel option (partial refund)

**Credits**: 20 credits per compilation

---

### 7.6 Compilation Generation

**Description**: AI creates complete manuscript.

**Process**:
1. Load all anecdote summaries and content
2. Analyze for narrative arc
3. Generate chapter structure
4. Write introduction
5. Generate transitions between anecdotes
6. Write conclusion
7. Create table of contents
8. Format manuscript

**Output**:
- Complete manuscript text
- Chapter markers
- Table of contents
- Word count
- Page count estimate

---

### 7.7 Manuscript Viewer

**Description**: Read and review generated manuscript.

**Features**:
- Clean reading interface
- Chapter navigation sidebar
- Page navigation
- Reading progress tracker
- Scroll position memory
- Font size adjustment
- Theme options (sepia, dark, light)

---

### 7.8 Inline Editing

**Description**: Edit manuscript directly.

**Features**:
- Click to edit any section
- Rich text editing
- Track changes (optional)
- Auto-save edits
- Version created on significant changes
- Revert section to original

---

### 7.9 Chapter Editing

**Description**: Modify chapter structure.

**Features**:
- Rename chapters
- Merge chapters
- Split chapters
- Reorder chapters
- Delete chapters
- Add new chapter manually

---

### 7.10 Feedback System

**Description**: Provide feedback for regeneration.

**Features**:
- Highlight text and comment
- Section-level feedback
- Overall feedback
- Request specific changes
- Rate sections (helpful for AI learning)

---

### 7.11 Partial Regeneration

**Description**: Regenerate specific sections.

**Options**:
- Regenerate introduction
- Regenerate specific chapter
- Regenerate transitions
- Regenerate conclusion

**Credits**: Partial credit cost based on scope

---

### 7.12 Full Regeneration

**Description**: Generate entirely new manuscript.

**Options**:
- With same settings
- With modified settings
- With different anecdote selection

**Behavior**:
- Previous version preserved
- New version created
- Full credit cost

---

## 8. Version Control & History

### 8.1 Version Creation

**Description**: Save manuscript versions.

**Automatic Versions**:
- On initial compilation
- On significant edits
- On regeneration

**Manual Versions**:
- "Save as New Version" button
- Commit message input
- Version name/label

---

### 8.2 Version History

**Description**: View all manuscript versions.

**Display**:
- Version list (newest first)
- Version name/number
- Commit message
- Date created
- Word count
- Author (for future collaboration)

**Interactions**:
- Click to preview
- Compare versions
- Restore version
- Delete version
- Download version

---

### 8.3 Version Comparison

**Description**: Compare two versions side-by-side.

**Features**:
- Side-by-side view
- Unified diff view
- Highlight additions (green)
- Highlight deletions (red)
- Chapter-by-chapter comparison
- Statistics (words added/removed)

---

### 8.4 Version Restore

**Description**: Revert to previous version.

**Options**:
- Restore as current (overwrites)
- Restore as new version (preserves both)
- Partial restore (specific chapters)

**Safeguards**:
- Confirmation dialog
- Current version auto-saved before restore

---

### 8.5 Version Branching (Future)

**Description**: Create parallel versions.

**Use Cases**:
- Different endings
- Alternative structures
- Audience variations

---

### 8.6 Anecdote Version History

**Description**: Version control for individual anecdotes.

**Tracked**:
- Raw content versions
- Refined content versions
- Q&A history

**Features**:
- View previous versions
- Restore previous version
- Compare versions

---

## 9. Export & Publishing

### 9.1 Export to DOCX

**Description**: Download manuscript as Word document.

**Features**:
- Formatted document
- Chapter headings
- Table of contents
- Page breaks
- Customizable styles
- Font selection
- Margin settings

**Use Case**: Further editing in Word, sharing with editors

---

### 9.2 Export to PDF

**Description**: Download manuscript as PDF.

**Features**:
- Print-ready format
- Page numbers
- Headers/footers
- Multiple page sizes (letter, A4, etc.)
- PDF metadata

**Options**:
- Include cover page
- Include table of contents
- Include page numbers
- Font embedding

---

### 9.3 Export to EPUB (Future)

**Description**: Download as e-book format.

**Features**:
- Standard EPUB format
- Chapter navigation
- Reflowable text
- Compatible with e-readers

---

### 9.4 Export to Markdown

**Description**: Download as plain Markdown.

**Use Case**: Further processing, GitHub storage, static sites

---

### 9.5 Export to Plain Text

**Description**: Download as simple text file.

**Use Case**: Maximum compatibility, word processors

---

### 9.6 Print Preview

**Description**: Preview how manuscript will print.

**Features**:
- Page layout view
- See page breaks
- Print settings
- Direct print option

---

### 9.7 Cover Page Generation (Future)

**Description**: Create book cover.

**Features**:
- Template selection
- Upload custom image
- Title and author placement
- AI-generated cover suggestions

---

### 9.8 ISBN Assignment (Future)

**Description**: Obtain ISBN for publishing.

**Integration**: Third-party ISBN services

---

### 9.9 Print-on-Demand Integration (Future)

**Description**: Order printed copies.

**Partners**: Amazon KDP, IngramSpark, Lulu

**Features**:
- Format manuscript for printing
- Select print options
- Order author copies
- Publish for sale

---

### 9.10 Digital Publishing (Future)

**Description**: Publish e-book to stores.

**Platforms**: Amazon Kindle, Apple Books, etc.

---

## 10. Credits & Billing System

### 10.1 Credits Dashboard

**Description**: View and manage credits.

**Displays**:
- Current balance
- Recent transactions
- Usage chart
- Purchase options

---

### 10.2 Credit Balance Display

**Description**: Always-visible credit indicator.

**Location**: Header, visible on all pages

**Features**:
- Current balance
- Warning at low balance
- Quick purchase link

---

### 10.3 Transaction History

**Description**: Detailed credit transaction log.

**Fields**:
- Date/time
- Transaction type
- Amount (+/-)
- Description
- Balance after
- Related item (anecdote/compilation)

**Filters**:
- Date range
- Transaction type
- Amount range

---

### 10.4 Usage Analytics

**Description**: Understand credit usage patterns.

**Charts**:
- Usage over time
- Usage by type
- Average per anecdote
- Average per compilation

---

### 10.5 Credit Costs

**Description**: Transparent pricing for operations.

| Operation | Credits |
|-----------|---------|
| Question generation (per round) | 2 |
| Content refinement | 5 |
| Summarization | 2 |
| Image analysis (per image) | 1 |
| Audio transcription (per minute) | 3 |
| Book compilation | 20 |
| Regeneration (refinement) | 3 |
| Regeneration (compilation) | 15 |

---

### 10.6 Cost Preview

**Description**: See credit cost before operations.

**Displayed For**:
- Lock anecdote
- Start compilation
- Any AI operation

**Shows**:
- Estimated cost
- Current balance
- Balance after
- Warning if insufficient

---

### 10.7 Insufficient Credits Handling

**Description**: Graceful handling when credits run out.

**Behavior**:
- Operation blocked
- Clear message
- Link to purchase
- Save work in progress
- Resume after purchase

---

### 10.8 Credit Purchase

**Description**: Buy additional credits.

**Packages**:
- 100 credits - $4.99
- 300 credits - $12.99 (save 13%)
- 500 credits - $19.99 (save 20%)
- 1000 credits - $34.99 (save 30%)

**Payment Methods**:
- Credit/debit card
- PayPal (future)

---

### 10.9 Subscription Plans (Future)

**Description**: Monthly credit subscriptions.

**Plans**:
- Starter: 500 credits/month - $9.99
- Writer: 1500 credits/month - $24.99
- Author: 4000 credits/month - $49.99

**Benefits**:
- Lower per-credit cost
- Priority processing
- Rollover unused credits (limited)

---

### 10.10 Free Credits

**Description**: Credits given without purchase.

**Sources**:
- Sign-up bonus: 100 credits
- Referral bonus: 50 credits
- Promotional offers
- Compensation for issues

---

### 10.11 Refunds

**Description**: Credit refunds for failures.

**Automatic Refunds**:
- AI processing failure
- Timeout errors
- System errors

**Manual Refund Request**:
- Contact support
- Explain issue
- Review and refund

---

### 10.12 Receipts & Invoices

**Description**: Documentation for purchases.

**Features**:
- Email receipt on purchase
- Download PDF invoice
- Transaction ID
- Payment details
- Tax information

---

## 11. Dashboard & Analytics

### 11.1 Home Dashboard

**Description**: Overview of all activity.

**Widgets**:
- Recent projects
- Recently edited anecdotes
- Quick actions
- Usage statistics
- Tips and suggestions

---

### 11.2 Writing Statistics

**Description**: Track writing progress.

**Metrics**:
- Total words written
- Words this week/month
- Anecdotes completed
- Books compiled
- Writing streak

---

### 11.3 Progress Tracking

**Description**: Visualize project progress.

**Charts**:
- Anecdote completion funnel
- Words over time
- Activity heatmap
- Time spent writing

---

### 11.4 Achievement System (Future)

**Description**: Gamification elements.

**Badges**:
- First anecdote
- First book compiled
- 10,000 words written
- 7-day streak
- 100 questions answered

---

### 11.5 Project Analytics

**Description**: Per-project statistics.

**Metrics**:
- Word count over time
- Anecdote completion rate
- Time distribution of anecdotes
- Theme distribution
- AI usage statistics

---

## 12. Search & Discovery

### 12.1 Global Search

**Description**: Search across all content.

**Searchable**:
- Project titles and descriptions
- Anecdote content (raw and refined)
- Q&A content
- Media captions
- Tags and themes

**Features**:
- Instant results
- Result previews
- Filter by type
- Sort by relevance/date

---

### 12.2 Project Search

**Description**: Search within a project.

**Searchable**:
- Anecdote content
- Q&A
- Summaries
- Media captions

---

### 12.3 Advanced Filters

**Description**: Complex filtering options.

**Filters**:
- Date range
- Status
- Theme
- People mentioned
- Location
- Word count range
- Has media

---

### 12.4 Timeline View

**Description**: Chronological visualization.

**Features**:
- Horizontal timeline
- Anecdotes as points
- Zoom in/out
- Filter by project
- Click to open anecdote
- See gaps in timeline

---

### 12.5 Tag Cloud

**Description**: Visual tag representation.

**Features**:
- Size by frequency
- Click to filter
- Across project or all projects

---

## 13. Notifications System

### 13.1 In-App Notifications

**Description**: Notifications within the app.

**Types**:
- AI processing complete
- Low credit balance
- System announcements
- Tips and suggestions

**Features**:
- Notification bell icon
- Unread count badge
- Mark as read
- Clear all
- Click to navigate

---

### 13.2 Email Notifications

**Description**: Notifications via email.

**Triggers**:
- AI processing complete
- Book compilation ready
- Low credit balance
- Weekly summary
- Security alerts

**Features**:
- Unsubscribe link
- Frequency settings
- HTML formatted

---

### 13.3 Browser Push Notifications (Future)

**Description**: Desktop notifications.

**Features**:
- Request permission
- Enable/disable per type
- Click to open app

---

### 13.4 Weekly Summary Email

**Description**: Weekly progress digest.

**Contents**:
- Words written this week
- Anecdotes completed
- Suggestions for next steps
- Tips and features

---

## 14. Collaboration Features (Future)

### 14.1 Invite Collaborator

**Description**: Share project with others.

**Roles**:
- Viewer: Read only
- Commenter: Add comments
- Editor: Edit content
- Admin: Full access

---

### 14.2 Real-Time Collaboration

**Description**: Multiple editors simultaneously.

**Features**:
- See other cursors
- Live updates
- Presence indicators
- Conflict resolution

---

### 14.3 Comments System

**Description**: Leave comments on content.

**Features**:
- Inline comments
- Comment threads
- Resolve comments
- Mention collaborators

---

### 14.4 Activity Feed

**Description**: See all project activity.

**Shows**:
- Who did what
- When
- Diff/preview

---

### 14.5 Share for Review

**Description**: Share read-only link.

**Features**:
- Generate unique link
- Optional password
- Expiration date
- View count tracking

---

## 15. Accessibility Features

### 15.1 Keyboard Navigation

**Description**: Full keyboard accessibility.

**Features**:
- Tab navigation
- Focus indicators
- Skip links
- Keyboard shortcuts

---

### 15.2 Screen Reader Support

**Description**: Optimized for screen readers.

**Features**:
- ARIA labels
- Semantic HTML
- Alt text for images
- Live regions for updates

---

### 15.3 High Contrast Mode

**Description**: Enhanced visual contrast.

**Features**:
- High contrast colors
- Clearer borders
- Larger text option

---

### 15.4 Reduced Motion

**Description**: Minimize animations.

**Features**:
- Respect prefers-reduced-motion
- Disable transitions
- Static alternatives

---

### 15.5 Text Scaling

**Description**: Adjust text size.

**Features**:
- Zoom support
- Font size settings
- Responsive layout

---

## 16. Mobile & Responsive Features

### 16.1 Responsive Design

**Description**: Adapts to all screen sizes.

**Breakpoints**:
- Mobile: < 640px
- Tablet: 640px - 1024px
- Desktop: > 1024px

---

### 16.2 Mobile Navigation

**Description**: Touch-friendly navigation.

**Features**:
- Hamburger menu
- Bottom navigation bar
- Swipe gestures
- Pull to refresh

---

### 16.3 Touch Interactions

**Description**: Optimized for touch.

**Features**:
- Large tap targets
- Swipe to delete
- Long press menus
- Pinch to zoom (images)

---

### 16.4 Offline Support (Future)

**Description**: Work without internet.

**Features**:
- Cache recent content
- Queue changes for sync
- Offline indicator
- Sync when online

---

### 16.5 Mobile Camera Integration

**Description**: Use device camera.

**Features**:
- Capture photos directly
- Scan documents
- Record video

---

### 16.6 Mobile Voice Recording

**Description**: Use device microphone.

**Features**:
- Native recording
- Background recording
- Audio visualization

---

### 16.7 Progressive Web App (Future)

**Description**: Install as app.

**Features**:
- Add to home screen
- App-like experience
- Push notifications
- Offline support

---

## 17. Keyboard Shortcuts

### 17.1 Global Shortcuts

| Shortcut | Action |
|----------|--------|
| Ctrl/Cmd + K | Global search |
| Ctrl/Cmd + N | New anecdote |
| Ctrl/Cmd + / | Show shortcuts |
| Esc | Close dialog/modal |

---

### 17.2 Editor Shortcuts

| Shortcut | Action |
|----------|--------|
| Ctrl/Cmd + S | Save |
| Ctrl/Cmd + B | Bold |
| Ctrl/Cmd + I | Italic |
| Ctrl/Cmd + U | Underline |
| Ctrl/Cmd + Z | Undo |
| Ctrl/Cmd + Shift + Z | Redo |
| Ctrl/Cmd + Enter | Lock anecdote |

---

### 17.3 Navigation Shortcuts

| Shortcut | Action |
|----------|--------|
| G then P | Go to projects |
| G then S | Go to settings |
| G then C | Go to credits |
| ← / → | Previous/next anecdote |

---

### 17.4 Custom Shortcuts (Future)

**Description**: User-defined shortcuts.

---

## 18. Data Management

### 18.1 Data Export

**Description**: Download all user data.

**Formats**:
- JSON (structured)
- ZIP (with media)

**Includes**:
- Profile
- Projects
- Anecdotes
- Media
- Settings
- History

---

### 18.2 Data Import

**Description**: Import data into account.

**Sources**:
- StoryWeaver export
- Plain text files
- Other writing tools (future)

---

### 18.3 Data Backup

**Description**: Automatic backups.

**Features**:
- Daily database backups
- Media redundancy
- Point-in-time recovery
- User-initiated backups

---

### 18.4 Data Retention

**Description**: How long data is kept.

**Policies**:
- Active data: indefinitely
- Deleted items: 30 days
- Backups: 90 days
- Logs: 12 months

---

### 18.5 Data Deletion

**Description**: Permanent data removal.

**Options**:
- Delete specific items
- Delete account (all data)
- Request complete erasure (GDPR)

---

## 19. Help & Support

### 19.1 Help Center

**Description**: Self-service documentation.

**Contents**:
- Getting started guide
- Feature documentation
- FAQs
- Troubleshooting
- Video tutorials

---

### 19.2 Contextual Help

**Description**: Help within the app.

**Features**:
- Tooltip hints
- Info icons with explanations
- "Learn more" links
- Feature tours

---

### 19.3 Onboarding Tutorial

**Description**: Guided first-time experience.

**Steps**:
1. Welcome and overview
2. Create first project
3. Write first anecdote
4. Understand AI features
5. Preview compilation

---

### 19.4 Contact Support

**Description**: Get human help.

**Channels**:
- Email support
- In-app chat (future)
- Community forum (future)

---

### 19.5 Bug Reporting

**Description**: Report issues.

**Features**:
- In-app report button
- Auto-capture context
- Screenshot attachment
- Status tracking

---

### 19.6 Feature Requests

**Description**: Suggest improvements.

**Features**:
- Submit ideas
- Vote on requests
- Status updates
- Roadmap visibility

---

### 19.7 Status Page

**Description**: System status information.

**Shows**:
- Current status
- Incident history
- Scheduled maintenance
- Subscribe to updates

---

## 20. Admin Features

### 20.1 Admin Dashboard

**Description**: Administrative overview.

**Metrics**:
- Total users
- Active users
- Revenue
- AI usage
- System health

---

### 20.2 User Management

**Description**: Manage user accounts.

**Features**:
- View all users
- Search users
- User details
- Credit adjustments
- Account actions

---

### 20.3 Content Moderation

**Description**: Monitor content.

**Features**:
- Flagged content review
- Content removal
- User warnings
- Ban management

---

### 20.4 Analytics Dashboard

**Description**: Business analytics.

**Charts**:
- User growth
- Retention
- Revenue
- Feature usage
- Conversion funnels

---

### 20.5 System Monitoring

**Description**: Technical health.

**Monitors**:
- Server status
- Database performance
- API response times
- Error rates
- Queue lengths

---

### 20.6 Configuration Management

**Description**: System settings.

**Configurable**:
- Credit costs
- Free credit amounts
- Feature flags
- AI model settings
- Email templates

---

### 20.7 Audit Logs

**Description**: Track system activities.

**Logs**:
- User actions
- Admin actions
- Security events
- Configuration changes

---

## Feature Summary Statistics

| Category | Feature Count |
|----------|---------------|
| Authentication & User Management | 5 |
| User Settings & Preferences | 7 |
| Projects Management | 10 |
| Anecdotes Core System | 17 |
| Media Management | 13 |
| AI Processing Pipeline | 14 |
| Book Compilation System | 12 |
| Version Control & History | 6 |
| Export & Publishing | 10 |
| Credits & Billing System | 12 |
| Dashboard & Analytics | 5 |
| Search & Discovery | 5 |
| Notifications System | 4 |
| Collaboration Features (Future) | 5 |
| Accessibility Features | 5 |
| Mobile & Responsive Features | 7 |
| Keyboard Shortcuts | 4 |
| Data Management | 5 |
| Help & Support | 7 |
| Admin Features | 7 |
| **Total** | **160+ Features** |

---

*This document provides a comprehensive catalog of all StoryWeaver AI features. Features marked as "(Future)" are planned for post-MVP development.*

**Version**: 1.0  
**Last Updated**: November 2025