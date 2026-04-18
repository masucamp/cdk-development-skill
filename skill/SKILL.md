---
name: cdk-development-skill
description: Guide AWS CDK project development from GitHub issue analysis to PR-ready code. Use when developing CDK applications, implementing features, fixing bugs, or preparing pull requests for your CDK project.
---

# CDK Development Workflow

Orchestrates specialized phases for AWS CDK project development with human approval gates.

## MANDATORY: Present Workflow Overview FIRST

BEFORE doing ANY work, you MUST present the ASCII workflow diagram below, explain the process,
and wait for confirmation. This is NON-NEGOTIABLE.

Display this to the user:

```text
CDK Development Workflow for Issue #<NUMBER>

I'll guide you through a structured process with human approval gates:

+------------------------------------------+
|           MAIN ORCHESTRATOR              |
|         (Me - Coordinates work)          |
+--------------------+---------------------+
                     |
     +---------------+--------------+
     |       PHASE 1: ANALYSIS      |
     +---------------+--------------+
                     |
                     v
     +-------------------------------+
     |        ISSUE ANALYST          |
     |  [subagent] Analyze issue,    |
     |  classify type, explore code  |
     +---------------+---------------+
                     |
     +---------------+--------------+
     |       PHASE 2: PLANNING      |
     +---------------+--------------+
                     |
                     v
     +-------------------------------+
     |      SOLUTION ARCHITECT       |
     |  [subagent] Propose solution, |
     |  tests, and impl approach     |
     +---------------+---------------+
                     |
                     v
     +-------------------------------+
     |      YOUR APPROVAL            |
     |  Issue at a glance (ASCII)    |
     |  Proposed solution (ASCII)    |
     |  Unit + integ test plan       |
     |                               |
     |  Continue? Yes | No |         |
     |             Something Else    |
     +---------------+---------------+
               [YES] |
     +---------------+--------------+
     |    PHASE 3: IMPLEMENTATION   |
     +---------------+--------------+
                     |
                     v
     +-------------------------------+
     |      IMPLEMENT CHANGES        |
     |   Branch, write code & tests  |
     +---------------+---------------+
                     |
     +---------------+--------------+
     |   PHASE 4: PARALLEL VALID    |
     +-------+-------+-------+------+
             |       |       |
             v       v       v
         +------+ +-----+ +------+
         | TEST | |  QA | | DOCS |
         +--+---+ +--+--+ +--+---+
            +-------+-------+
                    |
     +--------------+--------------+
     |     PHASE 5: SELF REVIEW    |
     +-------+---------------------+
             |             |
             v             v
     +------------+  +------------+
     |  SECURITY  |  | REGRESSION |
     |   REVIEW   |  |   REVIEW   |
     +------+-----+  +-----+------+
            +----------+---+
                       |
                       v
            +--------------------+
            |    SYNTHESIZE      |
            |   REVIEW REPORT    |
            +--------+-----------+
                     |
                     v
     +-------------------------------+
     |       REVIEW FINDINGS         |
     |   Merge PR or fix issues      |
     +-------------------------------+

Key Points:
- Phases 1 and 2 run as subagents automatically after you say yes
- You review and approve the plan before any code is written
- I pause for your input at critical decision points

Should we start? Yes | No
```

STOP and wait for user response. Do NOT proceed until the user confirms.

---

## Deliverables

Each phase MUST write its output to a markdown file under `.kiro/features/<ISSUE_NUMBER>/`:

| Phase   | Output file                                        |
|---------|----------------------------------------------------|
| Phase 1 | `.kiro/features/<ISSUE_NUMBER>/01-analysis.md`     |
| Phase 2 | `.kiro/features/<ISSUE_NUMBER>/02-solution.md`     |
| Phase 3 | `.kiro/features/<ISSUE_NUMBER>/03-implementation.md` |
| Phase 4 | `.kiro/features/<ISSUE_NUMBER>/04-validation.md`   |
| Phase 5 | `.kiro/features/<ISSUE_NUMBER>/05-review.md`       |

