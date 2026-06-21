# Khairiansyah_233510311_UAS_Prak_NLP

# 🗞️ News Summarizer Agent

> **UAS Natural Language Processing** — Sistem berbasis NLP/LLM yang menggunakan **LangChain**, **LangGraph**, dan **LangSmith** untuk mencari, meringkas, dan menganalisis berita terkini secara otomatis.

---

##  Deskripsi Proyek

**News Summarizer Agent** adalah sistem agen cerdas berbasis LLM yang mampu:
-  **Mencari** berita terkini dari internet secara otomatis
-  **Meringkas** konten berita menjadi informasi yang padat dan mudah dipahami
-  **Menganalisis** sentimen berita (positif / negatif / netral)
-  **Mengkategorikan** topik berita secara otomatis
-  **Memonitor** seluruh alur agent secara real-time via LangSmith

Proyek ini dibangun menggunakan tiga library wajib: **LangChain**, **LangGraph**, dan **LangSmith**, dengan memanfaatkan layanan yang **100% gratis**.

---

##  Arsitektur Sistem

```
┌─────────────────────────────────────────────────────────────────┐
│                     NEWS SUMMARIZER AGENT                       │
│                                                                 │
│   Input Topik                                                   │
│        │                                                        │
│   ┌────▼─────────────────────────────────────────────────┐     │
│   │              LangGraph State Machine                  │     │
│   │                                                       │     │
│   │  ┌─────────┐  ┌──────────┐  ┌──────────┐  ┌──────┐  │     │
│   │  │  Node 1  │→│  Node 2  │→│  Node 3  │→│Node 4│  │     │
│   │  │  Search  │  │Summarize │  │ Analyze  │  │Respond│ │     │
│   │  └─────────┘  └──────────┘  └──────────┘  └──────┘  │     │
│   │       │             │             │            │      │     │
│   │  DuckDuckGo    LangChain      LangChain     Format   │     │
│   │   Search       LLM Chain      LLM Chain     Output   │     │
│   └───────────────────────────────────────────────────────┘    │
│                                                                 │
│   [LangSmith — Monitoring & Tracing semua node secara real-time]│
└─────────────────────────────────────────────────────────────────┘
```

---

## 🔧 Peran Setiap Library

| Library | Versi | Peran dalam Proyek |
|---|---|---|
| **🦜 LangChain** | ≥0.3.0 | Membuat LLM chain dengan `ChatPromptTemplate` + `ChatGoogleGenerativeAI`. Digunakan di Node Summarize dan Node Analyze. |
| **🌐 LangGraph** | ≥0.2.0 | Membangun state machine 4-node yang mengatur alur: Search → Summarize → Analyze → Respond. Setiap node memiliki tanggung jawab spesifik. |
| **🔭 LangSmith** | ≥0.1.0 | Melakukan tracing & monitoring otomatis pada setiap node agent. Dapat diakses di dashboard smith.langchain.com. |

---

## 💡 Fitur Unggulan

- ✅ **100% Gratis** — Menggunakan Gemini Flash (Google AI Studio) + DuckDuckGo Search
- ✅ **Multi-bahasa** — Mencari berita dalam Bahasa Indonesia dan Inggris
- ✅ **Real-time Tracing** — Setiap langkah agent tercatat di LangSmith dashboard
- ✅ **UI Interaktif** — Dibangun dengan Streamlit, lengkap dengan tab hasil
- ✅ **Analisis Sentimen** — Otomatis mendeteksi sentimen dan kategori berita
- ✅ **4-Node Pipeline** — Arsitektur modular dengan LangGraph StateGraph

---

## 📁 Struktur Proyek

```
news-summarizer-agent/
│
├── app.py                  #   Streamlit UI utama
├── requirements.txt        #  Dependensi Python
├── .env.example            #  Template environment variables
├── README.md               #  Dokumentasi ini
│
└── src/
    ├── __init__.py
    ├── agent.py            #  LangGraph agent & semua node
    └── utils.py            #   Fungsi helper
```

---

## 🚀 Cara Menjalankan

### 1. Clone Repository

```bash
git clone https://github.com/USERNAME/news-summarizer-agent.git
cd news-summarizer-agent
```

### 2. Buat Virtual Environment

