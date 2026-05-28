---
name: requirements-extraction
description: >
  Use this skill whenever the user wants to extract, analyse, or structure
  requirements from any source material — uploaded documents (PDF, DOCX, TXT),
  meeting transcripts, workshop notes, email threads, or pasted text.
  The skill strips noise, extracts clean requirements grouped by functional Epic,
  identifies Out of Scope items, Flags (risks/dependencies/decisions), Ambiguities,
  and generates prioritised Discovery Questions. Outputs a structured Google Doc
  to the user's Drive automatically.

  ALWAYS trigger this skill when the user:
  - Uploads a transcript, doc, or notes alongside any prompt about requirements
  - Says "extract requirements", "pull out requirements", "analyse this transcript"
  - Says "what did the client ask for", "requirements from this doc", "log the reqs"
  - Mentions a discovery session, workshop, or client call they want documented
  - Pastes raw meeting notes and asks for structure

  This is a Salesforce implementation context. Epics are business functions,
  not Salesforce product clouds. Cloud-specific epic patterns are loaded from
  references/cloud-epic-patterns.md when a cloud context is provided.
---

# Requirements Extraction Skill

Extracts clean, structured client requirements from noisy source material.
Outputs a requirements register to Google Drive.

---

## Before You Start — Read the Cloud Context

Check the prompt for a Salesforce cloud context, e.g.:
- `cloud = Service Cloud`
- `SF cloud: Sales Cloud + Experience Cloud`
- `Agentforce project`

**If a cloud context is provided** → read `references/cloud-epic-patterns.md` and load
the relevant cloud section(s) before extracting. This gives you the right default epics,
OOB vs custom signals, and ambiguities to probe for that cloud.

**If no cloud context is provided** → auto-detect from the content. After extraction,
note which cloud(s) you inferred and flag it as an AMB for the consultant to confirm.

---

## Step 1 — Read the Source Material

| Input type | How to handle |
|---|---|
| Uploaded file (PDF, DOCX, TXT) | Consult `/mnt/skills/public/file-reading/SKILL.md` for the correct extraction method |
| Pasted text | Use directly from conversation |
| Multiple inputs | Process all before extracting. Deduplicate across sources |
| Google Doc URL | Use the Google Drive MCP to fetch the content first |

---

## Step 2 — Filter Noise

Strip these before extracting — do NOT include in output:

- Greetings, sign-offs, small talk, meeting logistics
- Vendor opinions, sales pitches, internal team discussion
- Repeated statements of the same point
- Current-state descriptions unless they imply a change is needed
- Unanswered questions → move to Ambiguities instead

---

## Step 3 — Extract & Group by Epic

### Epic naming rules

- Name by **business function**, never by Salesforce product cloud
- Auto-detect from content; use cloud-epic-patterns.md as your guide if cloud context is set
- If a requirement spans two epics, place it in the most relevant and note the dependency
- Good: `Case Management & SLA`, `Lead Management`, `Customer Portal`
- Bad: `Service Cloud`, `Sales Cloud`, `Experience Cloud`

### Requirement format

```
• [REQ-XXX] Clear statement of what the client needs — not a solution, not a task.
  Source: [who raised it / where it came from]
```

Number sequentially across the whole document: REQ-001, REQ-002, etc.

**Good:** `[REQ-012] Sales reps must be able to log a call and automatically associate it with the related Opportunity.`
**Too vague:** `The system should be better for sales reps.`
**Is a solution, not a requirement:** `Build a Flow that triggers on Opportunity stage change.`

For multi-cloud projects, add a cloud tag to the Source line:
```
  Source: Workshop transcript, Head of Sales | Cloud: Sales Cloud
```

---

## Step 4 — Out of Scope

```
• [OOS-XXX] What was discussed — Reason: why it's out of scope or needs confirming
```

Include anything explicitly excluded, and anything you assess as unlikely to be in scope
based on context (e.g. Phase 2 items, unconfirmed integrations, out-of-budget features).

---

## Step 5 — Flags

Risks, dependencies, and decisions that must be resolved before or during build.

```
• [FLAG-XXX] Description of the risk, dependency, or decision needed.
  Related to: REQ-XXX (if applicable)
```

Always flag:
- Third-party integrations with no confirmed API access
- Requirements that depend on another requirement being completed first
- Commercial/licensing implications (add-on products, additional orgs)
- Data migration with no confirmed source or format
- Stakeholder sign-off still needed
- Cloud-specific blockers (e.g. no Knowledge base for Agentforce grounding)

---

## Step 6 — Ambiguities

Unclear requirements that need follow-up before they can be built.

```
• [AMB-XXX] What was said — Question: what needs to be clarified
  Related to: REQ-XXX (if applicable)
```

Also add an AMB if:
- No cloud context was provided and you inferred it
- Project name was not provided

---

## Step 7 — Discovery Questions

Produce 10–20 prioritised questions for the consultant to ask the client.

These are **not** just restated ambiguities. Draw from three sources:
1. **Ambiguities** — reframe as direct client-facing questions
2. **Flags** — where a decision is needed, turn it into a question
3. **Consultant knowledge** — add questions the transcript didn't cover but
   a Salesforce consultant would know to ask (data migration, adoption, integrations,
   approval processes, existing tech stack, licensing)

If a cloud context was loaded from `references/cloud-epic-patterns.md`, use the
"Common ambiguities to probe" list from that cloud section as a starting point.

Group by topic. Format:
```
[TOPIC]
• Question one?
• Question two?
```

Prioritise questions that would **block build** if left unanswered.

---

## Step 8 — Quality Check

Before writing to Drive, verify:

- [ ] Every requirement is a client need, not a solution or task
- [ ] No duplicate requirements (consolidate if found)
- [ ] All epics named by business function, not Salesforce product
- [ ] Every Ambiguity has a clear follow-up question
- [ ] Every Flag is actionable with enough context
- [ ] REQ / OOS / FLAG / AMB numbers are sequential and unique
- [ ] Summary counts are accurate
- [ ] Cloud context is noted (provided or inferred)

---

## Step 9 — Write to Google Drive

Read `references/google-drive-output.md` for the exact doc structure,
file naming convention, MCP connection details, and error handling.

Key points:
- Assemble the full document content **before** calling the MCP
- File name format: `[ProjectName] — Requirements Register — [YYYY-MM-DD]`
- Target: root of user's Google Drive (no folder ID needed)
- After creation, return the Google Doc link to the user

---

## Edge Cases

**Sparse input**: Note the source material was limited. Flag that a more detailed
discovery session may be needed before requirements can be considered complete.

**Technical input** (solution design, architecture doc): Extract the functional
requirements embedded in it — ignore technical specifications unless they imply
a constraint on what must be built.

**Re-implementation / brownfield**: Note existing functionality vs. net-new
in the requirement where relevant. Flag any risk of config conflicts with existing org.

**Multiple source documents**: Extract from each individually first, then merge.
Deduplicate. Note in the Source field when a requirement was mentioned in multiple
sources (increases confidence). Flag contradictions between sources as Ambiguities.