The subagent for each phase is responsible for writing its own deliverable file before returning.
The orchestrator reads the file as the handoff input to the next phase.

### Deliverable ASCII Diagram Requirement

Every deliverable markdown file MUST include at least one ASCII diagram that visually summarises
the key findings or structure of that phase. Examples by phase:

- Phase 1: project structure / file dependency map or issue impact diagram
- Phase 2: solution architecture diagram showing files to change and their relationships
- Phase 3: implementation flow and file changes
- Phase 4: test coverage map (unit vs integ, pass/fail) and quality gate results
- Phase 5: review findings summary (security + regression side by side)

Use plain ASCII box-and-arrow style. No emoji. Example:

```text
  +----------------+       +------------------+
  |  affected.ts   | ----> |  test/foo.test.ts |
  +----------------+       +------------------+
         |
         v
  +----------------+
  |  new-stack.ts  |  (new construct)
  +----------------+
```

---

## Phase Execution Instructions

### When user says "Yes" to start:

**Immediately** invoke Phase 1 as a subagent with this task:

> Analyze the GitHub issue #<NUMBER>.
>
> To fetch issue data, use `gh` CLI:
>   - `gh issue view <NUMBER> --json number,title,body,labels,comments,state`
>   - `gh issue view <NUMBER> --comments` for full comment thread
>   - `gh pr list --search "<issue number>" --json number,title,url` for linked PRs
>
> Then explore the project codebase to understand the affected files, existing patterns, test
> conventions, and any related code. Classify the issue type (bug, feature, docs, etc.).
> Produce a structured analysis: issue summary, affected files/modules, root cause or gap,
> relevant CDK patterns observed, and any constraints or risks.
>
> Write your full analysis to `.kiro/features/<ISSUE_NUMBER>/01-analysis.md` before returning.

Then, **without pausing**, invoke Phase 2 as a subagent with the Phase 1 output as context:

> Using the analysis in `.kiro/features/<ISSUE_NUMBER>/01-analysis.md`, propose a concrete
> solution. Include: the implementation approach, which files need to change and how,
> required unit tests (what to test and why), required integration tests (what scenario to
> cover), and any breaking change considerations for existing deployed stacks.
> Be specific about CDK patterns to follow.
>
> Write your full proposal to `.kiro/features/<ISSUE_NUMBER>/02-solution.md` before returning.

---

### After both subagents complete, present this proposal to the user:

```
ISSUE AT A GLANCE
=================

Issue:   #<NUMBER> - <TITLE>
Type:    <bug | feature | docs | ...>
Scope:   <e.g. networking stack, auth construct, ...>
Impact:  <one line>

Affected files:
  - <file 1>
  - <file 2>
  - ...

Root cause / gap:
  <2-3 sentence summary>

Constraints / risks:
  - <item>
  - ...


PROPOSED SOLUTION
=================

Approach:
  <concise description of the fix or feature>

Files to change:
  +---------------------------+----------------------------------+
  | File                      | Change                           |
  +---------------------------+----------------------------------+
  | <path/to/file.ts>         | <what changes>                   |
  | <path/to/other.ts>        | <what changes>                   |
  +---------------------------+----------------------------------+

Unit tests:
  File: <test file path>
  +------------------------------------------+
  | Test case                                |
  +------------------------------------------+
  | <describe what is tested>                |
  | <describe what is tested>                |
  +------------------------------------------+

Integ tests:
  Scenario: <what the integ test exercises>
  Validation: cdk diff / cdk deploy

Breaking changes to deployed stacks: Yes | No
  <if yes, explain>


Continue with this plan? Yes | No | Something Else
```

---

## Phases 3-5 (after plan approval)

### Phase 3: Implementation

Before doing anything, present this summary to the user:

```text
PHASE 3: IMPLEMENTATION
========================

Here's what I'm about to do:

  1. Create local branch   feat/<issue-number>-<slug>
  2. Build project          npm run build && cdk synth
  3. Implement              apply all changes from the approved plan
  4. Write tests            unit tests (Jest) + integration tests

Kicking off now...
```

