# System Architecture

This document provides a high-level architectural overview of the Gallery Délice WhatsApp Automation System.  
The system is built on **n8n**, **Supabase**, **Postgres**, **Google Sheets**, **Redis**, and **OpenAI Agents**, orchestrating a fully automated conversational workflow.

---

## 1. High-Level Architecture Diagram (Conceptual)

```
WhatsApp (Wassenger Webhook)
│
▼
Input Layer (n8n)

Session filtering

Media processing (audio/image)

Input normalization

Debounce system (Redis)
│
▼
Message Interpretation Layer

URL detection for ads

Ad Interpreter (OpenAI)

Global Classifier (OpenAI)
│
▼
Context Preparation Layer

Product catalog (Postgres)

Dinner packages (Supabase)

Aggregated metadata
│
▼
Agent Layer

Inventory Agent (tools: products, dinners)

General Agent “Paula” (tools: orders, shipping, session control)

Postgres chat memory
│
▼
Output Layer

Google Sheets order logging

WhatsApp responses
```
