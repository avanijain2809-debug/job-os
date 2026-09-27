# profiles/ — one folder per user

The CV skill's method is the same for everyone. Each user's personal data lives in
their own folder here, copied from `_template/`.

```
profiles/
├── _template/        # blank starting point: copy this, never fill it in
└── <your-name>/      # your own copy (keep it private, see below)
```

## Getting started
1. Copy `_template/` to `profiles/<your-name>/`.
2. Put your existing CV(s) and any project notes in `sources/`.
3. Run the skill in **Onboarding** mode. It reads your sources, drafts `fact-sheet.md`,
   `evidence-bank.yaml` and `settings.yaml`, and asks short questions to fill the gaps.
4. Fill `voice-and-style.md` (or answer its questions in chat).

## Privacy
Profile folders contain personal data. Keep your own folder in a **private** repo or
outside version control; never commit it to a public fork.
