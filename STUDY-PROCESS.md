# PEGAI Study Process

How JB and Claude work through *Prompt Engineering for Generative AI*. One page; update it when the process changes.

## Where things live

| What | Where |
|---|---|
| The project (one location only) | `C:\Users\johnm\Projects\PEGAI-Book-Examples` (C: drive, never synced to OneDrive or Google Drive) |
| Backup and history | GitHub: `johnmichaelbrantley-gmail-com/PEGAI-Book-Examples` (public: study material only, nothing personal) |
| Book examples | `content\chapter_N\` |
| Companion lessons | `content\chapter_N\companion\` |
| Your notes | `content\chapter_N\notes\chapter-NN.md` |
| Setup record | `SETUP-REPORT.md` |

## Who does what

- **JB:** reads the book, runs notebook cells, answers exercise questions, writes notes, clicks **Sync**.
- **Claude:** writes and fixes code, builds companion lessons, makes commits, checks the setup. Claude never runs paid API calls.

## Each study session

1. **Start:** open VS Code (the project opens automatically). Open the chapter's notebook and confirm the kernel at the top right reads **.venv (3.9.12)**.
2. **Read** the chapter section in the book.
3. **Run the book's notebook** for that section, one cell at a time with **▷**. Don't use *Run All*.
4. **Work the companion lesson** in order, starting with its **Setup** cell. Answer **✍️ Your turn** questions in the box at the top of VS Code.
5. **Write notes** in your own words in `notes\chapter-NN.md`.
6. **End:** tell Claude "end of session". Claude commits your work; you click **Sync** in the bottom-left status bar.

## Cost safety

- All examples use **gpt-6-luna** (OpenAI's lowest-cost model) with reasoning turned off.
- Companion notebooks state how many requests each cell sends and stop at a built-in cap.
- API credit is prepaid with **auto-reload off**, so spending can never exceed the balance.
- The API key lives only in the Windows `OPENAI_API_KEY` setting. Never paste it into code, chat or screenshots.

## Model convention for all code

- OpenAI model: `gpt-6-luna`, with reasoning off:
  - LangChain: `ChatOpenAI(model="gpt-6-luna", model_kwargs={"extra_body": {"reasoning_effort": "none"}})`
  - OpenAI client: `client.chat.completions.create(model="gpt-6-luna", extra_body={"reasoning_effort": "none"}, ...)`
- Why reasoning off: it's cheaper and faster, and Luna rejects `temperature`, which LangChain always sends, unless reasoning is off.
- The book's original model names are preserved in git history.

## Known limitations

- Thumbs-up/down widget buttons don't display in VS Code with Python 3.9 (the code still runs).
- VS Code shows a "Python 3.9 no longer supported" warning: expected, because the book requires 3.9.12.
- LangChain cost tracking shows $0.00 for gpt-6-luna (it doesn't know the new prices); token counts are correct.
- Not yet confirmed with a live run: the embedding model `text-embedding-ada-002` (Ch 5), Stability AI image calls (Ch 9–10), Pinecone (Ch 5), LangChain Hub prompts (Ch 6), and the `stop` setting with gpt-6-luna (Ch 6 ReAct).
- Chapter 9's Stable Diffusion notebooks need a GPU (Google Colab), not this laptop.
