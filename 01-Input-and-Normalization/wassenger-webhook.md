# Wassenger Webhook

This document describes how incoming WhatsApp messages enter the system through the Wassenger webhook integration.

---

## 1. Webhook Trigger Overview

The system begins with a **Wassenger Webhook** that delivers message events directly to n8n.  
The webhook provides all message metadata required for processing, including:

- Sender information  
- Message ID  
- Chat ID  
- Timestamp  
- Message type  
- Text content  
- Media URLs (audio/image)  
- Link previews (for ads)

The workflow is triggered **only for new incoming messages** from clients.

---

## 2. Webhook Event Types

The automation supports the following Wassenger event types:

### **Text Messages**
Delivered with:
- `body.data.type = "text"`
- `body.data.text` field

### **Image Messages**
Delivered with:
- `body.data.type = "image"`
- Metadata containing the resource URL

### **Audio Messages**
Delivered with:
- `body.data.type = "audio"`
- URL for downloading the media file

### **URL Preview Payloads**
Used for advertisement interpretation:
- `body.data.linkPreview.canonicalUrl`

---

## 3. Message Identification

Each message contains:

- **`messageId`**: unique Wassenger message ID  
- **`chatId`**: conversation session ID  
- **`from`**: sender WhatsApp number  
- **`timestamp`**: UTC timestamp  

These values are essential for:
- Session filtering  
- Message deduplication  
- Debounce batching  
- Logging  

---

## 4. Filtering Self-Messages

The Webhook includes events for both sides of a conversation.  
The workflow filters out messages sent **from Gallery Délice** to avoid:

- Infinite loops  
- Unnecessary costs  
- Duplicate executions  

---

## 5. Summary

The Wassenger Webhook serves as the system’s gateway, reliably delivering all incoming WhatsApp messages with structured metadata that enables the automation pipeline to operate consistently and accurately.
