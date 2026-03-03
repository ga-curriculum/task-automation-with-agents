# Task Automation with Agents
**Session Time:** 120 minutes  
**Format:** Instructor-Led  

---

## Prerequisites

- Completion of Sessions 1–10  
- Understanding of how agents connect to external tools and business data (Sessions 7 and 10)  
- Familiarity with human-in-the-loop patterns and escalation points (Session 7)  
- Understanding of write patterns and data governance (Session 10)  

---

## Learning Objectives

By the end of this session, students will be able to:

1. **Identify** repetitive tasks and business processes that are suitable for automation using agents.
2. **Design** an automated workflow using an agent to handle predefined tasks.
3. **Evaluate** the impact of automation on efficiency and error rates, and **define** guardrails and fallback paths for when the agent fails or produces unsafe results.

> Automation is where the value of agentic AI becomes tangible and measurable. This session is about identifying the right opportunities, designing workflows that actually work in production, and building in the safety mechanisms that make automation trustworthy — not just fast.

---

## Session Breakdown

| # | Segment | Duration |
|---|---------|----------|
| 1 | Identifying the Right Tasks for Automation | 20 min |
| 2 | Designing an Automated Workflow | 20 min |
| 3 | Evaluating Impact and Building Guardrails | 15 min |
| 4 | Break | 10 min |
| 5 | Lab | 60 min |
| 6 | Wrap-Up & Reflection | 5 min |
|   | **Total** | **120 min** |

---

## 1. Identifying the Right Tasks for Automation (20 min)

### Not Everything Should Be Automated

The most common mistake in AI automation projects is picking the wrong task to start with. Teams often choose the most complex, most visible, or most impressive workflow — and then struggle because it's too ambiguous, too high-risk, or too dependent on human judgment to automate reliably.

The tasks that deliver the most value through automation are usually not the glamorous ones. They are the **repetitive, time-consuming, rule-based tasks that nobody enjoys doing** — the ones that eat hours every week, produce errors when humans are tired, and follow a predictable pattern every single time.

**A useful mental test:** before deciding to automate something, ask yourself — *"Does this task feel like work, or does it feel like thinking?"*

<img width="926" height="293" alt="image" src="https://git.generalassemb.ly/user-attachments/assets/e3398607-dbef-4d3a-b381-e29e512eeea4" />

---

### The Four Signals That a Task Is Ready for Automation

<img width="927" height="430" alt="image" src="https://git.generalassemb.ly/user-attachments/assets/735baa36-4544-4be1-aef0-bd457d8bcfb9" />

---

### The Automation Readiness Matrix

Use this matrix to evaluate any task before committing to automating it:

<img width="927" height="311" alt="image" src="https://git.generalassemb.ly/user-attachments/assets/aa30f499-bb68-4fdb-9022-e806bfcfce8c" />


**Score guide:**
- 4–5 Yes answers → Automate
- 2–3 Yes answers → Automate with strong guardrails and human oversight
- 0–1 Yes answers → Not ready for automation yet; define the process first

---

### From Individual Tasks to Processes

Automating a single task is useful. Automating a **connected sequence of tasks** — a workflow — is where compounding value appears.

A workflow is a series of tasks that happen in a defined sequence, where the output of one task triggers the next. Many business processes are already workflows that people execute manually, step by step, every day.

# Example: Sales Lead Qualification Workflow (manual today)

<img width="887" height="415" alt="image" src="https://git.generalassemb.ly/user-attachments/assets/8502755d-610d-42f5-9e93-cedb97dc03a9" />


Every one of those steps follows a rule. Every one of those steps accesses a system. An agent can handle this entire workflow — and handle it consistently, at any volume, at any hour.

### Discussion Prompt (2 min)

> Think about a task you or your team repeats every week. Apply the four signals: Is it repetitive? Does it have clear rules? Is the data available? Are errors recoverable? Is it a strong candidate for automation?

---

## 2. Designing an Automated Workflow (20 min)

### The Five Elements of a Well-Designed Automation

Before writing a single line of configuration, a well-designed automation requires five elements to be clearly defined. Skipping any one of them produces a workflow that breaks in ways that are hard to diagnose.


<img width="924" height="258" alt="image" src="https://git.generalassemb.ly/user-attachments/assets/d1388d1f-0fdf-4669-ac95-43886fbda0ff" />


---

#### Element 1: The Trigger

**What starts the workflow?**

Every automated workflow begins with a trigger — an event that tells the agent it's time to act. Triggers can be:

| Trigger type | Example | Best for |
|---|---|---|
| **Time-based** | Every Monday at 8 AM | Weekly reports, scheduled check-ins, recurring summaries |
| **Event-based** | New row added to spreadsheet | Lead qualification, order processing, ticket routing |
| **Condition-based** | Customer hasn't responded in 48 hours | Follow-up emails, escalation triggers, SLA alerts |
| **Manual** | User clicks a button or sends a message | On-demand tasks, approval-triggered workflows |

