# AI Customer Support Agent (n8n)

An AI Agent-based customer support automation built with **n8n**. The agent receives customer messages via chat, reasons over the request using an LLM, looks up relevant data in Google Sheets, and can respond to customers directly by email through Gmail — with conversation memory for multi-turn context.

## 🎯 Project Goal

Traditional customer support workflows require a human to read every incoming message, look up account/order data, and write a reply. This project automates that loop with an **AI Agent** that:

- Understands the customer's request in natural language
- Decides which tool it needs (data lookup, email) to resolve the request
- Retrieves supporting information from a Google Sheet (e.g. order/customer records)
- Sends a response by email when needed
- Remembers prior messages in the same session for coherent, contextual replies

## 🛠️ Tech Stack

| Component | Tool |
|---|---|
| Workflow automation | [n8n](https://n8n.io/) (self-hosted) |
| LLM / reasoning | OpenAI Chat Model (via OpenAI API) |
| Conversation memory | n8n Simple Memory |
| Data lookup | Google Sheets API (OAuth2) |
| Email delivery | Gmail API (OAuth2) |
| Trigger | n8n Chat Trigger |

## 🔄 Workflow Architecture

```
[Chat Message Received]
          │
          ▼
      [AI Agent] ──── Chat Model ──→ OpenAI Chat Model
          │
          ├──── Memory ──→ Simple Memory (session-based conversation history)
          │
          └──── Tools ──┬──→ Gmail (Send a message)
                         └──→ Google Sheets (Get row(s) in sheet)
```

1. **Trigger** – The workflow starts when a new chat message is received.
2. **AI Agent** – The core reasoning node. It decides, based on the user's message, whether it needs to:
   - look up information in the connected Google Sheet, and/or
   - send an email reply via Gmail.
3. **Chat Model (OpenAI)** – Powers the agent's reasoning and natural language responses.
4. **Simple Memory** – Keeps track of the conversation per session, so the agent can handle follow-up questions.
5. **Tools**
   - **Google Sheets — Get row(s) in sheet**: used to fetch customer/order data on demand.
   - **Gmail — Send a message**: used to send an email response to the customer; recipient, subject, and body are all populated automatically by the model based on conversation context.

## ⚙️ Setup

1. Import `workflow.json` into your n8n instance (`Workflows → Import from File`).
2. Create the following credentials in n8n:
   - **OpenAI API** — your own API key.
   - **Google Sheets OAuth2** — via a Google Cloud project (Gmail API + Sheets API enabled, OAuth consent screen configured, redirect URI set to `http://localhost:5678/rest/oauth2-credential/callback` for local/self-hosted setups).
   - **Gmail OAuth2** — same Google Cloud project as above.
3. Connect a Google Sheet with your sample customer/order data to the **Get row(s) in sheet** node.
4. Activate/publish the workflow.
5. Test via the built-in **Chat** panel in n8n, or send requests to the workflow's webhook/chat endpoint.

## 📌 Notes

- This project was built as a hands-on exercise in AI Agent design with n8n, covering LLM tool-calling, external API integration (Gmail, Google Sheets), and conversational memory.
- Data used for testing is either synthetic or anonymized.

## 📷 Screenshots

*(Add workflow screenshots and example chat interactions here.)*

## 🔮 Possible Improvements

- Add a "Human in the Loop" approval step before sending emails.
- Add sentiment/urgency classification to route complex complaints to a human agent.
- Extend the tool set with a ticketing system (e.g. Zendesk, Notion database) for logging support requests.