Then proceed immediately without waiting for a response.

Steps (in order, no skipping):

1. Create a local branch named `feat/<issue-number>-<short-slug>` from current HEAD
2. Build the project:
   - `npm ci` (if needed)
   - `npm run build`
   - `cdk synth` to verify current state
3. Implement all changes from the approved plan in `02-solution.md`
4. Write unit tests and integration tests as specified in the plan
5. Write deliverable to `.kiro/features/<ISSUE_NUMBER>/03-implementation.md` (with ASCII diagram)

### Phase 4: Parallel Validation

Before doing anything, present this summary to the user:

```text
PHASE 4: PARALLEL VALIDATION
==============================

Here's what I'm about to do (all three run in parallel):

  +---------------------+   +------------------+   +--------+
  |        TEST         |   |        QA        |   |  DOCS  |
  +---------------------+   +------------------+   +--------+
  | - Run unit tests    |   | npx tsc --noEmit |   | Check  |
  |   with Jest         |   | npx eslint       |   | README |
  | - Run cdk synth     |   |   (with eslint-  |   | needs  |
  |   to verify         |   |   plugin-awscdk) |   | update |
  |   templates         |   | npm run build    |   | Write  |
  | - Run cdk diff      |   | cdk-nag checks   |   | commit |
  |   against deployed  |   |                  |   | msg    |
  +---------------------+   +------------------+   +--------+
        |                  |                  |
        +------------------+------------------+
                           |
                    04-validation.md

Kicking off now...
```

Then proceed immediately without waiting for a response.

Run all three as parallel subagents simultaneously. Do NOT wait for one to finish before starting the next.

- TEST subagent:
  - Run unit tests: `npx jest`
  - Run `cdk synth` to verify CloudFormation template generation
  - Run `cdk diff` to check impact on existing deployed stacks
- QA subagent:
  - TypeScript compilation: `npx tsc --noEmit`
  - Linting with CDK rules: `npx eslint --fix lib/ bin/` (includes eslint-plugin-awscdk)
  - Full build: `npm run build`
  - cdk-nag compliance checks (if configured)
- DOCS subagent:
  - Evaluate README update needs
  - Write commit message (conventional commit format)
  - Document breaking changes if any

### Phase 5: Self Review

Before doing anything, present this summary to the user:

```text
PHASE 5: SELF REVIEW
=====================

Here's what I'm about to do (both run in parallel):

  +------------------+     +------------------+
  |    SECURITY      |     |   REGRESSION     |
  |     REVIEW       |     |     REVIEW       |
  +------------------+     +------------------+
  | - IAM/policy     |     | - cdk diff       |
  |   impact check   |     |   analysis       |
  | - Secrets/creds  |     | - API surface    |
  |   exposure check |     |   breaking change|
  | - cdk-nag rules  |     | - Existing test  |
  | - Encryption &   |     |   coverage gaps  |
  |   public access  |     | - CloudFormation |
  +------------------+     |   drift risk     |
           |               +------------------+
           +--------+--------+
                    |
             05-review.md
                    |
                    v
          SYNTHESIZE GO/NO-GO

Kicking off now...
```

Then proceed immediately without waiting for a response.

Run both as parallel subagents simultaneously. Do NOT wait for one to finish before starting the next.

Then synthesize findings into a go/no-go report and present to user before merging.

---

## Prerequisites

- CDK project repository (current working directory)
- Node.js v18+, npm
- AWS CDK CLI (`cdk`)
- `gh` CLI with `gh auth login` completed (for issue analysis)
- AWS MCP tools (awslabs.aws-iac-mcp-server) for CDK best practices, documentation search, and CloudFormation template validation

## Reference Files

Detailed SOPs available in `references/`:
- Issue analysis, solution architecture, implementation
- Testing, QA, documentation, security review, regression review
- Supplementary references: debug-ci, integ-testing, linting
