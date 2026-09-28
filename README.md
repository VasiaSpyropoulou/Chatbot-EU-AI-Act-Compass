# Chatbot-EU-AI-Act-Compass
A domain-specific RAG chatbot that helps professionals understand their obligations under the EU AI Act, grounded in the actual regulation text, the 2026 Digital Omnibus amendments, and official European Commission guidance. Built with LlamaIndex, Groq LLMs, HuggingFace embeddings, and a Gradio web interface.
<p align="center">
  <img src="logo.png" width="200" alt="AI Act Compass Logo">
</p>

<h1 align="center">AI Act Compass</h1>

<p align="center">
  <em>An AI-powered compliance advisor for the EU AI Act</em>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.10+-blue?logo=python" alt="Python">
  <img src="https://img.shields.io/badge/LlamaIndex-RAG-orange" alt="LlamaIndex">
  <img src="https://img.shields.io/badge/Groq-LLM-green" alt="Groq">
  <img src="https://img.shields.io/badge/Gradio-UI-yellow" alt="Gradio">
</p>

---

## **The Problem**

The EU AI Act is the world's first comprehensive AI regulation - over 400 pages of legal text, plus amendments, annexes, and implementation timelines. If you're building or deploying AI in Europe, you need to know what applies to you. But reading the full regulation isn't realistic for most teams.

**AI Act Compass** is a chatbot that reads the law so you don't have to. Describe your AI product in plain English, and it tells you whether you're high-risk, what your obligations are, and which specific Articles apply - with sources.

## **Who Is It For**

This tool is designed for anyone who needs to understand their position under the EU AI Act without spending weeks on legal research:

- **Startup founders & CTOs** evaluating whether their AI product triggers high-risk obligations
- **Product managers** planning AI features targeting EU users
- **Compliance & legal teams** mapping regulatory requirements to technical implementations
- **Marketing teams** using AI-generated content - ads, images, copy - that may require transparency disclosures
- **Data Protection Officers** aligning AI governance with existing GDPR frameworks
- **Investors & VCs** conducting due diligence on AI startups entering the EU market
- **Consultants** advising clients on AI strategy and regulatory readiness

## **What It Can Do**

The chatbot provides structured, actionable guidance:

- **Risk classification** - determines if your AI system is Unacceptable, High-Risk, Limited Risk, or Minimal Risk
- **Obligation mapping** - lists the specific duties that apply to your product, with bullet points
- **Article citations** - references the exact Articles and Annexes from the regulation
- **Deadline awareness** - includes compliance timelines, updated with the 2026 Digital Omnibus amendments
- **Conversational memory** - remembers context across follow-up questions, so you can explore a scenario step by step
- **Built-in disclaimer** - every response states that this is not legal advice

## **Demo**

<p align="center">
  <img src="screenshot.png" width="800" alt="AI Act Compass Demo">
</p>

## **A Note on Production Readiness**

This is a proof-of-concept built during a 4-day bootcamp sprint. It demonstrates the architecture and the potential, but **deploying a legal advisory tool in production would require significantly more work**:

- **Legal review** - every response pattern would need validation by qualified legal professionals
- **Comprehensive testing** - systematic evaluation against known edge cases and ambiguous classifications
- **Confabulation safeguards** - additional layers to detect when the model generates information not grounded in the source documents
- **Document coverage** - the current knowledge base covers the core regulation and amendments, but would benefit from delegated acts, national implementation guides, and case law as it develops
- **User guardrails** - clearer boundaries around what the tool can and cannot answer, with escalation paths to human experts

That said, the underlying architecture - RAG over authoritative legal documents with domain-specific prompting - is a pattern that scales. The same approach could power compliance tools for GDPR, the Digital Services Act, sector-specific regulations, or internal policy navigation for large organisations.

**If you're a business looking for a custom AI compliance tool, or any domain-specific AI assistant built on your own documents, this is the kind of solution I build. [Get in touch.](mailto:[vasiaspyropoulou@gmail.com])**

## **Technical Architecture**

### **How It Works**

