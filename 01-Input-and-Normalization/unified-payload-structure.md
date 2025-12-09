# Unified Payload Structure

This document defines the final structured payload that every AI agent in the system receives.  
Regardless of whether the user sends text, images, audio, or batches of messages, the Unified Payload ensures a consistent interface.

---

## 1. Purpose of the Unified Payload

Before reaching any agent (Classifier, Ad Interpreter, Inventory Agent, or Paula), every incoming message is transformed into a **strict, predictable JSON format**.

This guarantees:

- Stable prompts  
- Reliable tool execution  
- Cleaner memory entries  
- Zero ambiguity across message types  
- Smooth handling of debounced message batches  

The Unified Payload is the **contract** between the input pipeline and the AI agents.

---

## 2. Top-Level Structure Overview

The final payload contains:

```
{
"sessionId": "<string>",
"provider": "<string>",
"conversation": "<object>",
"sender": "<string>",
"message": { ... },
"timestamps": { ... },
"env": "<environment>"
}
```

Each field has a clear role in ensuring consistency and traceability.

---

## 3. Message Object Specification

The `message` field encapsulates all interpreted content:
```
message: {
id: "<unique message ID>",
type: "<text | image | audio>",
text: "<final normalized text>",
image: "<optional image description>",
audio: "<optional audio transcription>"
}
```

### **text**
Will always contain:

- The original text message, or  
- The transcription of an audio message, or  
- The interpretation of an image  

### **image**
Populated only when the incoming message is an image.

### **audio**
Populated only when the incoming message is audio.

---

## 4. Timestamp Structure
```
timestamps: {
received: "<ISO timestamp>",
processed: "<optional>",
debounced: "<optional>"
}
```

This enables debugging, traceability, and chronological ordering of merged messages.

---

## 5. Environment Field

The `env` field indicates whether the workflow is running in:

- `"dev"`
- `"prod"`
- `"local"`

This allows agents to behave differently if needed (logging, debugging, tool restrictions).

---

## 6. Why This Structure Works

### **Consistency**
Agents always receive the exact same structure, removing the need for conditional logic.

### **Scalability**
New agents or tools can plug in without modifying the input pipeline.

### **Memory Safety**
Postgres Chat Memory expects predictable fields.

### **Batch Safety**
Debounced batches merge seamlessly since every message already follows the strict schema.

### **Debugging**
Workflow logs show a clean, human-readable message object.

---

## 7. Unified Payload in the System Flow

The payload is produced **after**:

- Media processing  
- Audio transcription  
- Image interpretation  
- Normalization  

And **before**:

- Ad Interpreter  
- Global Classifier  
- Context Preparation  
- Agent routing  
- Final tool execution  

This makes it a central piece of the entire automation architecture.

---

## 8. Example Unified Payload
```
{
"sessionId": "5213334031857@c.us
",
"provider": "whatsapp",
"conversation": {},
"sender": "5213334031757@c.us
",
"message": {
"id": "AB12345CD",
"type": "audio",
"text": "Hello, I would like to place an order for a cheesecake.",
"audio": "Hello, I would like to place an order for a cheesecake.",
"image": null
},
"timestamps": {
"received": "2025-01-05T18:34:12.120Z"
},
"env": "prod"
}
```
---

## 9. Summary

The Unified Payload Structure:

- Normalizes all message types  
- Ensures agent compatibility  
- Enables clean prompt engineering  
- Supports batching and memory  
- Strengthens reliability across the entire system  

It is the backbone of every AI decision in the Gallery Délice automation workflow.


