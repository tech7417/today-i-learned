# Folder Structure & Branching Strategy
## Senior Engineer Daily Workflow Guide — @tech7417

---

## 📁 PART 1 — FOLDER STRUCTURES

### 1. Backend API Repo (Production-Grade)

```
backend-api/
├── .github/
│   ├── workflows/
│   │   ├── ci.yml               ← lint + test on every PR
│   │   └── deploy.yml           ← deploy on merge to main
│   └── PULL_REQUEST_TEMPLATE.md
│
├── src/
│   ├── api/
│   │   └── v1/                  ← version your API from day 1
│   │       ├── routes/
│   │       │   ├── auth.routes.js
│   │       │   └── user.routes.js
│   │       ├── controllers/     ← handles HTTP layer only
│   │       │   ├── auth.controller.js
│   │       │   └── user.controller.js
│   │       └── validators/      ← input validation (joi/zod)
│   │           └── user.validator.js
│   │
│   ├── services/                ← business logic lives here
│   │   ├── auth.service.js
│   │   └── user.service.js
│   │
│   ├── repositories/            ← database queries isolated here
│   │   ├── user.repository.js
│   │   └── base.repository.js
│   │
│   ├── models/                  ← DB schema/models
│   │   └── user.model.js
│   │
│   ├── middleware/
│   │   ├── auth.middleware.js
│   │   ├── error.middleware.js
│   │   └── rateLimiter.middleware.js
│   │
│   ├── config/
│   │   ├── db.js
│   │   ├── redis.js
│   │   └── env.js               ← validate env vars at startup
│   │
│   ├── utils/
│   │   ├── logger.js
│   │   ├── response.js          ← consistent API response format
│   │   └── crypto.js
│   │
│   └── app.js                   ← Express app setup (no server.listen here)
│
├── tests/
│   ├── unit/                    ← test services and utils in isolation
│   │   └── user.service.test.js
│   ├── integration/             ← test routes with supertest + real DB
│   │   └── user.routes.test.js
│   └── fixtures/                ← seed data for tests
│
├── docs/
│   └── api.md                   ← or use Swagger/OpenAPI
│
├── .env.example                 ← always commit this, never .env
├── .gitignore
├── Dockerfile
├── docker-compose.yml
├── package.json
└── README.md
```

**Key principle:** Controller → Service → Repository → Model
Each layer only talks to the one below it. Never call a repository from a controller.

---

### 2. Microservices / Monorepo

```
monorepo/
├── services/
│   ├── user-service/            ← standalone Node.js app
│   │   ├── src/
│   │   ├── Dockerfile
│   │   └── package.json
│   ├── order-service/
│   └── notification-service/
│
├── packages/                    ← shared libraries
│   ├── logger/
│   ├── auth-middleware/
│   └── db-client/
│
├── gateway/                     ← API Gateway
│   └── src/
│
├── infra/
│   ├── docker-compose.yml
│   └── k8s/
│       ├── deployments/
│       └── services/
│
├── .github/
│   └── workflows/
│       ├── ci-user-service.yml
│       └── ci-order-service.yml
│
└── README.md
```

---

### 3. AI / LLM Learning Repo

```
ai-learning/
├── openai-api/
│   ├── 01-chat-completions/
│   │   ├── basic-chat.js
│   │   └── README.md           ← always explain what you learned
│   ├── 02-function-calling/
│   ├── 03-embeddings/
│   └── 04-streaming/
│
├── langchain/
│   ├── 01-chains/
│   ├── 02-agents/
│   ├── 03-memory/
│   └── 04-tools/
│
├── rag-pipeline/
│   ├── document-loader/
│   ├── vector-store/
│   └── retrieval-chain/
│
├── agents/
│   ├── tool-use/
│   └── multi-agent/
│
└── notes/
    ├── llm-concepts.md
    ├── prompt-engineering.md
    └── resources.md
```

---

### 4. POC Experiments Repo

```
poc-experiments/
├── websocket-scaling/
│   ├── README.md               ← problem statement + findings
│   ├── src/
│   └── results.md              ← benchmark results, conclusion
│
├── rate-limiting/
│   ├── README.md
│   └── strategies/
│       ├── token-bucket.js
│       ├── sliding-window.js
│       └── redis-based.js
│
└── circuit-breaker/
    ├── README.md
    └── src/
```

**Rule:** Every POC folder must have a `README.md` with:
- Problem it solves
- Approach taken
- Outcome / conclusion

---

### 5. TIL (Today I Learned) Repo

```
today-i-learned/
├── nodejs/
│   ├── event-loop-phases.md
│   ├── worker-threads.md
│   └── memory-leaks.md
├── javascript/
├── typescript/
├── databases/
├── devops/
├── ai-llm/
├── system-design/
└── tools/
```

**Rule:** One file per concept. Date + category at the top of every file.

---

## 🌿 PART 2 — BRANCHING STRATEGY

### The Model (GitFlow Simplified)

