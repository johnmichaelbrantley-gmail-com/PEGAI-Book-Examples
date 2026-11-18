# PEGAI Progress Log

Dated record of study and setup work on *Prompt Engineering for Generative AI*. Newest entries first. See `STUDY-PROCESS.md` for how sessions run and `SETUP-REPORT.md` for setup details.

---

## 2026-11-04 to 2026-11-05: Setup, model updates, Chapter 1

**Environment**
- Installed all 284 pinned packages into the project's Python 3.9.12 virtual environment (`.venv`). System Python untouched.
- Created `requirements-windows.txt`: the book's pins without `uvloop` (unsupported on Windows), plus `scipy==1.12.0` so the pinned `gensim` loads (Chapter 5).
- Verified: `pip check` clean, key imports load, repository tests 2/2 passed.

**Model updates**
- OpenAI retired the book's models (gpt-3.5-turbo, gpt-4, the 1106 versions) in Sep–Oct 2026.
- Updated Chapter 1's `ab-testing.ipynb` and 34 example files in Chapters 3, 4, 5, 6 and 10 to `gpt-6-luna` with reasoning turned off. Without that setting, the LangChain examples fail.
- Request format for each updated pattern verified offline with matching library versions.

**Accounts and tools**
- OpenAI API key stored as a Windows user setting; prepaid credit with auto-reload off.
- VS Code Jupyter extension installed; notebooks run on the `.venv` kernel.

**Learning**
- First live GPT run (Chapter 1 A/B test): few-shot examples controlled the answer format.
- Built the Chapter 1 companion lesson (`content/chapter_1/companion/`): five principles, worked examples, pop-up question exercises, self-check, request cap.
- Started notes in `content/chapter_1/notes/chapter-01.md`.

**Organization**
- Project lives in one place on the laptop's C: drive; GitHub is the backup.
- Removed the empty Google Drive PEGAI folder and temporary setup files.
- Added `STUDY-PROCESS.md`.

**Decisions**
- Study PEGAI with GPT; build JBAI LLC with Claude.
- Phone for reading and review (GitHub app); laptop for running notebooks.

**Open items**
1. Update `STUDY-PROCESS.md` for the GPT workflow (JB commits and syncs).
2. Decide who builds the companion lessons for Chapters 2–10.
3. Confirm with a live run: the `stop` setting with gpt-6-luna (Ch 6 ReAct); the embedding model, Pinecone and LangChain Hub (Ch 5–6); Stability AI (Ch 9–10).
4. Known limitation: thumbs-up/down widget buttons don't display in VS Code with Python 3.9.
