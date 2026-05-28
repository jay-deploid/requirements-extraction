# Google Drive Output Reference

Instructions for creating the requirements register Google Doc at the end of the extraction skill.

---

## MCP Connection

Use the Google Drive MCP connector already configured on the user's account:

```
URL: https://drivemcp.googleapis.com/mcp/v1
```

This connector is available as a connected integration — no additional setup needed.

---

## Step-by-Step: Create the Google Doc

### 1. Assemble the full document content

Before calling the MCP, compile the complete document as a single string using the structure below. Do not make multiple MCP calls to build it incrementally.

### 2. Call the Drive MCP to create the file

Use the `create_file` tool from the Google Drive MCP. Target the root of the user's Drive (no parent folder ID needed — this is the simplest path and what the user has requested).

Parameters:
- `name`: `[ProjectName] — Requirements Register — [YYYY-MM-DD]`
- `mimeType`: `application/vnd.google-apps.document`
- `content`: the full document string assembled in step 1

### 3. Return the link

After creation, the MCP returns a file ID and URL. Share the full Google Doc URL with the user so they can open it immediately.

---

## Document Structure

Use exactly this structure. Keep headings consistent — they become the navigation anchors in the Doc.

```
[Project Name] — Requirements Register
Extracted: [Date]
Source(s): [list of input files or descriptions]
Salesforce Cloud(s): [cloud context passed in prompt, or "Auto-detected: X"]

════════════════════════════════════════

SUMMARY
• Total Requirements: X
• Epics identified: [Epic 1], [Epic 2], ...
• Flags: X
• Ambiguities: X
• Out of Scope items: X
• Discovery Questions: X

════════════════════════════════════════

REQUIREMENTS BY EPIC

[EPIC NAME]
• [REQ-001] Requirement statement.
  Source: [where this came from]
• [REQ-002] Requirement statement.
  Source: [where this came from]

[EPIC NAME]
• [REQ-003] ...

════════════════════════════════════════

OUT OF SCOPE
• [OOS-001] What was discussed — Reason: why it's out of scope
• [OOS-002] ...

════════════════════════════════════════

FLAGS
• [FLAG-001] Description of the risk, dependency, or decision needed.
  Related to: REQ-XXX
• [FLAG-002] ...

════════════════════════════════════════

AMBIGUITIES
• [AMB-001] What was said — Question: what needs to be clarified
  Related to: REQ-XXX
• [AMB-002] ...

════════════════════════════════════════

RECOMMENDED DISCOVERY QUESTIONS

[TOPIC]
• Question one?
• Question two?

[TOPIC]
• Question one?

════════════════════════════════════════

NOTES FOR CONSULTANT
Observations, patterns, complexity areas, suggested next steps.
Include a note on which requirements are likely OOB vs. custom build
based on the cloud context provided.
```

---

## File Naming Convention

Always name the file:
```
[ProjectName] — Requirements Register — [YYYY-MM-DD]
```

Examples:
- `Acme Corp — Requirements Register — 2025-11-14`
- `TechStart Sales Cloud — Requirements Register — 2025-11-14`

If no project name was provided, use `[Client Unknown]` and add an AMB flag prompting the user to rename the file.

---

## Error Handling

If the Drive MCP call fails:
1. Do not silently drop the output
2. Tell the user the Drive write failed
3. Output the full document content directly in the chat so nothing is lost
4. Suggest they copy it manually or retry
