# High-Level Flow Diagram

This file visually outlines the complete flow of message processing in the Gallery Délice WhatsApp Automation System.  
The diagram reflects the exact logic implemented in n8n.

---

## 1. End-to-End Flow

```
┌──────────────────────────────────────────┐
│ WhatsApp Incoming Message (Wassenger) │
└──────────────────────────────────────────┘
│
▼
┌────────────────────────┐
│ Session Filtering │
│ - Ignore groups │
│ - Check Supabase status │
└────────────────────────┘
│
if status != continue → STOP
│
▼
┌────────────────────────┐
│ Media Processing │
│ - Audio → Transcription │
│ - Image → Description │
│ - Text → Direct │
└────────────────────────┘
│
▼
┌────────────────────────┐
│ Input Normalization │
│ Build unified payload │
└────────────────────────┘
│
▼
┌────────────────────────┐
│ Debounce (Redis) │
│ Batch messages in 15s │
└────────────────────────┘
│
▼
┌────────────────────────┐
│ Ad Detection │
│ If URL → Ad Interpreter │
│ Else → Global Classifier│
└────────────────────────┘
│
▼
┌────────────────────────┐
│ Context Preparation │
│ - Product catalog │
│ - Dinner packages │
└────────────────────────┘
│
▼
┌──────────────────────────┐
│ Agent Routing │
│ - Inventory Agent │
│ - General Agent “Paula” │
└──────────────────────────┘
│
▼
┌────────────────────────┐
│ Tool Execution │
│ - Create order │
│ - Query DB │
│ - Stop bot session │
│ - Notify shipping team │
└────────────────────────┘
│
▼
┌────────────────────────┐
│ Output │
│ - WhatsApp response │
│ - Logging to Sheets │
└────────────────────────┘
```

---

## 2. Flow Summary

| Stage | Purpose |
|-------|---------|
| Input → | Capture and classify message types |
| Normalize → | Convert into unified message structure |
| Debounce → | Batch multiple messages into one |
| Interpret → | Detect ads, understand intent |
| Context → | Load product/dinner data |
| Agent → | Decide response & actions |
| Tools → | Execute structured tasks |
| Output → | Log, respond, notify |

---

## 3. Execution Guarantees

- No duplicated responses  
- No interruption when human takes over  
- Full traceability via logs  
- AI decisions follow strict tool rules  

