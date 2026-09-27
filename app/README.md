# CV Studio — downloadable app

`cv-studio.html` is the whole app in one file. Download it and open it in Google Chrome. There's nothing to install and no account to create.

## For users
1. **Download** `cv-studio.html` and double-click it (or drag it into Chrome).
2. **My profile:** paste your CV (or upload .docx / .pdf / .txt), then click **Build my profile from my CV**. Check the evidence entries and answer Claude's questions.
3. **Tailor a CV:** paste a job description, click **Create tailored CV**, then edit, check the one-page indicator and **Print / Save as PDF** (choose "Save as PDF" in Chrome's print dialog).

### Two ways to use Claude
- **Copy-paste (default):** the app gives you instructions to paste into your own Claude chat at claude.ai; you paste Claude's reply back. Works with a normal Claude plan.
- **API key (optional):** add an Anthropic API key in Settings and everything happens inside the page. Usage is billed to your API account. This mode can also read a job posting from a link.

### Your data
Everything is stored in this browser on this computer (localStorage). Use **Settings → Download backup** to keep a copy or move to another computer. Backups never include your API key.

## For developers
- The CV method (the portable "skill") is the `METHOD` constant at the top of the script. It implements `docs/cv-skill/ARCHITECTURE.md`.
- API mode calls `POST https://api.anthropic.com/v1/messages` directly from the browser (`anthropic-dangerous-direct-browser-access: true`), streaming, with adaptive thinking. Claude Opus 5 requests use server-side refusal fallbacks (`fallbacks: "default"`).
- `.docx` and `.pdf` import load mammoth.js / pdf.js from cdnjs on first use. Pasting text always works as a fallback.
- The automatic checks (avoided words, page fit, repeated verbs, bullet length, job titles against the fact sheet) are deterministic and run in the page. They don't use AI.
