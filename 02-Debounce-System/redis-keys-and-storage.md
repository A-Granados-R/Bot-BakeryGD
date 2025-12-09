# Redis Keys and Storage

This document describes how the Debounce System uses Redis to temporarily store incoming messages before batching and consolidation.

---

## 1. Why Redis?

Redis is ideal for the debounce mechanism because it provides:

- Fast in-memory operations  
- Efficient list handling  
- Expiration support  
- Low latency  
- Reliability in high-throughput environments  

---

## 2. Redis Key Structure

Each conversation uses a **unique Redis key** derived from:
```
debounce:<provider>:<env>:<sessionId>
```
Example:
```
debounce:whatsapp:prod:5213334031507@c.us
```

This ensures isolation across:

- Platforms  
- Environments  
- Customers  
- Conversations  

---

## 3. Stored Data Format

Each entry in the Redis list contains the **raw normalized message**, stored as a JSON string:
```
{
"message": { ... },
"timestamps": { ... },
"sessionId": "...",
"sender": "...",
...
}
```

Redis stores these entries **in order**, ensuring chronological merging.

---

## 4. Operations Used

### **LPUSH / RPUSH**  
Append each new message to the list.

### **LRANGE**  
Retrieve complete message history during batching.

### **DEL**  
Delete the key once the batch is processed.

### **TTL**  
Expiration ensures no orphaned keys remain.

---

## 5. Expiration Management

Redis keys automatically expire after a configurable timeout (commonly 1–5 minutes).  
This ensures no abandoned conversations persist longer than necessary.

---

## 6. Safety Considerations

- Every message write is atomic.  
- Duplicate IDs are checked before processing.  
- Redis deletion happens *only* after successful consolidation.  

---

## 7. Summary

Redis is used as a lightweight, fast, and reliable short-term storage system that powers the entire debounce workflow.  
It ensures that user messages are collected and processed as a coherent batch before passing them forward.
