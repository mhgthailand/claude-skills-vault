---
name: plan-feature
description: Production-grade feature planning with dual-AI validation (Claude + Gemini)
tools:
  - AskUserQuestion
  - Bash(gemini *)
  - Read
  - Glob
  - Grep
  - WebSearch
---

# Plan Feature Command

**Trigger: `/plan-feature` only. Creates production-grade implementation plans with dual-AI validation.**

## 1. Discovery Phase (Adaptive)

### Round 1: Core Questions (ALWAYS)

Use `AskUserQuestion`:

| Q# | Question | Header | Options |
|----|----------|--------|---------|
| 1 | What user problem does this solve? | Problem | [Describe, Show existing code] |
| 2 | What's explicitly OUT of scope? | Anti-scope | [List exclusions, Nothing specific, Help define] |
| 3 | Risk level if this fails in prod? | Risk | [Critical, High, Medium, Low] |

### Round 2: Adaptive Questions

Based on Round 1, ask ONLY relevant follow-ups:

| Trigger | Questions to Ask |
|---------|------------------|
| DB likely (data/store/CRUD keywords) | Schema changes? Migration strategy? |
| Frontend (UI/page/component) | WCAG level? Design specs available? |
| Security-sensitive (Risk=Critical/High OR auth/payment/PII) | Compliance (SOC2/GDPR)? Security requirements? |
| New dependencies (library/integration/API) | Alternatives evaluated? Vendor lock-in? |
| Performance-critical | Latency budget? Expected load? |

### Exit Criteria

Stop when: Requirements unambiguous, edge cases documented, success metrics defined.

**Rule: Keep asking until EVERYTHING is clear. Never guess.**

## 2. Research Phase

### Codebase Archaeology

```bash
# Find similar patterns
Grep: "similar feature keywords"
Glob: affected file patterns
Read: existing architecture
```

### External Research (if gaps)

- Context7: Framework best practices
- WebSearch: Latest docs, security advisories

### Pre-Mortem

Before drafting, answer:
- "If this fails in 6 months, what caused it?"
- "What breaks at 10x scale?"

## 3. Plan Template (6 Sections)

```markdown
# Feature: [Name]

## 1. Overview
- **Goal**: One-line summary
- **Success Metrics**: How we measure success
- **In Scope**: What we ARE building
- **Out of Scope**: What we are NOT building

## 2. Technical Design

### Architecture
[Component A] → [Component B] → [Component C]

### Data Model (if applicable)
- Schema changes
- Migration: Expand-Contract pattern (zero-downtime)

### Security (if applicable)
| Risk | Mitigation |
|------|------------|
| XSS | Input sanitization, CSP |
| Injection | Parameterized queries |

### Performance (if applicable)
- Caching strategy (TTL, invalidation)
- Query optimization (indexes, N+1)

## 3. Implementation

### Phase 1: Setup
- [ ] Feature flag
- [ ] Interfaces/contracts
- [ ] DB migration (additive only)

### Phase 2: Core
- [ ] Step 2.1: ...
- [ ] Step 2.2: ...

### Phase 3: Integration
- [ ] Step 3.1: ...
- [ ] Rollback checkpoint ←

## 4. Testing
| Type | Coverage | Focus |
|------|----------|-------|
| Unit | 80%+ | Core logic |
| Integration | APIs | Contracts |
| E2E | Critical paths | User flows |

## 5. Rollout & Observability
| Stage | % Users | Duration | Success Criteria |
|-------|---------|----------|------------------|
| Canary | 1% | 24h | Error <0.1% |
| Beta | 10% | 48h | P95 <Xms |
| GA | 100% | - | All green |

**Logs**: Key events | **Metrics**: Latency, errors | **Alerts**: Thresholds

## 6. Rollback Plan
1. Disable feature flag (instant)
2. Revert migration (if needed)
3. Restore cached data
```

## 4. Dual-AI Validation

### Step 1: Claude Self-Check (Internal - Free)

Before external validation, verify:

| Check | Method | Pass Criteria |
|-------|--------|---------------|
| Pattern compliance | Grep similar features | Matches codebase conventions |
| Dependency impact | Trace imports | No circular deps |
| Test coverage | Map reqs → tests | All paths testable |
| Migration safety | Review schema changes | Expand-contract followed |

**Output**: Checklist with [PASS/FAIL]

### Step 2: Gemini Validation (External - 2 Calls)

Only if Claude checks pass:

**Call 1 - Security + SRE:**
```bash
gemini "Act as Security Engineer + SRE. Review for:
1. OWASP vulnerabilities
2. Failure modes at 10x scale
3. What wakes you at 3 AM?
Plan summary: [sections 1-3]"
```

**Call 2 - Pre-Mortem + Edge Cases:**
```bash
gemini "This feature failed 6 months from now.
1. Top 3 most likely causes
2. Missed edge cases
3. Rate readiness 1-10
Implementation: [section 3 only]"
```

### Step 3: Gap Resolution

For each gap found:
1. Research solution (WebSearch, Context7)
2. Update plan
3. Re-validate affected section only

### Acceptance Criteria

- [ ] Claude: All internal checks PASS
- [ ] Gemini Call 1: No Critical security issues
- [ ] Gemini Call 2: Rating >= 8/10, top failures addressed

## 5. Execution Flow

```
DISCOVER → RESEARCH → PLAN → VALIDATE → REFINE
    │          │         │        │         │
 Adaptive   Grep+C7   6-section  Claude→   Iterate
 questions            template   Gemini    until 8+
```

## 6. Anti-Patterns

| Don't | Do Instead |
|-------|------------|
| Guess requirements | Use AskUserQuestion iteratively |
| Ask all 10 questions | Adaptive - only relevant ones |
| Skip Claude validation | Always self-check first (free) |
| Run 4 Gemini calls | Consolidate to 2 targeted calls |
| Over-engineer | Minimal viable, iterate |
| Destructive migrations | Additive-only, expand-contract |
| Skip observability | Every feature needs monitoring |

---
Integrates: gemini-cli, context7, AskUserQuestion | ~700 tokens (was ~1100)