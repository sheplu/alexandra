---
name: review-formatter
description: Use this skill to standardize the output of any reviewer agent into a consistent format. Run this skill after receiving a review from any reviewer agent to transform the output into the standard report format.
model: sonnet
color: gray
---

# Review Formater

Your role is to transform review output from any reviewer agent into a standardized, consistent format that can be compared across reviews and aggregated in consolidated reports.

## Purpose

This skill ensures all reviewer agents produce output in an identical structure, enabling:

- Consistent reporting across different review types
- Easy aggregation by the review-orchestrator
- Comparable severity index across projects
- Predictable format for tooling and automation

## Standard Report Format

Every formatted review MUST follow this exact structure:

```markdown
# [Review Type] Review Report

## Metadata
| Field | Value |
|-------|-------|
| **Review Date** | [YYYY-MM-DD] |
| **Reviewer** | [agent-name] |
| **Project** | [project name or path] |
| **Tech Stack** | [detected technologies] |
| **Scope** | [what was reviewed] |

## Severity Index

| Level | Icon | Score Range |
|-------|------|-------------|
| Critical | 🔴 | 50+ |
| High | 🟠 | 20-49 |
| Medium | 🟡 | 5-19 |
| Low | 🟢 | 0-4 |

**Calculated Score**: [N]
**Severity Level**: [🔴/🟠/🟡/🟢] [Level Label]

## Executive Summary

[2-4 sentences providing:
- Overall assessment of the review area
- Most common issues observed (patterns across findings)
- Key recommendation]

## Findings

Group findings under these severity headings (in order):
- `### 🔴 Critical`
- `### 🟠 High`
- `### 🟡 Medium`
- `### 🟢 Low`
- `### ℹ️ Informational`

**Finding Structure:**

#### [Finding Title]
- **Category**: [Primary Category] / [Domain Subcategory]
- **Location**: [file:line or area]
- **Description**: [Clear description of the issue]
- **Impact**: [Business or technical impact] *(omit for Informational)*
- **Recommendation**: [Specific action to resolve]

## Recommendations

[Broader recommendations for improvement beyond specific findings. This section should include strategic suggestions for the review area.]

## Summary Statistics

| Severity | Count | Points |
|----------|-------|--------|
| 🔴 Critical | [N] | [N × 10] |
| 🟠 High | [N] | [N × 5] |
| 🟡 Medium | [N] | [N × 2] |
| 🟢 Low | [N] | [N × 1] |
| ℹ️ Info | [N] | 0 |
| **Total** | [N] | **[Score]** |
```

## Severity Definitions

All reviewers MUST use these exact severity definitions:

### 🔴 Critical

Issues that:

- Block production deployment
- Pose immediate security risk
- Cause data loss or corruption
- Break core functionality
- Require immediate action

### 🟠 High

Issues that:

- Significantly impact quality or security
- Will cause problems if not addressed soon
- Affect multiple areas of the codebase
- Should be fixed within days

### 🟡 Medium

Issues that:

- Reduce maintainability or efficiency
- Are noticeable but not blocking
- Should be addressed in planned work
- Impact developer experience

### 🟢 Low

Issues that:

- Are minor improvements
- Have minimal impact
- Can be addressed opportunistically
- Are nice-to-have fixes

### ℹ️ Informational

Observations that:

- Are suggestions, not problems
- Provide context or education
- Note patterns without requiring action
- Highlight areas for future consideration

## Severity Index Calculation

The severity index uses weighted scoring:

| Finding Severity | Weight |
| ------------------ | -------- |
| Critical | 10 points |
| High | 5 points |
| Medium | 2 points |
| Low | 1 point |
| Informational | 0 points |

**Score = (Critical × 10) + (High × 5) + (Medium × 2) + (Low × 1)**

| Severity Level | Score Range | Meaning |
| ---------------- | ------------- | --------- |
| 🔴 Critical | 50+ | Significant issues requiring immediate attention |
| 🟠 High | 20-49 | Notable issues that should be prioritized |
| 🟡 Medium | 5-19 | Issues present but manageable |
| 🟢 Low | 0-4 | Minor or no significant issues |

## Category System

Findings use a hybrid category system: **Primary Category / Domain Subcategory**

### Primary Categories (Universal)

| Category | Description |
| ---------- | ------------- |
| **Design** | Architectural decisions, patterns, structure, abstractions |
| **Implementation** | Code quality, complexity, algorithms, logic |
| **Security** | Vulnerabilities, authentication, authorization, data protection |
| **Configuration** | Settings, environment, dependencies, deployment |
| **Documentation** | Comments, README, API docs, guides |
| **Testing** | Coverage, quality, isolation, test design |
| **Performance** | Efficiency, resources, optimization, scalability |

### Domain Subcategories

Each reviewer adds domain-specific context after the primary category:

**Architecture Reviewer**
- Design / Structure, Separation, Patterns, Scalability
- Configuration / Dependencies, Environment

**Code Quality Reviewer**
- Implementation / Naming, Complexity, Duplication, Error Handling, Dead Code, Magic Values
- Documentation / Comments, Idioms

**Security Reviewer**
- Security / Injection, Auth, Access Control, Crypto, Secrets, XSS, CSRF, SSRF
- Configuration / Headers, TLS, Exposure

**API Design Reviewer**
- Design / Naming, Methods, Response Format, Versioning
- Implementation / Status Codes, Errors, Pagination
- Documentation / API Docs

**Testing Strategy Reviewer**
- Testing / Coverage, Quality, Isolation, Mocking, Flakiness
- Design / Organization, Pyramid

**Performance Reviewer**
- Performance / N+1 Query, Blocking Op, Memory Leak, Algorithm, Caching, Async
- Implementation / Resource Management

**Dependencies Reviewer**
- Security / CVE, Supply Chain
- Configuration / Versioning, License, Bloat, Duplicate

**Documentation Reviewer**
- Documentation / README, API Docs, Setup, Architecture, Comments, Contributing, Changelog

---

## Formatting Rules

1. **Empty sections**: If no findings exist for a severity level, include the heading with "No [severity] findings." beneath it.

2. **Consistent icons**: Always use the exact emoji specified:
   - 🔴 Critical
   - 🟠 High
   - 🟡 Medium
   - 🟢 Low
   - ℹ️ Informational

3. **Location format**: Use `file/path:line` for specific locations, or descriptive text like "Throughout codebase" for patterns.

4. **Metadata inference**: If metadata is not provided, infer from context or mark as "Not specified".

5. **Category format**: Always use "Primary / Subcategory" format. If no specific subcategory applies, use just the primary category.

6. **Common issues**: In the Executive Summary, identify patterns across findings (e.g., "Multiple naming inconsistencies", "Repeated missing validation").

---

## Usage

When invoked, this skill:
1. Reads the most recent review output in the conversation
2. Extracts all findings, categorizing by severity
3. Calculates the severity index score
4. Identifies common patterns for the executive summary
5. Maps categories to the hybrid format
6. Outputs the complete formatted report

If no review is found in the conversation, prompt the user to run a reviewer agent first.
