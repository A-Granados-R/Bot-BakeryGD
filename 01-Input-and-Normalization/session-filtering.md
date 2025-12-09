# Session Filtering

This document explains how conversations are filtered and controlled through Supabase session management — including the manual override system used by the Gallery staff via Telegram.

---

## 1. Purpose of Session Filtering

Session filtering ensures that the bot only responds when the conversation is in **active mode**.  
This prevents:

- Automatic replies during human takeover  
- Infinite loops  
- Unwanted bot behavior  
- Conflicts between agent messages and staff messages  

The system supports **both automatic** and **manual** session control.

---

## 2. Supabase Session Table

Supabase stores session entries using:

- `sessionId`  
- `status` (values: `"continue"` or `"stop"`)  
- Audit timestamps  

When a session does not exist, it is created with:

status = "continue"

This allows the bot to treat all new customers as active conversations by default.

---

## 3. Filtering Logic in the Main WhatsApp Workflow

Before processing any message, the workflow checks:

1. If the message is from a group → **ignored**
2. If the phone number exists in the `comandos` table
3. If the session status is:

   - `"continue"` → the bot responds normally  
   - `"stop"` → the bot skips all AI agents and does not reply  

This ensures that the bot respects human intervention and avoids disruptions.

---

## 4. Manual Session Control via Telegram

Gallery Délice staff can manually pause or resume any session by sending a formatted command through a dedicated Telegram bot.

This feature is handled by a **separate n8n workflow**, referenced here:  
:contentReference[oaicite:0]{index=0}

### **How It Works**

1. Staff member sends a Telegram message such as:

Adrian, 3327215820 stop

or

Maria | 3312345678 | continue

2. The Telegram workflow processes the message:
   - The AI Agent extracts the **name**, **phone number**, and **desired status**.
   - The Code node formats the phone number into WhatsApp format (`521XXXXXXXXXX@c.us`).
   - The Switch node decides whether the command is `stop` or `continue`.

3. Supabase is updated accordingly:

### **When staff sends “stop”**

status = "stop"

→ Bot immediately stops replying to that session.

### **When staff sends “continue”**
→ Bot resumes full automation for that customer.

---

## 5. Why Manual Overrides Are Critical

The Telegram command workflow enables staff to:

- Take control of the conversation instantly  
- Prevent the bot from interrupting manual interactions  
- Resume automation whenever they finish  
- Avoid altering the main WhatsApp workflow  

This separation ensures reliability and operational safety.

---

## 6. Message Flow Summary

| Action | Trigger | Result |
|--------|---------|--------|
| Automatic handling | New WhatsApp message | Bot checks Supabase status |
| Staff sends “stop” | Telegram command | Bot pauses session |
| Staff sends “continue” | Telegram command | Bot resumes normal behavior |
| No status found | New customer | Status defaults to “continue” |

---

## 7. Safety Guarantees

- Bot will **never** respond when status = `"stop"`  
- Human staff always has priority  
- Staff commands are logged and auditable  
- AI cannot override session control without explicit instructions  

---

## 8. Summary

Session filtering operates on two layers:

### **Automatic layer (WhatsApp workflow)**
- Determines if the bot should respond  
- Creates new sessions  
- Enforces state rules  

### **Manual layer (Telegram workflow)**
- Lets staff pause or resume any conversation  
- Updates the session table in real time  
- Offers clean, simple commands for non-technical users  

Together, these systems ensure that the Gallery Délice automation is reliable, safe, and flexible.

