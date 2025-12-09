# Input Normalizer

The Input Normalizer is responsible for transforming every incoming WhatsApp message into a clean, unified, and predictable structure.  
This ensures that all downstream components — including classifiers, interpreters, and agents — receive a standardized input format.

---

## 1. Purpose of the Input Normalizer

WhatsApp messages can arrive in different formats (text, image, audio) and with inconsistent metadata.  
The Input Normalizer guarantees:

- Consistent field names
- Sanitized and safe text
- Unified structure for all message types
- Support for batching via the debounce system
- Compatibility with Postgres Chat Memory

It acts as the **foundation** for the rest of the automation system.

---

## 2. Normalization Workflow

### **Step 1: Extract message metadata**
The normalizer collects:

- `sessionId`
- Sender number
- Provider data
- Message ID
- Timestamp
- Message type

### **Step 2: Insert processed text**
Depending on message type:

- Text → used directly  
- Audio → transcribed text  
- Image → GPT-generated description  

This content is injected into `message.text`.

### **Step 3: Include auxiliary fields**
The normalizer adds:

- `message.image` → when applicable  
- `message.audio` → transcriptions  
- `conversation` → passthrough metadata  
- `env` → current environment label (dev, prod, etc.)

---

## 3. Data Cleanup and Protection

The normalizer performs several safety checks:

### **Remove leading `=` characters**
This prevents Google Sheets from interpreting user messages as formulas.

### **Trim and clean whitespace**
Ensures agent prompts are formatted cleanly.

### **Normalize timestamps**
Ensures a consistent datetime format across all nodes.

### **Guarantee message presence**
If a message arrives without text (e.g., empty audio), a placeholder is inserted to avoid runtime errors.

---

## 4. Final Output Format

The final normalized payload sent to the debounce system and agents looks like:

```
{
"sessionId": "<string>",
"provider": "<string>",
"conversation": "<object>",
"sender": "<string>",
"message": {
"id": "<string>",
"type": "<text | image | audio>",
"text": "<interpreted message>",
"image": "<optional>",
"audio": "<optional>"
},
"timestamps": {
"received": "<ISO timestamp>"
},
"env": "<environment label>"
}
```

---

## 5. Why Normalization Happens Before Debouncing

The debounce system merges multiple consecutive messages into one.  
If the inputs were not normalized beforehand:

- Media messages would merge incorrectly  
- IDs could conflict  
- Agents would receive unpredictable formats  
- Batch interpretation would fail  

Normalization ensures that **every message in the batch aligns perfectly**, allowing the merge process to work reliably.

---

## 6. Benefits of the Normalizer Layer

| Benefit | Description |
|--------|-------------|
| **Predictability** | Every agent receives the exact same structure. |
| **Error Prevention** | Avoids malformed messages, undefined fields, or empty payloads. |
| **Cleaner Prompts** | Agents do not need conditional logic for media types. |
| **Easier Debugging** | Logs contain consistent, readable objects. |
| **Future-proofing** | New message types can be integrated with minimal changes. |

---

## 7. Summary

The Input Normalizer functions as a **stabilizing layer** that guarantees:

- Clean, standardized inputs  
- Fully interpreted message text  
- Media compatibility  
- Consistent metadata  
- Safe downstream processing  

It is one of the most important components in ensuring reliability and clarity within the entire WhatsApp automation system.
