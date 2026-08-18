# EU AI Act Compliance Audit — AI Delivery Risk Assistant

**Repository:** https://github.com/akansha27nov/delivery-risk-assistant  
**Assessment date:** 18 August 2026  
**Assessment type:** First-pass EU AI Act audit for the lab; not legal advice

> **Scope discipline:** This assessment is based on the repository's documented purpose, architecture, sample reports, demo knowledge base, workflow, and implementation behavior.

## Phase 1 — System Brief

### What the system does

The AI Delivery Risk Assistant is an engineering delivery copilot that reads project artefacts to identify current software-delivery risks. Its core principle is grounding: it only reports risks supported by evidence retrieved from source documents. The system retrieves and reranks evidence, extracts up to three risks via an AI model, validates citations, evaluates severity, and generates either a Markdown delivery-risk report or routes high-severity issues for human review. It analyzes project delivery conditions, blockers, scope changes, dependencies, deadlines, and contradictions across project evidence.

### Inputs

The system loads project artefacts from `knowledge_base/` and maps them to project namespaces through `project_manifest.json`. The demo corpus contains two projects: Atlas — “Rewards Partner Launch” and Nova — “Checkout Redesign.” Source types include Markdown sprint/status reports, stakeholder or executive communications, Slack/standup text, incident/postmortem and retrospective notes, and a CSV ticket export.

The Streamlit UI also permits a new document to be uploaded into an existing project namespace. The ingestion code supports `.md`, `.txt`, and `.csv` sources. The repository states that the demo corpus is synthetic. The system nevertheless supports live project uploads, so a production deployment could process personal or confidential information contained in real project documents. The repository does not establish that special-category personal data is intentionally required.

### Outputs

Outputs include structured findings and an executive Markdown report detailing Delivery Health, a normalized Risk Score (out of 100), severity counts, risk explanations, Evidence Confidence, Business Impact (across Delivery, Customer, Business/Revenue, and Team), and actionable recommendations with citations. It can also pause for human review, publish to Notion via n8n, and send Telegram alerts.

### Who is affected

The tool targets delivery professionals—engineering managers, delivery leads, scrum masters, and executives. It impacts projects, milestones, dependencies, and resources. While it may indirectly affect team members through resource-allocation recommendations, it does not evaluate employee performance or make employment decisions.

### Human review

Human review is a deterministic part of the workflow for SEV-1/high-severity risks and status contradictions. If those conditions are detected after citation validation, the graph routes the result to Telegram for human review and returns `pending_hitl_approval`. Standard audits instead proceed to report generation. The repository does not show a universal human approval gate for every ordinary delivery finding, nor a UI for recording an override rationale.

### Who built it and who would use it

Built by Akansha Verma as a Project 3 implementation, the prototype uses LangGraph, OpenAI, Pinecone, Cohere Rerank, Telegram, FastAPI, Streamlit, and n8n/Notion. Targeted for technical leadership (VP Engineering, CTOs, Delivery Leads), future commercial deployment would likely see the developer as the AI Act "provider" and the using organization as the "deployer," subject to specific contract terms.

## Phase 2 — Risk Tier Classification

| Question | Answer |
|---|---|
| Does this system fall under any prohibited category (Article 5)? | **No, based on the documented purpose and implementation.** Nothing in the repository indicates subliminal manipulation, exploitation of vulnerabilities, social scoring, prohibited biometric identification/categorisation, predictive policing, or another Article 5 practice. |
| Does this system operate in any of the eight Annex III areas? | **No, based on the documented intended purpose.** The repository describes software delivery-risk analysis. It does not describe recruitment, selection, employment decisions, worker performance evaluation, credit, education assessment, essential-service eligibility, law enforcement, migration/border control, justice/democratic processes, or the Annex III biometric use cases. |
| If Annex III: does it “significantly influence” decisions in that area? | **Not applicable on the documented use case.** The system can recommend project actions, but the evidence in the repository shows project-management recommendations rather than decisions about a person's employment, legal status, access to essential services, or other Annex III decisions. |
| Does this system interact with end users or generate content requiring disclosure (Article 50)? | **Yes.** The Streamlit UI is an interactive AI system, so the provider should inform people when they directly interact with AI unless that is obvious. In addition, the system generates synthetic text reports using an LLM; Article 50(2) therefore requires consideration of machine-readable marking/detection of AI-generated text, subject to the statutory exceptions and applicable implementation guidance. |
| First-pass risk tier | **Limited risk / transparency obligations** |
| One-sentence justification | The repository supports a non-prohibited, non-Annex-III project-delivery use case, while the interactive AI interface and LLM-generated reports create Article 50 transparency/marking obligations rather than high-risk obligations. |

