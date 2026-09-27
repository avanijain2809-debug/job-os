# Profile template — copy this folder to `profiles/<your-name>/`

Everything the CV skill knows about one user lives in their copy of this folder.
The skill gets a copy of it, so each user edits their data in one place.

| File | What goes in it | Who fills it |
|---|---|---|
| `sources/` | Raw material: master CV, project logs or notes, old CVs | You (upload) |
| `fact-sheet.md` | Name, contacts and visa line per location, employers, titles, dates, education | You (template below) |
| `settings.yaml` | Seniority, page length, spelling, target roles, regional header rules | You, or Claude during onboarding |
| `voice-and-style.md` | Your writing voice: words you like and dislike, bullets that feel right | You (template below) |
| `evidence-bank.yaml` | Structured achievements, built from `sources/` | Claude drafts, you confirm |
| `feedback-log.md` | Lessons from your edits to generated CVs | Claude, with your approval |

## How to add your files

**Option 1: GitHub website (best for .docx / .xlsx / .pdf)**
1. Open your repo on github.com.
2. Go into `profiles/<your-name>/sources/`.
3. Click **Add file → Upload files**, drag in your CV(s) and project notes.
4. Click **Commit changes**.

**Option 2: paste into the chat**
Copy the text of your CV and project notes into a message. Claude saves them into `sources/`.

**Option 3: fill the templates**
Edit `fact-sheet.md` and `voice-and-style.md` directly on GitHub (pencil icon), or answer
the questions in chat and Claude fills them in.
