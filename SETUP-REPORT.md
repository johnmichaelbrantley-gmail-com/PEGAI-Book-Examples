# PEGAI Local Setup Report

**Book:** *Prompt Engineering for Generative AI*: example repository
**Repository:** `C:\Users\johnm\Projects\PEGAI-Book-Examples`
**Date of work:** 2026-11-04 (America/New_York), 14:45–16:25
**Performed by:** Claude, acting for JB. Commands ran on JB's Surface Pro from a temporary batch script, and all output was captured to log files.
**Log files:** `.venv\_pegai_setup\logs\` (00–11). The folder is inside `.venv`, which git ignores.

---

## 1. Bottom line

- **Installation: COMPLETE.** All 284 pinned packages from `requirements-windows.txt` are installed in `.venv` at their exact pinned versions. None are missing and none differ.
- **`pip check`: PASSED.** Output: "No broken requirements found." (exit code 0)
- **Repository tests: 2 of 2 PASSED.** None were skipped.
- **One defect found in the book's own lock file:** `gensim` cannot be imported, because the pinned `scipy` 1.13.0 removed a function that gensim 4.3.2 needs. This affects one Word2Vec cell in Chapter 5. It does not affect Chapter 1.
- **Chapter 1, local setup:** ready. **Chapter 1, live API calls:** the notebook uses `gpt-3.5-turbo`, which OpenAI's official deprecations page lists for shutdown on **October 23, 2026**, a date that has already passed. Expect the API call to fail unless the model name is changed. This was not tested with a live call (no credentials were used).

---

## 2. Environment

| Item | Value | Evidence |
|---|---|---|
| Python | 3.9.12 (64-bit, MSC v.1929) | `02_env_before.txt`, `05_env_after.txt` |
| Interpreter (`sys.executable`) | `C:\Users\johnm\Projects\PEGAI-Book-Examples\.venv\Scripts\python.exe` | same |
| `sys.prefix` | `C:\Users\johnm\Projects\PEGAI-Book-Examples\.venv` | `02_env_before.txt` |
| venv base interpreter | `C:\Users\johnm\AppData\Local\Programs\Python\Python39` (3.9.12) | `.venv\pyvenv.cfg` |
| pip | 22.2.2 (inside `.venv`, not upgraded during this work) | `02`, `05` |
| OS reported by Python | Windows-10-10.0.26300-SP0 (Windows 11 build) | `02_env_before.txt` |
| Git | 2.56.0.windows.4 | `01_git_initial.txt` |
| Packages before install | pip 22.2.2, setuptools 58.1.0 only | `02_env_before.txt` |
| Installed distributions after install | 290 total: 284 pinned, 4 Windows-only extras, plus pip and setuptools | `08_compare.txt` |

System-wide Python (3.12.10) was not touched. Every pip command ran through the `.venv` interpreter explicitly.

---

## 3. Requirements files

| File | Role | Changed? |
|---|---|---|
| `requirements.in` | Direct dependency list | **No.** Git reports it unchanged from HEAD. |
| `requirements.txt` | Original pinned set (pip-compile, Python 3.9; 285 pins) | **No.** Git reports it unchanged from HEAD. |
| `requirements-windows.txt` | **Created in this work.** Windows derivative used for installation. | New, untracked file |

### Windows-specific dependency changes

| Package | Change | Evidence | Reason | Effect on book examples |
|---|---|---|---|---|
| `uvloop==0.19.0` (pulled in by `uvicorn`) | **Removed** (this line plus its `# via uvicorn` comment line). A 4-line header comment explains the change. | Before this work, a dry run with the original `requirements.txt` failed while building uvloop, with an error saying uvloop does not support Windows. | uvloop is a Unix-only event loop. `uvicorn[standard]` requests it only on non-Windows platforms. | None expected. uvicorn falls back to the standard asyncio loop on Windows. |

No other pin was changed. A text diff of the two files shows only the removed uvloop line pair and the added header.

