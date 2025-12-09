# Multimedia Support: Text, Image, and Audio

This document explains how the automation system processes and standardizes different message types received from WhatsApp.  
All message formats — text, images, and audio — are transformed into a consistent, agent-ready structure.

---

## 1. Supported Message Types

The system fully supports the following WhatsApp message formats:

### **Text Messages**
- Processed directly with no conversion.
- Passed into the normalization layer as-is.

### **Image Messages**
- Downloaded using the Wassenger media URL.
- Interpreted by **GPT-4o-mini** to produce a human-readable description.

### **Audio Messages**
- Downloaded in `.ogg` or `.mp3` format.
- Transcribed via **OpenAI Audio Transcription** into text.

All message types ultimately produce **textual content**, which greatly simplifies downstream processing.

---

## 2. Image Processing Pipeline

1. The webhook receives an image message (`body.data.type = "image"`).
2. The file is downloaded via HTTP from Wassenger.
3. The image is submitted to **GPT-4o-mini**, which returns:
   - Object descriptions  
   - Quantities  
   - Visual characteristics  
   - Packaging / food items (especially relevant for bakery orders)

4. The resulting text is injected into the unified `message.text` field.

### **Purpose of Image Interpretation**
- Allow customers to order via photos.
- Enable intent detection based on product images.
- Provide meaningful context to AI agents.

---

## 3. Audio Processing Pipeline

1. The webhook receives an audio message (`body.data.type = "audio"`).
2. The file is downloaded using its Wassenger media URL.
3. It is fed into **OpenAI Whisper-style transcription**, producing:
   - Clean text  
   - Punctuation  
   - Proper segmentation  

4. The transcription becomes part of the `message.text` field.

### **Benefits**
- Customers can speak naturally.
- No additional logic is required for voice message handling.
- The rest of the workflow remains fully text-based.

---

## 4. Text Message Handling

- No conversion necessary.
- Passed directly into the Input Normalizer.
- Used as the base content for classification and context injection.

---

## 5. Standardization Goal

Regardless of the original format, the system guarantees that every agent receives:

message.text = interpreted text OR transcribed audio OR image description

This ensures:

- Consistent agent prompts  
- Simpler tool execution logic  
- Reliable logs and memory entries  
- Reduced branching complexity inside the workflow  

---

## 6. Summary

| Message Type | Processing Method | Final Output |
|--------------|------------------|--------------|
| Text | Direct passthrough | Clean text |
| Image | GPT-4o-mini visual interpretation | Image description text |
| Audio | OpenAI transcription | Transcribed text |

All messages converge into a **single text-based representation**, forming the foundation of the entire automation system.
