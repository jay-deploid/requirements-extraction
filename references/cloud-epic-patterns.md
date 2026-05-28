# Cloud-Specific Epic Patterns

Reference file for the requirements-extraction skill.
Load this file when a Salesforce cloud context has been provided in the prompt.
Use the relevant cloud section(s) to guide epic detection and requirement framing.

---

## Sales Cloud

**Default epics to look for:**
- Lead Management & Qualification
- Opportunity & Pipeline Management
- Account & Contact Management
- Activity Logging & Engagement Tracking
- Quoting & Order Management
- Forecasting & Revenue Reporting
- Territory & Assignment Rules
- Data & Integrations (CRM sync, marketing handoff)
- Reporting & Dashboards
- User Management & Profiles

**OOB capability signals** — if these come up, flag as likely OOB config (no custom dev needed):
- Lead assignment rules, web-to-lead
- Opportunity stages, probability, close date tracking
- Activity timeline (calls, emails, tasks)
- Forecasting categories and hierarchy
- Standard reports and dashboards
- Role hierarchy and sharing rules

**Custom dev signals** — flag these for complexity review:
- Complex approval processes with parallel branches
- CPQ-style dynamic quoting (likely needs Revenue Cloud)
- External system sync (ERP, billing) without confirmed integration
- AI-powered lead scoring (likely needs Einstein / Agentforce)
- Partner portal for channel sales (likely needs Experience Cloud)

**Common ambiguities to probe:**
- Is forecasting by rep, team, or product line?
- What does the current lead handoff from marketing look like?
- Are there multiple sales processes / record types needed?
- What integrations exist today (email, calendar, ERP)?

---

## Service Cloud

**Default epics to look for:**
- Case Creation & Intake (web, email, phone, chat)
- Case Routing & Queue Management
- SLA & Entitlement Management
- Knowledge Base & Article Management
- Escalation & Priority Management
- Omni-Channel & Channel Strategy
- Customer Communication & Templates
- Reporting & Service Analytics
- Agent Experience & Productivity
- Data & Integrations (telephony, ticketing, CRM)

**OOB capability signals:**
- Email-to-case, web-to-case
- Queue-based routing and assignment rules
- Escalation rules and milestone tracking
- Knowledge articles with visibility settings
- Case auto-response templates
- Standard service dashboards and reports

**Custom dev signals:**
- Complex routing logic based on skills, availability, or language
- CTI / telephony integration (Genesys, Amazon Connect, Twilio)
- Real-time chat or messaging beyond standard Messaging
- Custom SLA calculations outside standard entitlements
- Self-service portal (likely needs Experience Cloud)

**Common ambiguities to probe:**
- What channels do customers use today (email, phone, chat, social)?
- Is there an existing telephony platform to integrate?
- Who manages the knowledge base — agents, a dedicated team?
- Are SLAs contractual or internal targets?
- What does escalation look like — to a team, a manager, a partner?

---

## Experience Cloud

**Default epics to look for:**
- Portal Structure & Navigation
- Authentication & User Registration
- Self-Service Case Deflection & Submission
- Knowledge Base (public-facing)
- Community / Collaboration Features
- Account & Contact Data Visibility
- Branded Theme & Design
- Notifications & Alerts
- Reporting & Engagement Analytics
- Data & Integrations (SSO, CRM data exposure)

**OOB capability signals:**
- LWR (Lightning Web Runtime) templates
- Standard login / self-registration flows
- Knowledge article surfacing
- Case submission and tracking
- Salesforce CRM data exposure via standard components

**Custom dev signals:**
- Bespoke UI components (custom LWC)
- Complex permission sets (who sees what data by relationship)
- External data sources surfaced in the portal
- Mobile app parity expectations
- Large, complex member/partner networks with varied access

**Common ambiguities to probe:**
- Who are the portal users — customers, partners, employees, or all three?
- What data from Salesforce needs to be visible (and to whom)?
- Is SSO required — what identity provider?
- Will users be able to take actions (submit cases, update records) or just view?
- What does "branded" mean — light theming or full custom design?

---

## Agentforce / Einstein

**Default epics to look for:**
- Agent Use Case Definition (what the agent handles)
- Data Grounding & Knowledge Sources
- Handoff & Escalation to Human Agents
- Channel Deployment (web, messaging, voice)
- Guardrails & Topic Restrictions
- Testing & Quality Assurance
- Reporting & Agent Performance
- Integration with Service / Sales Cloud Records

