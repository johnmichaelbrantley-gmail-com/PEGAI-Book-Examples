ok, stupid question, .ipynb come from "Jupiter"

Not a stupid question. It’s close: the extension comes from Jupyter’s predecessor.

.ipynb stands for “IPython Notebook”. IPython (“Interactive Python”) was the original project, started around 2001, that created this notebook format.
In 2014 the notebook part grew beyond Python and was renamed Jupyter, spelled with a “y”. The name combines three programming languages it supports: Julia, Python and R. It’s also a nod to the planet Jupiter and Galileo’s notebooks of his observations of its moons.
The file extension never changed, so notebooks are still .ipynb today.
So the format began as an IPython notebook, and the project is now called Jupyter.

The simplest approach is a **notes folder inside the project**, with **one Markdown file per chapter**, written in VS Code.

```
PEGAI-Book-Examples\
  notes\
    chapter-01.md
    chapter-03.md
    ...
```

**Why this setup:**
- Your notes live **alongside the code** they're about, in the same one location you wanted.
- **Markdown** (`.md`) is plain text with light formatting: `#` for headings, `-` for bullet points. VS Code shows a formatted preview.
- Git saves your notes with each commit and backs them up to GitHub with **Sync**, just like the code.
- Keeping notes in their own files means you never edit the book's notebooks, so the book's code stays clean.

**To create a note in VS Code:**
1. In the Explorer panel, right-click **PEGAI-Book-Examples** (the top line) and choose **New Folder…**. Name it `notes`.
2. Right-click the new **notes** folder, choose **New File…**, and name it `chapter-01.md`.
3. Type your notes. Press **Ctrl+S** to save, and **Ctrl+Shift+V** to see the formatted preview.

**One thing to keep in mind:** your GitHub repository is public, so notes become visible there once you sync. Keep them to study notes, with nothing personal.

I can create the `notes` folder and a starter `chapter-01.md` with a few headings (*Key ideas*, *What I ran*, *Results*, *Questions*), already filled in with today's zero-shot vs few-shot result. Want me to?

**U** means **Untracked**. It's git's label for a file that's new in the project folder but not yet part of git's history.

In your project, `responses_validation.csv` shows **U** because the notebook just created it. It has never been committed, so git doesn't track it yet. You may also see a green dot next to the folders containing it (`content`, `chapter_1`); that just means something inside them has changed.

The other letters you'll see in VS Code's Explorer and Source Control panels:

| Letter | Meaning |
|---|---|
| **U** | Untracked: a new file git doesn't track yet |
| **M** | Modified: a tracked file that has changed since the last commit |
| **A** | Added: a new file staged to be included in the next commit |
| **D** | Deleted: a tracked file that was removed |

The letters disappear once the change is committed. Nothing is wrong when you see them; they just show what's new since your last restore point.

