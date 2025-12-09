# System Components

This document lists and describes all major components of the Gallery Délice automation ecosystem.

---

## 1. Messaging Layer

### **WhatsApp via Wassenger**
- Processes inbound messages
- Provides webhook format for media, text, metadata
- Handles message IDs, chat IDs, and contact identifiers

---

## 2. Orchestration Layer (n8n)

The core workflow runs on n8n and includes:

### **Nodes for Input Handling**
- Webhook trigger
- Expression-based field mapping
- Conditional filters (session, media type)

### **Processing Nodes**
- HTTP requests (media download)
- Audio transcription
- Image understanding
- JSON formatting
- Custom JavaScript transforms

### **Debounce System**
- Redis list operations
- Logic to batch messages
- Execution windows
- Cleanup handlers

---

## 3. Interpretation Layer (AI)

### **Ad Interpreter**
Transforms ambiguous ad-related messages using:
- GPT-4.1
- Text + Ad metadata

### **Global Classifier**
Assigns message meaning when ads are not involved:
- Intent classification
- Tone normalization
- Explicit restatement of user request

---

## 4. Context Preparation Layer

### **Product Catalog Source**
- Postgres table `CatalogoGalleryD`
- MCP SQL tool attached to agent

### **Dinner Packages Source**
- Supabase table `CenasGalleryDeliceCompleto`
- Filtered for “Paquete” entries

### **Aggregators**
- List of unique categories
- List of dinner packages with titles & prices

---

## 5. Agent Layer

### **Inventory Agent**
- Fetches inventory
- Retrieves dinner extras
- Uses Postgres chat memory

### **General Agent (“Paula”)**
Primary decision-maker with tools for:
- Order creation
- Order lookup
- Shipping notifications
- Session control
- Catalog browsing

### **Postgres Chat Memory**
Stores:
- Conversation turns
- Agent reasoning
- Persisted user context

---

## 6. Tooling Layer

### **Google Sheets Tools**
- Create order rows
- Fetch existing orders

### **Postgres SQL Tools**
- Query product catalog
- Query dinner extras

### **Supabase Tools**
- Read/modify session status

### **HTTP Tools**
- Notify internal team of shipping requests

---

## 7. Data Persistence Layer

### **Postgres**
- Chat memory  
- Product catalog  

### **Supabase**
- Session control  
- Dinner package lists  

### **Redis**
- Temporary message queues for debouncing  

### **Google Sheets**
- Live order collection  
- Critical bot behavior logging  

---

## 8. Output Layer

### **WhatsApp Response**
Sent from agent after tool execution.

### **Critical Bot Logging**
Automatically logs:
- User message
- Agent response
- Timestamp
- Workflow link
- Accuracy placeholders

---

## 9. Monitoring & Internal Operations

- Critical bot evaluates behavior
- Human team receives shipping alerts
- Sessions can be paused/resumed manually or automatically
