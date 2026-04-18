# CDK Development Skill

CDK Development Skill is a Kiro Agent Skill that guides AWS CDK project development from GitHub issue analysis to PR-ready code. It combines multiple specialized subagents, AWS tooling, and Agent SOPs to streamline the development process with human oversight at critical decision points.

> **Fork of [cdklabs/cdk-contribution-skill](https://github.com/cdklabs/cdk-contribution-skill)**, adapted for general CDK project development (not limited to aws-cdk repository contributions).

## What It Does

The skill implements an orchestrator pattern that:

- Analyzes GitHub issues and explores your project codebase
- Creates implementation plans with AWS documentation research and CDK best practices
- Includes a human approval gate before any code is written
- Implements changes with unit tests (Jest) and integration validation (cdk synth/diff)
- Runs 3-layer quality checks (eslint-plugin-awscdk + cdk-nag + manual review)
- Performs security and regression self-review before merge
- Writes structured deliverables to `.kiro/features/<ISSUE_NUMBER>/`

## How It Works

```
    ┌───────────────────────────┐
    │      ISSUE ANALYST        │  Phase 1: Analyze issue & code
    └─────────────┬─────────────┘
                  ▼
    ┌───────────────────────────┐
    │    SOLUTION ARCHITECT     │  Phase 2: Create impl plan
    └─────────────┬─────────────┘
                  ▼
    ┌───────────────────────────┐
    │   🧑 YOUR APPROVAL 🧑     │  Review plan before code is written
    └─────────────┬─────────────┘
            [YES] │
    ┌───────────────────────────┐
    │      IMPLEMENT            │  Phase 3: Branch, code & tests
    └─────────────┬─────────────┘
        ┌─────────┼─────────┐
        ▼         ▼         ▼
    ┌───────┐ ┌───────┐ ┌───────┐
    │ TEST  │ │  QA   │ │ DOCS  │  Phase 4: Parallel validation
    └───┬───┘ └───┬───┘ └───┬───┘
        └─────────┼─────────┘
        ┌─────────┴─────────┐
        ▼                   ▼
    ┌───────────┐     ┌───────────┐
    │ SECURITY  │     │REGRESSION │  Phase 5: Self review
    └─────┬─────┘     └─────┬─────┘
          └─────────┬─────────┘
                    ▼
    ┌───────────────────────────┐
    │   🧑 REVIEW & MERGE 🧑    │  Review findings, merge PR
    └───────────────────────────┘
```

## Deliverables

Each phase writes its output to `.kiro/features/<ISSUE_NUMBER>/`:

| Phase | Output File | Content |
|-------|-------------|---------|
| 1 | `01-analysis.md` | Issue analysis and codebase exploration |
| 2 | `02-solution.md` | Implementation plan (requires approval) |
| 3 | `03-implementation.md` | Code changes and test results |
| 4 | `04-validation.md` | Test, QA, and documentation reports |
| 5 | `05-review.md` | Security and regression review |

## Reference SOPs

| SOP | Phase | Purpose |
|-----|-------|---------|
| `issue-analyst-sop.md` | 1 | Issue analysis and classification |
| `solution-architect-sop.md` | 2 | Solution design with AWS docs research |
| `implementation-specialist-sop.md` | 3 | Code implementation and testing |
| `test-engineer-sop.md` | 4 | Jest test execution and cdk diff validation |
| `quality-assurance-sop.md` | 4 | 3-layer quality checks |
| `documentation-specialist-sop.md` | 4 | PR docs and README updates |
| `security-reviewer-sop.md` | 5 | Security review with cdk-nag |
| `regression-reviewer-sop.md` | 5 | Regression review with cdk diff |
| `review-report-generator-sop.md` | 5 | Final go/no-go report |

Supplementary: `debug-ci.md`, `integ-testing.md`, `linting.md`

## Getting Started

1. Install the CDK Development Skill in Kiro
2. Point the skill at a GitHub issue in your CDK project
3. Review and approve the implementation plan
4. Review the final output and merge your PR

## Prerequisites

- CDK project repository (skill runs in the current working directory)
- Node.js v18+, npm
- AWS CDK CLI (`cdk`)
- `gh` CLI with `gh auth login` completed
- AWS MCP tools (awslabs.aws-iac-mcp-server) for CDK best practices and documentation search

## Key Differences from cdk-contribution-skill

| Aspect | cdk-contribution-skill | cdk-development-skill |
|--------|----------------------|----------------------|
| Target | aws-cdk repository contributions | Any CDK project |
| Build | yarn + lerna + upstream sync | npm + cdk synth |
| Tests | yarn test + integ-runner | Jest + cdk diff |
| Linting | yarn lint (internal) | eslint-plugin-awscdk |
| Compliance | — | cdk-nag integration |
| JSII | Required | Not applicable |
| Issue source | aws/aws-cdk issues | Your project's issues |

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This project is licensed under the Apache-2.0 License.
