# profile/ — your career data (source of truth)

Everything the CV skill knows about you lives here. The skill gets a copy of this folder,
so you only ever edit it in one place.

| File | What goes in it | Who fills it |
|---|---|---|
| `sources/` | Raw material: Master CV, Master Work Experience Log workbook, old CVs | You (upload) |
| `fact-sheet.md` | Name, contacts and visa line per location, employers, titles, dates, education | You (template below) |
| `voice-and-style.md` | Your writing voice: words you like and dislike, bullets that feel right | You (template below) |
| `evidence-bank.yaml` | Structured achievements, built from `sources/` | Claude drafts, you confirm |
| `feedback-log.md` | Lessons from your edits to generated CVs | Claude, with your approval |

## How to add your files

**Option 1: GitHub website (best for .docx / .xlsx / .pdf)**
1. Open the repo on github.com and switch to the branch `claude/cv-generation-skill-design-cmqinw`.
2. Go into `profile/sources/`.
3. Click **Add file → Upload files**, drag in your Master CV and Master Work Experience Log.
4. Click **Commit changes**.

**Option 2: paste into the chat**
Copy the text of your CV and the workbook rows into a message. Claude saves them into `sources/`.

**Option 3: fill the templates**
Edit `fact-sheet.md` and `voice-and-style.md` directly on GitHub (pencil icon), or answer
the questions in chat and Claude fills them in.
