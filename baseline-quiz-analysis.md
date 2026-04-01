# CCA-F Baseline Quiz Analysis (19/30 = 63%)

## Executive Summary
**Root Cause**: Candidate confuses *specification and configuration* (how things are declared) with *runtime behavior and execution* (how things actually work). Strong practical foundation but weak on formal definitions and declarative syntax.

**Quick Win**: Fix D3 (4 errors) → +13% score improvement
**Strategic Win**: Fix D1 + D2 → +20% improvement, solidifies async/dependency foundation

---

## Pattern Analysis

### Pattern 1: Specification vs. Runtime Behavior (4 errors)
**Evidence:**
- Q7: Doesn't know that `allowedTools` config *blocks* tools (specification → runtime behavior)
- Q8: Understands parallel execution but misses that dependent tasks fail when run in parallel (execution consequence)
- Q17: Confuses *where* allowed tools are defined (SKILL.md vs. runtime) with *how* they're enforced
- Q20: Doesn't distinguish between CLAUDE.md configs vs. API-level parameters (what the system can vs. can't do)

**Root Cause**: Candidate knows *what to do* (parallelize, define tools) but doesn't understand *how configuration maps to execution*.

**Evidence Quality**: HIGH – 4 distinct questions testing this failure mode.

---

### Pattern 2: Primitive Types and Abstractions (3 errors)
**Evidence:**
- Q9: Wrong on the three core MCP primitives (Tools, Resources, Prompts)
- Q11: Confuses Tool vs. Resource for read-only access (doesn't understand when to use which primitive)
- Q14: Doesn't understand MCP server lifecycle (when servers become available)

**Root Cause**: Candidate hasn't internalized MCP's abstraction layer. Treats all MCP concepts as generic rather than understanding their distinct purposes.

**Evidence Quality**: HIGH – systematic confusion about MCP semantics.

---

### Pattern 3: Coordination and Concurrency Semantics (2 errors)
**Evidence:**
- Q4: Knows *how* to emit parallel tasks but not the *mechanics* (doesn't understand task granularity)
- Q8: Understands tasks run in parallel but misses *dependency semantics* (dependent tasks must not run together)

**Root Cause**: Candidate has incomplete mental model of agent coordination. Knows "parallel good" but not when parallel breaks things.

**Evidence Quality**: MEDIUM – only 2 questions, but they're tightly coupled.

---

### Pattern 4: Precedence and Composition (1 error)
**Evidence:**
- Q15: Thinks CLAUDE.md configs have "precedence" rather than being "accumulated/concatenated"

**Root Cause**: Doesn't understand composition semantics (how multiple configs combine).

**Evidence Quality**: LOW – single question, but it's a critical semantic distinction.

---

## Cross-Domain Connections

### D1 ↔ D3: Configuration Determines Execution
- Q7 (D1): `allowedTools` config blocks tools
- Q17 (D3): Where to define `allowedTools` (SKILL.md frontmatter)
- **Connection**: Same feature, two levels of abstraction. Candidate knows the "what" but not the "where" and vice versa.

### D1 ↔ D4: Structured Output and Coordination
- Q4 (D1): Parallel task emission
- Q21 (D4): Structured JSON via tool_use
- **Connection**: Both require understanding how to emit structured data that the coordinator understands. Candidate got Q21 right, so the issue isn't tool use, it's *coordination semantics*.

### D2 ↔ D3: System Configuration Scope
- Q14 (D2): MCP servers unavailable until restart
- Q19 (D3): Sharing config requires repo commit (not mid-session available)
- **Connection**: Both test understanding that *system configurations have session/restart boundaries*. Candidate missed both.

---

## Root Cause Diagnosis

### Primary (70% of errors): Configuration-Execution Mismatch
Candidate confuses:
- Where things are defined (SKILL.md? CLAUDE.md? API?)
- When they're available (session start? runtime? next restart?)
- How they affect execution (blocking? routing? validation?)

**Hypothesis**: Candidate has learned by *doing* (strong on Q1–3, Q5–6, Q10, Q12–13, Q16, Q18, Q21–30) but hasn't read the specification documents carefully.

### Secondary (20% of errors): MCP Primitive Semantics
Candidate treats Tools, Resources, and Prompts as interchangeable rather than understanding their distinct use cases.

### Tertiary (10% of errors): Composition and Accumulation
Doesn't understand how multiple configs combine (accumulated vs. precedence).

---

## Domain Performance Summary

| Domain | Score | Type | Key Weakness |
|--------|-------|------|--------------|
| D1 | 5/8 (63%) | Agent Coordination | When to parallelize; how config blocks tools |
| D2 | 3/6 (50%) | MCP Primitives | Which primitive for what; lifecycle boundaries |
| D3 | 2/6 (33%) | Configuration | Where to define; API params vs. configs |
| D4 | 5/6 (83%) | Output Formats | Strong; one gap on API params |
| D5 | 4/4 (100%) | Skill Publishing | Perfect |

**Weakest Domain**: D3 (Configuration) at 33% – THIS IS THE LEVERAGE POINT.

---

## Efficient Study Plan

### Phase 1: Fix Configuration Semantics (D3) – 30 min
**Why First**: Fixes 4 errors, requires understanding CLAUDE.md, SKILL.md, and .claude/settings.json.

Study checklist:
- [ ] Read CLAUDE.md specification → understand precedence is "accumulated"
- [ ] Read SKILL.md frontmatter spec → understand `allowedTools` declaration
- [ ] Map Q15, Q17, Q19, Q20 → understand API params vs. config params (max_tokens is NOT a CLAUDE.md setting)
- [ ] Create mental model: "CLAUDE.md + SKILL.md + .claude/settings.json are declarative; they specify what's available"

**Exam Impact**: +13% (4/30 errors fixed)

---

### Phase 2: Fix MCP Primitives (D2) – 40 min
**Why Second**: Fixes 3 errors, foundational to D1 coordination (tools are called, resources are read, prompts are injected).

Study checklist:
- [ ] Review MCP primitives: Tools (RPC), Resources (data), Prompts (system instructions)
- [ ] Map Q9 → all three primitives (not generic "capabilities")
- [ ] Map Q11 → Resources for read-only access (not Tools, which are RPC)
- [ ] Map Q14 → MCP servers load at session start, not mid-session
- [ ] Create mental model: "Each primitive has a purpose; servers are session-scoped"

**Exam Impact**: +10% (3/30 errors fixed)

---

### Phase 3: Fix Coordination Semantics (D1) – 40 min
**Why Third**: Fixes 2 of 3 remaining errors, requires integrating D2 + D3 knowledge.

Study checklist:
- [ ] Map Q4 → "emit multiple Task calls in single response" = parallel execution
- [ ] Map Q7 → "allowedTools blocks tools" (config → execution)
- [ ] Map Q8 → "dependent agents must NOT run in parallel" (dependency semantics)
- [ ] Create mental model: "Parallelization is declared via task emission; dependencies force sequential execution"

**Exam Impact**: +7% (2/30 errors fixed; Q4 may still be ambiguous)

---

### Phase 4: Composition Semantics (D3 leftover) – 10 min
**Why Last**: Fixes 1 error, lowest impact but ensures D3 is complete.

Study checklist:
- [ ] Understand: CLAUDE.md configs are *accumulated* across project hierarchies (not precedence)
- [ ] Map Q15 → Test this against actual behavior

**Exam Impact**: +3% (1/30 error fixed)

---

## Expected Score After Study

| Phase | Fixes | Cumulative | New Score |
|-------|-------|-----------|-----------|
| Baseline | — | — | 19/30 (63%) |
| After D3 | +4 | +13% | 23/30 (77%) |
| After D2 | +3 | +23% | 26/30 (87%) |
| After D1 | +2 | +30% | 28/30 (93%) |
| After Comp | +1 | +33% | 29/30 (97%) |

**Note**: Assumes no other mistakes emerge during targeted study.

---

## High-ROI Sub-Topics (Exam Weight Estimate)

Rank by (improvement points) × (estimated exam weight):

| Rank | Topic | Impact | Est. Weight | Score Lift | Priority |
|------|-------|--------|-------------|-----------|----------|
| 1 | Config declaration (D3) | +4 | 20% | +13% | CRITICAL |
| 2 | MCP primitives (D2) | +3 | 15% | +10% | HIGH |
| 3 | Parallel task mechanics (D1) | +2 | 25% | +7% | HIGH |
| 4 | Dependency semantics (D1) | +2 | 25% | +7% | HIGH |
| 5 | Config composition (D3) | +1 | 10% | +3% | MEDIUM |

**Recommendation**: Invest Phase 1–2 heavily (70 min). Phase 3–4 can overlap with practice exams.

---

## Study Materials to Review

### Must-Read (90 min)
1. CLAUDE.md – Section on `precedence` vs. `accumulated` configs
2. SKILL.md specification – frontmatter syntax for `allowedTools`
3. .claude/settings.json schema – understand API params vs. config params
4. MCP specification – Tools, Resources, Prompts definitions

### Should-Read (60 min)
1. Agent coordination docs – task emission and parallelization
2. Dependency semantics – when parallel breaks things
3. Session lifecycle – when configs load, when servers become available

### Nice-to-Have (30 min)
1. Config hierarchy examples (project vs. global)
2. Common mistakes in allowedTools declaration

---

## Verification Steps (After Study)

Create flashcards for these scenarios:

1. **Config Semantics**: "I want to limit a skill to only use the 'search' and 'browse' tools. Where do I declare this? What happens if I try to call a different tool?"
   - Expected: "SKILL.md frontmatter allowedTools; tool is blocked by coordinator"
   - Your answer: ___

2. **MCP Primitives**: "I have a read-only BigQuery table I want to expose. Should I use a Tool or a Resource? Why?"
   - Expected: "Resource; Tools are for RPC calls, Resources are for data access"
   - Your answer: ___

3. **Parallel Coordination**: "I emit 3 Task calls in a single response, where Task 2 depends on Task 1's output. What happens?"
   - Expected: "All 3 run in parallel; Task 2 fails because Task 1's output isn't ready"
   - Your answer: ___

4. **Config Accumulation**: "My project has global CLAUDE.md and local CLAUDE.md. How are configs resolved?"
   - Expected: "Accumulated/concatenated, with local overriding global"
   - Your answer: ___

5. **API vs. Config**: "Can I set `max_tokens` in CLAUDE.md?"
   - Expected: "No; max_tokens is an API parameter, not a config setting"
   - Your answer: ___

---

## Lessons for Capture

After study, update `/Users/huiyanwan/.claude/projects/-Users-huiyanwan-Documents/memory/lessons.md`:

- **CCA-F Pattern**: Confusing where things are declared vs. how they execute is common. Always map config → runtime behavior.
- **MCP Semantics**: Each primitive (Tool/Resource/Prompt) has a distinct purpose; study them separately.
- **Dependency Semantics**: Parallel ≠ always faster; dependencies require sequential execution.
- **Config Composition**: Accumulated means later configs override earlier ones; not "precedence".

---

## Summary

**Current Weakness**: Specification and configuration knowledge (D3) + MCP semantics (D2) + coordination mechanics (D1).

**Strategy**: Study in order D3 → D2 → D1 → Composition. Each phase builds on the previous.

**Expected Outcome**: 63% → 87% after Phase 2 (2.5 hours study). Can reach 93%+ with Phase 3.

**Time Investment**: 2.5 hours for +24% improvement = **~6 min per point gained**.