**Why the trigger matters:** A workflow without a clear trigger runs at the wrong time, runs too often, or never runs at all. Define the trigger explicitly — don't leave it as "when it makes sense."

---

#### Element 2: The Steps

**What does the agent do, in what order?**

Map every step the agent needs to take, from trigger to final output. At each step, identify:

- What information does the agent need?
- Where does that information come from? (Which tool or data source?)
- What decision does the agent make?
- What action does it take?

```
Lead Qualification Workflow — Steps:

Trigger: New lead added to HubSpot CRM

Step 1: Retrieve lead details (name, company, role, source)
        Tool: HubSpot connector — read lead record

Step 2: Check if company is already a customer
        Tool: HubSpot connector — search existing accounts

Step 3: Enrich lead with company data
        Tool: LinkedIn or company data API

Step 4: Score lead against ICP criteria
        Logic: Company size > 50? Role = Director+? Industry in target list?
        Decision: Enterprise (all 3 match) / SMB (1-2 match) / Disqualify (0 match)

Step 5: Assign to the right sales rep
        Tool: HubSpot connector — update lead owner

Step 6: Send personalized welcome email
        Tool: Gmail or HubSpot email — send from template

Step 7: Log the qualification reasoning
        Tool: HubSpot connector — add note to lead record
```

**Principle:** Each step should do one thing. If a step seems to require too many decisions or too many tools, split it into two steps. Simpler steps are easier to test, easier to debug, and easier to improve.

---

#### Element 3: The Decision Logic

**How does the agent choose between options?**

Most workflows contain at least one decision point — a fork where the agent's next action depends on the result of a previous step. Decision logic must be explicit and testable.

```
Decision point in lead qualification:

IF company_size > 50 AND role_level = "Director or above" AND industry IN target_list:
  → Route to Enterprise Team
  → Assign rep from enterprise roster

ELSE IF company_size > 10 AND role_level IN ["Manager", "Director"]:
  → Route to SMB Team
  → Assign rep from SMB roster

ELSE:
  → Mark as "Nurture"
  → Add to long-term email sequence
  → No immediate rep assignment
```

**Common mistake:** leaving decision logic vague in the system prompt. *"Use your judgment to determine if the lead is a good fit"* is not decision logic — it is an invitation for inconsistency. Write the criteria explicitly.

---

#### Element 4: The Output

**What does the workflow produce?**

Define what a completed run of the workflow should produce — not just what the agent does, but what the end state looks like. This gives you something concrete to test against.

```
Expected outputs of Lead Qualification Workflow:
✅ Lead record updated with: qualification score, assigned rep, routing category
✅ Welcome email sent to lead
✅ Qualification reasoning note added to CRM record
✅ Summary notification sent to assigned rep via Slack
```

If any of these outputs is missing after a run, the workflow has failed — even if the agent didn't produce an error message.

---

#### Element 5: The Success Metric

**How will you know if the automation is working?**

Define at least one measurable metric before going live. Without a baseline measurement taken before automation, there is no way to know whether the automation delivered value.

```
Lead Qualification Workflow — Success Metrics:

Before automation:
  → Average time to qualify one lead: 12 minutes
  → Qualification errors per week (wrong routing): 3–4
  → Leads processed per week: 50

After automation (target):
  → Average time to qualify one lead: < 2 minutes
  → Qualification errors per week: < 1
  → Leads processed per week: same 50 (same volume, less human time)

Metric to track: Time saved per week + error rate reduction
```

---

### Putting It Together: The Workflow Design Template

Before configuring anything in a platform, fill in this template:

```
WORKFLOW NAME: [Name of the workflow]

TRIGGER:
  What starts it? [Time-based / Event-based / Condition-based / Manual]
  Specific trigger: [e.g., "New row added to 'Leads' tab in Google Sheet"]

STEPS:
  1. [Step name] → [Tool/data source] → [Output of this step]
  2. [Step name] → [Tool/data source] → [Output of this step]
  3. [Decision point] → [Criteria] → [Path A / Path B]
  ... continue for all steps

OUTPUTS:
  What should exist when the workflow completes successfully?
  1. [Output 1]
  2. [Output 2]

SUCCESS METRIC:
  Before: [Current state — time, volume, error rate]
  Target: [What you expect after automation]
  How to measure: [Where will you track this?]
```

This template is also the foundation for the lab exercise.

---

## 3. Evaluating Impact and Building Guardrails (15 min)

### Measuring What Changed

Automation without measurement is faith, not engineering. After deploying an automated workflow, the two most important things to track are:

**Efficiency:** Did the workflow actually save time and resources?

```
Simple efficiency calculation:

Before: 12 minutes × 50 leads/week = 600 minutes/week (10 hours)
After:  2 minutes × 50 leads/week = 100 minutes/week (agent handles 90%)
        Human review: 30 minutes/week (spot checks + edge cases)

Time saved: ~470 minutes/week (≈ 8 hours/week)
Equivalent: one full working day per week returned to the team
```

