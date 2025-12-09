# Message Consolidation

Message Consolidation is the process of taking multiple debounced messages and merging them into a single coherent message for the AI agents.

---

## 1. Why Consolidation Matters

Users often send messages like:

```
"Hola"
"quiero ordenar"
"un cheesecake para mañana"
```

The correct bot response relies on understanding all three messages together.  
Consolidation ensures that the bot interprets the full intent.

---

## 2. Steps in the Consolidation Pipeline

### **1. Fetch Messages from Redis**
Using LRANGE, all pending messages for the session are retrieved.

### **2. Parse JSON Entries**
Each stored string is converted back into structured JSON.

### **3. Normalize Fields**
Each message is confirmed to follow the unified payload structure.

### **4. Sort Messages**
Sorting happens based on message timestamps to preserve the correct order.

### **5. Concatenate Text**
The `message.text` fields from all entries are merged into one:
```
"Hola. Quiero ordenar. Un cheesecake para mañana."
```

### **6. Create Final Consolidated Payload**
The system generates one structured object containing:

- Combined text  
- Original metadata  
- The latest message ID  
- Updated timestamps  

---

## 3. What Gets Merged?

| Field | Merge Method |
|-------|--------------|
| **message.text** | Concatenated |
| **message.image** | Included if any message provided one |
| **message.audio** | Included if any message provided one |
| **timestamps** | Uses earliest received timestamp |

---

## 4. What Does NOT Get Merged?

- Conversation IDs  
- Provider metadata  
- Non-user-side metadata  

These stay consistent across the batch.

---

## 5. Output of Consolidation

The AI receives a fully merged dataset that looks like a single user message, even if it originated from many.

Example:

```
"Hola quiero ordenar un cheesecake para mañana"
```

---

## 6. Benefits

- Dramatically improved intent detection  
- Cleaner, shorter memory logs  
- Less noise for classifiers  
- More human-like conversation behavior  

Message consolidation is a key pillar in building a reliable, natural-feeling WhatsApp automation experience.