### Why this is not an employment high-risk system on the repository evidence

The repository's own sample reports are important evidence. Atlas reports risks about scope additions and QA capacity, while Nova reports a migration deadline and a status contradiction. Their recommendations concern project scope, timelines, testing capacity, monitoring, contingency planning, and remediation ownership. They do not score individual workers, rank employees, evaluate performance, or decide who should be hired, fired, promoted, disciplined, or assigned work based on individual behavior.

The repository's demo standup does contain named team members and discussion of workload and stress, but the system's output for Atlas converts that material into a **team/project delivery risk** (“QA capacity constraints impacting delivery”), not an employee assessment. The documented purpose therefore should not be rewritten as an employment-management system merely because employee-related information can appear in source documents.

This classification should be revisited if the product's intended purpose changes. If a future version is explicitly designed to evaluate individual worker performance, monitor behavior, or make decisions about terms of work, the Annex III employment analysis would need to be performed again.

### Article 50 clarification

The current European Commission guidance states that Article 50 applies from 2 August 2026. It distinguishes the obligation to inform individuals when they interact directly with an AI system from obligations concerning AI-generated/manipulated content. For providers of AI systems generating synthetic text, Article 50(2) requires machine-readable marking/detection, subject to exceptions. For deployers, the specific visible disclosure for AI-generated text applies when text is published for the purpose of informing the public on matters of public interest and has not undergone human review/editorial control. The repository's internal executive project reports are not documented as public-interest publications, so the deployer-side public-interest text rule should not be assumed to apply. The provider-side Article 50(2) question should nevertheless be addressed for the report-generation system. [European Commission — Article 50 guidelines](https://digital-strategy.ec.europa.eu/en/policies/guidelines-transparency-ai-generated-content)

## Phase 3 — Role Map

| Role | Entity | Key AI Act obligations / assessment |
|---|---|---|
| **Provider candidate** | Project developer / owner of the Delivery Risk Assistant | If the developer places the assistant on the EU market or puts it into service under its own name, it is the likely provider. For the current non-high-risk classification, the main AI Act issue is compliance with applicable Article 50 transparency obligations and maintaining an accurate intended-purpose/classification description. |
| **Deployer** | Future client organization using the assistant for professional project delivery | Use the system according to its intended purpose and instructions; apply applicable Article 50 deployer transparency rules; maintain appropriate organizational, security, and data-protection controls. If the client changes the intended purpose into a high-risk use, the role analysis must be repeated. |
| **OpenAI** | OpenAI API | Provides the embedding and structured risk-extraction model services used by the application. Its precise AI Act role/obligations depend on the specific service/model and contractual arrangement; this assessment does not assume a particular GPAI legal classification without vendor documentation. |
| **Pinecone** | Pinecone vector database | Stores project-specific embedded evidence in namespaces. The repository documents it as infrastructure rather than an independent decision-making component. GDPR/security/vendor terms still need review for production use. |
| **Cohere** | Cohere Rerank `rerank-v3.5` | Reranks candidate evidence before LLM extraction. Its precise AI Act role depends on the service arrangement; it should be included in the production vendor/data-flow review. |
| **Telegram** | Telegram Bot API | Transports high-severity/contradiction human-review alerts. It is not treated as an AI provider merely because it is used for notification delivery. |
| **n8n / Notion** | Automation and reporting path | n8n triggers scheduled audits and can deliver reports to Notion. The production deployment should assess the data-processing and access implications of this path. |

## Phase 4 — High-Risk Obligation Checklist

**Not applicable to the current documented system.**

Because the repository does not establish an Annex III high-risk intended purpose, the 11-provider-obligation checklist for Articles 9–15, 43, 47–49, and 72 is not triggered by the current use case.

