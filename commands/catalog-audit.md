---
name: catalog-audit
description: Audit DataHub metadata and governance coverage across a defined catalog scope
argument-hint: "[scope or audit request]"
---

# DataHub Audit

Use the Skill tool to invoke the full `datahub-audit` skill:

```
Skill tool:
  skill: "datahub-skills:datahub-audit"
```

**User's request:** $ARGUMENTS

This skill produces systematic, read-only metadata and governance coverage reports with explicit denominators, exhaustive pagination when available, and prioritized gaps.

If no arguments are provided, ask what catalog scope and coverage dimensions the user wants to audit.
