# 11 — SDLC, Agile Methodology & Git Version Control

---

## 11.1 Software Development Life Cycle (SDLC)

### Phases of SDLC

```
1. Requirement Analysis → What does the client need?
2. System Design        → HLD (architecture) + LLD (module-level)
3. Implementation       → Actual coding
4. Testing              → Unit, Integration, System, UAT
5. Deployment           → Release to production
6. Maintenance          → Bug fixes, updates, enhancements
```

### SDLC Models Comparison

| Model | How It Works | Best For | Weakness |
|-------|-------------|----------|----------|
| **Waterfall** | Linear, sequential phases. Each phase completes before next begins | Well-defined, unchanging requirements | No going back; late testing |
| **Agile** | Iterative sprints. Deliver working software every 2-4 weeks | Evolving requirements, collaboration | Scope creep, needs discipline |
| **Spiral** | Iterative + risk analysis at each loop | High-risk, large-scale projects | Complex, expensive |
| **V-Model** | Each development phase has a corresponding testing phase | Safety-critical systems | Rigid, like waterfall |
| **Iterative** | Build incrementally, refine each iteration | Large systems built in stages | Can be chaotic without planning |

**Q: "Which SDLC model would you use for a project with changing requirements?"**
> "**Agile** — specifically Scrum. It embraces changing requirements through short sprint cycles. At TCS, most enterprise teams use Agile because client requirements evolve throughout the project lifecycle."

### HLD vs LLD

| Feature | HLD (High-Level Design) | LLD (Low-Level Design) |
|---------|------------------------|----------------------|
| **Scope** | Overall system architecture | Individual module/class design |
| **Audience** | Architects, managers, clients | Developers |
| **Content** | System components, data flow, tech stack | Class diagrams, function signatures, DB schema |
| **Your project (HLD)** | "Python ETL → MySQL → Power BI" | "etl.py has extract_data() using generator, clean_data() in transform.py" |

---

## 11.2 Agile & Scrum

### Agile Manifesto — Four Core Values

1. **Individuals and interactions** over processes and tools
2. **Working software** over comprehensive documentation
3. **Customer collaboration** over contract negotiation
4. **Responding to change** over following a plan

### Scrum Framework

```
Product Backlog (all features/tasks prioritized by Product Owner)
       │
       ▼
Sprint Planning (team picks items for this sprint)
       │
       ▼
Sprint (2-4 weeks of development)
  │
  ├── Daily Stand-up (15 min — What I did, What I'll do, Blockers)
  │
  ▼
Sprint Review (demo working software to stakeholders)
       │
       ▼
Sprint Retrospective (what went well, what to improve)
       │
       ▼
Repeat
```

### Scrum Roles

| Role | Responsibility |
|------|---------------|
| **Product Owner** | Owns the product backlog, prioritizes features, represents the client |
| **Scrum Master** | Facilitates Scrum ceremonies, removes blockers, ensures process adherence |
| **Development Team** | Self-organizing team that builds the product (3-9 members) |

### Key Scrum Terms

| Term | Definition |
|------|-----------|
| **Sprint** | Fixed time period (usually 2 weeks) to complete a set of tasks |
| **User Story** | Feature described from user perspective: "As a [user], I want [feature] so that [benefit]" |
| **Epic** | Large user story broken into smaller stories |
| **Velocity** | Amount of work a team completes per sprint (measured in story points) |
| **Burndown Chart** | Graph showing remaining work vs. time in a sprint |
| **Definition of Done (DoD)** | Agreed criteria for when a task is truly complete (coded, tested, reviewed, deployed) |

**Q: "Did you follow Agile in your project?"**
> "For my personal project, I followed an informal Agile approach — I broke the work into weekly iterations: Week 1 focused on data extraction, Week 2 on transformation logic, Week 3 on database loading, and Week 4 on visualization. I maintained a simple task board and logged progress. In a team environment at TCS, I'd follow formal Scrum with daily stand-ups and sprint reviews."