This conclusion is about **classification**, not about whether the engineering controls are good enough. Several of the same controls would be valuable as voluntary governance measures.

## Phase 5 — Gap Analysis and Remediation Plan

### Gap 1 — Article 50 direct AI interaction transparency

**Obligation:** Article 50 transparency for people directly interacting with an AI system.

**Current state:** The project has a Streamlit interface where users select a project namespace, inspect evidence, and run an AI audit. The repository does not document a dedicated “you are interacting with AI” notice.

**Required state:** Users should be explicitly informed that they are interacting with an AI system unless the AI nature is already obvious.

**Remediation:** Add a persistent notice in the Streamlit UI near the audit controls, such as: “AI-generated delivery-risk analysis: this assistant uses AI to analyze project evidence and produce risk findings.” Include the disclosure in the product's user documentation.

**Escalation needed?** **Yes — product/legal review** to determine whether the existing UI makes the AI nature obvious enough.

### Gap 2 — Article 50(2) marking of generated text

**Obligation:** Provider requirements for AI systems generating synthetic text.

**Current state:** The application uses OpenAI structured extraction and a programmatic Markdown report builder. The repository explicitly describes generated Markdown reports and executive risk briefings. No machine-readable marking mechanism for generated text is documented.

**Required state:** Where Article 50(2) applies, the provider must ensure generated synthetic text is marked in a machine-readable format and detectable as AI-generated/manipulated, subject to the statutory exceptions.

**Remediation:** Determine whether the report-generation output falls within an Article 50(2) exception. If not, add an appropriate machine-readable AI-origin marker to generated reports and document the technical mechanism. Keep a visible “Generated by Delivery Risk Assistant” label as a complementary product signal, but do not treat that alone as proof of machine-readable compliance.

**Escalation needed?** **Yes — legal/compliance review and engineering review.**

### Gap 3 — Personal-data and confidential-data governance for live uploads

**Obligation:** Parallel GDPR/data-protection and security governance; not a high-risk AI Act obligation under the current classification.

**Current state:** The repository's demo corpus is synthetic, but the live Streamlit upload flow allows a new document to be added to an existing project namespace. Real project documents could contain names, contact details, internal communications, customer information, or other personal/confidential data. Those documents may then flow through embeddings, Pinecone, reranking, LLM analysis, reporting, and notifications.

**Required state:** A production deployment should document the data categories, purpose, lawful basis, access controls, retention/deletion, vendor processing, international transfers where relevant, and handling of data-subject requests.

**Remediation:** Create a production data-flow inventory covering upload → chunking → embeddings → Pinecone → retrieval/reranking → OpenAI analysis → report → Notion/Telegram. Add data minimization, retention/deletion, access-control, and vendor-contract checks.

**Escalation needed?** **Yes — DPO/privacy counsel.**

### Gap 4 — Human-review evidence and accountability

**Obligation:** Governance/control improvement; not an Article 14 high-risk obligation under the current classification.

**Current state:** The project has a strong deterministic HITL gate for SEV-1/high-severity risks and status contradictions. These findings become `pending_hitl_approval` and are routed to Telegram. Standard audits proceed directly to report generation. The project does not document a structured mechanism for recording who approved/rejected a finding or why.

**Required state:** For a production decision-support product, material human decisions should be attributable and reconstructable, especially when recommendations affect project priorities, deadlines, customers, or resource allocation.

**Remediation:** Add an approval record for escalated findings containing finding ID, reviewer identity, decision, rationale, timestamp, and final action. Preserve the evidence/citation set that supported the finding.

**Escalation needed?** **No** for the basic engineering design; **yes to privacy/legal** when defining retention and access rules.

### Gap 5 — Operational traceability and retention

**Obligation:** Operational accountability and security best practice; not Article 12 high-risk logging under the current classification.

**Current state: Largely satisfied at the evidence-traceability level.** The system provides a Forensic Evidence Inspector that exposes the evidence retrieved for analysis, and the generated risk reports include citations showing where the evidence supporting a particular risk came from. The validation layer also checks that reported risks are grounded in retrieved evidence. This gives users a practical way to reconstruct why a risk was identified and which project sources supported it.

