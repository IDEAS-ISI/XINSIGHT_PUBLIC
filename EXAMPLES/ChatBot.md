# Using the Data Assistant Chatbot in IDEAS XInsight

## Overview

The **Data Assistant** is a floating chat widget accessible from every page of IDEAS XInsight. It uses a locally-running GGUF language model to answer questions in three modes:

- **SQL queries** — questions about real values in your dataset (e.g. "What is the total sales?"). The assistant generates DuckDB SQL, executes it against your data, and returns a plain-English answer backed by actual query results.
- **Dataset chat** — conceptual questions about the loaded dataset (e.g. "Is this dataset suitable for regression?"). Answered from the dataset schema, no query required.
- **General chat** — questions about statistics, machine learning, SQL, Python, data visualisation, or AI, with no dataset required.

> **You do not need a dataset loaded to start chatting.** A dataset is only required if you want to query or analyse real data values.

---

## Prerequisites

Before using the Data Assistant, you need:

1. **A configured GGUF model** — required for any response. See [Step 1: Configure the Model in Settings](#step-1-configure-the-model-in-settings) below.
2. **An ingested dataset** — required only for SQL queries and dataset-specific questions. General questions work without one.

---

## Step 1: Configure the Model in Settings

Open **Settings** from the left sidebar. Settings has two tabs: **💻 System** and **🤖 LLM**.

### System Tab

The System tab shows a live hardware overview of the machine running XInsight:

- **System RAM** — available and total RAM, plus percentage used. The assistant loads the GGUF model into RAM; ensure you have enough free memory before loading a large model.
- **CPU** — physical cores, logical threads, and current load.
- **Storage** — free and total disk space.
- **GPU Detection** — if a supported GPU is detected, XInsight can offload model layers to it for faster inference. If no GPU is found, the model runs in CPU-only mode and a warning is shown.

<img width="1920" height="1032" alt="alt text" src="https://github.com/user-attachments/assets/93048443-7e4a-44ae-8efb-05ed5f67dbb7">
### LLM Tab

The LLM tab is where you configure the language model and its inference parameters.

#### Model Path

In the **Model file path (.gguf)** field, type the full path to your GGUF model file, or click **📁 Browse File** to open a file picker and navigate to the file.

**Example model used in this tutorial:**
```
Qwen3-4B-Q4_K_M.gguf
```

Once a valid path is entered, XInsight shows the file size and an expandable **🔍 Inspection: GGUF Model Details** section. This reads metadata directly from the model file header:

| Field | What it means |
|---|---|
| Architecture | Model family (e.g. `Qwen2`) |
| Max Native Context | The maximum context length the model natively supports |
| Layer Blocks | Number of transformer layers |
| Embedding Length | Hidden dimension size |
| Tokenizer | Tokenizer type (e.g. `BPE`) |
| Tensors | Total number of weight tensors |

<img width="1920" height="1032" alt="alt text" src="https://github.com/user-attachments/assets/c592a903-6b01-4f74-aee2-2c206eec7a37">
#### Model Display Name

The **Model display name** field sets the label shown in the chat widget header. If left blank, XInsight derives a name from the filename automatically (e.g. `Qwen3 4B Q4 K M`).

#### Hardware & Memory Settings

| Setting | Description | Default |
|---|---|---|
| **Context Window (n_ctx)** | Maximum tokens the model keeps in memory at once. Higher values let the model consider more context but use more RAM. | 2048 |
| **GPU Offload Layers (n_gpu_layers)** | Number of model layers to offload to the GPU. Set to `-1` to offload all layers; `0` for CPU-only mode. | -1 |
| **CPU Threads (n_threads)** | Number of CPU threads used for inference. | 4 |

> **Tip:** If you see out-of-memory errors, reduce `n_ctx` (e.g. from 2048 to 1024) or use a more heavily quantized model.

#### Inference Sampling Parameters

These settings control how the model generates text:

| Setting | Description | Default |
|---|---|---|
| **Temperature** | Controls output randomness. `0.0` is fully deterministic; higher values produce more varied output. | 0.2 |
| **Top-P** | Nucleus sampling threshold. The model only considers tokens whose cumulative probability reaches this value. | 0.95 |
| **Top-K** | Limits each generation step to the K most likely tokens. | 40 |
| **Repeat Penalty** | Penalises the model for repeating tokens. `1.0` means no penalty. | 1.1 |

#### SQL Output Toggle

Check **Show SQL Query & Output expander in chat responses** if you want to see the generated SQL and a preview of the result table beneath each data answer. This is off by default.

#### Saving

Click **💾 Save Settings**. The model loads automatically — you do not need to restart the app.

<img width="1920" height="1032" alt="alt text" src="https://github.com/user-attachments/assets/ed36ca39-9031-4c85-bc7a-c9fefde187b1">
---

## Step 2: Open the Data Assistant

The **💬 Ask Data Assistant** button is fixed in the **bottom-right corner** of every page in XInsight. Click it to open the chat popover.

<img width="1920" height="1032" alt="alt text" src="https://github.com/user-attachments/assets/aa600cfc-e353-4b5c-abf9-58d375d7c280">

The popover opens above the button and contains the following from top to bottom:

- **Model indicator** — shows the active model name and context window size:
  ```
  Model: Qwen3 4B Q4 K M
  Context window: 2,048 tokens
  ```
  If no model is configured, it shows *"No model loaded"* instead.

- **📋 Schema Context expander** — visible only when a dataset is selected. Expand it to see a column-level profile of the dataset (column name, DB type, semantic type, and a data summary). This is the context the assistant uses when answering data questions.

- **Chat history area** — a scrollable window showing the conversation. The view auto-scrolls to the latest exchange after each response.

- **Suggestion chips** — before you have asked anything, clickable starter questions appear under the label "Try asking:". See [Starter Suggestions](#starter-suggestions) below.

- **Chat input box** — type your question and press Enter to send.

- **Export Chat** and **Clear Chat** buttons at the bottom.

<img width="1920" height="1032" alt="alt text" src="https://github.com/user-attachments/assets/3e4c62d8-1a87-48d8-afa3-fe2a5f80b854">
---

## Step 3: Using the Chat

### Starter Suggestions

Before you type your first message, the assistant shows up to **4 clickable suggestion chips** under "Try asking:".

- **With a dataset selected:** Suggestions are generated from the dataset's actual column names and types. The first chip is always **"Describe this dataset."** Suggestions are cached after the first generation, so they appear instantly on subsequent visits to the same dataset.
- **Without a dataset:** A fixed set of general questions about statistics, ML, SQL, and data visualisation is shown.

Click any chip to send that question immediately — no need to type.

### General Chat (No Dataset Required)

Without a dataset loaded, the chat input placeholder reads:

> *Ask me anything about statistics, SQL, ML, Python, or AI...*

The assistant can answer general questions on these topics:

- Statistics
- Machine Learning
- SQL
- Python
- Data Visualisation
- Artificial Intelligence

**Example questions:**
- "What is factor analysis?"
- "When should I use PCA instead of factor analysis?"
- "What's the difference between correlation and causation?"
- "How do I choose the right chart type for categorical data?"

<img width="1920" height="1032" alt="alt text" src="https://github.com/user-attachments/assets/552dafcb-90af-40af-b407-21a6acb2d9bf">
### Dataset Schema Chat

When a dataset is loaded, you can ask conceptual questions about it — questions that do not require running a query:

**Example questions:**
- "Describe this dataset."
- "What columns does this dataset have?"
- "Is this dataset suitable for regression analysis?"
- "What does the `discount` column represent?"

The assistant answers using the column schema and semantic type information, without executing SQL.

### SQL Queries

For questions about actual data values, the assistant generates a DuckDB SQL query, executes it against your dataset, and returns a plain-English answer.

**Example questions:**
- "How many rows are in this dataset?"
- "What is the total sales?"
- "Show the top 5 categories by revenue."
- "What is the average discount?"

The assistant attempts to generate correct SQL on the first try. If the query fails, it makes one automatic repair attempt before responding that it could not answer.

> **Note:** Only SELECT queries are permitted. The assistant will never execute INSERT, UPDATE, DELETE, DROP, or any data-modifying SQL.

<img width="1920" height="1032" alt="alt text" src="https://github.com/user-attachments/assets/24161721-a4c6-4516-a29d-f64d19e21c97">

### Switching Datasets

You can switch the active dataset at any time without losing your conversation. The chat history continues in a single thread. When the dataset changes, the assistant inserts an inline notice:

> 🔄 *I'm now connected to **new_dataset.csv**. You can continue asking questions — I'll answer based on this dataset.*

If no dataset is selected:

> 🔄 *No dataset is selected anymore. I can still help with general questions, or select a dataset to query it directly.*

---

## Step 4: Viewing Results

### Chat Bubbles

- **Your messages** appear on the right in a blue bubble.
- **Assistant responses** appear on the left in a light gray bubble. Markdown formatting is rendered — bold text, bullet lists, numbered lists, code blocks, and tables all display correctly.

### Response Time

A small gray timestamp (e.g. `2.4s`) appears beneath each assistant response, showing how long the model took to generate it.

### Schema Context

Click the **📋 Schema Context** expander inside the popover (visible when a dataset is active) to review the column profile the assistant is using. This shows each column's name, DB type, semantic type, and a brief profile summary (value range for numeric columns, sample values for categorical ones).

### SQL & Output Expander

When the **Show SQL Query & Output expander** toggle is enabled in Settings, a collapsible section labelled **"View Generated SQL & Output"** appears below each SQL-based answer:

- **Generated SQL** — the exact DuckDB query the assistant executed.
- **Output preview** — up to 20 rows of the query result, displayed as a scrollable table with a row count shown above it.

---

## Step 5: Export and Clear Chat

Two buttons appear side by side at the bottom of the popover.

### Export Chat

Click **Export Chat** to download a PDF transcript of the entire conversation. The PDF includes:

- All user and assistant messages
- Generated SQL for any data queries
- Result table previews (up to 50 rows per table)
- Response time per assistant message

The file is saved as **`xinsight_chat.pdf`**.

### Clear Chat

Click **Clear Chat** to remove all messages from the current session. This also deletes the saved session file, so the conversation will not be restored the next time you open XInsight.

The **Clear Chat** button is disabled when there is only the opening welcome message (nothing to clear yet).

<img width="448" height="925" alt="alt text" src="https://github.com/user-attachments/assets/96596130-f16c-4f80-b040-e1d6114ab465">

---

## Step 6: Session Persistence

The Data Assistant automatically saves the conversation to disk after every response. When you reopen XInsight, the previous conversation is restored exactly as you left it — including the dataset context and any inline dataset-switch notices.

To start with a blank conversation, click **Clear Chat** before closing the app, or at any time during a session.

---

## Step 7: Troubleshooting

### "No model configured"

**Symptom:** The chat input is disabled and a warning appears:
> *⚠️ No model configured. Please go to ⚙️ Settings, enter the full path to your `.gguf` model file, and click Save.*

**Fix:** Go to **Settings → 🤖 LLM**, enter the path to your GGUF file (e.g. `Qwen3-4B-Q4_K_M.gguf`), and click **💾 Save Settings**.

---

### "llama_cpp could not be loaded"

**Symptom:** The assistant shows:
> *❌ llama_cpp could not be loaded. This usually happens if the Microsoft Visual C++ Redistributable is missing.*

**Fix:** Run the installer at:
```
Installer\redist\VC_redist.x64.exe
```
Restart XInsight after installation.

---

### Out-of-Memory / Access Violation

**Symptom:** The assistant shows:
> *⚠️ The AI ran out of memory. I have reset the local model and will retry with a smaller context next time.*

**Fix (try in order):**
1. Lower **Context Window (n_ctx)** in Settings → LLM (e.g. from 2048 to 1024) and save.
2. Use a more heavily quantized version of your model (e.g. Q4_K_M instead of Q8_0).
3. Close other memory-intensive applications before running XInsight.

The model resets automatically after a memory error — you can continue asking questions without restarting the app.

---

### "I wasn't able to find a good answer"

**Symptom:** The assistant responds:
> *I wasn't able to find a good answer to that question. Could you try rephrasing it, or asking something more specific about the dataset?*

**Fix:** Rephrase the question to be more specific. Use the **📋 Schema Context** expander to check exact column names, then refer to them directly in your question.

---

## Key Points

- The Data Assistant runs **locally and privately** — no data or questions leave your machine.
- It routes each message automatically: **SQL** (runs a real query), **dataset chat** (schema-grounded answer), or **general chat** (statistics, ML, SQL, Python, AI).
- A dataset is **not required** — general questions work without one.
- SQL answers are backed by real query results, not generated text.
- The conversation **persists across app restarts** unless you clear it.
- Use the **📋 Schema Context** expander and **starter suggestion chips** to explore an unfamiliar dataset quickly.

---

