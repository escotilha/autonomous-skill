# Autonomous Agent Examples

## Story Splitting Patterns

### Too Big -> Split Into
| Original | Split Into |
|----------|-----------|
| Build entire dashboard | 1. Create layout, 2. Add nav, 3. Build each widget |
| Add authentication | 1. User model, 2. Login endpoint, 3. Session handling, 4. UI |
| Refactor API | 1. Extract types, 2. Update endpoint X, 3. Update endpoint Y |

## Acceptance Criteria Templates

### API Endpoint
```markdown
- [ ] Endpoint responds with correct status codes
- [ ] Request validation works
- [ ] Response matches schema
- [ ] Typecheck passes
- [ ] Tests pass
```

### UI Component
```markdown
- [ ] Component renders correctly
- [ ] Props are typed
- [ ] Responsive on mobile/desktop
- [ ] Accessibility: keyboard nav, ARIA
- [ ] Typecheck passes
```

### Database Migration
```markdown
- [ ] Migration runs successfully
- [ ] Rollback works
- [ ] Existing data preserved
- [ ] Indexes added for query patterns
```

## Complete prd.json Example

```json
{
  "project": "Task Management App",
  "branchName": "feature/task-filtering",
  "description": "Add ability to filter tasks by status, date, and assignee",
  "createdAt": "2025-01-10T10:00:00Z",
  "verification": {
    "typecheck": "npm run typecheck",
    "test": "npm run test",
    "lint": "npm run lint"
  },
  "userStories": [
    {
      "id": "US-001",
      "title": "Add filter dropdown component",
      "description": "As a user, I want a filter dropdown so I can select filter criteria",
      "acceptanceCriteria": [
        "Dropdown opens on click",
        "Shows status, date, assignee options",
        "Typecheck passes"
      ],
      "priority": 1,
      "dependsOn": [],
      "passes": false,
      "attempts": 0,
      "notes": ""
    }
  ]
}
```

## progress.md Format

```markdown
# Progress Log: Task Filtering

Branch: `feature/task-filtering`
Started: 2025-01-10

---

## 2025-01-10 10:30 - US-001: Add filter dropdown component

**Implementation:**
- Created FilterDropdown component in src/components/
- Used Radix UI Popover for dropdown
- Added filter icon button to toolbar

**Learnings:**
- Existing Button component accepts icon prop
- Toolbar has specific spacing requirements (gap-2)

**Files Changed:**
- src/components/FilterDropdown.tsx (new)
- src/components/Toolbar.tsx (modified)

---
```

## Smart Delegation Examples

### Story Type Detection

Examples of how different stories are classified:

#### Frontend Story
```json
{
  "id": "US-001",
  "title": "Add dark mode toggle to settings page",
  "description": "As a user, I want a dark mode toggle button in settings",
  "acceptanceCriteria": [
    "Toggle button renders on settings page",
    "Clicking toggle switches theme",
    "Theme persists in localStorage"
  ]
}
```
**Detected Type:** `frontend`
**Signals:** `{ frontend: 5, backend: 0, api: 0 }`
**Keywords found:** "toggle", "button", "settings page", "renders"
**Delegated to:** `frontend-agent`

#### API Story
```json
{
  "id": "US-002",
  "title": "Add user profile endpoint",
  "description": "As a frontend dev, I want GET /api/users/:id endpoint",
  "acceptanceCriteria": [
    "GET /api/users/:id returns user object",
    "Returns 404 if user not found",
    "Returns 401 if not authenticated"
  ]
}
```
**Detected Type:** `api`
**Signals:** `{ api: 4, backend: 4, frontend: 0 }`
**Keywords found:** "endpoint", "GET", "api/users", "returns"
**Delegated to:** `api-agent`

#### Database Story
```json
{
  "id": "US-003",
  "title": "Add email column to users table",
  "description": "As a developer, I need an email field in the users schema",
  "acceptanceCriteria": [
    "Migration adds email column",
    "Email is unique and required",
    "Migration is reversible"
  ]
}
```
**Detected Type:** `database`
**Signals:** `{ database: 4, api: 0, frontend: 0 }`
**Keywords found:** "column", "table", "schema", "migration"
**Delegated to:** `database-agent`

#### DevOps Story
```json
{
  "id": "US-004",
  "title": "Set up CI/CD pipeline for testing",
  "description": "As a team, we want automated tests on every PR",
  "acceptanceCriteria": [
    "GitHub Actions workflow runs on PR",
    "Runs typecheck and tests",
    "Fails PR if tests fail"
  ]
}
```
**Detected Type:** `devops`
**Signals:** `{ devops: 3, api: 0, frontend: 0 }`
**Keywords found:** "CI/CD", "GitHub Actions", "workflow"
**Delegated to:** `devops-agent`