**Remaining gap:** The repository does not establish whether the application persistently retains the complete lifecycle metadata for every audit run — for example, the run configuration/model version, validation result, HITL decision, and downstream delivery event. This is not a claim that the system lacks traceability; it is a narrower production-governance question about how long the traceability information remains available and whether the full audit lifecycle can be reconstructed after the UI/report is no longer available.

**Required state:** Preserve the existing evidence-level traceability and, for production use, define what operational metadata needs to be retained alongside the generated report and evidence references. The goal is to preserve the ability to reconstruct a material finding without duplicating the evidence already exposed by the Forensic Evidence Inspector.

**Remediation:** Strengthen rather than replace the existing mechanism. Retain a stable audit/run identifier and references to the evidence and generated report, together with the relevant model/configuration version and HITL outcome where applicable. Do not duplicate the underlying evidence if the existing evidence store/report already provides it. Define an appropriate retention period and access policy.

**Escalation needed? No** for the evidence-traceability design. **Yes — privacy/legal review** only for retention and access requirements if real client/project data is used.

## Phase 6 — Compliance Memo

### MEMORANDUM

**To:** Head of Engineering, Head of Product, Project Client  
**From:** AI Compliance Review  
**Date:** 18 August 2026  
**Subject:** First-Pass EU AI Act Assessment — AI Delivery Risk Assistant

#### System classification

The current Delivery Risk Assistant is best classified as a **limited-risk / transparency** AI system: the repository documents project-delivery risk analysis rather than a prohibited practice or an Annex III high-risk use case.

#### Role map

The project developer is the likely **provider** if the system is placed on the EU market or put into service under the developer's own name. The client organization is the likely **deployer** when it uses the system for professional delivery management. OpenAI, Pinecone, Cohere, Telegram, and n8n/Notion are third-party services in the technical chain; their exact AI Act roles should be confirmed from the relevant services and contracts.

#### Key findings

1. **Article 50 transparency is not documented.** The Streamlit interface directly exposes users to an AI-powered audit workflow, but the repository does not document a dedicated AI-interaction notice.
2. **Generated-text marking needs assessment.** The system produces synthetic Markdown risk reports using an LLM. The provider should determine whether Article 50(2) applies and, if it does, implement the required machine-readable marking.
3. **Live uploads create a GDPR/data-governance exposure.** The demo data is synthetic, but the product can ingest real project files. Those files can contain personal or confidential information and are sent through multiple external services.
4. **The HITL design is a genuine control.** SEV-1 risks and status contradictions are deterministically routed to human review rather than automatically finalized. The remaining weakness is the lack of a documented approval/decision record.

#### Recommended next steps

**First:** add and test the Article 50 AI-interaction notice.

**Second:** conduct a focused Article 50(2) assessment of the generated Markdown reports and implement machine-readable marking if required.

**Third:** document the production data flow and complete the GDPR/vendor review before connecting real client data.

**Fourth:** strengthen the existing HITL path with reviewer identity, decision, rationale, and timestamp records.

**Fifth:** maintain a clear intended-purpose statement that describes the product as project-delivery risk analysis. If the product is later changed to evaluate individual workers, monitor worker behavior, or make employment decisions, perform a new Annex III classification before introducing that use.

#### Caveats

This is a first-pass educational assessment based on the repository as currently documented. It is not a legal opinion, formal conformity assessment, certification, or regulatory determination. Production deployment should be reviewed by qualified legal/privacy specialists.

## Reinforce — What the repository reveals that matters for the audit

### 1. The project's strongest compliance-relevant design decision

The most important positive design choice is **strict grounding**. The repository states that a risk cannot be reported unless it can be directly grounded in source chunks. The workflow first checks whether evidence exists, validates citations and context, and rejects unsupported responses. Contradictions also require distinct evidence supporting the status claim and the blocking/problem condition.

This is materially different from a generic LLM chatbot. The system has explicit evidence gates intended to reduce hallucinated risks and improve traceability.

### 2. Human-in-the-loop is real, but narrowly scoped

HITL is not merely a statement in the README. The graph contains a deterministic route for high-severity risks and contradictions, and the result becomes `pending_hitl_approval`. Telegram is used for the approval request. Standard audits bypass that route and generate the final report.

The compliance gap is therefore not “there is no human oversight.” The accurate finding is: **there is a real human-review gate for defined high-severity conditions, but the repository does not document structured recording of the human decision and does not require review of every standard finding.**