```bash
python -m venv venv

# Windows
venv\Scripts\activate

# Mac/Linux
source venv/bin/activate
```

### 3. Install Dependensi

```bash
pip install -r requirements.txt
```

### 4. Dapatkan API Key (Gratis)

#### 🔑 Google Gemini API Key (WAJIB)
1. Buka [https://aistudio.google.com/app/apikey](https://aistudio.google.com/app/apikey)
2. Login dengan akun Google
3. Klik **"Create API Key"**
4. Salin API key yang dihasilkan

#### 🔑 LangSmith API Key (Opsional, untuk monitoring)
1. Daftar di [https://smith.langchain.com](https://smith.langchain.com) (tanpa kartu kredit)
2. Masuk ke **Settings → API Keys**
3. Klik **"Create API Key"**

### 5. Konfigurasi Environment

```bash
# Salin file template
cp .env.example .env

# Edit file .env dan masukkan API key kamu
nano .env   # atau buka dengan text editor
```

Isi file `.env`:
```env
GOOGLE_API_KEY=your_gemini_api_key_here
LANGCHAIN_API_KEY=your_langsmith_api_key_here
LANGCHAIN_TRACING_V2=true
LANGCHAIN_PROJECT=news-summarizer-agent
```

> **💡 Alternatif:** API key bisa juga dimasukkan langsung di sidebar aplikasi Streamlit tanpa perlu file `.env`.

### 6. Jalankan Aplikasi

```bash
streamlit run app.py
```

Buka browser dan akses: **http://localhost:8501**

---

## 🖼️ Screenshot

### Halaman Utama
![Screenshot halaman utama](assets/screenshot_home.png)

### Hasil Ringkasan Berita
![Screenshot hasil ringkasan](assets/screenshot_result.png)

### LangSmith Tracing Dashboard
![Screenshot LangSmith](assets/screenshot_langsmith.png)

---

## 📊 Alur Kerja Agent (LangGraph)

### Node 1 — Search
```python
def node_search(state: NewsAgentState) -> NewsAgentState:
    search_tool = DuckDuckGoSearchRun()
    results = search_tool.run(f"berita terkini {state['topic']}")
    return {**state, "raw_news": results}
```

### Node 2 — Summarize (LangChain Chain)
```python
prompt = ChatPromptTemplate.from_messages([...])
chain = prompt | llm
summary = chain.invoke({"topic": ..., "raw_news": ...})
```

### Node 3 — Analyze (LangChain Chain)
```python
# Analisis sentimen & kategori dalam format JSON
response = chain.invoke({"summary": ...})
data = json.loads(response.content)
```

### Node 4 — Respond
```python
# Format semua hasil menjadi respons final
final_response = f"Ringkasan: {summary}\nSentimen: {sentiment}"
```

### LangGraph State Machine
```python
workflow = StateGraph(NewsAgentState)
workflow.add_node("search",    node_search)
workflow.add_node("summarize", node_summarize)
workflow.add_node("analyze",   node_analyze)
workflow.add_node("respond",   node_respond)
workflow.set_entry_point("search")
workflow.add_edge("search",    "summarize")
workflow.add_edge("summarize", "analyze")
workflow.add_edge("analyze",   "respond")
workflow.add_edge("respond",   END)
agent = workflow.compile()
```

---

## 🛠️ Tech Stack

| Teknologi | Kegunaan |
|---|---|
| Python 3.10+ | Bahasa pemrograman utama |
| LangChain | LLM orchestration & prompt templates |
| LangGraph | Agent state machine |
| LangSmith | Monitoring & tracing |
| Google Gemini Flash | LLM gratis via Google AI Studio |
| DuckDuckGo Search | Tool pencarian berita (gratis, tanpa API key) |
| Streamlit | UI web interaktif |

---

## 📚 Referensi

- [LangChain Documentation](https://python.langchain.com/docs/)
- [LangGraph Documentation](https://langchain-ai.github.io/langgraph/)
- [LangSmith Documentation](https://docs.smith.langchain.com/)
- [Google AI Studio (Gemini)](https://aistudio.google.com/)

---

## 👨‍💻 Dibuat Oleh

| Nama | NPM | Mata Kuliah |
|---|---|---|
| Khairiansyah| 233510311 | Praktikum Natural Language Processing |

---


