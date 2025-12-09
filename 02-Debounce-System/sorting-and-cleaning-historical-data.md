# Sorting and Cleaning Historical Data

This document describes how the debounce system organizes and sanitizes batched messages before sending them to the AI agents.

---

## 1. Sorting Logic

Messages retrieved from Redis may not always be in perfect chronological order.  
The workflow sorts them based on:

```
message.timestamps.received
```

Sorting ensures:

- Accurate sentence flow  
- Correct interpretation by AI models  
- Reliable memory storage  
- Clean concatenation results  

---

## 2. Cleaning Operations

Before merging the sorted messages, the following cleanups are applied:

### **1. Remove System Noise**
Any stray characters, incorrect values, or empty fields are cleaned.

### **2. Strip Leading '='**
Prevents Google Sheets from treating messages as formulas.

### **3. Remove Null Fields**
Empty `image` or `audio` fields are excluded unless relevant.

### **4. Standardize Whitespace**
Ensures clean, readable concatenated messages.

### **5. Validate Unified Structure**
Each message is checked against the Unified Payload schema.

---

## 3. Timestamp Integrity

The consolidated payload uses:

- **Earliest** timestamp → `received`
- **Most recent** timestamp → `debounced`

This preserves the chronological truth of the interaction.

---

## 4. Optional Enhancements

The sorting layer supports:

- Deduplication of identical messages  
- Filtering out system-generated ghosts  
- Constraints to ensure messages do not exceed configured limits  

---

## 5. Final Output

The cleaning and sorting process ensures that the final text delivered to the agent is:

- Ordered  
- Clean  
- Structurally sound  
- Free of noise  
- Faithful to the user's original intent  

---

## 6. Summary

Sorting and cleaning transforms raw debounced messages into a coherent input that AI agents can reliably interpret.  
This layer ensures that the automation remains stable, accurate, and natural across all message types and batching scenarios.