#### Fullstack Story
```json
{
  "id": "US-005",
  "title": "Implement OAuth login flow",
  "description": "As a user, I want to log in with Google OAuth",
  "acceptanceCriteria": [
    "Login button in UI redirects to OAuth",
    "Backend handles OAuth callback",
    "Session stored in database",
    "User redirected to dashboard after login"
  ]
}
```
**Detected Type:** `fullstack`
**Signals:** `{ fullstack: 2, frontend: 3, backend: 2, database: 1 }`
**Keywords found:** "login button", "UI", "backend handles", "database"
**Delegated to:** `orchestrator-fullstack`

#### General/Unclear Story
```json
{
  "id": "US-006",
  "title": "Fix bug in app",
  "description": "Something is broken, please fix",
  "acceptanceCriteria": [
    "Bug is fixed",
    "App works"
  ]
}
```
**Detected Type:** `general`
**Signals:** `{ frontend: 0, backend: 0, api: 0, database: 0 }`
**No clear signals** → Direct implementation (no delegation)

### Delegation Flow Examples

#### Successful Delegation

```
## Starting: US-002 - Add user profile endpoint

Story Analysis:
- Detected type: API endpoint
- Signals: { api: 4, backend: 4, frontend: 0 }
- Selected agent: api-agent
- Agent status: Available ✓

Delegating to api-agent...

[api-agent working...]

✓ Read app/api/users/route.ts
✓ Created app/api/users/[id]/route.ts
✓ Added authentication middleware
✓ Typecheck passed
✓ Tests passed (2/2)

RESULT: SUCCESS

Files changed:
- app/api/users/[id]/route.ts (new)

Verification:
- Typecheck: PASS
- Tests: PASS - 2/2 passed

Implementation notes:
Used existing auth middleware pattern. Returns user object with id, name, email fields. Added 404 and 401 error handling.

Learnings:
Auth middleware is in lib/auth.ts. Error responses follow { error: string, code: number } format.

api-agent completed in 2m 34s

✓ US-002 complete (attempt 1)
  Implemented by: api-agent
  Files changed: 1

Updating prd.json... ✓
Committing changes... ✓

3 stories remaining.

Continuing to next story...
```

#### Delegation with Fallback

```
## Starting: US-007 - Refactor authentication logic

Story Analysis:
- Detected type: backend
- Signals: { backend: 2, frontend: 1, api: 1 }
- Selected agent: backend-agent
- Agent status: Not available ⚠

⚠ Agent 'backend-agent' not found
Reason: Skill not installed

Falling back to direct implementation...

[Direct implementation proceeds...]
```

#### Delegation Failure with Retry

```
## Starting: US-003 - Add email column to users table

Story Analysis:
- Detected type: database
- Selected agent: database-agent

Delegating to database-agent...

[database-agent attempt 1...]

RESULT: FAILURE

Verification:
- Migration test: FAIL

Error: Migration file has syntax error on line 12

Incrementing attempts (1/3)...

Retrying delegation to database-agent...

[database-agent attempt 2...]

✓ Fixed syntax error
✓ Migration runs successfully
✓ Rollback works

RESULT: SUCCESS

database-agent completed in 1m 52s (attempt 2)
```

### prd.json with Delegation

Complete example with delegation fields:

```json
{
  "project": "User Management System",
  "branchName": "feature/user-profiles",
  "description": "Add user profile viewing and editing",
  "createdAt": "2025-01-15T10:00:00Z",
  "delegation": {
    "enabled": true,
    "fallbackToDirect": true
  },
  "verification": {
    "typecheck": "npm run typecheck",
    "test": "npm run test"
  },
  "userStories": [
    {
      "id": "US-001",
      "title": "Add users table schema",
      "description": "As a developer, I need a users table with id, name, email",
      "acceptanceCriteria": [
        "Migration creates users table",
        "Has id, name, email columns",
        "Migration is reversible"
      ],
      "priority": 1,
      "dependsOn": [],
      "passes": true,
      "attempts": 1,
      "notes": "",
      "detectedType": "database",
      "delegatedTo": "database-agent",
      "completedAt": "2025-01-15T10:15:00Z"
    },
    {
      "id": "US-002",
      "title": "Create user profile API endpoint",
      "description": "As a frontend, I want GET /api/users/:id",
      "acceptanceCriteria": [
        "Returns user with id, name, email",
        "Returns 404 if not found",
        "Typecheck passes"
      ],
      "priority": 2,
      "dependsOn": ["US-001"],
      "passes": true,
      "attempts": 1,
      "notes": "",
      "detectedType": "api",
      "delegatedTo": "api-agent",
      "completedAt": "2025-01-15T10:28:00Z"
    },
    {
      "id": "US-003",
      "title": "Build profile page UI",
      "description": "As a user, I want to view my profile",
      "acceptanceCriteria": [
        "Profile page shows name and email",
        "Fetches data from API",
        "Shows loading state"
      ],
      "priority": 3,
      "dependsOn": ["US-002"],
      "passes": true,
      "attempts": 2,
      "notes": "First attempt had missing loading state",
      "detectedType": "frontend",
      "delegatedTo": "frontend-agent",
      "completedAt": "2025-01-15T10:45:00Z"
    },
    {
      "id": "US-004",
      "title": "Add edit profile functionality",
      "description": "As a user, I want to edit my name and email",
      "acceptanceCriteria": [
        "Edit form with name/email fields",
        "PUT /api/users/:id endpoint updates user",
        "Optimistic UI updates",
        "Typecheck and tests pass"
      ],
      "priority": 4,
      "dependsOn": ["US-003"],
      "passes": false,
      "attempts": 0,
      "notes": "",
      "detectedType": "fullstack",
      "delegatedTo": null
    }
  ]
}
```

### progress.md with Delegation

```markdown
# Progress Log: User Profiles

Branch: `feature/user-profiles`
Started: 2025-01-15

---

## Delegation Statistics

Total stories: 4
Completed: 3 (75%)
In progress: 1

Delegation breakdown:
- database-agent: 1 story (100% success)
- api-agent: 1 story (100% success)
- frontend-agent: 1 story (50% first-attempt, 100% after retry)

---

## 2025-01-15 10:15 - US-001: Add users table schema

**Delegated to:** database-agent
**Attempt:** 1
**Duration:** 2m 15s

**Implementation:**
- Created migration file: migrations/001_create_users.sql
- Added id (uuid primary key), name (text), email (text unique)
- Added down migration for rollback

**Learnings:**
- Project uses raw SQL migrations
- Migration files are numbered sequentially
- All tables have created_at/updated_at columns by convention

**Files Changed:**
- migrations/001_create_users.sql (new)

**Verification:**
- Typecheck: PASS
- Test: PASS - migration test suite

---

## 2025-01-15 10:28 - US-002: Create user profile API endpoint

**Delegated to:** api-agent
**Attempt:** 1
**Duration:** 2m 34s

**Implementation:**
- Created app/api/users/[id]/route.ts
- GET endpoint fetches from users table
- Added 404 for missing users, 401 for unauthenticated

**Learnings:**
- Auth middleware is in lib/auth.ts
- Error format: { error: string, code: number }
- Database client is Supabase

**Files Changed:**
- app/api/users/[id]/route.ts (new)

**Verification:**
- Typecheck: PASS
- Test: PASS - 2/2 endpoint tests

---

## 2025-01-15 10:45 - US-003: Build profile page UI

**Delegated to:** frontend-agent
**Attempt:** 2
**Duration:** 3m 12s (including retry)

**Implementation:**
- Created app/profile/page.tsx
- Added useUser hook for fetching user data
- Shows loading spinner while fetching
- Displays name and email in card layout

**Learnings:**
- First attempt forgot loading state in acceptance criteria
- Used existing Card and Spinner components
- Profile layout follows dashboard pattern (max-w-2xl mx-auto)

**Files Changed:**
- app/profile/page.tsx (new)
- hooks/useUser.ts (new)

**Verification:**
- Typecheck: PASS
- Test: PASS - 3/3 component tests

---
```

### Enabling Delegation

To enable smart delegation in your project:

1. **Edit prd.json:**
   ```json
   {
     "delegation": {
       "enabled": true,
       "fallbackToDirect": true
     }
   }
   ```

2. **Install specialized agents** (optional but recommended):
   ```bash
   # Install frontend agent
   git clone https://github.com/user/frontend-agent ~/.claude/skills/frontend-agent

   # Install API agent
   git clone https://github.com/user/api-agent ~/.claude/skills/api-agent
   ```

3. **Run autonomous-dev as normal:**
   - Detection happens automatically in Step 3.0a
   - Delegation occurs in Step 3.2 if enabled
   - Falls back to direct implementation if agent unavailable
