# Debounce Logic

The Debounce System prevents the bot from responding prematurely when users send multiple messages in quick succession.  
Instead of treating each message separately, the system batches them into a single consolidated message.

---

## 1. Purpose of the Debounce System

WhatsApp users frequently send:

- Multiple short messages  
- Voice notes followed by text  
- Images followed by clarifications  
- Corrections or additional details  

If processed individually, the bot may respond incorrectly or interrupt the user.  
The debounce system ensures:

- More coherent conversation flow  
- Higher-quality AI interpretation  
- Reduced noise in memory and logs  
- Fewer API calls and lower compute costs  

---

## 2. How Debouncing Works

The Debounce System uses a **15-second window** (configurable) to collect all messages in a short burst.

### When a message arrives:

1. A **unique Redis list** is created for the session.
2. The message is appended to the list.
3. The system checks:
   - How long since the last message
   - Whether the message is a duplicate
4. If a threshold is reached (time or message position):
   - The entire batch is released for processing.
   - The Redis key is deleted to prevent accumulation.

---

## 3. Trigger Conditions

Debouncing stops and messages are forwarded when:

### **A. Window Timeout Reached**
If no new message arrives after 15 seconds.

### **B. Message ID Matches Previous ID**
Prevents the same event from being processed twice.

### **C. First Message of a New Interaction**
If the list was empty before this message.

---

## 4. Execution Flow Summary

```
Incoming message → Save to Redis → Check rules →
If batching → wait
Else → release messages → sort → merge → send to agent
```

---

## 5. Benefits

- More accurate AI interpretation  
- More natural conversational feel  
- Cleaner memory entries  
- Centralized message flow  
- Prevents “bot interrupting user” problems  

---

## 6. Example Use Case

**User message sequence:**

1. “Hola”
2. “quiero ordenar”
3. “un cheesecake”

Without debounce → bot might reply to “Hola”.

With debounce → bot replies to the full merged message:
```
Hola quiero ordenar un cheesecake
```

This dramatically improves accuracy and user experience.
