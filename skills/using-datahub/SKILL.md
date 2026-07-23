---
name: using-datahub
description: |
  This skill provides routing guidance for all DataHub interaction skills. It is injected at session start and helps map user intent to the correct skill. Do not invoke this skill directly — it is loaded automatically.
---

# Using DataHub Skills

You have access to 6 DataHub catalog interaction skills. Use this guide to route the user's request to the correct skill.

---

## Skill Routing Table

| User Intent                                                                      | Skill       | Command            |
| -------------------------------------------------------------------------------- | ----------- | ------------------ |
| **Find or discover entities** (search, browse, filter, list)                     | **Search**  | `/datahub-search`  |
| **Answer an ad-hoc question** about the catalog ("who owns X?", "what is X?")     | **Search**  | `/datahub-search`  |
| **Systematic metadata/governance audit** (coverage %, completeness, gap reports) | **Audit**   | `/datahub-audit`   |
| **Update metadata** (descriptions, tags, glossary terms, ownership, deprecation) | **Enrich**  | `/datahub-enrich`  |
| **Explore lineage** (upstream, downstream, impact, root cause, dependencies)     | **Lineage** | `/datahub-lineage` |
| **Data quality** (assertions, incidents, health checks)                          | **Quality** | `/datahub-quality` |
| **Notifications** (subscribe to assertion failures, incidents)                   | **Quality** | `/datahub-quality` |
| **Install CLI, authenticate, verify connection**                                 | **Setup**   | `/datahub-setup`   |
| **Configure default scopes and profiles**                                        | **Setup**   | `/datahub-setup`   |

---

## Disambiguation Rules

When the intent is ambiguous, use these rules:

### Search vs. Audit

- **One-off answer** — "Who owns this dataset?", "Does X have a description?" → **Search**
- **Systematic report** — "What percentage of PROD datasets lack owners?", "Audit metadata completeness" → **Audit**
- Audit is read-only. If the user wants to fix the gaps, route the approved writes to **Enrich**.

### "Tag" requests

- **All tag write operations** (PII, sensitive, important, reviewed, team-x) → **Enrich**
- **Coverage report** ("what percentage of datasets are tagged?") → **Audit**

### "Domain" requests

- **Filter search to a domain** → **Search**
- **Domain assignment coverage** → **Audit**
- **Configure default domain** → **Setup**

### "Quality" or "health" requests

- **Failing assertions, active incidents, data quality health status** → **Quality**
- **Create assertions, run quality checks, raise incidents** → **Quality**
- **Subscribe to assertion failures or incidents** → **Quality**
- **Metadata quality/documentation/ownership coverage** → **Audit**

### Lineage vs. Search

- **"What feeds into X" / "what depends on X" / "impact of changing X"** → **Lineage**
- **"What dashboards use table X"** → **Lineage** (relationship traversal)
- **"Who owns X" / "what is X"** → **Search** (metadata lookup)

### Setup vs. other skills

- **"Set up" / "install" / "authenticate" / "verify connection"** → **Setup**
- **"Configure defaults" / "set default platform" / "create profile"** → **Setup**
- **"Check if DataHub is working"** → **Setup** (connectivity verification)

---

## CLI Attribution

When running `datahub` CLI commands, pass `-C skill=<name>` on the root command so usage can be attributed:

```bash
datahub -C skill=datahub-search search "revenue"
datahub -C skill=datahub-audit search "*" --where "entity_type = dataset"
datahub -C skill=datahub-enrich graphql --query '...'
datahub -C skill=datahub-lineage lineage --urn "..."
```

Use the skill name from the YAML frontmatter. If `-C` is not recognized, omit it — the command works the same without it.

---

## Critical Rules

1. **Never guess the skill.** If the intent is genuinely ambiguous, ask the user to clarify.
2. **One skill per request** unless the user explicitly asks for multiple operations.
3. **Search is ad-hoc; Audit is systematic.** Coverage percentages and governance completeness reports belong to Audit.
4. **Audit is read-only.** Remediation writes are routed to Enrich.
5. **Lineage is for lineage only** — not for general entity questions.
6. **Enrich handles all metadata writes** — descriptions, tags, glossary terms, ownership, deprecation.
7. **Quality handles data quality** — assertions, incidents, health checks, subscriptions.
8. **Setup handles environment and configuration** — CLI install, auth, connectivity, default scopes.