### Packages pip added that are not in the lock file

The lock file was generated on a non-Windows machine, so it does not list these Windows-only dependencies. pip installed them automatically to satisfy other packages' Windows requirements. None of them conflicts with a pin (`pip check` passed).

| Package | Version | Required by (typical) |
|---|---|---|
| colorama | 0.4.6 | click, tqdm (Windows console colors) |
| pyreadline3 | 3.5.6 | humanfriendly (Windows readline) |
| pywin32 | 312 | jupyter-core (Windows only) |
| pywinpty | 3.0.3 | terminado / Jupyter terminals (Windows only) |

---

## 4. Dry run and installation

| Step | Command (via `.venv\Scripts\python.exe -m pip …`) | Result | Log |
|---|---|---|---|
| Dry run | `install --dry-run -r requirements-windows.txt` | **Exit 0**, no errors | `03_dryrun.txt` |
| Install | `install -r requirements-windows.txt` | **Exit 0**. Ran from 15:02 to 16:16 (about 74 minutes); most of that was unpacking and byte-compiling large packages such as torch. | `04_install.txt` |

Note on the run: around 15:40 the session briefly lost its connection to the laptop while pip was byte-compiling torch, which produced no output for about 9 minutes. Before taking any action, I confirmed the original pip process was still alive: torch's `INSTALLER`, `REQUESTED`, and `RECORD` files were written at 15:49:54. A backup restart script (`.venv\_pegai_setup\run_setup2.cmd`) was prepared but **never run**. The install finished normally in a single uninterrupted pip process.

---

## 5. Verification

### `pip check`
```
No broken requirements found.
pipcheck_exit=0
```

### Installed set compared with `requirements-windows.txt` (`08_compare.txt`)
```
REQUIRED_PINS 284
INSTALLED_DISTS 290
MISSING 0
VERSION_MISMATCH 0
EXTRA_NOT_IN_FILE 4   (colorama, pyreadline3, pywin32, pywinpty)
```
The full frozen list is in `07_freeze.txt` (288 lines; `pip freeze` leaves out pip and setuptools).

### Import check of key libraries (`09_imports.txt`)
35 of 36 imports succeeded, including numpy 1.26.4, pandas 2.2.2, torch 2.2.2+cpu, transformers 4.39.3, openai 1.23.2, langchain 0.1.16, langchain-core 0.1.45, chromadb 0.4.24, faiss 1.8.0, pinecone 3.2.2, spacy 2.3.9, gradio 4.27.0, notebook 7.1.3, and pytest 8.1.1.

**1 failure:**
```
FAIL gensim ImportError cannot import name 'triu' from 'scipy.linalg'
```
- **Classification:** a defect in the original lock file, not caused by Windows. The pins `gensim==4.3.2` and `scipy==1.13.0` are incompatible with each other: scipy 1.13 removed `scipy.linalg.triu`, which gensim 4.3.2 imports. The same failure would occur with the original `requirements.txt` on any operating system.
- **Affected example:** `content/chapter_5/embeddings.ipynb`, in the cell starting `from gensim.models import Word2Vec`. No other example file imports gensim.
- **Not fixed.** The fix changes a book pin, so JB decides. The smallest likely correction is `scipy==1.12.0` in `requirements-windows.txt`. The alternative, upgrading gensim, has a larger footprint. Neither option has been tested here.

Other observations:
- torch installed as the **CPU-only** build (`2.2.2+cpu`), which is the default PyPI wheel on Windows. Chapter 9's Stable Diffusion and DreamBooth notebooks expect a GPU or Colab, and would run very slowly or not at all on this machine.
- The import check confirms that modules load. It does not prove every example works end to end.

---

## 6. Repository tests

