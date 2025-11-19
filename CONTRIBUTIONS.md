# CONTRIBUTIONS.md - Complete Development Guidelines

## Table of Contents

1. [Getting Started](#1-getting-started)
2. [Code of Conduct](#2-code-of-conduct)
3. [Development Environment Setup](#3-development-environment-setup)
4. [Git Workflow & Conventions](#4-git-workflow--conventions)
5. [Branch Strategy](#5-branch-strategy)
6. [Commit Guidelines](#6-commit-guidelines)
7. [Pull Request Process](#7-pull-request-process)
8. [Code Review Guidelines](#8-code-review-guidelines)
9. [General Coding Principles](#9-general-coding-principles)
10. [Naming Conventions](#10-naming-conventions)
11. [File & Folder Organization](#11-file--folder-organization)
12. [TypeScript Guidelines](#12-typescript-guidelines)
13. [React/Next.js Guidelines](#13-reactnextjs-guidelines)
14. [State Management](#14-state-management)
15. [Styling Guidelines (CSS/Tailwind)](#15-styling-guidelines-csstailwind)
16. [Node.js/Express Guidelines](#16-nodejsexpress-guidelines)
17. [FastAPI/Python Guidelines](#17-fastapipython-guidelines)
18. [Database Guidelines](#18-database-guidelines)
19. [API Design Guidelines](#19-api-design-guidelines)
20. [Error Handling](#20-error-handling)
21. [Logging Guidelines](#21-logging-guidelines)
22. [Testing Guidelines](#22-testing-guidelines)
23. [Security Best Practices](#23-security-best-practices)
24. [Performance Guidelines](#24-performance-guidelines)
25. [Accessibility Guidelines](#25-accessibility-guidelines)
26. [Documentation Standards](#26-documentation-standards)
27. [Docker Guidelines](#27-docker-guidelines)
28. [CI/CD Guidelines](#28-cicd-guidelines)
29. [Environment Variables](#29-environment-variables)
30. [Dependency Management](#30-dependency-management)
31. [Internationalization (i18n)](#31-internationalization-i18n)
32. [Monitoring & Observability](#32-monitoring--observability)
33. [Release Process](#33-release-process)
34. [Troubleshooting Common Issues](#34-troubleshooting-common-issues)

---

## 1. Getting Started

### Prerequisites

Before contributing, ensure you have:

- Node.js 18+ (LTS recommended)
- Python 3.10+ (for FastAPI backend)
- Docker & Docker Compose
- Git 2.30+
- Your preferred IDE (VS Code recommended)
- Access to the repository

### First-Time Setup

```bash
# Clone the repository
git clone https://github.com/your-org/storyweaver-ai.git
cd storyweaver-ai

# Install dependencies
npm install

# Set up environment variables
cp .env.example .env.local

# Start development servers
npm run dev
```

### IDE Setup (VS Code)

Install these extensions:
- ESLint
- Prettier
- TypeScript and JavaScript Language Features
- Tailwind CSS IntelliSense
- Python
- Pylance
- Docker
- GitLens
- Error Lens

Use workspace settings from `.vscode/settings.json`.

---

## 2. Code of Conduct

### Our Standards

✅ **DO**:
- Be respectful and inclusive
- Give constructive feedback
- Accept constructive criticism gracefully
- Focus on what's best for the project
- Show empathy to other contributors

❌ **DON'T**:
- Use offensive language or imagery
- Make personal attacks
- Troll or make inflammatory comments
- Harass others publicly or privately
- Share others' private information

---

## 3. Development Environment Setup

### Required Tools

| Tool | Version | Purpose |
|------|---------|---------|
| Node.js | 18.x LTS | JavaScript runtime |
| npm/yarn/pnpm | Latest | Package manager |
| Python | 3.10+ | Backend runtime |
| Docker | 20.10+ | Containerization |
| PostgreSQL | 14+ | Database (via Docker) |
| Redis | 7+ | Caching (optional) |

### Environment Configuration

✅ **DO**:
- Use `.env.local` for local development
- Never commit `.env` files with secrets
- Use `.env.example` as a template
- Document all environment variables
- Use different configs for dev/staging/prod

❌ **DON'T**:
- Hardcode configuration values
- Commit API keys or secrets
- Use production credentials locally
- Share your personal `.env` file

### Local Development Commands

```bash
# Start all services
npm run dev

# Start frontend only
npm run dev:web

# Start backend only
npm run dev:api

# Start with Docker
docker-compose up -d

# Run database migrations
npm run db:migrate

# Seed database
npm run db:seed

# Run tests
npm run test

# Run linting
npm run lint

# Format code
npm run format
```

---

## 4. Git Workflow & Conventions

### General Git Rules

✅ **DO**:
- Pull latest changes before starting work
- Create feature branches from `main`
- Keep commits small and focused
- Write meaningful commit messages
- Rebase feature branches on `main` before PR
- Delete branches after merging
- Use `.gitignore` properly
- Sign your commits (recommended)

❌ **DON'T**:
- Commit directly to `main`
- Force push to shared branches
- Commit generated files (build/, dist/, node_modules/)
- Commit large binary files
- Commit sensitive data (keys, passwords)
- Leave commented-out code in commits
- Squash commits that should remain separate
- Rewrite public branch history

### Git Configuration

```bash
# Set up your identity
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Enable commit signing (recommended)
git config --global commit.gpgsign true

# Set default branch name
git config --global init.defaultBranch main

# Enable helpful aliases
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.lg "log --oneline --graph --all"
```

### .gitignore Best Practices

Always ignore:
```gitignore
# Dependencies
node_modules/
__pycache__/
venv/
.venv/

# Build outputs
dist/
build/
.next/
out/

# Environment files
.env
.env.local
.env.*.local

# IDE files
.idea/
.vscode/
*.swp
*.swo

# OS files
.DS_Store
Thumbs.db

# Logs
*.log
logs/

# Test coverage
coverage/
.nyc_output/

# Temporary files
tmp/
temp/
*.tmp
```

---

## 5. Branch Strategy

### Branch Types

| Branch | Purpose | Naming |
|--------|---------|--------|
| `main` | Production-ready code | Protected |
| `develop` | Integration branch (optional) | Protected |
| `feature/*` | New features | `feature/add-user-auth` |
| `fix/*` | Bug fixes | `fix/login-validation` |
| `hotfix/*` | Urgent production fixes | `hotfix/security-patch` |
| `refactor/*` | Code refactoring | `refactor/api-structure` |
| `docs/*` | Documentation | `docs/api-reference` |
| `test/*` | Test additions | `test/auth-unit-tests` |
| `chore/*` | Maintenance tasks | `chore/update-deps` |

### Branch Naming Rules

✅ **DO**:
- Use lowercase letters
- Use hyphens to separate words
- Include ticket/issue number if applicable
- Keep names short but descriptive
- Use prefixes consistently

❌ **DON'T**:
- Use spaces or special characters
- Use CamelCase or PascalCase
- Create overly long branch names
- Use ambiguous names like `fix` or `update`

### Examples

```bash
# Good branch names
feature/user-authentication
feature/PROJ-123-add-payment
fix/upload-timeout-error
hotfix/xss-vulnerability
refactor/database-queries
docs/contribution-guide

# Bad branch names
Feature/UserAuth          # CamelCase, no description
my-branch                 # Ambiguous
fix                       # No description
feature/add_new_user_registration_flow_with_email_verification  # Too long
```

### Branch Workflow

```bash
# Create a new feature branch
git checkout main
git pull origin main
git checkout -b feature/my-feature

# Work on your feature
git add .
git commit -m "feat: add feature description"

# Keep branch updated
git fetch origin
git rebase origin/main

# Push branch
git push origin feature/my-feature

# After PR merged, clean up
git checkout main
git pull origin main
git branch -d feature/my-feature
```

---

## 6. Commit Guidelines

### Commit Message Format

We follow [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Commit Types

| Type | Description |
|------|-------------|
| `feat` | New feature |
| `fix` | Bug fix |
| `docs` | Documentation only |
| `style` | Formatting, no code change |
| `refactor` | Code restructuring |
| `perf` | Performance improvement |
| `test` | Adding tests |
| `chore` | Maintenance tasks |
| `ci` | CI/CD changes |
| `build` | Build system changes |
| `revert` | Reverting changes |

### Commit Scope (Optional)

- `auth` - Authentication
- `api` - API endpoints
- `ui` - User interface
- `db` - Database
- `config` - Configuration
- `deps` - Dependencies

### Commit Subject Rules

✅ **DO**:
- Use imperative mood ("add" not "added")
- Keep under 50 characters
- Start with lowercase
- No period at end
- Be specific and clear

❌ **DON'T**:
- Use past tense
- End with punctuation
- Exceed 50 characters
- Be vague ("fix stuff")
- Include ticket numbers in subject (use footer)

### Commit Examples

```bash
# Good commits
feat(auth): add Google OAuth integration
fix(upload): resolve timeout for large files
docs: update API documentation
refactor(api): simplify error handling logic
perf(db): add index on users.email column
test(auth): add unit tests for login flow
chore(deps): update React to v18.2.0

# Bad commits
Fixed bug                     # Vague, past tense
feat: Add User Authentication # Capitalized, too brief
Updated stuff                 # Vague, past tense
WIP                          # Work in progress shouldn't be committed
```

### Commit Body Guidelines

✅ **DO**:
- Explain WHY the change was made
- Explain WHAT changed at a high level
- Wrap at 72 characters
- Leave blank line after subject
- Reference issues being fixed

❌ **DON'T**:
- Describe HOW (that's what the code is for)
- Include large code snippets
- Write redundant information

### Full Commit Example

```
feat(auth): add password reset functionality

Implement password reset flow with email verification.
Users can now request a reset link that expires after 24 hours.

This addresses frequent support requests for account recovery
and improves user experience for forgotten passwords.

Closes #123
Related to #100
```

### Atomic Commits

✅ **DO**:
- Make each commit a single logical change
- Ensure each commit leaves codebase working
- Separate refactoring from features
- Separate formatting from logic changes

❌ **DON'T**:
- Mix multiple unrelated changes
- Commit broken code
- Include "WIP" commits in PRs
- Bundle unrelated fixes together

---

## 7. Pull Request Process

### Before Creating PR

✅ **DO**:
- Rebase on latest `main`
- Run all tests locally
- Run linting and fix issues
- Self-review your changes
- Update documentation if needed
- Add/update tests for changes
- Check for console.logs or debug code

❌ **DON'T**:
- Create PR with failing tests
- Create PR with linting errors
- Include unrelated changes
- Forget to update types

### PR Title Format

Follow same convention as commits:

```
feat(scope): short description
fix(scope): short description
```

### PR Description Template

```markdown
## Description
Brief description of changes and motivation.

## Type of Change
- [ ] Bug fix (non-breaking change fixing an issue)
- [ ] New feature (non-breaking change adding functionality)
- [ ] Breaking change (fix or feature causing existing functionality to change)
- [ ] Documentation update

## How Has This Been Tested?
Describe tests you ran and how to reproduce.

## Screenshots (if applicable)
Add screenshots for UI changes.

## Checklist
- [ ] My code follows the project's style guidelines
- [ ] I have performed a self-review
- [ ] I have commented my code where necessary
- [ ] I have updated the documentation
- [ ] My changes generate no new warnings
- [ ] I have added tests that prove my fix/feature works
- [ ] New and existing unit tests pass locally
- [ ] Any dependent changes have been merged

## Related Issues
Closes #123
```

### PR Size Guidelines

✅ **DO**:
- Keep PRs small (< 400 lines ideally)
- Split large features into smaller PRs
- Create separate PRs for refactoring
- One feature/fix per PR

❌ **DON'T**:
- Create massive PRs (> 1000 lines)
- Bundle unrelated changes
- Mix refactoring with features

### PR Review Requirements

- Minimum 1 approval required
- All CI checks must pass
- No unresolved comments
- Up to date with base branch

### Merging Strategy

- **Squash and merge**: For feature branches (clean history)
- **Rebase and merge**: For clean commit history
- **Merge commit**: Rarely used, for major merges

---

## 8. Code Review Guidelines

### For Authors

✅ **DO**:
- Provide context in PR description
- Respond to feedback promptly
- Be open to suggestions
- Explain complex code with comments
- Break up large PRs

❌ **DON'T**:
- Take feedback personally
- Ignore reviewer comments
- Merge without addressing concerns
- Rush reviewers

### For Reviewers

✅ **DO**:
- Review promptly (within 24 hours)
- Be constructive and respectful
- Explain your suggestions
- Acknowledge good work
- Ask questions if unclear
- Test the code locally for complex changes
- Check for security issues
- Verify test coverage

❌ **DON'T**:
- Be harsh or dismissive
- Nitpick style issues (use linters)
- Block PRs for minor issues
- Approve without reading
- Leave vague comments

### Review Checklist

**Functionality**:
- [ ] Code does what it's supposed to do
- [ ] Edge cases are handled
- [ ] Error handling is appropriate

**Code Quality**:
- [ ] Code is readable and maintainable
- [ ] No code duplication
- [ ] Functions are appropriately sized
- [ ] No dead code or commented-out code

**Security**:
- [ ] No hardcoded secrets
- [ ] Input is validated
- [ ] SQL injection prevented
- [ ] XSS prevented

**Testing**:
- [ ] Tests cover new code
- [ ] Tests are meaningful
- [ ] Edge cases tested

**Documentation**:
- [ ] Complex logic is commented
- [ ] Public APIs are documented
- [ ] README updated if needed

### Review Comment Prefixes

Use prefixes to indicate comment importance:

```
[MUST] - Must be fixed before approval
[SHOULD] - Strongly recommended
[NIT] - Minor nitpick, optional
[QUESTION] - Need clarification
[SUGGESTION] - Optional improvement
[PRAISE] - Positive feedback
```

---

## 9. General Coding Principles

### SOLID Principles

✅ **DO**:
- **S**ingle Responsibility: One reason to change
- **O**pen/Closed: Open for extension, closed for modification
- **L**iskov Substitution: Subtypes must be substitutable
- **I**nterface Segregation: Many specific interfaces
- **D**ependency Inversion: Depend on abstractions

### DRY (Don't Repeat Yourself)

✅ **DO**:
- Extract repeated code into functions
- Use constants for repeated values
- Create reusable components
- Share types and interfaces

❌ **DON'T**:
- Copy-paste code blocks
- Repeat magic numbers
- Duplicate logic across files

### KISS (Keep It Simple, Stupid)

✅ **DO**:
- Write straightforward code
- Use simple solutions first
- Avoid premature optimization
- Prefer readability over cleverness

❌ **DON'T**:
- Over-engineer solutions
- Use complex patterns unnecessarily
- Write "clever" one-liners that are hard to read

### YAGNI (You Aren't Gonna Need It)

✅ **DO**:
- Implement features when needed
- Remove unused code
- Question every abstraction

❌ **DON'T**:
- Add features "just in case"
- Create abstractions before needed
- Keep code for "future use"

### Code Clarity

✅ **DO**:
- Write self-documenting code
- Use descriptive variable names
- Keep functions short (< 30 lines ideal)
- Use early returns to reduce nesting
- Add comments for WHY, not WHAT

❌ **DON'T**:
- Use abbreviations (except common ones)
- Write deeply nested code
- Use single-letter variables (except loops)
- Write functions > 100 lines

### Examples

```typescript
// ❌ BAD: Unclear, nested, cryptic
function p(d) {
  if (d) {
    if (d.u) {
      if (d.u.a) {
        return d.u.a > 18;
      }
    }
  }
  return false;
}

// ✅ GOOD: Clear, early returns, descriptive
function isUserAdult(data: UserData | null): boolean {
  if (!data?.user?.age) {
    return false;
  }
  
  return data.user.age > 18;
}
```

---

## 10. Naming Conventions

### General Rules

✅ **DO**:
- Use descriptive, meaningful names
- Be consistent throughout codebase
- Use domain terminology
- Make names pronounceable
- Make names searchable

❌ **DON'T**:
- Use single letters (except `i`, `j` for loops)
- Use abbreviations (except common: `id`, `url`, `api`)
- Use Hungarian notation
- Encode type in name

### JavaScript/TypeScript Naming

| Element | Convention | Example |
|---------|------------|---------|
| Variables | camelCase | `userName`, `isActive` |
| Constants | UPPER_SNAKE_CASE | `MAX_RETRY_COUNT`, `API_URL` |
| Functions | camelCase | `getUserById`, `calculateTotal` |
| Classes | PascalCase | `UserService`, `ApiClient` |
| Interfaces | PascalCase | `UserProfile`, `ApiResponse` |
| Types | PascalCase | `RequestConfig`, `ButtonProps` |
| Enums | PascalCase | `UserRole`, `HttpStatus` |
| Enum values | UPPER_SNAKE_CASE | `ADMIN`, `READ_ONLY` |
| React components | PascalCase | `UserCard`, `LoginForm` |
| Hooks | camelCase with `use` | `useAuth`, `useFetch` |
| Files (components) | PascalCase | `UserCard.tsx` |
| Files (utilities) | camelCase | `formatDate.ts` |
| Folders | kebab-case | `user-profile/`, `api-client/` |

### Python Naming

| Element | Convention | Example |
|---------|------------|---------|
| Variables | snake_case | `user_name`, `is_active` |
| Constants | UPPER_SNAKE_CASE | `MAX_RETRY_COUNT` |
| Functions | snake_case | `get_user_by_id` |
| Classes | PascalCase | `UserService` |
| Methods | snake_case | `calculate_total` |
| Private | Leading underscore | `_internal_method` |
| Modules | snake_case | `user_service.py` |
| Packages | snake_case | `api_client/` |

### Boolean Naming

✅ **DO**:
- Use `is`, `has`, `should`, `can`, `will` prefixes
- Make it read like a question

```typescript
// ✅ GOOD
const isActive = true;
const hasPermission = false;
const shouldRefresh = true;
const canEdit = user.role === 'admin';

// ❌ BAD
const active = true;
const permission = false;
const refresh = true;
const edit = user.role === 'admin';
```

### Function Naming

✅ **DO**:
- Start with verb
- Be specific about action
- Include return type hint in name

```typescript
// ✅ GOOD
function getUserById(id: string): User
function calculateTotalPrice(items: Item[]): number
function validateEmail(email: string): boolean
function formatCurrency(amount: number): string
async function fetchUserProfile(userId: string): Promise<Profile>

// ❌ BAD
function user(id: string)           // No verb
function process(data)              // Too vague
function doStuff()                  // Meaningless
function handleClick()              // OK for events, not for logic
```

### Event Handler Naming

```typescript
// ✅ GOOD - use handle prefix for handlers
const handleClick = () => {};
const handleSubmit = () => {};
const handleInputChange = () => {};

// ✅ GOOD - use on prefix for props
<Button onClick={handleClick} />
<Form onSubmit={handleSubmit} />
```

### Array/Collection Naming

```typescript
// ✅ GOOD - use plural
const users = [user1, user2];
const selectedItems = [item1, item2];
const userIds = ['1', '2', '3'];

// ❌ BAD
const userList = [...];      // Redundant "List"
const userArray = [...];     // Don't encode type
const user = [user1, user2]; // Singular for collection
```

### Database Naming

| Element | Convention | Example |
|---------|------------|---------|
| Tables | snake_case, plural | `users`, `user_sessions` |
| Columns | snake_case | `created_at`, `user_id` |
| Primary keys | `id` | `id` |
| Foreign keys | `<table>_id` | `user_id`, `project_id` |
| Indexes | `idx_<table>_<column>` | `idx_users_email` |
| Constraints | `<table>_<column>_<type>` | `users_email_unique` |

---

## 11. File & Folder Organization

### Next.js Project Structure

```
storyweaver-ai/
├── .github/                    # GitHub workflows, templates
│   ├── workflows/
│   │   ├── ci.yml
│   │   └── deploy.yml
│   └── PULL_REQUEST_TEMPLATE.md
├── .husky/                     # Git hooks
├── apps/
│   └── web/                    # Next.js application
│       ├── app/                # App Router pages
│       │   ├── (auth)/         # Auth route group
│       │   │   ├── login/
│       │   │   │   └── page.tsx
│       │   │   └── layout.tsx
│       │   ├── (dashboard)/    # Dashboard route group
│       │   │   ├── projects/
│       │   │   │   ├── [id]/
│       │   │   │   │   └── page.tsx
│       │   │   │   └── page.tsx
│       │   │   └── layout.tsx
│       │   ├── api/            # API routes
│       │   │   └── auth/
│       │   │       └── route.ts
│       │   ├── layout.tsx      # Root layout
│       │   ├── page.tsx        # Home page
│       │   └── globals.css
│       ├── components/         # React components
│       │   ├── ui/             # Base UI components
│       │   │   ├── button.tsx
│       │   │   ├── input.tsx
│       │   │   └── index.ts    # Barrel export
│       │   ├── features/       # Feature components
│       │   │   ├── auth/
│       │   │   └── projects/
│       │   └── shared/         # Shared components
│       │       ├── header.tsx
│       │       └── footer.tsx
│       ├── hooks/              # Custom React hooks
│       │   ├── use-auth.ts
│       │   └── use-projects.ts
│       ├── lib/                # Utilities & configs
│       │   ├── api/            # API client
│       │   ├── utils/          # Utility functions
│       │   └── constants.ts
│       ├── stores/             # State management
│       │   └── auth-store.ts
│       ├── types/              # TypeScript types
│       │   ├── api.ts
│       │   └── index.ts
│       ├── public/             # Static assets
│       │   ├── images/
│       │   └── fonts/
│       └── next.config.js
├── packages/                   # Shared packages
│   └── shared/
│       ├── types/
│       └── utils/
├── services/                   # Backend services
│   └── api/                    # FastAPI or Express
│       ├── src/
│       │   ├── routes/
│       │   ├── services/
│       │   ├── models/
│       │   └── utils/
│       └── tests/
├── docker/                     # Docker configs
│   ├── Dockerfile.web
│   └── Dockerfile.api
├── docs/                       # Documentation
├── scripts/                    # Build/deploy scripts
├── .env.example
├── docker-compose.yml
├── package.json
├── tsconfig.json
└── README.md
```

### File Organization Rules

✅ **DO**:
- Group files by feature, not by type
- Keep related files close together
- Use index files for clean imports
- Separate concerns clearly
- Keep flat structure when possible

❌ **DON'T**:
- Create deeply nested structures (> 4 levels)
- Mix concerns in single files
- Create catch-all folders
- Leave orphan files

### Component File Structure

For complex components:

```
components/
└── user-profile/
    ├── index.ts              # Barrel export
    ├── user-profile.tsx      # Main component
    ├── user-profile.test.tsx # Tests
    ├── user-profile.styles.ts # Styled components (if not Tailwind)
    ├── user-avatar.tsx       # Sub-component
    └── types.ts              # Local types
```

For simple components:

```
components/
└── ui/
    ├── button.tsx
    ├── input.tsx
    └── card.tsx
```

### Import Order

```typescript
// 1. React/Next imports
import React, { useState, useEffect } from 'react';
import { useRouter } from 'next/navigation';

// 2. Third-party imports
import { z } from 'zod';
import { toast } from 'sonner';

// 3. Internal absolute imports
import { Button } from '@/components/ui';
import { useAuth } from '@/hooks/use-auth';
import { formatDate } from '@/lib/utils';

// 4. Relative imports
import { UserAvatar } from './user-avatar';
import type { UserProfileProps } from './types';

// 5. Styles (if not using Tailwind)
import styles from './user-profile.module.css';

// 6. Types (if separate)
import type { User } from '@/types';
```

---

## 12. TypeScript Guidelines

### General Rules

✅ **DO**:
- Enable strict mode in `tsconfig.json`
- Type all function parameters and returns
- Use type inference when obvious
- Prefer `interface` for objects, `type` for unions
- Use generics for reusable code
- Use `unknown` over `any`
- Use `readonly` for immutable data

❌ **DON'T**:
- Use `any` type (use `unknown` instead)
- Ignore TypeScript errors with `@ts-ignore`
- Use non-null assertion `!` excessively
- Create overly complex types
- Use `as` for type casting (use type guards)

### Type Definition Examples

```typescript
// ✅ GOOD: Interface for object shapes
interface User {
  id: string;
  email: string;
  name: string;
  createdAt: Date;
}

// ✅ GOOD: Type for unions and intersections
type Status = 'pending' | 'active' | 'inactive';
type ApiResponse<T> = { data: T } | { error: string };

// ✅ GOOD: Generic types
interface PaginatedResponse<T> {
  data: T[];
  total: number;
  page: number;
  pageSize: number;
}

// ✅ GOOD: Utility types
type PartialUser = Partial<User>;
type UserWithoutId = Omit<User, 'id'>;
type CreateUserDto = Pick<User, 'email' | 'name'>;

// ❌ BAD: Using any
function processData(data: any) { } // Never use any

// ✅ GOOD: Using unknown with type guard
function processData(data: unknown) {
  if (isValidData(data)) {
    // Now TypeScript knows the type
  }
}
```

### Strict Null Checks

```typescript
// ✅ GOOD: Handle null/undefined explicitly
function getUser(id: string): User | null {
  const user = users.find(u => u.id === id);
  return user ?? null;
}

// ✅ GOOD: Optional chaining
const userName = user?.profile?.name;

// ✅ GOOD: Nullish coalescing
const displayName = user.name ?? 'Anonymous';

// ❌ BAD: Non-null assertion without checking
const userName = user!.name; // Dangerous
```

### Type Guards

```typescript
// ✅ GOOD: Custom type guard
function isUser(obj: unknown): obj is User {
  return (
    typeof obj === 'object' &&
    obj !== null &&
    'id' in obj &&
    'email' in obj
  );
}

// ✅ GOOD: Usage
function processItem(item: unknown) {
  if (isUser(item)) {
    console.log(item.email); // TypeScript knows it's User
  }
}
```

### Enums vs Union Types

```typescript
// ✅ PREFERRED: Union types (more type-safe)
type UserRole = 'admin' | 'user' | 'guest';

// ✅ OK: Const object for values
const UserRole = {
  ADMIN: 'admin',
  USER: 'user',
  GUEST: 'guest',
} as const;
type UserRole = typeof UserRole[keyof typeof UserRole];

// ⚠️ USE SPARINGLY: Enums (can have runtime surprises)
enum UserRole {
  ADMIN = 'admin',
  USER = 'user',
  GUEST = 'guest',
}
```

### Function Types

```typescript
// ✅ GOOD: Type function parameters and return
function calculateTotal(items: Item[]): number {
  return items.reduce((sum, item) => sum + item.price, 0);
}

// ✅ GOOD: Async function types
async function fetchUser(id: string): Promise<User> {
  const response = await api.get(`/users/${id}`);
  return response.data;
}

// ✅ GOOD: Function type alias
type ClickHandler = (event: MouseEvent) => void;
type Validator<T> = (value: T) => boolean;

// ✅ GOOD: Generic functions
function first<T>(array: T[]): T | undefined {
  return array[0];
}
```

### tsconfig.json Recommended Settings

```json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": true,
    "forceConsistentCasingInFileNames": true,
    "skipLibCheck": true,
    "esModuleInterop": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve",
    "incremental": true,
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

---

## 13. React/Next.js Guidelines

### Component Structure

✅ **DO**:
- Use functional components with hooks
- Keep components small and focused
- Extract logic into custom hooks
- Use composition over inheritance
- Memoize expensive computations
- Clean up effects properly

❌ **DON'T**:
- Use class components (unless necessary)
- Put business logic in components
- Create deeply nested components
- Forget cleanup in useEffect
- Mutate state directly

### Component Template

```typescript
'use client'; // Only if client-side needed

import { useState, useEffect, useCallback } from 'react';
import type { FC } from 'react';

// Types
interface UserCardProps {
  user: User;
  onSelect?: (user: User) => void;
  className?: string;
}

// Component
export const UserCard: FC<UserCardProps> = ({ 
  user, 
  onSelect,
  className 
}) => {
  // State
  const [isExpanded, setIsExpanded] = useState(false);
  
  // Hooks
  const { isLoading } = useUserData(user.id);
  
  // Callbacks
  const handleClick = useCallback(() => {
    onSelect?.(user);
  }, [user, onSelect]);
  
  // Effects
  useEffect(() => {
    // Effect logic
    return () => {
      // Cleanup
    };
  }, [dependency]);
  
  // Early returns for loading/error states
  if (isLoading) {
    return <Skeleton />;
  }
  
  // Render
  return (
    <div className={className} onClick={handleClick}>
      <h3>{user.name}</h3>
      <p>{user.email}</p>
    </div>
  );
};
```

### Hooks Rules

✅ **DO**:
- Call hooks at the top level
- Call hooks only in React functions
- Use the `use` prefix for custom hooks
- Return consistent types from hooks

❌ **DON'T**:
- Call hooks in loops or conditions
- Call hooks in regular functions
- Forget dependencies in useEffect/useCallback

### Custom Hook Template

```typescript
import { useState, useEffect, useCallback } from 'react';

interface UseUserResult {
  user: User | null;
  isLoading: boolean;
  error: Error | null;
  refetch: () => Promise<void>;
}

export function useUser(userId: string): UseUserResult {
  const [user, setUser] = useState<User | null>(null);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);
  
  const fetchUser = useCallback(async () => {
    try {
      setIsLoading(true);
      setError(null);
      const data = await api.getUser(userId);
      setUser(data);
    } catch (err) {
      setError(err as Error);
    } finally {
      setIsLoading(false);
    }
  }, [userId]);
  
  useEffect(() => {
    fetchUser();
  }, [fetchUser]);
  
  return { user, isLoading, error, refetch: fetchUser };
}
```

### Server vs Client Components (Next.js 13+)

```typescript
// ✅ Server Component (default) - No 'use client' directive
// Use for: data fetching, accessing backend resources, keeping sensitive info
export default async function ProjectsPage() {
  const projects = await getProjects(); // Direct database call
  
  return (
    <div>
      {projects.map(project => (
        <ProjectCard key={project.id} project={project} />
      ))}
    </div>
  );
}

// ✅ Client Component - Add 'use client' directive
// Use for: interactivity, event handlers, useState, useEffect, browser APIs
'use client';

export function LikeButton({ projectId }: { projectId: string }) {
  const [likes, setLikes] = useState(0);
  
  return (
    <button onClick={() => setLikes(l => l + 1)}>
      Likes: {likes}
    </button>
  );
}
```

### Performance Optimization

```typescript
// ✅ Memoize expensive components
import { memo } from 'react';

export const ExpensiveList = memo(function ExpensiveList({ 
  items 
}: { 
  items: Item[] 
}) {
  return (
    <ul>
      {items.map(item => (
        <li key={item.id}>{item.name}</li>
      ))}
    </ul>
  );
});

// ✅ Memoize expensive calculations
const sortedItems = useMemo(() => {
  return items.sort((a, b) => a.name.localeCompare(b.name));
}, [items]);

// ✅ Memoize callbacks
const handleSubmit = useCallback((data: FormData) => {
  onSubmit(data);
}, [onSubmit]);

// ✅ Use dynamic imports for code splitting
const HeavyComponent = dynamic(() => import('./HeavyComponent'), {
  loading: () => <Spinner />,
});
```

### Key Prop Usage

```typescript
// ✅ GOOD: Unique, stable key
{users.map(user => (
  <UserCard key={user.id} user={user} />
))}

// ✅ GOOD: Composite key if no single unique field
{items.map(item => (
  <Item key={`${item.category}-${item.name}`} item={item} />
))}

// ❌ BAD: Index as key (causes issues with reordering)
{users.map((user, index) => (
  <UserCard key={index} user={user} />
))}

// ❌ BAD: Random key (causes re-render every time)
{users.map(user => (
  <UserCard key={Math.random()} user={user} />
))}
```

### Form Handling

```typescript
'use client';

import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';

// Schema
const loginSchema = z.object({
  email: z.string().email('Invalid email'),
  password: z.string().min(8, 'Password must be at least 8 characters'),
});

type LoginFormData = z.infer<typeof loginSchema>;

// Component
export function LoginForm() {
  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitting },
  } = useForm<LoginFormData>({
    resolver: zodResolver(loginSchema),
  });
  
  const onSubmit = async (data: LoginFormData) => {
    try {
      await signIn(data);
    } catch (error) {
      toast.error('Login failed');
    }
  };
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('email')} type="email" />
      {errors.email && <span>{errors.email.message}</span>}
      
      <input {...register('password')} type="password" />
      {errors.password && <span>{errors.password.message}</span>}
      
      <button type="submit" disabled={isSubmitting}>
        {isSubmitting ? 'Loading...' : 'Sign In'}
      </button>
    </form>
  );
}
```

---

## 14. State Management

### Local State (useState)

```typescript
// ✅ GOOD: Simple local state
const [count, setCount] = useState(0);
const [user, setUser] = useState<User | null>(null);

// ✅ GOOD: Functional updates for derived state
setCount(prev => prev + 1);

// ✅ GOOD: Lazy initialization for expensive initial state
const [data] = useState(() => expensiveComputation());
```

### Complex Local State (useReducer)

```typescript
// ✅ GOOD: Use reducer for complex state logic
interface State {
  items: Item[];
  isLoading: boolean;
  error: string | null;
}

type Action =
  | { type: 'FETCH_START' }
  | { type: 'FETCH_SUCCESS'; payload: Item[] }
  | { type: 'FETCH_ERROR'; payload: string };

function reducer(state: State, action: Action): State {
  switch (action.type) {
    case 'FETCH_START':
      return { ...state, isLoading: true, error: null };
    case 'FETCH_SUCCESS':
      return { ...state, isLoading: false, items: action.payload };
    case 'FETCH_ERROR':
      return { ...state, isLoading: false, error: action.payload };
    default:
      return state;
  }
}
```

### Global State (Zustand)

```typescript
// stores/auth-store.ts
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

interface AuthState {
  user: User | null;
  isAuthenticated: boolean;
  login: (user: User) => void;
  logout: () => void;
}

export const useAuthStore = create<AuthState>()(
  persist(
    (set) => ({
      user: null,
      isAuthenticated: false,
      login: (user) => set({ user, isAuthenticated: true }),
      logout: () => set({ user: null, isAuthenticated: false }),
    }),
    {
      name: 'auth-storage',
    }
  )
);

// Usage in component
const { user, isAuthenticated, login, logout } = useAuthStore();
```

### Server State (React Query / SWR)

```typescript
// ✅ GOOD: Use React Query for server state
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';

// Fetch data
export function useProjects() {
  return useQuery({
    queryKey: ['projects'],
    queryFn: () => api.getProjects(),
    staleTime: 5 * 60 * 1000, // 5 minutes
  });
}

// Mutate data
export function useCreateProject() {
  const queryClient = useQueryClient();
  
  return useMutation({
    mutationFn: (data: CreateProjectDto) => api.createProject(data),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['projects'] });
      toast.success('Project created');
    },
    onError: (error) => {
      toast.error(error.message);
    },
  });
}

// Usage
function ProjectList() {
  const { data: projects, isLoading, error } = useProjects();
  const createProject = useCreateProject();
  
  if (isLoading) return <Spinner />;
  if (error) return <Error message={error.message} />;
  
  return (
    <div>
      {projects.map(project => (
        <ProjectCard key={project.id} project={project} />
      ))}
      <button onClick={() => createProject.mutate(newProjectData)}>
        Create
      </button>
    </div>
  );
}
```

### State Management Rules

✅ **DO**:
- Use the simplest solution that works
- Colocate state with components that use it
- Lift state only when necessary
- Use React Query for server state
- Use Zustand/Jotai for global client state

❌ **DON'T**:
- Put everything in global state
- Duplicate server state in client state
- Use Redux for simple applications
- Store derived state

---

## 15. Styling Guidelines (CSS/Tailwind)

### Tailwind CSS Best Practices

✅ **DO**:
- Use Tailwind utility classes
- Extract repeated patterns into components
- Use `@apply` sparingly for common patterns
- Use CSS variables for theming
- Follow consistent spacing scale

❌ **DON'T**:
- Write custom CSS when Tailwind works
- Use arbitrary values excessively `[123px]`
- Create overly long class strings
- Mix Tailwind with other CSS frameworks

### Class Organization

```tsx
// ✅ GOOD: Organized class order
<div
  className={cn(
    // Layout
    'flex items-center justify-between',
    // Sizing
    'w-full h-12',
    // Spacing
    'px-4 py-2',
    // Typography
    'text-sm font-medium',
    // Colors
    'bg-white text-gray-900',
    // Borders
    'border border-gray-200 rounded-lg',
    // Effects
    'shadow-sm hover:shadow-md',
    // Transitions
    'transition-shadow duration-200',
    // Conditional
    isActive && 'ring-2 ring-blue-500',
    // Custom classes
    className
  )}
>
```

### Responsive Design

```tsx
// ✅ GOOD: Mobile-first approach
<div className="
  flex flex-col         // Mobile: stack
  md:flex-row           // Tablet+: row
  lg:justify-between    // Desktop: spread
">
  <div className="
    w-full              // Mobile: full width
    md:w-1/2            // Tablet+: half width
    lg:w-1/3            // Desktop: third width
  ">
```

### Component Variants with cva

```typescript
import { cva, type VariantProps } from 'class-variance-authority';

const buttonVariants = cva(
  // Base styles
  'inline-flex items-center justify-center rounded-md font-medium transition-colors focus-visible:outline-none focus-visible:ring-2 disabled:pointer-events-none disabled:opacity-50',
  {
    variants: {
      variant: {
        default: 'bg-primary text-primary-foreground hover:bg-primary/90',
        destructive: 'bg-destructive text-destructive-foreground hover:bg-destructive/90',
        outline: 'border border-input bg-background hover:bg-accent',
        ghost: 'hover:bg-accent hover:text-accent-foreground',
      },
      size: {
        default: 'h-10 px-4 py-2',
        sm: 'h-9 rounded-md px-3',
        lg: 'h-11 rounded-md px-8',
        icon: 'h-10 w-10',
      },
    },
    defaultVariants: {
      variant: 'default',
      size: 'default',
    },
  }
);

interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {}

export function Button({ className, variant, size, ...props }: ButtonProps) {
  return (
    <button
      className={cn(buttonVariants({ variant, size, className }))}
      {...props}
    />
  );
}
```

### Dark Mode

```tsx
// ✅ GOOD: Use dark: variant
<div className="bg-white dark:bg-gray-900 text-gray-900 dark:text-white">
  <h1 className="text-black dark:text-white">Title</h1>
  <p className="text-gray-600 dark:text-gray-400">Content</p>
</div>

// Configure in tailwind.config.js
module.exports = {
  darkMode: 'class', // or 'media'
};
```

---

## 16. Node.js/Express Guidelines

### Project Structure

```
services/api/
├── src/
│   ├── routes/           # Route definitions
│   │   ├── index.ts
│   │   ├── auth.routes.ts
│   │   └── users.routes.ts
│   ├── controllers/      # Request handlers
│   │   ├── auth.controller.ts
│   │   └── users.controller.ts
│   ├── services/         # Business logic
│   │   ├── auth.service.ts
│   │   └── users.service.ts
│   ├── models/           # Data models
│   │   └── user.model.ts
│   ├── middleware/       # Express middleware
│   │   ├── auth.middleware.ts
│   │   ├── error.middleware.ts
│   │   └── validation.middleware.ts
│   ├── utils/            # Utilities
│   │   ├── logger.ts
│   │   └── errors.ts
│   ├── config/           # Configuration
│   │   └── index.ts
│   ├── types/            # TypeScript types
│   │   └── index.ts
│   └── app.ts            # Express app setup
├── tests/
├── package.json
└── tsconfig.json
```

### Route Definition

```typescript
// routes/users.routes.ts
import { Router } from 'express';
import { UsersController } from '../controllers/users.controller';
import { authMiddleware } from '../middleware/auth.middleware';
import { validate } from '../middleware/validation.middleware';
import { createUserSchema, updateUserSchema } from '../schemas/user.schema';

const router = Router();
const controller = new UsersController();

router.get('/', authMiddleware, controller.getAll);
router.get('/:id', authMiddleware, controller.getById);
router.post('/', authMiddleware, validate(createUserSchema), controller.create);
router.patch('/:id', authMiddleware, validate(updateUserSchema), controller.update);
router.delete('/:id', authMiddleware, controller.delete);

export default router;
```

### Controller Pattern

```typescript
// controllers/users.controller.ts
import { Request, Response, NextFunction } from 'express';
import { UsersService } from '../services/users.service';
import { AppError } from '../utils/errors';

export class UsersController {
  private usersService = new UsersService();
  
  getAll = async (req: Request, res: Response, next: NextFunction) => {
    try {
      const users = await this.usersService.findAll();
      res.json({ data: users });
    } catch (error) {
      next(error);
    }
  };
  
  getById = async (req: Request, res: Response, next: NextFunction) => {
    try {
      const { id } = req.params;
      const user = await this.usersService.findById(id);
      
      if (!user) {
        throw new AppError('User not found', 404);
      }
      
      res.json({ data: user });
    } catch (error) {
      next(error);
    }
  };
  
  create = async (req: Request, res: Response, next: NextFunction) => {
    try {
      const user = await this.usersService.create(req.body);
      res.status(201).json({ data: user });
    } catch (error) {
      next(error);
    }
  };
}
```

### Service Pattern

```typescript
// services/users.service.ts
import { prisma } from '../lib/prisma';
import { CreateUserDto, UpdateUserDto } from '../types';
import { hashPassword } from '../utils/auth';

export class UsersService {
  async findAll() {
    return prisma.user.findMany({
      select: {
        id: true,
        email: true,
        name: true,
        createdAt: true,
      },
    });
  }
  
  async findById(id: string) {
    return prisma.user.findUnique({
      where: { id },
    });
  }
  
  async create(data: CreateUserDto) {
    const hashedPassword = await hashPassword(data.password);
    
    return prisma.user.create({
      data: {
        ...data,
        password: hashedPassword,
      },
    });
  }
  
  async update(id: string, data: UpdateUserDto) {
    return prisma.user.update({
      where: { id },
      data,
    });
  }
  
  async delete(id: string) {
    return prisma.user.delete({
      where: { id },
    });
  }
}
```

### Error Handling Middleware

```typescript
// middleware/error.middleware.ts
import { Request, Response, NextFunction } from 'express';
import { AppError } from '../utils/errors';
import { logger } from '../utils/logger';

export function errorHandler(
  err: Error,
  req: Request,
  res: Response,
  next: NextFunction
) {
  logger.error(err);
  
  if (err instanceof AppError) {
    return res.status(err.statusCode).json({
      status: 'error',
      message: err.message,
    });
  }
  
  // Don't leak error details in production
  const message = process.env.NODE_ENV === 'production' 
    ? 'Internal server error' 
    : err.message;
  
  res.status(500).json({
    status: 'error',
    message,
  });
}
```

### Async Handler Wrapper

```typescript
// utils/async-handler.ts
import { Request, Response, NextFunction } from 'express';

type AsyncHandler = (
  req: Request,
  res: Response,
  next: NextFunction
) => Promise<any>;

export const asyncHandler = (fn: AsyncHandler) => {
  return (req: Request, res: Response, next: NextFunction) => {
    Promise.resolve(fn(req, res, next)).catch(next);
  };
};

// Usage
router.get('/', asyncHandler(async (req, res) => {
  const users = await usersService.findAll();
  res.json({ data: users });
}));
```

---

## 17. FastAPI/Python Guidelines

### Project Structure

```
services/api/
├── app/
│   ├── __init__.py
│   ├── main.py           # FastAPI app
│   ├── config.py         # Configuration
│   ├── database.py       # Database setup
│   ├── dependencies.py   # Dependency injection
│   ├── routers/          # Route definitions
│   │   ├── __init__.py
│   │   ├── auth.py
│   │   └── users.py
│   ├── models/           # SQLAlchemy models
│   │   ├── __init__.py
│   │   └── user.py
│   ├── schemas/          # Pydantic schemas
│   │   ├── __init__.py
│   │   └── user.py
│   ├── services/         # Business logic
│   │   ├── __init__.py
│   │   └── user_service.py
│   ├── utils/            # Utilities
│   │   ├── __init__.py
│   │   └── security.py
│   └── exceptions/       # Custom exceptions
│       └── __init__.py
├── tests/
├── alembic/              # Migrations
├── requirements.txt
└── pyproject.toml
```

### Main Application

```python
# app/main.py
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from app.routers import auth, users
from app.config import settings
from app.database import engine
from app.models import Base

app = FastAPI(
    title=settings.app_name,
    version="1.0.0",
    docs_url="/docs" if settings.debug else None,
)

# CORS
app.add_middleware(
    CORSMiddleware,
    allow_origins=settings.allowed_origins,
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# Routers
app.include_router(auth.router, prefix="/auth", tags=["auth"])
app.include_router(users.router, prefix="/users", tags=["users"])

@app.on_event("startup")
async def startup():
    async with engine.begin() as conn:
        await conn.run_sync(Base.metadata.create_all)

@app.get("/health")
async def health_check():
    return {"status": "healthy"}
```

### Router Definition

```python
# app/routers/users.py
from fastapi import APIRouter, Depends, HTTPException, status
from sqlalchemy.ext.asyncio import AsyncSession
from typing import List

from app.database import get_db
from app.schemas.user import UserCreate, UserUpdate, UserResponse
from app.services.user_service import UserService
from app.dependencies import get_current_user
from app.models.user import User

router = APIRouter()

@router.get("/", response_model=List[UserResponse])
async def get_users(
    skip: int = 0,
    limit: int = 100,
    db: AsyncSession = Depends(get_db),
    current_user: User = Depends(get_current_user)
):
    """Get all users with pagination."""
    service = UserService(db)
    return await service.get_all(skip=skip, limit=limit)

@router.get("/{user_id}", response_model=UserResponse)
async def get_user(
    user_id: str,
    db: AsyncSession = Depends(get_db),
    current_user: User = Depends(get_current_user)
):
    """Get user by ID."""
    service = UserService(db)
    user = await service.get_by_id(user_id)
    
    if not user:
        raise HTTPException(
            status_code=status.HTTP_404_NOT_FOUND,
            detail="User not found"
        )
    
    return user

@router.post("/", response_model=UserResponse, status_code=status.HTTP_201_CREATED)
async def create_user(
    user_data: UserCreate,
    db: AsyncSession = Depends(get_db)
):
    """Create a new user."""
    service = UserService(db)
    return await service.create(user_data)

@router.patch("/{user_id}", response_model=UserResponse)
async def update_user(
    user_id: str,
    user_data: UserUpdate,
    db: AsyncSession = Depends(get_db),
    current_user: User = Depends(get_current_user)
):
    """Update user."""
    service = UserService(db)
    return await service.update(user_id, user_data)

@router.delete("/{user_id}", status_code=status.HTTP_204_NO_CONTENT)
async def delete_user(
    user_id: str,
    db: AsyncSession = Depends(get_db),
    current_user: User = Depends(get_current_user)
):
    """Delete user."""
    service = UserService(db)
    await service.delete(user_id)
```

### Pydantic Schemas

```python
# app/schemas/user.py
from pydantic import BaseModel, EmailStr, Field
from datetime import datetime
from typing import Optional

class UserBase(BaseModel):
    email: EmailStr
    name: str = Field(..., min_length=1, max_length=100)

class UserCreate(UserBase):
    password: str = Field(..., min_length=8)

class UserUpdate(BaseModel):
    email: Optional[EmailStr] = None
    name: Optional[str] = Field(None, min_length=1, max_length=100)

class UserResponse(UserBase):
    id: str
    created_at: datetime
    updated_at: datetime
    
    class Config:
        from_attributes = True
```

### Service Layer

```python
# app/services/user_service.py
from sqlalchemy.ext.asyncio import AsyncSession
from sqlalchemy import select
from typing import List, Optional

from app.models.user import User
from app.schemas.user import UserCreate, UserUpdate
from app.utils.security import hash_password

class UserService:
    def __init__(self, db: AsyncSession):
        self.db = db
    
    async def get_all(self, skip: int = 0, limit: int = 100) -> List[User]:
        result = await self.db.execute(
            select(User).offset(skip).limit(limit)
        )
        return result.scalars().all()
    
    async def get_by_id(self, user_id: str) -> Optional[User]:
        result = await self.db.execute(
            select(User).where(User.id == user_id)
        )
        return result.scalar_one_or_none()
    
    async def get_by_email(self, email: str) -> Optional[User]:
        result = await self.db.execute(
            select(User).where(User.email == email)
        )
        return result.scalar_one_or_none()
    
    async def create(self, data: UserCreate) -> User:
        hashed_password = hash_password(data.password)
        
        user = User(
            email=data.email,
            name=data.name,
            password=hashed_password
        )
        
        self.db.add(user)
        await self.db.commit()
        await self.db.refresh(user)
        
        return user
    
    async def update(self, user_id: str, data: UserUpdate) -> User:
        user = await self.get_by_id(user_id)
        
        if not user:
            raise ValueError("User not found")
        
        update_data = data.model_dump(exclude_unset=True)
        
        for field, value in update_data.items():
            setattr(user, field, value)
        
        await self.db.commit()
        await self.db.refresh(user)
        
        return user
    
    async def delete(self, user_id: str) -> None:
        user = await self.get_by_id(user_id)
        
        if not user:
            raise ValueError("User not found")
        
        await self.db.delete(user)
        await self.db.commit()
```

### Python Code Style

✅ **DO**:
- Follow PEP 8 style guide
- Use type hints everywhere
- Use async/await for I/O operations
- Use Pydantic for validation
- Use dependency injection
- Keep functions small

❌ **DON'T**:
- Use `from module import *`
- Ignore type hints
- Use bare `except:`
- Mutate function arguments
- Use global state

### Python Formatting Tools

```bash
# Install tools
pip install black isort flake8 mypy

# Format code
black app/
isort app/

# Lint code
flake8 app/
mypy app/
```

---

## 18. Database Guidelines

### Schema Design Rules

✅ **DO**:
- Use UUIDs for primary keys
- Add `created_at` and `updated_at` to all tables
- Use foreign keys for relationships
- Add indexes for frequently queried columns
- Use meaningful table and column names
- Normalize data appropriately
- Use soft deletes for important data

❌ **DON'T**:
- Use auto-increment IDs (use UUIDs)
- Store JSON when structured data is better
- Create circular foreign keys
- Forget indexes on foreign keys
- Use reserved words as names

### Migration Best Practices

✅ **DO**:
- Use migrations for all schema changes
- Write reversible migrations
- Test migrations on copy of production data
- Keep migrations small and focused
- Name migrations descriptively

❌ **DON'T**:
- Modify production database directly
- Delete old migrations
- Put data changes in schema migrations
- Create breaking migrations without plan

### Query Best Practices

```typescript
// ✅ GOOD: Select only needed columns
const users = await prisma.user.findMany({
  select: {
    id: true,
    email: true,
    name: true,
  },
});

// ✅ GOOD: Use pagination
const users = await prisma.user.findMany({
  skip: page * pageSize,
  take: pageSize,
});

// ✅ GOOD: Use transactions for multiple operations
await prisma.$transaction([
  prisma.user.create({ data: userData }),
  prisma.profile.create({ data: profileData }),
]);

// ❌ BAD: Select all columns
const users = await prisma.user.findMany();

// ❌ BAD: N+1 queries
const posts = await prisma.post.findMany();
for (const post of posts) {
  const author = await prisma.user.findUnique({ where: { id: post.authorId } });
}

// ✅ GOOD: Use include to avoid N+1
const posts = await prisma.post.findMany({
  include: { author: true },
});
```

### Indexing Strategy

```sql
-- ✅ Index foreign keys
CREATE INDEX idx_posts_user_id ON posts(user_id);

-- ✅ Index frequently queried columns
CREATE INDEX idx_users_email ON users(email);

-- ✅ Composite index for multi-column queries
CREATE INDEX idx_posts_user_created ON posts(user_id, created_at DESC);

-- ✅ Partial index for filtered queries
CREATE INDEX idx_active_users ON users(email) WHERE status = 'active';
```

---

## 19. API Design Guidelines

### RESTful Conventions

✅ **DO**:
- Use nouns for resources (`/users`, not `/getUsers`)
- Use plural names (`/users`, not `/user`)
- Use HTTP methods correctly
- Use proper status codes
- Version your API
- Use consistent naming

❌ **DON'T**:
- Use verbs in URLs
- Mix singular and plural
- Return 200 for errors
- Change API without versioning

### HTTP Methods

| Method | Use Case | Example |
|--------|----------|---------|
| GET | Retrieve resource(s) | `GET /users/123` |
| POST | Create resource | `POST /users` |
| PUT | Replace resource | `PUT /users/123` |
| PATCH | Partial update | `PATCH /users/123` |
| DELETE | Remove resource | `DELETE /users/123` |

### HTTP Status Codes

| Code | Meaning | Use Case |
|------|---------|----------|
| 200 | OK | Successful GET, PATCH |
| 201 | Created | Successful POST |
| 204 | No Content | Successful DELETE |
| 400 | Bad Request | Validation error |
| 401 | Unauthorized | Missing/invalid auth |
| 403 | Forbidden | No permission |
| 404 | Not Found | Resource not found |
| 409 | Conflict | Duplicate resource |
| 422 | Unprocessable | Semantic error |
| 500 | Server Error | Unexpected error |

### Response Format

```typescript
// ✅ Success response
{
  "data": {
    "id": "123",
    "email": "user@example.com",
    "name": "John Doe"
  }
}

// ✅ List response with pagination
{
  "data": [
    { "id": "1", "name": "Item 1" },
    { "id": "2", "name": "Item 2" }
  ],
  "meta": {
    "total": 100,
    "page": 1,
    "pageSize": 10,
    "totalPages": 10
  }
}

// ✅ Error response
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input data",
    "details": [
      {
        "field": "email",
        "message": "Invalid email format"
      }
    ]
  }
}
```

### URL Structure

```
# ✅ Good URL patterns
GET    /api/v1/users              # List users
GET    /api/v1/users/123          # Get user
POST   /api/v1/users              # Create user
PATCH  /api/v1/users/123          # Update user
DELETE /api/v1/users/123          # Delete user

# Nested resources
GET    /api/v1/users/123/posts    # User's posts
POST   /api/v1/users/123/posts    # Create user's post

# Filtering and pagination
GET    /api/v1/users?status=active&page=2&limit=20

# ❌ Bad URL patterns
GET    /api/v1/getUsers           # Verb in URL
POST   /api/v1/user/create        # Redundant
GET    /api/v1/users/123/delete   # Should use DELETE method
```

---

## 20. Error Handling

### Frontend Error Handling

```typescript
// ✅ API call with error handling
async function fetchUser(id: string): Promise<User> {
  try {
    const response = await api.get(`/users/${id}`);
    return response.data;
  } catch (error) {
    if (error instanceof ApiError) {
      if (error.status === 404) {
        throw new Error('User not found');
      }
      if (error.status === 401) {
        // Redirect to login
        router.push('/login');
        throw new Error('Please log in');
      }
    }
    throw new Error('Failed to fetch user');
  }
}

// ✅ React Error Boundary
class ErrorBoundary extends React.Component {
  state = { hasError: false };
  
  static getDerivedStateFromError() {
    return { hasError: true };
  }
  
  componentDidCatch(error: Error, errorInfo: React.ErrorInfo) {
    logger.error('React error:', error, errorInfo);
  }
  
  render() {
    if (this.state.hasError) {
      return <ErrorFallback />;
    }
    return this.props.children;
  }
}
```

### Backend Error Handling

```typescript
// ✅ Custom error classes
class AppError extends Error {
  constructor(
    public message: string,
    public statusCode: number,
    public code?: string
  ) {
    super(message);
    this.name = 'AppError';
  }
}

class NotFoundError extends AppError {
  constructor(resource: string) {
    super(`${resource} not found`, 404, 'NOT_FOUND');
  }
}

class ValidationError extends AppError {
  constructor(message: string) {
    super(message, 400, 'VALIDATION_ERROR');
  }
}

// ✅ Usage
if (!user) {
  throw new NotFoundError('User');
}
```

### Error Handling Rules

✅ **DO**:
- Create custom error classes
- Log errors with context
- Return user-friendly messages
- Include error codes for client handling
- Handle async errors properly
- Use try-catch for expected errors

❌ **DON'T**:
- Swallow errors silently
- Expose internal details to users
- Log sensitive data
- Use generic error messages
- Throw strings instead of Error objects

---

## 21. Logging Guidelines

### What to Log

✅ **DO** log:
- Application startup/shutdown
- API requests (method, path, status, duration)
- Authentication events
- Errors with stack traces
- Important business events
- Performance metrics

❌ **DON'T** log:
- Passwords or secrets
- Personal data (emails, names)
- Credit card numbers
- Full request/response bodies
- Every single operation

### Log Levels

| Level | Use Case |
|-------|----------|
| ERROR | Errors requiring immediate attention |
| WARN | Potential issues, degraded state |
| INFO | Important business events |
| DEBUG | Detailed debugging information |
| TRACE | Very detailed tracing (dev only) |

### Logging Implementation

```typescript
// lib/logger.ts
import pino from 'pino';

export const logger = pino({
  level: process.env.LOG_LEVEL || 'info',
  transport: process.env.NODE_ENV === 'development' 
    ? { target: 'pino-pretty' } 
    : undefined,
});

// Usage
logger.info({ userId: user.id }, 'User logged in');
logger.error({ err, userId }, 'Failed to process payment');
logger.warn({ attempts }, 'Rate limit approaching');
```

### Structured Logging

```typescript
// ✅ GOOD: Structured logs
logger.info({
  event: 'user.created',
  userId: user.id,
  email: maskEmail(user.email),
  source: 'signup-form',
}, 'User created successfully');

// ❌ BAD: Unstructured logs
console.log(`User ${user.email} created`);
```

---

## 22. Testing Guidelines

### Test Types

| Type | Purpose | Tools |
|------|---------|-------|
| Unit | Test individual functions | Vitest, Jest |
| Integration | Test component interactions | Testing Library |
| E2E | Test complete flows | Playwright, Cypress |
| API | Test endpoints | Supertest |

### Test File Organization

```
src/
├── components/
│   └── Button/
│       ├── Button.tsx
│       └── Button.test.tsx
├── hooks/
│   └── useAuth/
│       ├── useAuth.ts
│       └── useAuth.test.ts
├── utils/
│   └── format/
│       ├── format.ts
│       └── format.test.ts
tests/
├── e2e/
│   └── auth.spec.ts
└── integration/
    └── api.test.ts
```

### Test Naming

```typescript
// ✅ GOOD: Descriptive test names
describe('UserService', () => {
  describe('create', () => {
    it('should create a user with valid data', () => {});
    it('should throw error if email already exists', () => {});
    it('should hash password before saving', () => {});
  });
});

// ❌ BAD: Vague test names
describe('UserService', () => {
  it('should work', () => {});
  it('test 1', () => {});
});
```

### Unit Test Template

```typescript
import { describe, it, expect, beforeEach, vi } from 'vitest';
import { formatCurrency } from './format';

describe('formatCurrency', () => {
  it('should format positive numbers with dollar sign', () => {
    expect(formatCurrency(100)).toBe('$100.00');
  });
  
  it('should handle zero', () => {
    expect(formatCurrency(0)).toBe('$0.00');
  });
  
  it('should format negative numbers with parentheses', () => {
    expect(formatCurrency(-100)).toBe('($100.00)');
  });
  
  it('should round to 2 decimal places', () => {
    expect(formatCurrency(100.999)).toBe('$101.00');
  });
});
```

### Component Test Template

```typescript
import { render, screen, fireEvent } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { LoginForm } from './LoginForm';

describe('LoginForm', () => {
  const mockOnSubmit = vi.fn();
  
  beforeEach(() => {
    mockOnSubmit.mockClear();
  });
  
  it('should render email and password fields', () => {
    render(<LoginForm onSubmit={mockOnSubmit} />);
    
    expect(screen.getByLabelText(/email/i)).toBeInTheDocument();
    expect(screen.getByLabelText(/password/i)).toBeInTheDocument();
  });
  
  it('should show validation errors for invalid email', async () => {
    const user = userEvent.setup();
    render(<LoginForm onSubmit={mockOnSubmit} />);
    
    await user.type(screen.getByLabelText(/email/i), 'invalid');
    await user.click(screen.getByRole('button', { name: /sign in/i }));
    
    expect(screen.getByText(/invalid email/i)).toBeInTheDocument();
    expect(mockOnSubmit).not.toHaveBeenCalled();
  });
  
  it('should call onSubmit with form data', async () => {
    const user = userEvent.setup();
    render(<LoginForm onSubmit={mockOnSubmit} />);
    
    await user.type(screen.getByLabelText(/email/i), 'test@example.com');
    await user.type(screen.getByLabelText(/password/i), 'password123');
    await user.click(screen.getByRole('button', { name: /sign in/i }));
    
    expect(mockOnSubmit).toHaveBeenCalledWith({
      email: 'test@example.com',
      password: 'password123',
    });
  });
});
```

### Testing Rules

✅ **DO**:
- Write tests for critical paths
- Test edge cases and error states
- Use meaningful assertions
- Keep tests independent
- Mock external dependencies
- Use factories for test data
- Aim for 80%+ coverage on critical code

❌ **DON'T**:
- Test implementation details
- Write tests that depend on order
- Use magic numbers without explanation
- Skip error case testing
- Write overly complex tests
- Aim for 100% coverage everywhere

---

## 23. Security Best Practices

### Authentication & Authorization

✅ **DO**:
- Use established auth libraries (NextAuth, Passport)
- Store passwords with bcrypt (cost factor 12+)
- Use JWT with short expiry
- Implement refresh token rotation
- Use HTTPS everywhere
- Validate JWT on every request

❌ **DON'T**:
- Store passwords in plain text
- Use MD5 or SHA1 for passwords
- Store JWT in localStorage (use httpOnly cookies)
- Use long-lived access tokens
- Trust client-side auth state

### Input Validation

```typescript
// ✅ GOOD: Validate all inputs
const userSchema = z.object({
  email: z.string().email(),
  password: z.string().min(8).max(100),
  name: z.string().min(1).max(100).trim(),
  age: z.number().int().positive().max(150),
});

// ✅ GOOD: Sanitize HTML input
import DOMPurify from 'dompurify';
const sanitizedHtml = DOMPurify.sanitize(userInput);

// ✅ GOOD: Parameterized queries (Prisma does this automatically)
const user = await prisma.user.findUnique({
  where: { email: userInput }, // Safe
});

// ❌ BAD: String concatenation in queries
const query = `SELECT * FROM users WHERE email = '${userInput}'`; // SQL injection!
```

### XSS Prevention

```typescript
// ✅ React automatically escapes JSX
<div>{userInput}</div> // Safe

// ⚠️ Dangerous - avoid if possible
<div dangerouslySetInnerHTML={{ __html: sanitizedHtml }} />

// ✅ Use Content Security Policy
// next.config.js
const securityHeaders = [
  {
    key: 'Content-Security-Policy',
    value: "default-src 'self'; script-src 'self' 'unsafe-inline'",
  },
];
```

### CSRF Prevention

```typescript
// ✅ Use SameSite cookies
res.cookie('token', jwt, {
  httpOnly: true,
  secure: true,
  sameSite: 'strict',
});

// ✅ Verify origin header
const origin = req.headers.origin;
if (!allowedOrigins.includes(origin)) {
  return res.status(403).json({ error: 'Invalid origin' });
}
```

### Secrets Management

✅ **DO**:
- Use environment variables
- Use secret management services (Vault, AWS Secrets)
- Rotate secrets regularly
- Use different secrets per environment
- Encrypt secrets at rest

❌ **DON'T**:
- Commit secrets to git
- Log secrets
- Share secrets in chat/email
- Use same secrets in dev and prod
- Hardcode secrets in code

### Security Headers

```typescript
// next.config.js
const securityHeaders = [
  { key: 'X-DNS-Prefetch-Control', value: 'on' },
  { key: 'Strict-Transport-Security', value: 'max-age=63072000; includeSubDomains' },
  { key: 'X-XSS-Protection', value: '1; mode=block' },
  { key: 'X-Frame-Options', value: 'DENY' },
  { key: 'X-Content-Type-Options', value: 'nosniff' },
  { key: 'Referrer-Policy', value: 'strict-origin-when-cross-origin' },
];
```

---

## 24. Performance Guidelines

### Frontend Performance

✅ **DO**:
- Use React.memo for expensive components
- Use useMemo/useCallback appropriately
- Lazy load routes and heavy components
- Optimize images (next/image)
- Use code splitting
- Minimize bundle size
- Use virtualization for long lists

❌ **DON'T**:
- Memoize everything (has overhead)
- Import entire libraries
- Render large lists without virtualization
- Block main thread
- Use unoptimized images

### Next.js Optimization

```typescript
// ✅ Use next/image for automatic optimization
import Image from 'next/image';

<Image
  src="/hero.jpg"
  alt="Hero"
  width={1200}
  height={600}
  priority // For above-the-fold images
/>

// ✅ Use dynamic imports for code splitting
import dynamic from 'next/dynamic';

const HeavyChart = dynamic(() => import('./HeavyChart'), {
  loading: () => <ChartSkeleton />,
  ssr: false, // Disable SSR for client-only components
});

// ✅ Use generateStaticParams for static generation
export async function generateStaticParams() {
  const posts = await getPosts();
  return posts.map((post) => ({ id: post.id }));
}
```

### Backend Performance

✅ **DO**:
- Use database indexes
- Implement caching (Redis)
- Use connection pooling
- Paginate large queries
- Use async operations
- Compress responses

❌ **DON'T**:
- Load all records at once
- Make synchronous I/O calls
- Ignore N+1 queries
- Cache without invalidation strategy

### Caching Strategy

```typescript
// ✅ React Query caching
const { data } = useQuery({
  queryKey: ['users', userId],
  queryFn: () => fetchUser(userId),
  staleTime: 5 * 60 * 1000, // Consider fresh for 5 minutes
  cacheTime: 30 * 60 * 1000, // Keep in cache for 30 minutes
});

// ✅ HTTP caching headers
res.setHeader('Cache-Control', 'public, max-age=3600, stale-while-revalidate=86400');

// ✅ Redis caching
async function getUser(id: string): Promise<User> {
  const cached = await redis.get(`user:${id}`);
  if (cached) return JSON.parse(cached);
  
  const user = await db.user.findUnique({ where: { id } });
  await redis.setex(`user:${id}`, 3600, JSON.stringify(user));
  
  return user;
}
```

---

## 25. Accessibility Guidelines

### WCAG Compliance

Target WCAG 2.1 Level AA compliance.

### Essential Rules

✅ **DO**:
- Use semantic HTML elements
- Add alt text to all images
- Ensure sufficient color contrast (4.5:1)
- Make all interactive elements keyboard accessible
- Use ARIA labels when necessary
- Support screen readers
- Allow text resizing up to 200%

❌ **DON'T**:
- Use color alone to convey information
- Remove focus indicators
- Use generic link text ("click here")
- Auto-play media
- Use CAPTCHA without alternatives

### Semantic HTML

```tsx
// ✅ GOOD: Semantic HTML
<header>
  <nav aria-label="Main navigation">
    <ul>
      <li><a href="/">Home</a></li>
      <li><a href="/about">About</a></li>
    </ul>
  </nav>
</header>

<main>
  <article>
    <h1>Article Title</h1>
    <p>Content...</p>
  </article>
</main>

<footer>
  <p>© 2025 Company</p>
</footer>

// ❌ BAD: Div soup
<div class="header">
  <div class="nav">
    <div><a href="/">Home</a></div>
  </div>
</div>
```

### Keyboard Navigation

```tsx
// ✅ GOOD: Keyboard accessible
<button onClick={handleClick}>
  Click me
</button>

// ✅ GOOD: Custom keyboard handler
<div
  role="button"
  tabIndex={0}
  onClick={handleClick}
  onKeyDown={(e) => {
    if (e.key === 'Enter' || e.key === ' ') {
      handleClick();
    }
  }}
>
  Click me
</div>

// ❌ BAD: Not keyboard accessible
<div onClick={handleClick}>Click me</div>
```

### ARIA Labels

```tsx
// ✅ GOOD: Descriptive labels
<button aria-label="Close dialog">
  <XIcon />
</button>

<input
  type="search"
  aria-label="Search projects"
  placeholder="Search..."
/>

// ✅ GOOD: Describe regions
<nav aria-label="Main navigation">...</nav>
<nav aria-label="Footer navigation">...</nav>

// ✅ GOOD: Live regions for updates
<div role="status" aria-live="polite">
  {message}
</div>
```

### Focus Management

```tsx
// ✅ GOOD: Manage focus in dialogs
function Dialog({ isOpen, onClose, children }) {
  const closeButtonRef = useRef<HTMLButtonElement>(null);
  
  useEffect(() => {
    if (isOpen) {
      closeButtonRef.current?.focus();
    }
  }, [isOpen]);
  
  return (
    <dialog open={isOpen}>
      <button ref={closeButtonRef} onClick={onClose}>
        Close
      </button>
      {children}
    </dialog>
  );
}
```

---

## 26. Documentation Standards

### Code Comments

✅ **DO**:
- Comment WHY, not WHAT
- Document complex algorithms
- Add JSDoc for public APIs
- Keep comments up to date
- Use TODO/FIXME with tickets

❌ **DON'T**:
- Comment obvious code
- Leave outdated comments
- Write essays in comments
- Comment out code (delete it)

### JSDoc Examples

```typescript
/**
 * Calculates the total price of items including tax.
 * 
 * @param items - Array of items with price property
 * @param taxRate - Tax rate as decimal (e.g., 0.08 for 8%)
 * @returns Total price including tax, rounded to 2 decimal places
 * 
 * @example
 * ```typescript
 * const total = calculateTotal([{ price: 10 }, { price: 20 }], 0.08);
 * console.log(total); // 32.40
 * ```
 */
function calculateTotal(items: Item[], taxRate: number): number {
  const subtotal = items.reduce((sum, item) => sum + item.price, 0);
  const total = subtotal * (1 + taxRate);
  return Math.round(total * 100) / 100;
}
```

### README Structure

```markdown
# Project Name

Brief description of the project.

## Features

- Feature 1
- Feature 2

## Getting Started

### Prerequisites

- Node.js 18+
- Docker

### Installation

```bash
npm install
```

### Development

```bash
npm run dev
```

## Usage

How to use the project.

## API Reference

Link to API docs.

## Contributing

Link to CONTRIBUTING.md.

## License

MIT License
```

### API Documentation

Document all endpoints with:
- HTTP method and path
- Description
- Request parameters
- Request body schema
- Response schema
- Error responses
- Example request/response

---

## 27. Docker Guidelines

### Dockerfile Best Practices

```dockerfile
# ✅ GOOD: Multi-stage build
# Stage 1: Dependencies
FROM node:18-alpine AS deps
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

# Stage 2: Build
FROM node:18-alpine AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

# Stage 3: Production
FROM node:18-alpine AS runner
WORKDIR /app

# Security: Don't run as root
RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

COPY --from=builder /app/public ./public
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static

USER nextjs

EXPOSE 3000
ENV PORT 3000

CMD ["node", "server.js"]
```

### Docker Compose

```yaml
# docker-compose.yml
version: '3.8'

services:
  web:
    build:
      context: .
      dockerfile: docker/Dockerfile.web
    ports:
      - "3000:3000"
    environment:
      - DATABASE_URL=postgresql://postgres:postgres@db:5432/storyweaver
      - REDIS_URL=redis://redis:6379
    depends_on:
      - db
      - redis
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 30s
      timeout: 10s
      retries: 3

  api:
    build:
      context: .
      dockerfile: docker/Dockerfile.api
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://postgres:postgres@db:5432/storyweaver
    depends_on:
      - db

  db:
    image: postgres:14-alpine
    volumes:
      - postgres_data:/var/lib/postgresql/data
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres
      - POSTGRES_DB=storyweaver
    ports:
      - "5432:5432"

  redis:
    image: redis:7-alpine
    volumes:
      - redis_data:/data
    ports:
      - "6379:6379"

volumes:
  postgres_data:
  redis_data:
```

### Docker Rules

✅ **DO**:
- Use specific image tags (not `latest`)
- Use multi-stage builds
- Use `.dockerignore`
- Run as non-root user
- Use health checks
- Keep images small
- Use docker-compose for local dev

❌ **DON'T**:
- Include secrets in images
- Use root user
- Copy unnecessary files
- Use `latest` tag in production
- Store data in containers

### .dockerignore

```
node_modules
.git
.gitignore
*.md
.env*
.next
coverage
.nyc_output
*.log
Dockerfile*
docker-compose*
```

---

## 28. CI/CD Guidelines

### GitHub Actions Workflow

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'
      - run: npm ci
      - run: npm run lint
      - run: npm run type-check

  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'
      - run: npm ci
      - run: npm run test:coverage
      - uses: codecov/codecov-action@v3

  build:
    runs-on: ubuntu-latest
    needs: [lint, test]
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'
      - run: npm ci
      - run: npm run build

  e2e:
    runs-on: ubuntu-latest
    needs: build
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'
      - run: npm ci
      - run: npx playwright install --with-deps
      - run: npm run test:e2e
```

### CI/CD Rules

✅ **DO**:
- Run lint, test, build on every PR
- Cache dependencies
- Run E2E tests on staging
- Use environment-specific configs
- Automate deployments
- Use branch protection rules

❌ **DON'T**:
- Skip tests for "quick fixes"
- Deploy without testing
- Share production secrets in CI
- Ignore failing checks

---

## 29. Environment Variables

### Naming Convention

```bash
# Format: [APP]_[CATEGORY]_[NAME]

# Database
DATABASE_URL=postgresql://...
DATABASE_POOL_SIZE=10

# Authentication
AUTH_SECRET=...
AUTH_GOOGLE_CLIENT_ID=...
AUTH_GOOGLE_CLIENT_SECRET=...

# External APIs
ANTHROPIC_API_KEY=...
STRIPE_SECRET_KEY=...

# App Configuration
NEXT_PUBLIC_APP_URL=http://localhost:3000
NEXT_PUBLIC_API_URL=http://localhost:8000

# Feature Flags
FEATURE_NEW_EDITOR=true
```

### Environment Files

```bash
.env                  # Default values (committed)
.env.local            # Local overrides (not committed)
.env.development      # Development values
.env.production       # Production values
.env.test             # Test values
.env.example          # Example with all keys (committed)
```

### Rules

✅ **DO**:
- Use `.env.example` as template
- Validate required variables at startup
- Use `NEXT_PUBLIC_` prefix for client-side variables
- Document all variables
- Use different values per environment

❌ **DON'T**:
- Commit `.env` with secrets
- Use production values locally
- Hardcode environment-specific values
- Forget to add new variables to `.env.example`

---

## 30. Dependency Management

### Package.json Best Practices

```json
{
  "name": "storyweaver-ai",
  "version": "1.0.0",
  "private": true,
  "engines": {
    "node": ">=18.0.0",
    "npm": ">=9.0.0"
  },
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "eslint . --ext .ts,.tsx",
    "lint:fix": "eslint . --ext .ts,.tsx --fix",
    "type-check": "tsc --noEmit",
    "test": "vitest",
    "test:coverage": "vitest --coverage",
    "test:e2e": "playwright test",
    "format": "prettier --write .",
    "prepare": "husky"
  }
}
```

### Dependency Rules

✅ **DO**:
- Use exact versions in production
- Audit dependencies regularly
- Keep dependencies updated
- Use `npm ci` in CI
- Check bundle size impact
- Prefer well-maintained packages

❌ **DON'T**:
- Use `*` or `latest` versions
- Install unnecessary packages
- Ignore security vulnerabilities
- Use deprecated packages
- Add dependencies without review

### Dependency Commands

```bash
# Install dependencies
npm install

# Install exact version
npm install package@1.2.3 --save-exact

# Check for updates
npm outdated

# Update dependencies
npm update

# Audit for vulnerabilities
npm audit
npm audit fix

# Check bundle size
npx bundlephobia package-name
```

---

## 31. Internationalization (i18n)

### Setup with next-intl

```typescript
// messages/en.json
{
  "common": {
    "save": "Save",
    "cancel": "Cancel",
    "delete": "Delete"
  },
  "auth": {
    "signIn": "Sign in",
    "signOut": "Sign out",
    "welcome": "Welcome, {name}!"
  },
  "errors": {
    "required": "{field} is required",
    "minLength": "{field} must be at least {min} characters"
  }
}

// Usage in component
import { useTranslations } from 'next-intl';

function LoginButton() {
  const t = useTranslations('auth');
  return <button>{t('signIn')}</button>;
}

function WelcomeMessage({ name }: { name: string }) {
  const t = useTranslations('auth');
  return <p>{t('welcome', { name })}</p>;
}
```

### i18n Rules

✅ **DO**:
- Extract all user-facing strings
- Use placeholders for dynamic content
- Support RTL languages
- Format dates/numbers for locale
- Use ICU message format

❌ **DON'T**:
- Concatenate translated strings
- Hardcode text in components
- Assume date/number formats
- Forget pluralization

---

## 32. Monitoring & Observability

### Error Tracking

```typescript
// lib/sentry.ts
import * as Sentry from '@sentry/nextjs';

Sentry.init({
  dsn: process.env.SENTRY_DSN,
  environment: process.env.NODE_ENV,
  tracesSampleRate: 1.0,
});

// Usage
try {
  await riskyOperation();
} catch (error) {
  Sentry.captureException(error, {
    extra: { userId, operation: 'riskyOperation' },
  });
  throw error;
}
```

### Health Checks

```typescript
// app/api/health/route.ts
export async function GET() {
  const checks = {
    database: await checkDatabase(),
    redis: await checkRedis(),
    externalApi: await checkExternalApi(),
  };
  
  const healthy = Object.values(checks).every(Boolean);
  
  return Response.json(
    { status: healthy ? 'healthy' : 'unhealthy', checks },
    { status: healthy ? 200 : 503 }
  );
}
```

### Metrics to Track

- Response times (p50, p95, p99)
- Error rates
- Request volume
- Database query times
- Cache hit rates
- Memory usage
- CPU usage

---

## 33. Release Process

### Semantic Versioning

```
MAJOR.MINOR.PATCH

MAJOR: Breaking changes
MINOR: New features (backward compatible)
PATCH: Bug fixes (backward compatible)

Examples:
1.0.0 - Initial release
1.1.0 - New feature added
1.1.1 - Bug fix
2.0.0 - Breaking change
```

### Release Checklist

- [ ] All tests passing
- [ ] Code reviewed and approved
- [ ] CHANGELOG updated
- [ ] Version bumped
- [ ] Documentation updated
- [ ] Security audit passed
- [ ] Performance tested
- [ ] Rollback plan ready
- [ ] Stakeholders notified

### CHANGELOG Format

```markdown
# Changelog

## [1.2.0] - 2025-01-15

### Added
- New book compilation feature
- Support for audio transcription

### Changed
- Improved anecdote editor performance
- Updated AI model to Claude 3.5

### Fixed
- Fixed login redirect issue
- Resolved image upload timeout

### Security
- Updated dependencies to patch vulnerabilities
```

---

## 34. Troubleshooting Common Issues

### Node.js Issues

```bash
# Clear npm cache
npm cache clean --force

# Delete node_modules and reinstall
rm -rf node_modules package-lock.json
npm install

# Check Node version
node -v
nvm use 18
```

### Next.js Issues

```bash
# Clear Next.js cache
rm -rf .next

# Check for port conflicts
lsof -i :3000
kill -9 <PID>
```

### Database Issues

```bash
# Reset database
npx prisma db push --force-reset

# Generate client
npx prisma generate

# View database
npx prisma studio
```

### Docker Issues

```bash
# Remove all containers and volumes
docker-compose down -v

# Rebuild without cache
docker-compose build --no-cache

# Check logs
docker-compose logs -f service_name
```

### Common Errors

| Error | Solution |
|-------|----------|
| `ENOENT` | File/directory not found - check paths |
| `EADDRINUSE` | Port in use - kill process or change port |
| `MODULE_NOT_FOUND` | Run `npm install` |
| `Type error` | Run `npm run type-check` |
| `Hydration mismatch` | Check server/client rendering |

---

## Quick Reference Card

### Essential Commands

```bash
# Development
npm run dev          # Start dev server
npm run build        # Build for production
npm run lint         # Run linter
npm run test         # Run tests
npm run type-check   # Check types

# Git
git checkout -b feature/name  # Create branch
git add -p                    # Interactive staging
git commit -m "type: message" # Commit
git push origin branch        # Push
git pull --rebase origin main # Update

# Docker
docker-compose up -d   # Start services
docker-compose down    # Stop services
docker-compose logs -f # View logs
```

### File Naming

```
components/UserCard.tsx     # React component
hooks/use-auth.ts          # Custom hook
utils/format-date.ts       # Utility function
types/user.ts              # TypeScript types
user-profile/              # Folder: kebab-case
```

### Commit Format

```
type(scope): subject

feat(auth): add Google OAuth
fix(api): resolve timeout error
docs: update README
```

---

## Contributing Checklist

Before submitting a PR, ensure:

- [ ] Code follows all guidelines in this document
- [ ] All tests pass (`npm run test`)
- [ ] No linting errors (`npm run lint`)
- [ ] No type errors (`npm run type-check`)
- [ ] Code is formatted (`npm run format`)
- [ ] Documentation is updated
- [ ] Commit messages follow conventions
- [ ] PR description is complete
- [ ] Self-review completed

---

*Thank you for contributing to StoryWeaver AI! Following these guidelines helps maintain a high-quality, consistent codebase.*

**Version**: 1.0  
**Last Updated**: November 2025