| Step | Component | What It Does |
|------|-----------|-------------|
| 1. **Document loading** | SimpleDirectoryReader + pypdf | Loads EU AI Act PDFs and text files into the pipeline |
| 2. **Chunking** | SentenceSplitter (800 tokens, 150 overlap) | Splits documents into searchable pieces, preserving legal cross-references |
| 3. **Embedding** | HuggingFace all-MiniLM-L6-v2 (384 dimensions) | Converts text chunks into numerical vectors for semantic search |
| 4. **Indexing** | VectorStoreIndex | Stores all chunk vectors for fast similarity lookup |
| 5. **Retrieval** | Retriever (top-k=3) | Finds the 3 most relevant chunks for each user question |
| 6. **Generation** | Groq LLM (openai/gpt-oss-120b) | Reads the retrieved context + conversation history and generates the answer |
| 7. **Memory** | ChatMemoryBuffer (3000 tokens) | Maintains conversation context across follow-up questions |
| 8. **Interface** | Gradio | Web-based chat UI with example questions and settings |

### **Key Decisions Shaped by the Legal Domain**

| Decision | Why |
|----------|-----|
| **RAG over fine-tuning** | Legislation changes - the Digital Omnibus amended the AI Act in July 2026. With RAG, updating means replacing a PDF. With fine-tuning, it means retraining the model. |
| **Temperature 0.01** | Legal advice demands consistency. The same question should produce the same answer every time. Near-zero randomness prevents creative but unreliable responses. |
| **Chunk size 800 with 150 overlap** | Legal articles frequently cross-reference each other ("systems listed in Annex III"). Generous overlap preserves these connections across chunk boundaries. |
| **System prompt with caution rules** | The model is instructed to say "I don't know" rather than guess, and to always include a not-legal-advice disclaimer. In compliance, a wrong answer is worse than no answer. |
| **Compact response mode** | Minimises API calls per query (1 instead of 2-3 with tree_summarize), reducing latency and rate-limit pressure on the free tier. |

### **Data Sources**

| Source | Description | Pages |
|--------|-------------|-------|
| EU AI Act | Regulation 2024/1689 - full text from EUR-Lex | ~144 |
| Digital Omnibus on AI | Regulation 2026/1744 - 2026 amendments | ~30 |
| Navigating the AI Act | European Commission official FAQ | ~6 |
| Implementation Timeline | Compliance deadlines and milestones | ~6 |
| Service Desk FAQs | Web-scraped from the EU AI Act Service Desk (6 pages) | ~16 |

### **Stack**

| Component | Technology |
|-----------|-----------|
| **LLM** | Groq API (openai/gpt-oss-120b) |
| **Embeddings** | HuggingFace sentence-transformers/all-MiniLM-L6-v2 |
| **RAG framework** | LlamaIndex |
| **Web scraping** | BeautifulSoup |
| **UI** | Gradio |
| **Environment** | Jupyter / Google Colab |

## **Setup**

### **Prerequisites**

| Requirement | Details |
|-------------|---------|
| **Python** | 3.10 or higher |
| **Groq API key** | Free account at [console.groq.com](https://console.groq.com/) → API Keys → Create |
| **HuggingFace token** | Free account at [huggingface.co](https://huggingface.co/) → Edit Profile → Access Tokens |

### **Getting Started**

The full implementation, from installation to running the chatbot, lives in a single notebook: [`eu_ai_act_navigator.ipynb`](eu_ai_act_navigator.ipynb). Open it and follow the cells in order - each section is documented with explanations and instructions. The notebook covers dependency installation, API key configuration, document loading, index building, and launching the Gradio web interface.

It runs either locally (Jupyter) or on Google Colab.

## **Future Extensions**

| Feature | Description |
|---------|-------------|
| **URL Scanner** | Paste a website URL - the tool scrapes it and checks for proper AI disclosures |
| **Multimodal Analysis** | Upload ad screenshots for automated transparency compliance checks |
| **Compliance Checklist** | Export a structured PDF checklist based on the conversation |
| **Risk Scoring** | Interactive questionnaire with weighted risk classification |
| **Multi-language** | German, Greek, French and other EU languages |
| **Live Updates** | Automated pipeline to pull new guidance from the European Commission |

## **License**

This project is for educational purposes. The EU AI Act text is public domain (EUR-Lex). This tool does not constitute legal advice.

---

<p align="center">
  Built by <b>Vasia Spyropoulou</b> - WBS Coding School Data Science Bootcamp 2026
</p>