```
main          ← production only. Protected. Never push directly.
  │
  └── develop ← integration branch. All features merge here first.
        │
        ├── feature/jwt-refresh-token
        ├── feature/rate-limiting-redis
        ├── fix/cors-headers-bug
        ├── docs/swagger-api-docs
        ├── poc/websocket-scaling
        └── chore/upgrade-dependencies
```

### Branch Naming Rules

| Type | Pattern | Example |
|------|---------|---------|
| New feature | `feature/short-description` | `feature/user-authentication` |
| Bug fix | `fix/what-was-broken` | `fix/token-expiry-crash` |
| Documentation | `docs/topic` | `docs/api-endpoints` |
| POC / Spike | `poc/idea-name` | `poc/redis-pub-sub` |
| Refactor | `refactor/what` | `refactor/user-service-clean` |
| Chore / deps | `chore/what` | `chore/update-mongoose-v8` |
| Release | `release/v1.2.0` | `release/v2.0.0` |
| Hotfix | `hotfix/critical-issue` | `hotfix/auth-bypass-fix` |

### Commit Message Format (Conventional Commits)

```
<type>(<scope>): <short description>

feat(auth): add JWT refresh token rotation
fix(user): resolve null pointer in profile update
docs(api): add swagger docs for /users endpoint
chore(deps): upgrade express to v5
refactor(service): extract email logic into EmailService
test(auth): add unit tests for token validation
```

**Types:** `feat` `fix` `docs` `chore` `refactor` `test` `perf` `ci`

---

## 📅 PART 3 — DAILY WORKFLOW (Keep Commit Graph Green)

### Morning Routine (10–15 min)

```
1. git pull origin develop          ← sync latest
2. git checkout -b feature/today-task
3. Work on your task
4. git add -p                       ← stage in small chunks (not git add .)
5. git commit -m "feat: ..."
6. Push + open PR to develop
```

### Daily Commit Habit (non-negotiable)

Even on days you don't code a big feature, commit to `today-i-learned`:

```bash
# Takes 5 minutes, adds a green square to your graph
cd today-i-learned
echo "# Topic\n\nWhat I learned today..." > nodejs/new-topic.md
git add . && git commit -m "til: node.js cluster module basics"
git push
```

### Weekly Rhythm

| Day | Activity |
|-----|----------|
| Mon | Start new feature branch, plan the week |
| Tue–Thu | Feature development, daily commits |
| Fri | PR review, merge to develop, TIL entry, POC idea |
| Weekend | AI learning experiments, TIL entries (optional but great for graph) |

### Monthly

- Merge `develop` → `main` for stable repos (tag a release)
- Archive any POC branch that's done: document findings first
- Review and update `tech-interview-hub` with anything new you learned
- Check your contribution graph — identify gaps and fill them

---

## 🔒 PART 4 — BRANCH PROTECTION (Setup Once Per Repo)

Go to: **Repo → Settings → Branches → Add rule → branch name: `main`**

```
✅ Require a pull request before merging
✅ Require at least 1 approval (even if it's just you reviewing your own PR)
✅ Require status checks to pass before merging
✅ Do not allow bypassing the above settings
✅ Restrict who can push to matching branches
```

This forces good habits even on solo projects — PRs give you a full diff to review before merging.

---

## 🟩 PART 5 — COMMIT GRAPH STRATEGY

### How the graph works
- Every commit on **any branch** in a public repo counts
- Private repo commits count only if you enable "Private contributions" in your profile settings
- The graph shows the last 12 months — consistency matters more than volume

### Enable private contributions
Go to: **github.com/tech7417 → Edit profile → Include private contributions on my profile** ✅

### Target: At Least 1 Commit Every Weekday

| Source | Frequency |
|--------|-----------|
| `today-i-learned` | Daily (even 1 file = 1 green square) |
| `ai-learning` | 3–4x per week (code experiments) |
| `poc-experiments` | 2–3x per week |
| Feature branches | Whenever working on a project |
| `tech-interview-hub` | Weekly (add new Q&A) |

### The "Never Break the Chain" Rule

> Every weekday must have at least 1 commit. Even a README fix, a typo correction,
> or a TIL entry counts. Consistency is more impressive than occasional bursts.

A profile with 200+ days of activity looks far more senior than one with 2000 commits in one month.

---

## ✅ QUICK REFERENCE CARD

```bash
# Start new work
git checkout develop
git pull origin develop
git checkout -b feature/my-task

# Save progress (small atomic commits)
git add -p                          # stage chunks, not whole files
git commit -m "feat(api): add user endpoint validation"

# Keep branch up to date
git fetch origin
git rebase origin/develop           # cleaner history than merge

# Daily TIL commit (takes 5 mins)
cd ~/repos/today-i-learned
# create a new .md file
git add . && git commit -m "til: docker multi-stage builds reduce image size by 60%"
git push

# Finish feature
git push origin feature/my-task
# Open PR on GitHub → develop
```