### 3. The system's actual outputs do not support the worker-performance interpretation

The Atlas and Nova reports are particularly useful evidence for classification. Atlas reports scope creep and QA capacity; Nova reports a migration deadline and a contradictory status/remediation record. Their business-impact dimensions include Delivery, Customer, Business/Revenue, and Team. This is project-delivery risk analysis.

The project should therefore not be audited as an HR performance system merely because a source document can contain a person's name or workload comment.

## Stretch — Proposed Human Oversight Procedure

**Purpose:** Strengthen the existing HITL mechanism without inventing a new use case.

1. **Trigger:** A validated SEV-1/high-severity risk or status contradiction enters HITL.
2. **Reviewer:** A designated delivery manager reviews the finding and the cited evidence.
3. **Checks:** Confirm that the cited chunks support the finding, that the issue is current, and that the recommendation is reasonable in the project context.
4. **Decision:** Record **approve / reject / request more evidence**.
5. **Rationale:** Record a short explanation for the decision, especially when rejecting or materially changing the recommendation.
6. **Audit record:** Store run ID, project namespace, finding, risk ID, citations, severity, reviewer, decision, rationale, and timestamp.

`ai_audit_ledger`

```json
{
  "timestamp_utc": "2026-08-18T16:20:00Z",
  "audit_run_id": "run-88392-atl",
  "project_namespace": "Atlas-Launch",
  "risk_finding": {
    "risk_id": "R-01",
    "severity": "SEV-1",
    "description": "QA capacity constraint delaying launch window",
    "evidence_citations": ["sprint_report_3.md#chunk-2"]
  },
  "hitl_interaction": {
    "reviewer_id": "user_mgr_912",
    "decision": "APPROVED",
    "rationale": "Verified QA backlog against ticket velocity; risk is accurate.",
    "response_time_seconds": 84
  }
}
```

7. **Failure handling:** If Telegram delivery fails, the system should retain the `requires_hitl` state and surface the failed notification clearly rather than silently treating the audit as approved.
8. **Scope boundary:** The assistant remains decision support. It should not be repurposed as an employee-performance evaluation or employment decision system without a fresh AI Act assessment.

## Conclusion

Based strictly on the repository, the Delivery Risk Assistant is a **project-delivery risk analysis and decision-support system**, not an employee-performance management system. Its demo artefacts analyze scope creep, blocked dependencies, QA capacity, migration deadlines, contradictions between status and ticket evidence, customer impact, business/revenue impact, and team impact. The implementation retrieves evidence, reranks it, performs structured risk extraction, validates citations/context, rejects unsupported findings, and routes SEV-1/contradiction findings to human review.

The first-pass EU AI Act classification is therefore **limited risk / transparency**, with the main AI Act work focused on Article 50. The most important parallel compliance issue is GDPR/data governance if real project documents are uploaded. The classification should be reassessed only if the product's intended purpose materially changes, especially toward employment or worker-management decisions.

## Repository Evidence

- Repository README: system purpose, pipeline, sample outputs, architecture, external services, HITL routing, data notes, and terminology. 
- `samples/atlas_risk_report.md`: generated Atlas report covering scope addition and QA capacity. 
- `samples/nova_risk_report.md`: generated Nova report covering migration deadline and a status contradiction. 
- `project_manifest.json`: project namespaces and their source artefacts.
- `knowledge_base/`: synthetic demo corpus for Atlas and Nova.
- `src/graph.py`: deterministic retrieval → analysis → validation → severity → HITL/report workflow.
- `src/agent_validation.py`: citation, context, and contradiction validation.
- `src/agent_analysis.py`: risk extraction and contradiction handling.

## External Regulatory References

- European Commission, *Guidelines on transparency obligations for providers and deployers of certain AI systems*: https://digital-strategy.ec.europa.eu/en/policies/guidelines-transparency-ai-generated-content
- European Commission, *Quick Facts: Transparency rules for AI systems*: https://digital-strategy.ec.europa.eu/en/factpages/quick-facts-transparency-rules-ai-systems
- EUR-Lex, *Regulation (EU) 2024/1689 — Article 50*: https://eur-lex.europa.eu/eli/reg/2024/1689/oj