---

## 11.3 Git & Version Control

### Git vs GitHub

| Feature | Git | GitHub |
|---------|-----|--------|
| **What** | Distributed Version Control System (software) | Cloud hosting platform for Git repositories |
| **Where** | Runs locally on your machine | Web-based (github.com) |
| **Purpose** | Track code changes, branching, merging | Collaboration, code review, CI/CD, issue tracking |
| **Analogy** | The engine | The car showroom where engines are displayed |

### Essential Git Commands (Know These Cold)

```bash
# Setup & Init
git init                          # Initialize a new repo
git clone <url>                   # Clone remote repo

# Daily workflow
git status                        # See modified/staged files
git add .                         # Stage all changes
git add <file>                    # Stage specific file
git commit -m "descriptive msg"   # Commit with message
# -m flag is important because without it, Git opens a text editor

git push origin main              # Push to remote
git pull origin main              # Fetch + merge from remote

# Branching
git branch feature-etl            # Create branch
git checkout feature-etl          # Switch to branch
git checkout -b feature-etl       # Create + switch (shortcut)
git merge feature-etl             # Merge branch into current

# Viewing history
git log --oneline -10             # Last 10 commits, compact
git diff                          # See unstaged changes
git diff --staged                 # See staged changes
```

### The Staging Area (Index)

```
Working Directory → git add → Staging Area → git commit → Local Repository → git push → Remote
      (modified)                 (staged)                    (committed)              (pushed)
```

**Q: "Why does Git have a staging area?"**
> "It allows you to selectively choose which changes to include in a commit. For example, if I modified both `etl.py` and `config.py`, I can stage only `etl.py` for a commit focused on ETL logic, and commit `config.py` separately with a different message. This keeps commits atomic and meaningful."

### Merge Conflicts

**When**: Two branches modify the same line of the same file.

**How to resolve:**
1. Git marks the conflict in the file with `<<<<<<<`, `=======`, `>>>>>>>`
2. You manually edit to keep the correct version
3. `git add` the resolved file
4. `git commit` to complete the merge

### `.gitignore`

**What**: Tells Git which files/folders to ignore (not track).

```gitignore
# Your project's .gitignore
__pycache__/
*.pyc
data/            # Don't commit large datasets
logs/            # Don't commit log files
.env             # Don't commit credentials
```

**Q: "Your project has a `.gitignore`. What does it contain and why?"**
> "It ignores the `data/` directory (8M-row CSV is too large for Git — GitHub has a 100MB file limit), `logs/` (generated at runtime), `__pycache__/` (compiled bytecode), and any `.env` files with database credentials. Sensitive data and generated files should never be in version control."

### Pull Request (PR) Workflow

```
1. Create a feature branch from main
2. Make changes, commit
3. Push branch to GitHub
4. Open a Pull Request (PR)
5. Team reviews code, suggests changes
6. After approval, merge PR into main
7. Delete feature branch
```

---

## 11.4 Testing Types

| Type | What It Tests | Example |
|------|--------------|---------|
| **Unit Testing** | Individual functions/methods | Testing `clean_data()` returns correct dtypes |
| **Integration Testing** | Modules working together | ETL pipeline → MySQL loading works end-to-end |
| **System Testing** | Complete application | Full pipeline runs without errors on full dataset |
| **UAT (User Acceptance)** | Business requirements met | Power BI dashboard shows correct insights to stakeholders |
| **Regression Testing** | New changes don't break existing features | After modifying transform.py, ETL still produces correct output |

**Q: "Did you write tests for your project?"**
> "I implemented basic validation checks — logging row counts after each chunk, verifying data types after transformation, and checking for null primary keys before database insertion. For a production system, I'd add formal unit tests using `pytest` for each transformation function and SQL validation queries to verify data integrity after loading."

---

*Next: [12_EMERGING_TECH.md](./12_EMERGING_TECH.md) — GenAI, AI/ML Awareness*