**Error rate:** Did the workflow produce fewer errors than the manual process?

```
Before automation: 3–4 routing errors per week
After automation (week 1): 1 routing error
After automation (week 4): 0 routing errors (criteria refined based on week 1 failure)
```

Track both metrics from the first week. If efficiency improves but error rate increases, the automation is creating a new problem. If error rate improves but efficiency is flat, the workflow has bottlenecks worth addressing.

---

### What "Failure" Looks Like in an Automated Workflow

In a manual process, a human notices when something goes wrong and corrects it in the moment. In an automated workflow, failures can go unnoticed for hours or days if no one is watching — and small failures can compound into larger ones.

Common failure modes in automated workflows:

<img width="926" height="381" alt="image" src="https://git.generalassemb.ly/user-attachments/assets/f13c6ccb-1bbb-4773-b3b5-84957fe1ebe1" />

---

### Guardrails: Building Safety Into the Workflow

A guardrail is any mechanism that limits the damage when something goes wrong. Good guardrails don't prevent the agent from acting — they ensure that when the agent acts incorrectly, the consequences are detected and contained quickly.

**Guardrail 1: Human review for edge cases**

Define what "normal" looks like for your workflow. Anything outside normal goes to a human.

```
Lead Qualification — edge case rule:
  IF the lead's role is "C-Suite" or "Founder"
  OR if the company has > 1,000 employees
  → Do not auto-assign. Flag for manual review by senior sales manager.
  
Reason: These are high-value leads where a wrong first impression is costly.
        The extra 5 minutes of human attention is worth it.
```

**Guardrail 2: Volume and rate limits**

Define a maximum number of actions the agent can take in a given time window. This prevents runaway automation from sending hundreds of emails or making thousands of CRM updates before anyone can intervene.

```
Email sending guardrail:
  Maximum 20 welcome emails per hour
  Maximum 100 welcome emails per day
  
  IF limit reached → pause workflow and notify team via Slack
```

**Guardrail 3: Confidence thresholds**

If the agent's decision logic produces a result with low confidence — because the data was incomplete, ambiguous, or unusual — require human approval before acting.

```
Scoring confidence rule:
  IF the lead's company data is incomplete (missing size or industry)
  → Score cannot be computed reliably
  → Route to "Needs Manual Review" queue
  → Do NOT auto-assign or send welcome email
```

**Guardrail 4: Fallback paths**

Define explicitly what happens at every failure point. A workflow with no fallback will stall silently when it hits an error.

```
Tool failure fallback:
  IF LinkedIn enrichment API returns an error:
    → Log the error
    → Proceed with qualification using only the data available in HubSpot
    → Add a note to the lead record: "Enrichment unavailable — score based on partial data"
    → Flag for manual review if the partial score is borderline

  IF HubSpot CRM is unreachable:
    → Log the failed trigger
    → Retry after 15 minutes (max 3 retries)
    → If still failing, send alert to ops team via Slack
    → Do NOT proceed — wait for system recovery
```

**Guardrail 5: Monitoring and alerts**

Someone needs to be watching the workflow — not constantly, but regularly. Define who receives alerts when the workflow fails, and what constitutes an alert.

```
Monitoring setup:
  Alert to Slack #automation-alerts if:
    → Workflow fails to complete 3 times in a row
    → Error rate exceeds 10% in a single run
    → No leads processed in 24 hours (potential trigger failure)
    → Volume spike: more than 2x the average weekly volume in one day

  Weekly review: team reviews success metrics every Monday morning
```

---

### The Guardrail Design Checklist

<img width="616" height="388" alt="image" src="https://git.generalassemb.ly/user-attachments/assets/e817b6e5-08a7-46ce-ab0a-9e4e9a348e2c" />


A workflow that passes this checklist is ready for supervised production deployment. One that fails more than two items should not go live with real data until the gaps are addressed.

---

## 4. Break (10 min)

---

## 5. Lab (60 min)

*Lab details in the Lab Guide document.*

---

## 6. Wrap-Up & Reflection (5 min)

### Key Takeaways
- Automate the Right Tasks — Repetitive, Rule-Based, Recoverable
- Design Before You Build — Trigger, Steps, Logic, Output, Metric
- Every Automation Needs a Fallback

---

## Resources

- [n8n — Workflow Automation Documentation](https://docs.n8n.io/)
- [Zapier — Automation Best Practices](https://zapier.com/blog/automation-best-practices/)
- [Relevance AI — Building Automated Workflows](https://relevanceai.com/docs)
- [Make (formerly Integromat) — Scenario Design Guide](https://www.make.com/en/help/scenarios)
- [Flowise — Agent Workflow Configuration](https://docs.flowiseai.com/)
- McKinsey Global Institute — *The Economic Potential of Generative AI* (2023) — automation productivity section
- NIST — *Principle of Least Privilege and Fail-Safe Defaults* (SP 800-53)