**OOB capability signals:**
- Einstein Copilot for standard Sales / Service actions
- Agentforce Service Agent for case deflection (pre-built topics)
- Standard knowledge grounding from Salesforce Knowledge
- Out-of-box Einstein activity capture

**Custom dev signals:**
- Custom topics and actions beyond standard library
- Complex data grounding (external systems, unstructured docs)
- Multi-turn conversation flows with branching logic
- Voice channel deployment
- Integration with non-Salesforce knowledge bases

**Data readiness signals** — flag these as blockers:
- No existing Knowledge base → agent has nothing to ground on
- Unstructured data sources (PDFs, SharePoint, wikis) → Data Cloud likely needed
- No defined escalation path → blocks handoff design

**Common ambiguities to probe:**
- What is the primary use case — deflection, assisted selling, or internal productivity?
- What knowledge sources exist today and in what format?
- What does a successful handoff to a human look like?
- Are there topics the agent must never handle (legal, complaints, refunds)?
- What channel is the agent being deployed on first?

---

## Revenue Cloud / CPQ

**Default epics to look for:**
- Product Catalogue & Pricing
- Quote Configuration & Rules
- Approval Workflows
- Contract Lifecycle Management
- Order Management & Fulfilment
- Billing & Invoicing
- Amendments, Renewals & Upsells
- Revenue Recognition & Reporting
- Data & Integrations (ERP, finance systems)

**OOB capability signals:**
- Standard product bundles and option sets
- Price rules and discount schedules
- Quote templates and PDF generation
- Standard approval chains
- Subscription and renewal management

**Custom dev signals:**
- Highly complex pricing logic (tiered, usage-based, multi-currency)
- ERP integration for order fulfilment (SAP, NetSuite, Oracle)
- Custom invoice formatting
- Revenue recognition rules outside standard schedules
- Multi-entity or multi-org environments

**Common ambiguities to probe:**
- How many products / SKUs are in scope?
- Is pricing rules-based, negotiated, or both?
- Who approves quotes — one level or multiple?
- Does the contract need to flow into an ERP system?
- Are subscriptions, renewals, or amendments in scope for Phase 1?

---

## Marketing Cloud / Account Engagement (Pardot)

**Default epics to look for:**
- Lead Capture & Form Management
- Email Campaign Management
- Lead Nurture & Automation
- Lead Scoring & Grading
- CRM Sync & Handoff to Sales
- Segmentation & List Management
- Landing Pages & Assets
- Reporting & Campaign Attribution
- Data & Integrations (website, events, ad platforms)

**OOB capability signals (Account Engagement / Pardot):**
- Form handlers, landing page templates
- Engagement Studio automation programs
- Standard lead scoring and grading
- Salesforce connector sync (standard field mapping)
- Email send and tracking

**Custom dev signals:**
- Complex multi-touch attribution
- Custom scoring models
- Integration with event platforms (Cvent, Eventbrite)
- Ad platform sync (Google, LinkedIn, Meta)
- Large-scale data migrations from legacy MAP

**Common ambiguities to probe:**
- Is this Account Engagement (Pardot) or Marketing Cloud Engagement (SFMC)?
- What does the current lead handoff to sales look like — who owns MQL definition?
- How many active nurture programs are needed at launch?
- Is website tracking (tracking pixel) in scope?
- Are there existing lists or a database to migrate?

---

## Multi-Cloud Projects

When two or more clouds are in scope, note cross-cloud dependencies as FLAGS:

- Sales + Service → shared Account/Contact data model, unified activity timeline
- Service + Experience → portal users linked to Service Cloud cases and knowledge
- Sales + Revenue Cloud → quote-to-order handoff, approval chain alignment
- Any cloud + Agentforce → data readiness is a prerequisite, not a parallel workstream
- Any cloud + Marketing Cloud → CRM sync field mapping, MQL definition, lead routing

When extracting requirements for multi-cloud projects, tag each requirement with the primary cloud it belongs to in the Source field:

```
• [REQ-014] The sales team must receive an automated alert when a high-scoring lead is handed off from marketing.
  Source: Workshop transcript, raised by Sales Director | Cloud: Sales Cloud + Account Engagement
```