**Inspection before running:** `pytest.ini` sets `testpaths = tests`. There is one test file, `tests/chapter_3/test_1_hierarchical_list_generation.py`. Its two tests run regex checks against a hard-coded string in `content/chapter_3/one_hierarchical_list_generation.py`. They make no network calls, use no credentials, write nothing outside the repo, and do nothing destructive, so both were safe to run. The pytest cache was disabled (`-p no:cacheprovider`) so no `.pytest_cache` folder was written.

**Result (`10_pytest.txt`):**
```
test_heading_extraction     PASSED
test_subheading_extraction  PASSED
2 passed in 0.37s
```
**Skipped tests:** none.

---

## 7. Book examples: outdated or retired AI APIs (review only, nothing edited)

**Method:** I extracted and searched the code cells of all `.py` and `.ipynb` files in chapters 1, 3, 4, 5, 6 and 10, and in Chapter 9's `sd-api.ipynb`. I checked model status against OpenAI's official deprecations page (developers.openai.com/api/docs/deprecations), retrieved 2026-11-04. I confirmed the default models in the installed `langchain-openai` 0.1.3 source: `ChatOpenAI` defaults to `gpt-3.5-turbo` and `OpenAI` (completions) defaults to `gpt-3.5-turbo-instruct`.

**Confidence key:**
- **Likely retired:** OpenAI's official page lists a shutdown date that has already passed.
- **Unverified:** not listed, or confirming would need a live call, credentials, or a third-party service.

None of these is a *confirmed runtime failure*. No live API calls were made.

### 7a. OpenAI models

| File(s) | Model referenced | Status per official page | Confidence |
|---|---|---|---|
| `chapter_1/ab-testing.ipynb` | `gpt-3.5-turbo` (openai SDK, `client.chat.completions.create`) | Shutdown **Oct 23, 2026** (replacement listed: `gpt-5.6-terra`) | Likely retired |
| `chapter_3/criteria.ipynb`, `chapter_3/majority_vote_classification.ipynb` | `gpt-3.5-turbo`, `gpt-4` | gpt-3.5-turbo: Oct 23, 2026. gpt-4 / gpt-4-0613: **Oct 23, 2026** (replacement: `gpt-5.6-sol`) | Likely retired |
| Every file calling `ChatOpenAI()` with no model (chapters 4, 5, 6, 10; about 25 files, e.g. `chapter_4/business_name_generator.ipynb`, `chapter_5/rag_in_lcel.ipynb`, `chapter_6/token_usage.ipynb`, `chapter_10/gradio_code_example.py`) | defaults to `gpt-3.5-turbo` | Oct 23, 2026 | Likely retired |
| `chapter_6/agent_in_lcel.ipynb`, `chapter_6/react_agent_with_memory.ipynb`, `chapter_5/rag.ipynb` | `gpt-3.5-turbo` (explicit) | Oct 23, 2026 | Likely retired |
| `chapter_4/function_calling.ipynb`, `chapter_4/langchain-evals.ipynb` | `gpt-3.5-turbo-1106` | Shutdown **2026-09-28** | Likely retired |
| `chapter_4/langchain-evals.ipynb` | `gpt-4-1106-preview` | Shutdown Oct 23, 2026 | Likely retired |
| `chapter_4/chaining_story_generation.ipynb`, `chapter_10/article_generation.py`, `chapter_10/custom_summarize_chain.py` | `gpt-3.5-turbo-16k` | Alias not listed. The `-16k-0613` snapshot was shut down 2024-09-13. | Unverified (probably retired) |
| `chapter_6/request_callbacks.ipynb` | `langchain_openai.OpenAI()` defaults to `gpt-3.5-turbo-instruct` | Not checked on the page | Unverified |
| `chapter_5/embeddings.ipynb`, `chapter_5/pinecone.ipynb`, `chapter_5/rag.ipynb` | `text-embedding-ada-002` | Not listed on the page | Unverified |
| `chapter_3/tiktoken_example.py` | Many model names (`gpt-3.5-turbo-0301/0613`, `gpt-4-0314/0613`, `gpt-4-32k-*`) | Several are retired (e.g. gpt-4-32k on 2025-06-06, gpt-4-0314 on 2026-03-26) | Low impact: used only to choose a local tokenizer. No API call was found in this file. |

