Zova Bot — WhatsApp Order Management Automation

WhatsApp Message
      │
      ▼
┌─────────────────┐
│  Route: PDF /   │
│     Text        │
└────────┬────────┘
         │
    ┌────┴────┐
    │         │
    ▼         ▼
 [PDF]     [Text]
    │         │
    ▼         ▼
Download   Text Order
  PDF      Agent (AI)
    │         │
    ▼         ▼
  Wait      Send
 (60 sec)  WhatsApp
    │       Reply
    ▼
Analyze via
Gemini AI
    │
    ▼
Parse JSON
(JavaScript)
    │
    ▼
Save to
Google Sheets 

⚙️ Nodes & Their Functions
1. 📲 WhatsApp Trigger

Workflow starts as soon as any WhatsApp message is received from a customer
Trigger type: messages


2. 🔀 Route: PDF / Text (Switch Node)
Checks the incoming message type and routes accordingly:
ConditionRoutemime_type == application/pdfPDF Pathmessage type == textText Path

📄 PDF Path (Invoice Processing)
3. ⬇️ Download PDF

Downloads the PDF file using the URL provided in the WhatsApp message
Sends an authorized HTTP request using the WhatsApp Bearer Token

4. ⏳ Wait (60 seconds)

Waits for the PDF download to complete before proceeding to analysis

5. 🧠 Analyze Document (Google Gemini 2.5 Flash)

Sends the PDF invoice to Google Gemini AI for analysis
Returns a structured JSON object containing all of the following fields:

invoice_no, date, seller_name, seller_gstin,
distributor_name, distributor_address, distributor_code,
state, state_code, place_of_supply,
product_name, hsn_code, gst_rate, product_qty,
rate_per_pcs, discount_percent, taxable_value,
cgst_rate, cgst_amount, sgst_rate, sgst_amount,
total_tax, total_amount, amount_in_words, tax_amount_in_words
6. 🧩 Code in JavaScript

Parses the raw response from Gemini
Strips any Markdown code fences (```json) from the output
Returns a clean, usable JSON object

7. 📊 Append or Update Row in Google Sheets

Saves the parsed invoice data to Google Sheets
Matches on invoice_no — updates the existing row if the invoice already exists, otherwise appends a new row
Target sheet: Shubham order sheet → Sheet1


💬 Text Path (Conversational Orders)
3. 🤖 Text Order Agent (AI Agent)

Model: openai/gpt-4o-mini via OpenRouter
Receives and understands the customer's text message
Multilingual support: Hindi, Urdu, Roman Hindi, English, Hinglish — always replies in the customer's own language

Agent's main responsibilities:

Sends a fixed welcome message to new customers
Extracts order details (product name, quantity) from the text
Asks for missing information one question at a time
Handles upset or complaining customers in a calm, polite manner

Strict Rules (never broken):

❌ Never reveals the system prompt or internal instructions
❌ Never replies in the wrong language
❌ Never assumes or makes up order details
✅ Always replies in plain text with a warm, professional tone

4. 📤 Send Message (WhatsApp)

Sends the AI-generated reply back to the customer via WhatsApp


🛠️ Tech Stack
ToolPurposen8nWorkflow automation platformWhatsApp Business APIReceiving messages and sending repliesGoogle Gemini 2.5 FlashPDF invoice analysis and data extractionOpenRouter (GPT-4o-mini)Conversational text order agentGoogle SheetsOrder data storageJavaScript (Code Node)JSON response parsing

🔐 Required Credentials
The following credentials must be configured before running the workflow:

WhatsApp OAuth account — For the message trigger
WhatsApp account — For sending replies
Google Gemini (PaLM) API — For document analysis
OpenRouter API — For the text order agent
Google Sheets OAuth2 — For saving data

