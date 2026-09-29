# 🎫 AI-Powered Support Ticket Classifier & Router

## 📌 Overview
A no-code automation workflow that automatically classifies incoming support tickets using **Claude (Anthropic)**, then routes them to the correct team based on urgency — all without human review. This project moves beyond simple text generation into **AI-driven decision-making**, where the automation acts on the model's output rather than just displaying it.

## 🎯 Problem Solved
Manually triaging support tickets — reading each one, deciding its category, judging urgency, and assigning it to the right team — is slow and inconsistent across different agents. This project solves that by:
- Automatically classifying every incoming ticket by category and priority
- Applying consistent, rule-based urgency judgment instead of subjective human calls
- Automatically splitting tickets into different workflow paths based on priority
- Laying the foundation for real-time routing to the correct team (Engineering, Billing, Product, Customer Success)

## 🛠️ Tools Used
- **n8n** – Workflow automation platform
- **Claude (Anthropic API)** – Generative AI model (claude-sonnet-5)
- **Google Sheets** – Data source (support tickets)
- **n8n IF Node** – Conditional logic for routing decisions

## ⚙️ Workflow
```
[Manual Trigger] 
      ↓
[Read support tickets from Google Sheets] 
      ↓
[Send each ticket to Claude, requesting a structured JSON classification]
      ↓
[IF Node checks whether the response contains "Urgent"]
      ↓
   ┌─────────────┴─────────────┐
   ↓ (True)                    ↓ (False)
Urgent tickets              Normal/Low tickets
(ready for priority routing) (ready for standard routing)
```

## 🧠 Prompt Design — Structured JSON Output
Unlike the previous projects, which asked for free-form text, this project required the model to output **structured, machine-readable data** so the automation could act on it programmatically.

Key prompt elements:
- **A defined role** (support ticket triage system)
- **A dynamic variable** for the ticket text (`{{ $json['ticket_text'] }}`)
- **A strict JSON schema** with a fixed set of allowed values for `category`, `priority`, and `routed_team`
- **An explicit "no extra text" instruction**, ensuring the response is valid, parseable JSON with nothing before or after it
- **A defined business rule** for what counts as "Urgent" (blocking multiple users, or involving money/security) — turning a subjective judgment into a consistent, repeatable classification

## 📊 Sample Output & Validation
Tested on 5 diverse tickets: a team-wide login outage, a minor UI customization request, a recurring billing overcharge, a broken export button, and a feature request. Results:
- All 5 tickets were classified correctly and consistently, matching human judgment
- The two genuinely urgent tickets (login outage, billing error) were correctly flagged `"priority": "Urgent"` and routed accordingly
- The IF Node successfully split the 5 tickets into 2 "Urgent" and 3 "Normal/Low" — exactly as expected

## 💡 Key Takeaways
- Prompting an LLM for **structured JSON output** (instead of free text) is what enables an automation to make decisions, not just display information
- Defining business rules explicitly in the prompt (what qualifies as "Urgent") produces far more consistent results than leaving judgment calls to the model
- Conditional logic (IF nodes) is what turns a simple "AI text generator" workflow into a true decision-making system — a key building block toward AI agents
- This same category → condition → route pattern generalizes to many real business use cases: lead scoring, content moderation, expense approval, etc.

## 🚀 Future Improvements
- Connect the "Urgent" path to a Slack/email alert for instant team notification
- Add a second IF layer to route by `routed_team` (Engineering vs Billing vs Product) into separate channels
- Write classification results back into the Google Sheet for a persistent ticket log