### 7b. Other services

| File(s) | Pattern | Concern | Confidence |
|---|---|---|---|
| `chapter_9/sd-api.ipynb`, `chapter_10/image_generation_chain.py` | Stability AI `POST /v1/generation/stable-diffusion-xl-1024-v1-0/text-to-image` | v1 engine-based endpoint; its current availability could not be confirmed from sources found | Unverified |
| `chapter_5/pinecone.ipynb` | `pinecone-client` 3.2.2 with `Pinecone(...)` and `ServerlessSpec` | The client package has since been renamed upstream (`pinecone`). Server-side compatibility is unknown. | Unverified |
| `chapter_6/agents_creating_custom_tools.ipynb`, `chapter_6/calculator_and_research_agents.ipynb` | `hub.pull("hwchase17/react")`, `hub.pull("hwchase17/openai-functions-agent")` | Needs LangChain Hub network access; prompts may have moved | Unverified |
| `chapter_4/langchain-evals.ipynb` | Mistral `mistral-small` | Model-name status not checked | Unverified |

### 7c. Old-style LangChain APIs (not a local problem at the pinned versions)
`LLMChain` (`chapter_10/article_generation.py`, `chapter_10/custom_summarize_chain.py`, `chapter_6/request_callbacks.ipynb`), `load_summarize_chain` (`chapter_4/chaining_story_generation.ipynb`, `chapter_6/custom_tools_memory_agent.ipynb`), `AgentType` and `create_csv_agent` (`chapter_6/csv_agent.ipynb`, `chapter_6/sql_database_agent.ipynb`), and `.run()`.

These are deprecated in later LangChain releases but **exist in the pinned langchain 0.1.16**, and they imported cleanly. They will only matter if the LangChain packages are upgraded.

---

## 8. Git status

**Initial** (`01_git_initial.txt`, captured at the start of the run):
```
## main...origin/main
 M .gitignore
?? requirements-windows.txt
```

**Final** (`11_git_final.txt`, captured at the end of the run):
```
## main...origin/main
 M .gitignore
?? requirements-windows.txt
```
After the final snapshot was taken, this report (`SETUP-REPORT.md`) was added, so it will also appear as untracked.

| Path | Origin |
|---|---|
| `.gitignore` (modified: `.venv/` exclusion added) | **Pre-existing JB change.** Preserved, not touched. |
| `requirements-windows.txt` (untracked) | **Created in this work.** It already existed in the initial snapshot only because it was written to disk just before the script started. |
| `SETUP-REPORT.md` (untracked) | **Created in this work** |
| `.venv\_pegai_setup\` (runner scripts, helper scripts, logs) | Created in this work. It lives inside `.venv`, which git ignores, so it does not appear in git status. It can be deleted once this report has been reviewed. |

Nothing was committed, pushed, or deleted. The book's examples, notebooks, tests, README, `requirements.in` and `requirements.txt` were not modified. Google Drive syncing was not touched.

---

## 9. Can JB proceed to Chapter 1 locally?

**Yes, for the local environment.** Python 3.9.12, the isolated `.venv`, every pinned library Chapter 1 needs (openai 1.23.2, pandas, notebook), and the repository tests are all in working order.

**One expected obstacle when running Chapter 1's notebook against OpenAI:** `ab-testing.ipynb` requests `gpt-3.5-turbo`, which OpenAI lists for shutdown on October 23, 2026. Expect a model-not-found or similar error. You will need to decide whether to substitute a current model (for example the replacement OpenAI lists) for live runs, knowing the outputs will differ from the book's. Running it also requires `OPENAI_API_KEY` to be set, and each run incurs a small charge.

**Not blocking Chapter 1:** the gensim/scipy conflict (Chapter 5 only) and the other items in section 7.
