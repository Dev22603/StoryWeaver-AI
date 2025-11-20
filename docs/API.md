# StoryWeaver AI - API Documentation

Complete API reference for StoryWeaver AI backend endpoints.

---

## Table of Contents

- [Overview](#overview)
- [Authentication](#authentication)
- [Error Handling](#error-handling)
- [Rate Limiting](#rate-limiting)
- [API Endpoints](#api-endpoints)
  - [Authentication](#authentication-endpoints)
  - [Projects](#projects-endpoints)
  - [Anecdotes](#anecdotes-endpoints)
  - [Media](#media-endpoints)
  - [Compilations](#compilations-endpoints)
  - [Credits](#credits-endpoints)
  - [User](#user-endpoints)

---

## Overview

**Base URL**: `https://storyweaver.ai/api`
**Content Type**: `application/json`
**Authentication**: Session-based (NextAuth.js)

### Request Format

```http
POST /api/projects HTTP/1.1
Host: storyweaver.ai
Content-Type: application/json
Cookie: next-auth.session-token=...

{
  "title": "My Memoir",
  "description": "A collection of memories"
}
```

### Response Format

**Success (2xx)**:
```json
{
  "data": {
    ...
  }
}
```

**Error (4xx/5xx)**:
```json
{
  "error": "Error message",
  "code": "ERROR_CODE",
  "details": [ ... ] // Optional
}
```

---

## Authentication

StoryWeaver AI uses **NextAuth.js** for authentication with Google OAuth.

### Session Management

- Sessions are stored in PostgreSQL
- Session cookies are HTTP-only and secure
- Sessions expire after 30 days of inactivity

### Checking Authentication Status

All API endpoints require authentication except:
- `/api/auth/*` endpoints
- Public marketing pages

Protected endpoints will return `401 Unauthorized` if not authenticated.

---

## Error Handling

### HTTP Status Codes

| Code | Meaning | Description |
|------|---------|-------------|
| 200 | OK | Request succeeded |
| 201 | Created | Resource created successfully |
| 400 | Bad Request | Invalid request body or parameters |
| 401 | Unauthorized | Authentication required |
| 403 | Forbidden | Insufficient permissions |
| 404 | Not Found | Resource doesn't exist |
| 429 | Too Many Requests | Rate limit exceeded |
| 500 | Internal Server Error | Server-side error |
| 503 | Service Unavailable | Service temporarily unavailable |

### Error Response Format

```json
{
  "error": "Human-readable error message",
  "code": "MACHINE_READABLE_CODE",
  "details": [
    {
      "field": "title",
      "message": "Title is required",
      "code": "required"
    }
  ],
  "requestId": "uuid"
}
```

### Common Error Codes

- `UNAUTHORIZED`: Not authenticated
- `FORBIDDEN`: Insufficient permissions
- `NOT_FOUND`: Resource not found
- `VALIDATION_ERROR`: Input validation failed
- `INSUFFICIENT_CREDITS`: Not enough credits
- `RATE_LIMIT_EXCEEDED`: Too many requests
- `INTERNAL_ERROR`: Server error

---

## Rate Limiting

### Limits

- **Anonymous**: 10 requests/minute
- **Authenticated**: 100 requests/minute
- **AI Operations**: 10 per hour

### Headers

Response includes rate limit information:

```http
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1640995200
```

### Exceeding Limits

Returns `429 Too Many Requests`:

```json
{
  "error": "Rate limit exceeded",
  "code": "RATE_LIMIT_EXCEEDED",
  "retryAfter": 60
}
```

---

## API Endpoints

## Authentication Endpoints

### Sign In with Google

**Endpoint**: `GET /api/auth/signin`

Initiates Google OAuth flow.

**Response**: Redirects to Google OAuth consent screen.

---

### Get Session

**Endpoint**: `GET /api/auth/session`

Get current user session.

**Response** (200):
```json
{
  "user": {
    "id": "uuid",
    "email": "user@example.com",
    "name": "John Doe",
    "image": "https://...",
    "creditsBalance": 100,
    "subscriptionTier": "free"
  },
  "expires": "2025-02-01T00:00:00Z"
}
```

---

### Sign Out

**Endpoint**: `POST /api/auth/signout`

Ends user session.

**Response** (200):
```json
{
  "url": "/login"
}
```

---

## Projects Endpoints

### List Projects

**Endpoint**: `GET /api/projects`

Get all projects for authenticated user.

**Query Parameters**:
- `status` (optional): Filter by status (`active`, `archived`)
- `sort` (optional): Sort order (`createdAt`, `updatedAt`, `title`)
- `order` (optional): Sort direction (`asc`, `desc`)
- `limit` (optional): Number of results (default: 20, max: 100)
- `cursor` (optional): Pagination cursor

**Response** (200):
```json
{
  "projects": [
    {
      "id": "uuid",
      "title": "My Memoir",
      "description": "A collection of memories",
      "genre": "memoir",
      "targetAudience": "family",
      "toneStyle": "conversational",
      "questionRounds": 3,
      "status": "active",
      "coverImageUrl": "https://...",
      "settings": {},
      "createdAt": "2025-01-01T00:00:00Z",
      "updatedAt": "2025-01-01T00:00:00Z",
      "_count": {
        "anecdotes": 5
      }
    }
  ],
  "nextCursor": "cursor-string",
  "hasMore": true
}
```

---

### Get Project

**Endpoint**: `GET /api/projects/:id`

Get a specific project.

**Path Parameters**:
- `id`: Project ID

**Response** (200):
```json
{
  "project": {
    "id": "uuid",
    "title": "My Memoir",
    "description": "A collection of memories",
    "genre": "memoir",
    "targetAudience": "family",
    "toneStyle": "conversational",
    "questionRounds": 3,
    "status": "active",
    "coverImageUrl": "https://...",
    "settings": {},
    "createdAt": "2025-01-01T00:00:00Z",
    "updatedAt": "2025-01-01T00:00:00Z",
    "stats": {
      "totalAnecdotes": 5,
      "completedAnecdotes": 3,
      "totalWords": 12500,
      "compilations": 1
    }
  }
}
```

**Errors**:
- `403`: Not authorized to access this project
- `404`: Project not found

---

### Create Project

**Endpoint**: `POST /api/projects`

Create a new project.

**Request Body**:
```json
{
  "title": "My Memoir",
  "description": "A collection of memories",
  "genre": "memoir",
  "targetAudience": "family",
  "toneStyle": "conversational",
  "questionRounds": 3,
  "settings": {}
}
```

**Validation**:
- `title`: Required, 1-200 characters
- `description`: Optional, max 1000 characters
- `genre`: Optional string
- `targetAudience`: Optional string
- `toneStyle`: One of `conversational`, `formal`, `literary` (default: `conversational`)
- `questionRounds`: Number 1-5 (default: 3)

**Response** (201):
```json
{
  "project": {
    "id": "uuid",
    "title": "My Memoir",
    ...
  }
}
```

**Errors**:
- `400`: Validation error

---

### Update Project

**Endpoint**: `PATCH /api/projects/:id`

Update an existing project.

**Path Parameters**:
- `id`: Project ID

**Request Body** (all fields optional):
```json
{
  "title": "Updated Title",
  "description": "Updated description",
  "genre": "autobiography",
  "toneStyle": "literary",
  "coverImageUrl": "https://..."
}
```

**Response** (200):
```json
{
  "project": {
    "id": "uuid",
    "title": "Updated Title",
    ...
  }
}
```

**Errors**:
- `403`: Not authorized
- `404`: Project not found
- `400`: Validation error

---

### Delete Project

**Endpoint**: `DELETE /api/projects/:id`

Delete a project and all associated anecdotes.

**Path Parameters**:
- `id`: Project ID

**Response** (200):
```json
{
  "message": "Project deleted successfully"
}
```

**Errors**:
- `403`: Not authorized
- `404`: Project not found

---

### Get Project Stats

**Endpoint**: `GET /api/projects/:id/stats`

Get detailed statistics for a project.

**Path Parameters**:
- `id`: Project ID

**Response** (200):
```json
{
  "stats": {
    "anecdotes": {
      "total": 10,
      "byStatus": {
        "draft": 3,
        "locked": 1,
        "questioning": 2,
        "refining": 1,
        "completed": 3
      }
    },
    "words": {
      "total": 25000,
      "average": 2500
    },
    "compilations": {
      "total": 2,
      "latest": {
        "id": "uuid",
        "versionNumber": 2,
        "createdAt": "2025-01-15T00:00:00Z"
      }
    },
    "creditsUsed": 47
  }
}
```

---

## Anecdotes Endpoints

### List Anecdotes

**Endpoint**: `GET /api/projects/:projectId/anecdotes`

Get all anecdotes for a project.

**Path Parameters**:
- `projectId`: Project ID

**Query Parameters**:
- `status` (optional): Filter by status
- `sort` (optional): Sort field
- `order` (optional): Sort direction
- `limit` (optional): Number of results

**Response** (200):
```json
{
  "anecdotes": [
    {
      "id": "uuid",
      "projectId": "uuid",
      "title": "Summer of 1995",
      "rawContent": "I remember that summer...",
      "timeFrameStart": "1995-06-01T00:00:00Z",
      "timeFrameEnd": "1995-08-31T00:00:00Z",
      "location": "Lake Tahoe",
      "peopleInvolved": ["Dad", "Mom", "Sister"],
      "themes": ["family", "childhood"],
      "status": "completed",
      "wordCount": 850,
      "sortOrder": 1,
      "createdAt": "2025-01-01T00:00:00Z",
      "updatedAt": "2025-01-05T00:00:00Z",
      "completedAt": "2025-01-05T00:00:00Z"
    }
  ]
}
```

---

### Get Anecdote

**Endpoint**: `GET /api/anecdotes/:id`

Get a specific anecdote with full details.

**Path Parameters**:
- `id`: Anecdote ID

**Response** (200):
```json
{
  "anecdote": {
    "id": "uuid",
    "projectId": "uuid",
    "title": "Summer of 1995",
    "rawContent": "I remember that summer...",
    "timeFrameStart": "1995-06-01T00:00:00Z",
    "timeFrameEnd": "1995-08-31T00:00:00Z",
    "timeFrameApproximate": "Early summer",
    "location": "Lake Tahoe",
    "peopleInvolved": ["Dad", "Mom", "Sister"],
    "themes": ["family", "childhood"],
    "status": "completed",
    "currentQuestionRound": 3,
    "questionnaire": [
      {
        "round": 1,
        "question": "What specific smells do you remember?",
        "answer": "Pine trees and sunscreen",
        "purpose": "sensory_details"
      }
    ],
    "aiRefinedContent": "That summer of 1995 at Lake Tahoe...",
    "aiSummary500": "A detailed 500-word summary...",
    "aiSummary100": "A brief 100-word summary...",
    "aiExtractedThemes": ["family bonding", "childhood innocence"],
    "userFeedback": null,
    "refinementIterations": 1,
    "wordCount": 850,
    "estimatedReadingTime": 4,
    "sortOrder": 1,
    "createdAt": "2025-01-01T00:00:00Z",
    "updatedAt": "2025-01-05T00:00:00Z",
    "lockedAt": "2025-01-02T00:00:00Z",
    "completedAt": "2025-01-05T00:00:00Z",
    "mediaAttachments": [
      {
        "id": "uuid",
        "mediaType": "image",
        "fileUrl": "https://...",
        "fileName": "lake-photo.jpg",
        "aiDescription": "A family photo at a lake",
        "caption": "Us at the lake",
        "sortOrder": 0
      }
    ]
  }
}
```

---

### Create Anecdote

**Endpoint**: `POST /api/projects/:projectId/anecdotes`

Create a new anecdote.

**Path Parameters**:
- `projectId`: Project ID

**Request Body**:
```json
{
  "title": "Summer of 1995",
  "rawContent": "I remember that summer...",
  "timeFrameStart": "1995-06-01",
  "timeFrameEnd": "1995-08-31",
  "timeFrameApproximate": "Early summer",
  "location": "Lake Tahoe",
  "peopleInvolved": ["Dad", "Mom"],
  "themes": ["family"]
}
```

**Validation**:
- `rawContent`: Required, min 10 characters
- `title`: Optional, max 200 characters
- Other fields: Optional

**Response** (201):
```json
{
  "anecdote": {
    "id": "uuid",
    ...
  }
}
```

---

### Update Anecdote

**Endpoint**: `PATCH /api/anecdotes/:id`

Update an existing anecdote.

**Path Parameters**:
- `id`: Anecdote ID

**Request Body** (all fields optional):
```json
{
  "title": "Updated Title",
  "rawContent": "Updated content...",
  "location": "Updated location"
}
```

**Response** (200):
```json
{
  "anecdote": {
    "id": "uuid",
    ...
  }
}
```

**Errors**:
- `400`: Cannot update anecdote in processing state
- `403`: Not authorized
- `404`: Anecdote not found

---

### Delete Anecdote

**Endpoint**: `DELETE /api/anecdotes/:id`

Delete an anecdote.

**Path Parameters**:
- `id`: Anecdote ID

**Response** (200):
```json
{
  "message": "Anecdote deleted successfully"
}
```

---

### Lock Anecdote

**Endpoint**: `POST /api/anecdotes/:id/lock`

Lock an anecdote and start AI processing.

**Path Parameters**:
- `id`: Anecdote ID

**Request Body**:
```json
{
  "includeImageAnalysis": true
}
```

**Response** (202):
```json
{
  "jobId": "uuid",
  "message": "Processing started",
  "estimatedTime": 30
}
```

**Errors**:
- `400`: Anecdote already locked or insufficient content
- `402`: Insufficient credits
- `403`: Not authorized

**Credits**: 2 credits

---

### Submit Answers

**Endpoint**: `POST /api/anecdotes/:id/answer`

Submit answers to AI-generated questions.

**Path Parameters**:
- `id`: Anecdote ID

**Request Body**:
```json
{
  "answers": [
    {
      "questionId": "q1",
      "answer": "Pine trees and sunscreen"
    }
  ],
  "requestMoreQuestions": false
}
```

**Response** (200):
```json
{
  "anecdote": {
    "id": "uuid",
    "status": "questioning",
    "currentQuestionRound": 2
  },
  "nextQuestions": [
    // New questions if requestMoreQuestions = true
  ]
}
```

---

### Request Refinement

**Endpoint**: `POST /api/anecdotes/:id/refine`

Request AI refinement of anecdote.

**Path Parameters**:
- `id`: Anecdote ID

**Request Body**:
```json
{
  "feedback": "Make it more emotional",
  "preserveVoice": true
}
```

**Response** (202):
```json
{
  "jobId": "uuid",
  "message": "Refinement started",
  "estimatedTime": 45
}
```

**Credits**: 5 credits

---

### Mark Complete

**Endpoint**: `POST /api/anecdotes/:id/complete`

Mark anecdote as complete and generate summaries.

**Path Parameters**:
- `id`: Anecdote ID

**Response** (202):
```json
{
  "jobId": "uuid",
  "message": "Summarization started"
}
```

**Credits**: 2 credits

---

### Reorder Anecdotes

**Endpoint**: `POST /api/anecdotes/reorder`

Change the order of anecdotes in a project.

**Request Body**:
```json
{
  "projectId": "uuid",
  "anecdoteIds": [
    "uuid1",
    "uuid2",
    "uuid3"
  ]
}
```

**Response** (200):
```json
{
  "message": "Anecdotes reordered successfully"
}
```

---

## Media Endpoints

### Upload Media

**Endpoint**: `POST /api/anecdotes/:anecdoteId/media`

Upload media attachment to an anecdote.

**Path Parameters**:
- `anecdoteId`: Anecdote ID

**Request**: Multipart form data

```http
POST /api/anecdotes/:anecdoteId/media
Content-Type: multipart/form-data

file: [binary data]
caption: "Photo at the lake"
mediaType: "image"
```

**Response** (201):
```json
{
  "mediaAttachment": {
    "id": "uuid",
    "anecdoteId": "uuid",
    "mediaType": "image",
    "fileUrl": "https://...",
    "fileName": "photo.jpg",
    "fileSize": 1024000,
    "mimeType": "image/jpeg",
    "caption": "Photo at the lake",
    "sortOrder": 0,
    "createdAt": "2025-01-01T00:00:00Z"
  }
}
```

**Supported Types**:
- Images: JPG, PNG, WebP (max 10MB)
- Audio: MP3, WAV, M4A (max 50MB)
- Video: MP4, MOV (max 100MB)
- Documents: PDF (max 20MB)

**Errors**:
- `400`: Invalid file type or size
- `403`: Not authorized

---

### Delete Media

**Endpoint**: `DELETE /api/media/:id`

Delete a media attachment.

**Path Parameters**:
- `id`: Media attachment ID

**Response** (200):
```json
{
  "message": "Media deleted successfully"
}
```

---

### Transcribe Audio

**Endpoint**: `POST /api/media/:id/transcribe`

Transcribe audio attachment.

**Path Parameters**:
- `id`: Media attachment ID (must be audio type)

**Response** (202):
```json
{
  "jobId": "uuid",
  "message": "Transcription started",
  "estimatedTime": 60
}
```

**Credits**: 3 credits per minute

---

## Compilations Endpoints

### Start Compilation

**Endpoint**: `POST /api/projects/:projectId/compile`

Start book compilation.

**Path Parameters**:
- `projectId`: Project ID

**Request Body**:
```json
{
  "anecdoteIds": ["uuid1", "uuid2", "uuid3"],
  "versionName": "First Draft",
  "commitMessage": "Initial compilation",
  "settings": {
    "chapterGrouping": "chronological",
    "includeTableOfContents": true,
    "includeIntroduction": true,
    "includeConclusion": true
  }
}
```

**Response** (202):
```json
{
  "compilationId": "uuid",
  "jobId": "uuid",
  "versionNumber": 1,
  "message": "Compilation started",
  "estimatedTime": 180
}
```

**Credits**: 20 credits

---

### Get Compilation

**Endpoint**: `GET /api/compilations/:id`

Get a specific compilation.

**Path Parameters**:
- `id`: Compilation ID

**Response** (200):
```json
{
  "compilation": {
    "id": "uuid",
    "projectId": "uuid",
    "versionNumber": 1,
    "versionName": "First Draft",
    "commitMessage": "Initial compilation",
    "manuscriptContent": "Full manuscript text...",
    "tableOfContents": {
      "chapters": [
        {
          "number": 1,
          "title": "Beginnings",
          "page": 1
        }
      ]
    },
    "chapterStructure": [
      {
        "number": 1,
        "title": "Beginnings",
        "anecdoteIds": ["uuid1", "uuid2"],
        "wordCount": 2500
      }
    ],
    "includedAnecdoteIds": ["uuid1", "uuid2"],
    "status": "draft",
    "wordCount": 15000,
    "pageCount": 45,
    "docxUrl": "https://...",
    "pdfUrl": "https://...",
    "createdAt": "2025-01-10T00:00:00Z"
  }
}
```

---

### List Compilations

**Endpoint**: `GET /api/projects/:projectId/compilations`

Get all compilations for a project.

**Path Parameters**:
- `projectId`: Project ID

**Response** (200):
```json
{
  "compilations": [
    {
      "id": "uuid",
      "versionNumber": 2,
      "versionName": "Revised Draft",
      "status": "draft",
      "wordCount": 16000,
      "createdAt": "2025-01-15T00:00:00Z"
    },
    {
      "id": "uuid",
      "versionNumber": 1,
      "versionName": "First Draft",
      "status": "draft",
      "wordCount": 15000,
      "createdAt": "2025-01-10T00:00:00Z"
    }
  ]
}
```

---

### Submit Feedback

**Endpoint**: `POST /api/compilations/:id/feedback`

Submit feedback on a compilation.

**Path Parameters**:
- `id`: Compilation ID

**Request Body**:
```json
{
  "feedback": "Please make the transitions smoother",
  "highlightedSections": [
    {
      "chapterNumber": 2,
      "startOffset": 100,
      "endOffset": 250,
      "comment": "This transition feels abrupt"
    }
  ]
}
```

**Response** (200):
```json
{
  "message": "Feedback submitted successfully"
}
```

---

### Regenerate Compilation

**Endpoint**: `POST /api/compilations/:id/regenerate`

Regenerate compilation with feedback.

**Path Parameters**:
- `id`: Compilation ID

**Request Body**:
```json
{
  "versionName": "Second Draft",
  "commitMessage": "Applied feedback"
}
```

**Response** (202):
```json
{
  "compilationId": "uuid",
  "jobId": "uuid",
  "versionNumber": 3,
  "message": "Regeneration started"
}
```

**Credits**: 20 credits

---

### Export Compilation

**Endpoint**: `POST /api/compilations/:id/export`

Export compilation to specific format.

**Path Parameters**:
- `id`: Compilation ID

**Request Body**:
```json
{
  "format": "docx",
  "options": {
    "fontSize": 12,
    "fontFamily": "Times New Roman",
    "lineSpacing": 1.5,
    "margins": {
      "top": 1,
      "bottom": 1,
      "left": 1,
      "right": 1
    }
  }
}
```

**Supported Formats**:
- `docx`: Microsoft Word
- `pdf`: PDF
- `epub`: EPUB (coming soon)

**Response** (202):
```json
{
  "jobId": "uuid",
  "message": "Export started",
  "estimatedTime": 60
}
```

When complete, the compilation record is updated with the file URL.

---

## Credits Endpoints

### Get Balance

**Endpoint**: `GET /api/credits/balance`

Get current credit balance.

**Response** (200):
```json
{
  "balance": 75,
  "subscriptionTier": "free"
}
```

---

### Get Transaction History

**Endpoint**: `GET /api/credits/history`

Get credit transaction history.

**Query Parameters**:
- `limit` (optional): Number of results (default: 50)
- `cursor` (optional): Pagination cursor

**Response** (200):
```json
{
  "transactions": [
    {
      "id": "uuid",
      "amount": -5,
      "transactionType": "usage",
      "description": "Content refinement for anecdote",
      "referenceId": "anecdote-uuid",
      "balanceAfter": 75,
      "createdAt": "2025-01-10T12:00:00Z"
    },
    {
      "id": "uuid",
      "amount": 100,
      "transactionType": "purchase",
      "description": "Credit pack purchase",
      "balanceAfter": 80,
      "createdAt": "2025-01-05T10:00:00Z"
    }
  ],
  "nextCursor": "cursor-string"
}
```

---

### Purchase Credits

**Endpoint**: `POST /api/credits/purchase`

Purchase credit pack (requires Stripe integration).

**Request Body**:
```json
{
  "packSize": "100",
  "paymentMethodId": "pm_xxx"
}
```

**Pack Sizes**:
- `100`: $9.99
- `500`: $39.99
- `1000`: $69.99

**Response** (201):
```json
{
  "transaction": {
    "id": "uuid",
    "amount": 100,
    "paymentIntent": "pi_xxx",
    "status": "succeeded"
  }
}
```

---

## User Endpoints

### Get Profile

**Endpoint**: `GET /api/user/profile`

Get current user profile.

**Response** (200):
```json
{
  "user": {
    "id": "uuid",
    "email": "user@example.com",
    "displayName": "John Doe",
    "avatarUrl": "https://...",
    "creditsBalance": 75,
    "subscriptionTier": "free",
    "createdAt": "2025-01-01T00:00:00Z",
    "stats": {
      "totalProjects": 3,
      "totalAnecdotes": 15,
      "totalCompilations": 2
    }
  }
}
```

---

### Update Profile

**Endpoint**: `PATCH /api/user/profile`

Update user profile.

**Request Body**:
```json
{
  "displayName": "Jane Doe",
  "avatarUrl": "https://..."
}
```

**Response** (200):
```json
{
  "user": {
    "id": "uuid",
    "displayName": "Jane Doe",
    ...
  }
}
```

---

### Delete Account

**Endpoint**: `DELETE /api/user/account`

Delete user account and all associated data.

**Request Body**:
```json
{
  "confirmation": "DELETE"
}
```

**Response** (200):
```json
{
  "message": "Account deleted successfully"
}
```

⚠️ **Warning**: This action is irreversible.

---

## Job Status Endpoint

### Check Job Status

**Endpoint**: `GET /api/jobs/:jobId`

Check the status of a background job.

**Path Parameters**:
- `jobId`: Job ID

**Response** (200):
```json
{
  "job": {
    "id": "uuid",
    "state": "completed",
    "progress": 100,
    "result": {
      ...
    },
    "failedReason": null,
    "processedOn": "2025-01-10T12:00:00Z",
    "finishedOn": "2025-01-10T12:01:30Z"
  }
}
```

**Job States**:
- `waiting`: Queued, not started
- `active`: Currently processing
- `completed`: Successfully finished
- `failed`: Failed with error
- `delayed`: Delayed for retry

---

## Webhooks

### Stripe Webhook

**Endpoint**: `POST /api/webhooks/stripe`

Handles Stripe payment events.

Events handled:
- `payment_intent.succeeded`
- `payment_intent.payment_failed`
- `customer.subscription.created`
- `customer.subscription.deleted`

**Authentication**: Stripe signature verification

---

## API Versioning

Currently on **v1** (implicit). Future versions will be explicitly versioned:

```
/api/v2/projects
```

---

## Best Practices

### Polling

When processing long-running operations:

1. Start the operation (returns `jobId`)
2. Poll `/api/jobs/:jobId` every 2-5 seconds
3. Stop polling when state is `completed` or `failed`

```typescript
async function pollJob(jobId: string): Promise<any> {
  while (true) {
    const response = await fetch(`/api/jobs/${jobId}`);
    const { job } = await response.json();

    if (job.state === 'completed') {
      return job.result;
    }

    if (job.state === 'failed') {
      throw new Error(job.failedReason);
    }

    await new Promise(resolve => setTimeout(resolve, 3000));
  }
}
```

### Error Handling

Always check for errors:

```typescript
const response = await fetch('/api/projects', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify(data)
});

if (!response.ok) {
  const error = await response.json();
  console.error('API Error:', error);
  throw new Error(error.message);
}

const result = await response.json();
```

### Pagination

Use cursor-based pagination for large lists:

```typescript
async function fetchAllProjects() {
  const projects = [];
  let cursor = null;

  do {
    const url = cursor
      ? `/api/projects?cursor=${cursor}`
      : '/api/projects';

    const response = await fetch(url);
    const { projects: batch, nextCursor } = await response.json();

    projects.push(...batch);
    cursor = nextCursor;
  } while (cursor);

  return projects;
}
```

---

**Last Updated**: November 2025
**Version**: 2.0 (Node.js + Prisma + PostgreSQL)
