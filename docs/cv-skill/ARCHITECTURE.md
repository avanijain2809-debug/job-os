# CV Skill — Architecture Proposal (v0.2, for review)

> Status: **design only**. No SKILL.md has been written yet. This document proposes the architecture, explains why each component exists, and lists the decisions needed before the build.

---

## 0. Design principles (the "why" behind everything below)

1. **Separate *truth* from *presentation*.** Career facts live in a structured evidence bank. The CV is one *rendering* of that truth for one role. Claude may change the rendering freely but may not touch the truth.
2. **Constant core, variable surface.** Every CV version has the same identity, employers, titles, dates, voice and quality bar. Only selection, order, emphasis and vocabulary flex per role.
3. **Decide before writing.** The skill forms a one-sentence *positioning thesis* for the role before it writes a single bullet. Every later choice is tested against that thesis.
4. **Write for the human, verify for the ATS.** The primary reader is a senior recruiter or hiring manager scanning for 6–30 seconds. ATS compliance is a constraint, not the objective.
5. **Method is generic, candidate data is personal.** The workflow, rules and QC are written so they would work for anyone. Your identity, evidence and preferences sit in separate files that can be updated without touching the method.
6. **Progressive disclosure.** SKILL.md stays short: the workflow, non-negotiables and pointers. Detailed rules, role lenses and examples live in reference files that load only when needed, so context stays lean and each file is easy to maintain.
7. **Learn from your edits.** Every time you correct a CV, the skill should capture the lesson so the next CV needs fewer corrections.

### Relationship to the existing `cv-tailor` skill

There is already a `cv-tailor` skill in your account. It's a good v0: the verb list, banned phrases, role tuning and "10-second recruiter test" are all worth keeping. Its main gaps:

| Gap in current `cv-tailor` | Addressed here by |
|---|---|
| Experience lives in a workbook; no structured, claim-level source of truth | Evidence bank with fact-locked fields (§3) |
| No explicit positioning step; goes straight from keywords to bullets | Positioning thesis + space budget in the decision engine (§4) |
| "Never fabricate" is one sentence; no rules on what reframing is allowed | Full integrity model with allowed and forbidden transforms (§7) |
| No limit on how far tailoring can drift | Tailoring-distance limits and the "same candidate" test (§6) |
| No anti-AI-writing layer beyond a banned-verb list | Vocabulary *and* structural tells, verb caps, rhythm rules (§5) |
| Also owns .docx rendering and tracker logging | Moved to JobOS (§10) |
| No learning loop | Feedback log → preference promotion (§3B) |

**Recommendation:** the new skill replaces `cv-tailor` once it's built and tested.

---

## 1. Skill objective

**One-line objective:**
> Given your verified career evidence and a specific role, produce the strongest *truthful* CV for that role, written in your voice to a consulting-grade standard, and make every positioning decision and risk visible to you.

**The skill is responsible for:**

| Responsibility | Meaning |
|---|---|
| **Interpret** | Read a JD the way a hiring manager would: the real problem being hired for, must-haves vs signals, seniority, archetype. |
| **Position** | Decide which version of you this role needs to see (the lens), within what's true. |
| **Select** | Choose the evidence that best proves fit, and decide what to cut or compress. |
| **Write** | Turn evidence into sharp, natural, specific bullets and a summary in your voice. |
| **Assemble** | Build the CV to the right structure, length and regional conventions. |
| **Verify** | Trace every claim to evidence, run QC, and flag gaps and anything needing your confirmation. |
| **Review / improve** | Critique an existing CV (or single bullets) against a JD or against the standard. |
| **Maintain** | Turn new achievements you describe into structured evidence entries. |

**The skill is *not* responsible for:** finding jobs, fetching or scraping URLs, the go/no-go decision, tracking applications, rendering files, cover letters, outreach or interview prep. These belong to JobOS (§10). The skill *produces reusable outputs* for them (positioning thesis, evidence used, gaps).

### Skill modes

The skill should support four modes, because "written, tailored, reviewed and improved" are different jobs:

| Mode | Trigger | Output |
|---|---|---|
| **Tailor** (primary) | JD + profile → CV | Full CV + decision brief |
| **Review** | Existing CV (+ optional JD) → critique | Scored critique, prioritised fixes, rewritten weak bullets |
| **Refine** | "Improve this bullet / section" | 2–3 alternatives with the trade-off of each |
| **Bank** | "Add this project / achievement" | New structured evidence entry + questions to fill gaps |

---

## 2. Skill architecture

I considered your suggested list. Most items are real, but several belong *together*, and a few are missing. The recommended structure has **five layers**:

```
┌─────────────────────────────────────────────────────────────┐
│ L1  CANDIDATE LAYER   (who you are — personal, persistent)  │
│     profile · evidence bank · voice & preferences · log     │
├─────────────────────────────────────────────────────────────┤
│ L2  INTERPRETATION LAYER  (what this role needs)            │
│     JD analysis · role lenses (archetypes) · seniority read │
├─────────────────────────────────────────────────────────────┤
│ L3  DECISION LAYER  (how to position you for it)            │
│     positioning thesis · evidence mapping · space budget ·  │
│     gap triage · tailoring limits                           │
├─────────────────────────────────────────────────────────────┤
│ L4  WRITING LAYER  (how it's expressed)                     │
│     bullet rules · summary rules · metrics · AI positioning │
│     · anti-AI-writing · structure/format/ATS/regional       │
├─────────────────────────────────────────────────────────────┤
│ L5  ASSURANCE LAYER  (is it true and good)                  │
│     factual integrity · QC gates · recruiter simulation ·   │
│     output contract                                         │
└─────────────────────────────────────────────────────────────┘
```

How your suggested sections fit in:

| Your suggestion | Where it goes | Verdict |
|---|---|---|
| Candidate positioning, Career narrative | L1 profile (stable narrative) + L3 thesis (per-role lens) | **Split.** The narrative is stable; positioning is per-role. Mixing them causes drift. |
| Target-role interpretation, JD analysis | L2 | **Merge.** Same job. Role lenses are reusable priors; JD analysis is per-JD evidence. |
| Experience / achievement bank | L1 evidence bank | **Keep, and make it the most important file.** |
| Evidence hierarchy | L3 evidence mapping | Keep, as the scoring model for selection. |
| Bullet-writing rules, Consulting language, Metrics rules | L4 bullet rules | **Merge.** Consulting language and metrics are sub-rules of bullet writing. |
| AI experience positioning | L4, own section | **Keep separate.** It's the easiest thing to get wrong (over-claiming or hype) and flexes most by role. |
| CV structure, Formatting rules, ATS considerations | L4 format module | **Merge**, and add **regional conventions** (HK / UAE / UK differ). |
| Tailoring rules | L3 | Keep. Add **tailoring-distance limits** (new). |
| Factual accuracy | L5 integrity | Keep. Also placed at the top of SKILL.md as non-negotiables. |
| Anti-AI-writing rules | L4 | Keep. Expand to structural tells, not just words. |
| Quality control | L5 | Keep. Split into hard gates and soft scoring. |
| *(missing)* Seniority calibration | L2 + L5 | **Add.** You're early-career (0–3 yrs); over-claiming seniority is the biggest credibility risk. |
| *(missing)* Gap triage | L3 | **Add.** Decides what to do when the JD asks for something you don't have. |
| *(missing)* Feedback / learning loop | L1 | **Add.** Your edits become rules. |
| *(missing)* Output contract | L5 | **Add.** A fixed output shape so JobOS can consume it. |
| *(missing)* Modes | SKILL.md top | **Add.** Review/refine/bank are different jobs from tailoring. |
| *(missing)* Confidentiality | L1 + L5 | **Add.** Client names, deal names and data from consulting work need anonymisation rules. |

---

## 3. Candidate knowledge layer

The key design decision: **the evidence bank, not the master CV, is the source of truth.** A master CV is already a compressed rendering; it has lost context, scope, and the alternative framings you'd need for different roles. The master CV becomes one *output* of the bank (the "general" version).

### A. Stable (changes rarely; edit deliberately)

| Item | What it contains | Why the skill needs it |
|---|---|---|
| **Fact sheet** | Legal name, contact details, employers, **exact titles**, **exact dates**, locations, education (degree, institution, dates, grades/honours), certifications, languages + proficiency, work authorisation per region | These are fact-locked and never rewritten. QC checks every CV against them. |
| **Core identity & narrative** | 3–5 sentences: who you are professionally, the through-line of your career, what you're unusually good at, why your path makes sense | This is what keeps every version "the same candidate". The per-role summary is always a lens *on* this. |
| **Signature strengths** (3–5) | E.g. "structured market analysis under ambiguity", "end-to-end solo ownership of client deliverables", "AI-native research workflows" — each linked to evidence IDs | These strengths appear in some form in every CV, and each is backed by evidence. |
| **Evidence bank** | Structured achievement entries (schema below) | The only source for bullets. |
| **Voice & style preferences** | Tone, preferred verbs, disliked words and phrases, sentence length, British vs American spelling, how you like metrics shown, tense conventions | Makes the output sound like you. |
| **Hard boundaries** | Things never to claim, e.g. "never say I managed people", "never name client X", "never call myself a data scientist" | Explicit guardrails beyond general integrity. |
| **Confidentiality rules** | How each client or project may be referred to ("a mid-market PE fund", "a GCC sovereign-backed investor"), what data can be shown | Consulting CVs routinely leak client identity. This prevents it. |

#### Evidence bank entry schema (proposed)

Each achievement is a **claim unit**: one project or accomplishment, recorded with enough context to be reframed truthfully for different roles.

```yaml
- id: JUM-07
  employer: JUMUCON
  period: 2025-03 → 2025-05
  project: Fertility clinics market report
  client_descriptor: "a Hong Kong healthcare investor"   # approved anonymised label
  context: >            # situation / why it mattered
    Investor evaluating entry into HK/SEA fertility market; no public market sizing existed.
  my_role: >            # EXACT ownership, not inflated
    Sole analyst; owned model and report end-to-end; partner reviewed final deck.
  ownership_level: led   # led | co-led | contributed | supported
  actions:
    - Built bottom-up TAM model from clinic counts, cycles/clinic and pricing
    - Ran N expert interviews with clinic operators
    - Mapped value chain and competitive landscape
  results:
    - Report delivered to investor IC; used in go/no-go decision
  metrics:
    - value: "US$XXXm TAM"
      provenance: exact         # exact | approximate | estimate-by-me | unknown
      shareable: true
    - value: "8 expert interviews"
      provenance: exact
  skills_evidenced: [market sizing, TAM modelling, expert interviews, healthcare]
  archetype_tags: [consulting, cdd, pe-value-creation, corp-strategy]
  ai_involvement: "Used Claude for desk-research synthesis; built prompt workflow that cut research time ~40%"  # or none
  strength: 4            # 1–5, how impressive to a senior reader
  approved_phrasings:    # optional: bullets you've already signed off
    - "Built bottom-up TAM model for HK fertility market used in investor's entry decision"
  do_not_claim:          # entry-specific guardrails
    - "Do not imply I presented to the IC"
```

Why this schema: `my_role` and `ownership_level` stop seniority inflation, `provenance` stops invented or inflated metrics, `client_descriptor` handles confidentiality, `archetype_tags` and `skills_evidenced` enable fast mapping, `approved_phrasings` preserve bullets you've already signed off, and `do_not_claim` records the specific ways an entry could be misread.

### B. Evolving (reviewed every few weeks or on a trigger)

| Item | Why it changes | Update trigger |
|---|---|---|
| **Target archetypes & priority order** | Your search focus will shift (e.g. consulting → PE → AI) | Change of search strategy |
| **Regional focus** | HK / UAE / London priorities, visa status | Market or visa change |
| **Summary / headline variants** | Pre-approved per archetype | After a CV gets strong responses |
| **New evidence entries** | New projects and results | Every completed project (use **Bank** mode) |
| **Skills & tools inventory** | Especially your AI toolkit, which changes monthly | New tool used in real work |
| **Transition narrative** | How you explain moving from consulting into CoS, AI or PE | When the target role family changes |
| **Feedback log** | Your edits to generated CVs, and what they teach | Automatically after every review |
| **Outcome signals** *(optional, from JobOS)* | Which versions got interviews | When JobOS status changes |

**The feedback loop:** after you edit a generated CV, the skill writes short entries to `feedback-log.md` (e.g. "User replaced 'orchestrated' with 'ran', twice. Prefers plainer verbs."). When a pattern appears 2–3 times, the skill proposes promoting it into `voice-and-style.md`. Nothing is promoted without your approval.

### C. Dynamic (provided per application)

| Input | Required? | Notes |
|---|---|---|
| **JD text** | Required | JobOS normalises it from a paste or URL |
| **Company & role basics** | Required | Name, title, location. JobOS supplies them. |
| **Region / market** | Required | Drives format conventions (§4, step 7) |
| **Application channel** | Recommended | Portal/ATS vs referral vs direct to a partner. This changes keyword density and summary style. |
| **JobOS fit analysis** | Optional | If JobOS already scored fit, reuse it rather than redo it |
| **Company context** | Optional | What the company does, stage, recent news. Sharpens the positioning thesis. |
| **User steer** | Optional | E.g. "lean on the PE work", "keep it to 1 page", "don't mention X" |
| **Length constraint** | Optional | Default is set by region and archetype |
| **Known reader** | Optional | E.g. an ex-MBB partner vs an HR screener |

**Missing-input protocol:** if a required input is missing, ask. If an optional one is missing, use the default and say which default was used.

---

## 4. CV decision engine

Your proposed pipeline is right in direction but is missing three things: (1) **a positioning thesis** before any writing, (2) **a space budget**, meaning deciding how much of the page each experience earns, and (3) **a separate gap triage step**, so gaps get handled explicitly rather than papered over. It also benefits from reading the role as a *problem* rather than a keyword list.

### Improved pipeline

```
 0. INTAKE CHECK      Are the profile, bank, JD and region present? Load the role lens.
        │
 1. ROLE READ         What is this role, really?
        │             archetype · seniority band · the hiring manager's actual problem ·
        │             who reads the CV first · company context
        │
 2. REQUIREMENTS      Extract requirements and classify each one:
        │             MUST-HAVE (screen-out) · DIFFERENTIATOR (wins the interview) ·
        │             NICE-TO-HAVE · NOISE (boilerplate; ignore)
        │             + the JD's vocabulary (terms to mirror where true)
        │
 3. EVIDENCE MAP      For each requirement → candidate evidence IDs, scored:
        │             relevance × strength × evidence tier × recency
        │             → coverage matrix (strong / partial / transferable / none)
        │
 4. POSITIONING       Write the POSITIONING THESIS (1 sentence):
    THESIS            "For this role, Avani is a ___ who has ___, proven by ___."
        │             + 3 proof points the recruiter must take away in 6 seconds
        │             Test: consistent with the core identity? supportable by the bank?
        │
 5. GAP TRIAGE        For each must-have without strong coverage:
        │             • Reframable: evidence exists, needs surfacing → do it
        │             • Transferable: adjacent evidence → bridge honestly
        │             • Genuine gap → do NOT paper over; flag for cover letter or interview
        │             • Deal-breaker → warn the user (and JobOS) before continuing
        │
 6. SPACE BUDGET      Allocate page real estate by relevance to the thesis:
        │             bullets per role/project, what's cut, what's compressed to one line,
        │             section order, summary yes/no, skills section content
        │
 7. STRUCTURE         Choose the template variant for archetype + region
        │             (section order, length, regional conventions)
        │
 8. WRITE             Summary → bullets → skills, within the tailoring limits (§6)
        │             Priority: approved phrasings > adapted approved phrasings > new bullets
        │
 9. VERIFY            Integrity trace (every claim → evidence ID) · QC hard gates
        │             → fix and re-run on failure
        │
10. RECRUITER READ    Simulate a 6-second scan and a 60-second read by the likely reader.
        │             Do the 3 proof points land? Does anything raise a doubt?
        │             → targeted fixes
        │
11. OUTPUT            CV + decision brief + gaps + verification flags + JobOS metadata
```

### Why each added step matters

- **Role read (1):** "Strategy & Operations" at a 40-person startup and at a bank are different jobs. Reading the actual problem ("they need someone to build the operating cadence the CEO doesn't have time for") produces better positioning than keyword extraction does.
- **Requirement classes (2):** without them, every JD bullet looks equally important and the CV becomes a checklist. Most JDs have 2–4 real must-haves and a lot of boilerplate.
- **Evidence tiers (3):** a way to rank evidence objectively:

  | Tier | Evidence type | Example shape |
  |---|---|---|
  | T1 | Owned outcome, quantified | "…model used in investor's US$Xm entry decision" |
  | T2 | Owned scope or scale, quantified | "…across 6 markets and 40+ companies" |
  | T3 | Owned outcome, qualitative but specific | "…adopted as the client's standard screening tool" |
  | T4 | Contribution to a team outcome | "…contributed the value-chain analysis to…" |
  | T5 | Activity or responsibility only | "…conducted desk research on…" |

  Selection prefers higher tiers. A T5 item should only appear when it's the only evidence for a must-have.
- **Positioning thesis (4):** the single biggest quality lever. Without it, tailoring turns into keyword insertion. With it, every bullet has a job to do.
- **Gap triage (5):** makes honesty an explicit step rather than a hope.
- **Space budget (6):** recruiters infer importance from space. If your most relevant project gets one line and an irrelevant one gets four, the CV argues against you.
- **Recruiter read (10):** a separate adversarial pass. Writing mode and reading mode catch different problems.

---

## 5. Bullet-writing framework

### 5.1 The anatomy of a strong bullet

```
[Precise verb matching real ownership] + [specific object: what, for whom, at what scale]
 + [how / method, only if it's a differentiator] + [so-what: outcome, use, or decision enabled]
```

There are two permitted shapes. Mixing them avoids AI-like uniformity:

- **Action-led (default):** "Built a bottom-up TAM model for the HK fertility market, used by a healthcare investor in its entry decision"
- **Outcome-led (for your strongest 1–2 bullets per role):** "Informed a healthcare investor's HK market-entry decision by building…"

*(Examples here are illustrative. Real bullets must come from the evidence bank.)*

### 5.2 Rules

| # | Rule | Detail |
|---|---|---|
| B1 | **One idea per bullet** | If it needs "and" twice, split it or cut it. |
| B2 | **Length** | 1 line ideal, 2 lines max (roughly 15–28 words). No 3-line bullets. |
| B3 | **Verb must match ownership** | `led` → Led / Built / Ran / Owned. `co-led` → Co-led / Jointly built. `contributed` → Developed [the X component of] / Delivered [part]. `supported` → Supported / Analysed for. Never upgrade. |
| B4 | **Specific nouns beat adjectives** | Sector, deliverable type, audience ("investment committee", "CEO"), geography, scale. Not "key", "various", "multiple", "strategic". |
| B5 | **So-what test** | Every bullet answers "so what?", with an outcome, a use or decision, or a scale. If none exists in the bank, write the most specific scope available. Never invent an outcome. |
| B6 | **Metrics where they exist** | See 5.3. |
| B7 | **Front-load relevance** | The first 5–7 words carry the role-relevant signal, because that's what gets scanned. |
| B8 | **Verb variety** | No verb used more than twice in the CV. Never start consecutive bullets with the same verb. |
| B9 | **Tense** | Past tense for completed work. Present tense only for the current role's ongoing duties. |
| B10 | **Order within a role** | Most relevant to the thesis first, not chronological. |
| B11 | **Consulting register** | Plain, precise and confident. Jargon only when it's the recruiter's own vocabulary (e.g. "CDD", "value creation plan", "100-day plan", "IC memo") *and* the work truly matches it. |
| B12 | **No first person, no articles at the start** | "Built…", not "I built…" or "The…". |

### 5.3 Metrics rules

- **Types of metric, in order of power:** outcome (value created, decision enabled, cost saved) › scale (market size, # companies, # markets, deal size) › speed/efficiency (time cut, turnaround) › volume (# interviews, # reports) › audience (who used it).
- **Only metrics present in the bank.** A metric's `provenance` decides how it's written:
  - `exact` → as is.
  - `approximate` → "~" or "c." (pick one convention per CV) and round **down**.
  - `estimate-by-me` → only if you've approved it, written conservatively and flagged.
  - `unknown` → no number. Use specific scope instead.
- **Derived metrics** (e.g. adding up interviews across projects to get "40+ interviews") are allowed only when every input is in the bank. The skill must flag the derivation in the verification list.
- **Market sizes are not results.** "Sized a US$2bn market" is scope, not impact. Don't imply you created that value.
- **Density:** aim for roughly 50–70% of bullets carrying a number. 100% reads as manufactured.

### 5.4 Weak vs strong (the skill's teaching examples)

| Weak (responsibility) | Why it fails | Strong (achievement) |
|---|---|---|
| Responsible for market research across multiple industries | Duty, vague, no so-what | Sized and mapped 9 industries, from data centres to dental, for PE and corporate clients, each delivered as a full industry report |
| Helped with financial modelling for client projects | Hides ownership, no object | Built bottom-up TAM models for 6 investor pitches, including [specific example with result] |
| Leveraged cutting-edge AI tools to drive efficiency | AI clichés, no evidence | Built a Claude-based desk-research workflow that cut first-draft report time from ~X to ~Y days |
| Worked cross-functionally with stakeholders to deliver strategic insights | Could be anyone | Ran 8 expert interviews with clinic operators and synthesised findings into the client's market-entry recommendation |

*(Numbers and details above are placeholders. The real ones come from the bank.)*

### 5.5 Summary rules

- 2–3 lines, no "results-driven professional"-style openers.
- Formula: **[identity through this role's lens] + [2 proof points] + [what you bring to *this kind* of role]**.
- Built from the core narrative plus the positioning thesis. Never a new identity.
- Optional by region and archetype. MBB-style CVs often omit it; S&O, CoS and AI roles benefit from it.

### 5.6 AI experience positioning

AI is your most distinctive asset and also the easiest thing to over-claim or to dilute with hype.

**Three levels of AI claim. The skill must never blur them:**

| Level | Meaning | Allowed language |
|---|---|---|
| **Uses** | Uses AI tools in your own work | "Used [tool] to…", often better folded into the result rather than stated |
| **Builds** | Built workflows, prompts, agents or automations that others use or that changed a process | "Built / designed / automated…" + measurable effect |
| **Advises / implements** | Advised a client or organisation on AI strategy or rollout | Only if the bank has it |

**Positioning by archetype:**

- *Consulting / CDD / PE:* AI is a **lever, not the headline**. Show it as speed, depth and rigour ("delivered X in Y days using…"). One or two mentions at most. Over-indexing can signal "not a real analyst".
- *CoS / S&O / Corp Strategy:* AI is **operating leverage**. Show automation of cadences, reporting and research. Medium prominence.
- *AI / AI implementation:* AI **leads**. Separate projects section or lead bullets; concrete tools, workflows, adoption and effect. Still no hype.

**Banned in AI context:** "cutting-edge", "harnessed the power of", "AI-driven insights", "revolutionised", "AI enthusiast", and tool lists with no evidence behind them.

### 5.7 Anti-AI-writing rules

Two kinds of tell: **words** and **structure**. Structure matters more.

**Banned or discouraged vocabulary** (seed list, extended by your preferences):
spearheaded, leveraged (as a default verb), orchestrated, synergy, dynamic, passionate, results-driven, proven track record, cutting-edge, seamless, robust, holistic, pivotal, delve, tapestry, fast-paced environment, stakeholder alignment (unless it's the actual work), "responsible for", "helped", "assisted", "worked on", "various", "multiple", "key" (as filler), "successfully" (always redundant).

**Structural tells to avoid:**

- Every bullet the same length and shape → vary between 1-line and 2-line, and between action-led and outcome-led.
- Participle chains: "…, driving X, enabling Y, resulting in Z" → one so-what per bullet.
- Reflexive triads ("strategy, execution and leadership") everywhere → at most one per CV.
- Em-dash overuse → use commas or split the sentence.
- Abstract nouns stacked ("strategic transformation initiatives") → name the actual thing.
- JD phrases pasted verbatim → mirror *terms*, not *sentences* (§6).
- Every bullet ending in a percentage → vary the type of so-what.

**Natural-writing test:** could you say this bullet out loud to a partner in an interview without cringing? If not, rewrite it.

### 5.8 Structure, format, ATS and regional conventions

- **Default structure:** Header → (Summary) → Experience → Education → Skills & Languages → (Additional / Selected AI Projects, by archetype).
- **Length:** 1 page default for 0–5 yrs experience (MBB/PE norm). Two pages only if you explicitly ask.
- **ATS-safe:** single column, standard section headings, no tables, text boxes, icons, headers or footers for key info, standard date format (e.g. "Mar 2025 – Present"), each important acronym spelled out once ("commercial due diligence (CDD)").
- **Keywords:** each must-have term from the JD appears at least once *in context* (in a bullet, not only in a skills list) where the evidence supports it. No keyword appears without evidence behind it.
- **Regional module** (to confirm with you):
  - *Hong Kong:* English CV, 1 page; nationality or visa line optional; no photo by default.
  - *UAE:* nationality and visa or sponsorship status often expected; mention languages; photo is common but optional (your call).
  - *UK:* no photo, no DOB, no nationality needed; state right-to-work or sponsorship need only if you choose; British spelling.
- **Rendering:** the skill outputs a **canonical structured Markdown CV**. JobOS turns it into .docx or PDF with your template (§10).

---

## 6. Tailoring philosophy

> **"The same strong candidate, seen through the lens of this role."**

### 6.1 Constant core vs variable surface

| NEVER changes across versions (core) | MAY change per role (surface) |
|---|---|
| Name, contact, employers, **titles**, **dates**, locations | Which evidence is shown |
| Education, grades, certifications | Order of bullets within a role |
| Facts, metrics, ownership levels | Space given to each role or project |
| Core identity and signature strengths (in some form) | Which part of a bullet is front-loaded |
| Voice, tone, quality bar | Vocabulary, *where the JD's term truthfully describes the same work* |
| Hard boundaries and confidentiality rules | Summary lens (from pre-approved variants where they exist) |
| Section architecture (within the chosen template) | Skills section order and selection |
| | Optional sections (e.g. AI projects) |

### 6.2 Tailoring levers, from lightest to heaviest

Use the lightest lever that does the job:

1. **Select:** choose which bullets appear. *Most tailoring should happen here.*
2. **Order:** put the most relevant first.
3. **Allocate:** give more space to the most relevant role or project.
4. **Front-load:** rewrite a bullet so the relevant element comes first (same facts).
5. **Translate:** use the JD's term for the same work ("market sizing" → "market assessment" for a CDD role), only when it's accurate.
6. **Rewrite:** a new bullet from bank evidence, used when no existing phrasing serves the thesis.

### 6.3 Tailoring-distance limits (the anti-over-customisation guard)

- **Mirror terms, not sentences.** Never reuse a JD phrase of more than about 4 words verbatim, except proper terms like "commercial due diligence".
- **Keyword ceiling:** each JD keyword appears at most 2 times in the CV.
- **Rewrite budget:** most bullets should be selected or reordered, not rewritten. Rough target: no more than ~40% of bullets materially rewritten against their closest approved phrasing. If the thesis needs more, the skill says so in the decision brief.
- **No archetype cosplay:** you don't become "a PE professional" for a PE role. You're "a strategy consultant with investor-facing diligence-style experience". Titles and identity don't morph.

### 6.4 The two consistency tests (run in QC)

1. **Colleague test:** would a former colleague who worked with you recognise every bullet as your work?
2. **Side-by-side test:** if a recruiter saw this CV next to your consulting CV and your AI CV, would it obviously be the same person with a different emphasis? Or would it look like three different people?

---

## 7. Factual integrity

### 7.1 Core rule

> **Every claim in the CV must trace to a specific evidence-bank entry or the fact sheet. Presentation may change; facts may not.**

### 7.2 Allowed transformations (reframe and strengthen)

| Allowed | Example |
|---|---|
| Reorder or compress facts | Merge two logged actions into one bullet |
| Choose which true facts to emphasise | Lead with the investor use rather than the method |
| Replace vague wording with a *more specific true* wording from the bank | "research" → "8 expert interviews" (if logged) |
| Translate to an equivalent professional term | "industry report for investor" → "market study for an investor client" |
| Generalise to protect confidentiality | Client name → approved descriptor |
| Aggregate across entries when every input is logged | "40+ expert interviews across 9 sectors" (flagged as derived) |
| Round numbers **down** or to a conservative bound | 43 → "40+" |
| Surface implicit but documented scope | Bank says "sole analyst" → "independently delivered…" |

### 7.3 Forbidden transformations (fabrication)

| Forbidden | Example of a violation |
|---|---|
| Inventing or estimating metrics | Adding "saving 30%" where none is logged |
| Inventing outcomes | "leading to a successful acquisition" when the outcome is unknown |
| Upgrading ownership or seniority | `contributed` → "Led"; "managed a team" when there was no team |
| Inventing responsibilities | "managed client relationships" when not logged |
| Inventing clients, sectors or geographies | "for a Fortune 500 client" |
| Adding skills or tools not evidenced | Listing "Python" or "LBO modelling" because the JD asks for it |
| Implying qualifications | "CFA candidate" unless in the fact sheet |
| Changing titles or dates | "Senior Analyst" when the title was "Analyst" |
| Implying a client identity through detail | Enough detail that the client is identifiable, against confidentiality rules |
| Misattributing team outcomes as individual | "Delivered a US$Xm deal" when you worked on one workstream |
| Merging projects to inflate scale | Presenting 3 small projects as one large one |

### 7.4 Uncertainty protocol

When the skill *wants* a fact it doesn't have:

1. Don't guess and don't soften into something vaguely true-sounding.
2. Write the strongest version supported by the bank.
3. Add an item to **Verification Questions** ("Do you know what the investor decided? If so, bullet 3 could lead with the outcome.").
4. If you answer, the answer goes into the bank (Bank mode), not just this CV.

The draft CV never contains `[VERIFY]` placeholders. It always uses the safe version, and the questions sit beside it.

### 7.5 Skills section integrity

A skill may be listed only if (a) at least one evidence entry demonstrates it, or (b) it's in the fact sheet (certifications, languages). No "familiar with" padding.

---

## 8. Output architecture

### 8.1 Shown to the user (in this order)

1. **Positioning brief** (3–5 lines): the thesis, the 3 proof points, and the archetype and region template used. *Why first:* you should approve the angle before reading the CV.
2. **The CV:** canonical Markdown, ready for JobOS to render.
3. **Key tailoring decisions** (5–8 bullets): what was emphasised, cut or compressed, and why. Example: "Compressed the toys and tires projects to one line; moved PE-facing work to the top; summary uses the 'investor-facing analyst' variant."
4. **Gaps and risks:** must-haves with weak or no coverage, rated, each with a suggested action (address in cover letter, prepare for interview, or "do you have evidence?"). Any deal-breakers are flagged prominently.
5. **Verification questions:** facts that would strengthen the CV if you can confirm them, plus any derived metrics to check.
6. **Diff vs master (compact):** bullets added, removed and rewritten, with evidence IDs. Collapsed by default.

**Optional, on request:** recruiter notes (how a recruiter would likely read this CV and the one question they'd ask), alternative bullets for the top 3 positions, and the full requirement-coverage matrix.

### 8.2 Internal (worked through but not shown unless asked)

- Full requirement extraction and classification
- Evidence scoring and the candidates that were considered and rejected
- Space-budget calculation
- Integrity trace table (claim → evidence ID)
- QC scores and fix iterations

*Why hidden:* it's scaffolding. Showing it adds noise and invites reviewing the process instead of the product. It's available in a "show your working" mode for when you're tuning the skill.

### 8.3 Machine-readable output for JobOS

A small JSON/YAML block at the end:

```yaml
cv_meta:
  company: …
  role: …
  archetype: cdd
  region: HK
  positioning_thesis: "…"
  proof_points: […]
  evidence_ids_used: [JUM-07, JUM-03, …]
  must_have_coverage: {strong: 4, partial: 1, none: 1}
  gaps: [{requirement: "LBO modelling", severity: high, suggestion: "…"}]
  version: v1
```

JobOS stores this with the application. Cover-letter, interview-prep and outreach skills can reuse the thesis and gaps, so they tell the same story.

---

## 9. Quality-control framework

There are two tiers. **Hard gates** must pass or the CV is fixed before output. **Soft scores** guide improvement and are reported only if they're low.

### 9.1 Hard gates (binary; any failure → fix → re-run)

| # | Gate | Check |
|---|---|---|
| H1 | **Traceability** | Every bullet, metric, skill and summary claim maps to an evidence ID or the fact sheet |
| H2 | **Fact-lock** | Titles, employers, dates and education match the fact sheet exactly |
| H3 | **Ownership** | Every verb is consistent with `ownership_level`. No seniority inflation. |
| H4 | **Metric provenance** | Every number is logged. Approximate numbers are marked and rounded down. Derived numbers are flagged. |
| H5 | **Confidentiality** | No client names or identifying details beyond the approved descriptors |
| H6 | **Hard boundaries** | None of your "never claim" items appear |
| H7 | **Banned language** | Zero banned words or phrases |
| H8 | **Length** | Within the page limit for the template |
| H9 | **ATS format** | Single column, standard headings, standard dates, no tables or graphics in the canonical output |
| H10 | **Must-have honesty** | No must-have "covered" by an unsupported claim |

### 9.2 Soft scoring (1–5; below 4 triggers a targeted rewrite)

| Dimension | Question |
|---|---|
| **Relevance** | Does ≥ 70% of the page directly support the thesis? |
| **Thesis landing** | In a 6-second scan (name, titles, first words of top bullets, summary), do the 3 proof points come through? |
| **Evidence strength** | Are the top 3 bullets per role T1–T2 wherever the bank allows? |
| **Clarity** | Could a non-specialist recruiter understand every bullet on one read? |
| **Concision** | Any bullet over 2 lines? Any filler words? |
| **Metrics** | Are numbers present where the bank has them, at a natural density? |
| **Seniority fit** | Does the CV read at the right level: not inflated, not underselling? |
| **Narrative** | Does the CV tell a coherent story that matches the core identity? |
| **Natural voice** | No structural AI tells; varied rhythm; passes the "say it out loud" test |
| **Keyword coverage** | Is each must-have term present in context, with none repeated more than twice? |
| **Consistency** | Passes the colleague test and the side-by-side test |
| **Recruiter readability** | Scannable, front-loaded, clean hierarchy |

### 9.3 Recruiter simulation (final pass)

Claude takes the likely first reader's role (HR screener / MBB recruiter / PE associate / startup founder) and answers:

1. In 6 seconds, who is this person?
2. Top 3 things I'd remember.
3. The one thing that makes me doubt the fit.
4. The one question I'd ask in the interview.

If (1) and (2) don't match the thesis, or (3) is fixable within the evidence, the CV goes back to step 8.

---

## 10. Skill vs JobOS

### 10.1 Responsibility split

| Concern | CV Skill | JobOS |
|---|---|---|
| Finding jobs | — | (User finds; JobOS takes input) |
| JD from URL: fetch, clean, normalise | — | ✅ |
| Company research / context | Consumes | ✅ Produces |
| Fit scoring and go/no-go | Consumes if provided | ✅ Owns |
| **Role interpretation for CV purposes** | ✅ | — |
| **Positioning thesis** | ✅ Produces | Stores and reuses downstream |
| **Evidence selection and CV writing** | ✅ | — |
| **CV review / bullet refinement** | ✅ | Triggers |
| **Integrity and QC** | ✅ | — |
| Evidence bank: *structure and entry quality* | ✅ (Bank mode writes entries) | — |
| Evidence bank: *storage, versioning, sharing* | — | ✅ |
| CV rendering (.docx / PDF, visual template) | — | ✅ |
| CV version storage and naming | — | ✅ |
| Application tracker and status | — | ✅ |
| Cover letters, outreach, interview prep | — | ✅ (other skills, reusing `cv_meta`) |
| Outcome analytics (which versions work) | — | ✅ Produces; skill consumes as a signal |
| Workflow orchestration (what happens next) | — | ✅ |

**Rule of thumb:** *the CV skill knows how to position and write about you. JobOS knows about jobs, files and workflow.*

### 10.2 Interface contract

**JobOS → Skill (input):** `jd_text`, `company`, `role_title`, `location/region`, `channel?`, `fit_analysis?`, `company_context?`, `user_steer?`, `length?`, `mode`.

**Skill → JobOS (output):** `cv_markdown`, `positioning_brief`, `tailoring_decisions`, `gaps`, `verification_questions`, `diff`, `cv_meta`.

A fixed contract lets JobOS change (a new UI, another model, a new renderer) without touching the skill, and the skill can be improved without breaking JobOS.

### 10.3 Where your candidate data lives (decision needed)

Your evidence bank is **shared infrastructure**. Cover letters, interview prep and outreach need the same facts. There are two options:

- **Option A (recommended):** the source of truth lives in the JobOS repo (`profiles/<user>/`: fact sheet, evidence bank, voice, feedback log), versioned in git. The skill references those files. For standalone use (claude.ai, outside JobOS), a small sync step copies them into the skill's `candidate/` folder.
- **Option B:** the data lives inside the skill folder only. Simpler, but other JobOS modules then have to read from inside the skill, or duplicate it.

---

## 11. Recommended SKILL.md structure

### 11.1 Package layout

```
cv-skill/
├── SKILL.md                      # ≤ ~300 lines: purpose, modes, non-negotiables, workflow, pointers
├── candidate/                    # PERSONAL, per user (synced from JobOS profiles/<user>/; empty in the shared package)
│   ├── profile.md                # fact sheet + core identity + signature strengths + hard boundaries
│   ├── evidence-bank.yaml        # claim units (§3 schema)
│   ├── voice-and-style.md        # preferred/disliked language, spelling, tone
│   ├── summary-variants.md       # approved summaries per archetype
│   └── feedback-log.md           # captured lessons, pending promotion
├── method/                       # GENERIC (reusable for anyone)
│   ├── decision-engine.md        # steps 0–11 in detail, requirement classes, evidence tiers, gap triage
│   ├── role-lenses/              # library: one file per role family (users pick theirs)
│   ├── regions/                  # library: one file per region's CV conventions
│   ├── onboarding.md             # §12.2 ingest → extract → interview → calibrate → baseline
│   ├── role-lenses.md            # (index of the library) one section per archetype: what readers screen for, signals, AI prominence,
│   │                             #   section order, vocabulary, common pitfalls
│   ├── bullet-rules.md           # §5.1–5.4 + summary rules
│   ├── ai-positioning.md         # §5.6
│   ├── anti-ai-writing.md        # §5.7 vocabulary + structural tells
│   ├── tailoring-rules.md        # §6 levers + distance limits
│   ├── integrity-rules.md        # §7 allowed/forbidden transforms + uncertainty protocol
│   ├── format-ats-regional.md    # §5.8 templates, ATS, HK/UAE/UK
│   └── qc-checklist.md           # §9 hard gates, soft scores, recruiter simulation
├── templates/
│   ├── cv-template.md            # canonical Markdown CV skeleton (+ variants)
│   └── output-report.md          # positioning brief / decisions / gaps / questions / cv_meta shape
└── examples/
    ├── bullets-before-after.md   # weak→strong pairs for a FICTIONAL persona
    └── example-profile/          # complete fictional profile, used for demos and evals
```

**Why this split:** method files rarely change, and changing them improves every CV. Candidate files change often and never require touching the method. Examples are the strongest style signal Claude gets, so they're kept as real, approved outputs rather than invented ones.

### 11.2 SKILL.md sections

```markdown
---
name: cv-skill
description: <when to trigger: JD shared, "tailor/review/improve my CV", "add this
             project to my bank"; what it produces>
---

# CV Skill

## 1. Purpose & scope            — objective (§1); what it does NOT do (defer to JobOS)
## 2. Non-negotiables            — top integrity rules, placed early so they carry the most weight:
                                   trace every claim · never inflate ownership · never invent
                                   metrics/skills · confidentiality · ask, don't guess
## 3. Modes                      — Onboarding / Tailor / Review / Refine / Bank: triggers + outputs
## 4. Inputs                     — required vs optional; missing-input protocol; files to load
## 5. Loading the candidate      — read settings.yaml + profile; never assume personal defaults
                                   (no personal data in SKILL.md itself)
## 6. Workflow (Tailor mode)     — the 12-step decision engine, one short paragraph per step,
                                   each pointing to its method file
## 7. Writing standard (summary) — the 8–10 rules that matter most + pointers to
                                   bullet-rules / ai-positioning / anti-ai-writing
## 8. Tailoring limits (summary) — core vs surface; levers; distance limits
## 9. Quality gates              — list hard gates H1–H10; "fix before output" rule
## 10. Output contract           — what's shown, in what order; cv_meta block;
                                   what stays internal; "show working" switch
## 11. Other modes               — short procedure for Onboarding, Review, Refine, Bank
## 12. Learning loop             — how edits become feedback-log entries → promotions
## 13. Reference index           — table: file → when to load it
```

**Why this order:** Claude pays the most attention to what comes early and to what's stated as a rule, so the non-negotiables come right after the purpose. The workflow is the backbone and the other sections support it. The reference index at the end tells Claude which file to open at which step, which keeps context small.

---

## 12. Multi-user design (added in v0.2)

**Goal:** anyone can use the system. The first user (Avani) is one profile, not the design target.

The layered design already separates the generic *method* from personal *candidate data*. Going multi-user means finishing that separation and adding onboarding.

### 12.1 What changes

| Area | v0.1 (one user) | v0.2 (anyone) |
|---|---|---|
| Candidate data | `profile/` | `profiles/<user>/`, copied from `profiles/_template/` |
| Personal defaults (length, spelling, regions, header fields) | Written into the rules | `settings.yaml` per user; the method reads settings and contains no one's defaults |
| Seniority | Assumes early-career | `seniority_band` (student → executive) drives length, summary style, verb ceilings and how much leadership evidence is expected |
| Role types | 7 hard-coded families | A **role-lens library** shipped with the skill. Users pick theirs in `settings.yaml`; new lenses can be added as files. |
| Regions | HK / UAE / UK | A **regional-conventions library** (one section per region). Users list the regions they apply to. |
| Banned / preferred language | One person's list | Default anti-AI list in the method **plus** per-user additions and exceptions in `voice-and-style.md` |
| AI positioning | Assumes an AI builder | `ai_experience_level` caps what can be claimed (none / uses / builds / advises) |
| Examples | The user's real bullets | Method examples use a **fictional persona**; real bullets stay in each user's own profile |
| First use | Manual setup | New **Onboarding mode** (below) |

### 12.2 New mode: Onboarding

This is the most important feature for a new user, because the quality of every CV depends on the evidence bank.

1. **Ingest:** read everything in `sources/` (CVs, project notes, LinkedIn export, performance reviews).
2. **Extract:** draft the fact sheet and one evidence entry per project or achievement.
3. **Interview:** for each entry, ask only what's missing: what the user personally owned, whether each number is exact or approximate, the outcome, and how the client may be named. Questions go in batches of 5–8, so it isn't an interrogation.
4. **Calibrate voice:** ask for 2–3 bullets the user likes and a few words they dislike; draft `voice-and-style.md`.
5. **Settings:** infer the seniority band and regions from the CV; confirm target role families.
6. **Baseline:** generate a general-purpose master CV from the bank for the user to review. Their edits seed `feedback-log.md`.

Onboarding can be resumed, so a user can stop midway and continue later.

### 12.3 Privacy

- Profiles hold personal data (contacts, visa status, client work). The public/shared repo ships **only** the method, the templates and a fictional example profile.
- Each user keeps their own profile in a **private** repo, a private fork, or outside git.
- The method never writes one user's data into shared files (feedback promotions go into that user's `voice-and-style.md`, never the shared method).

### 12.4 Distribution (decision needed)

| Option | What users do | Effort | Fit with "lightweight, portable" |
|---|---|---|---|
| **A. Template repo + Claude skill** (recommended first) | Fork or copy the repo, drop in their CV, run Onboarding in Claude | Low | Strong |
| **B. Installable skill only** | Install the skill; keep their profile in a folder they upload each time | Low | Strong, but no JobOS workflow |
| **C. Hosted web app** | Sign up, upload their CV, use a UI | High: accounts, storage, security, API costs | Weak for now |

Recommendation: build **A**, keep the skill usable on its own (so **B** comes free), and treat **C** as a later product decision once the method is proven.

## 13. Decisions

### Confirmed (2026-09-27)
- **Audience:** the system is for anyone, not one person (§12).
- **Data location:** each user's source of truth is `profiles/<user>/` in their JobOS repo, copied into the skill's `candidate/` folder.
- **First user's settings (Avani):** 1 page; British spelling; no photo or nationality; a phone number and visa line per location. These are stored as *her* settings, not as defaults in the method.

### Still open
1. **Distribution:** template repo + skill (recommended), skill only, or hosted web app (§12.4)?
2. **Privacy of your own profile:** if the JobOS repo will be public or shared, your own profile needs a private home (a private repo, or a private fork).
3. **Existing `cv-tailor` skill:** switch it off once the new skill passes testing?
4. **Seed material** for the first real profile (your CV and project log) and the voice questionnaire.

## 14. Proposed build sequence (after approval)

1. Write the generic `method/` files, including the role-lens and regional libraries and Onboarding.
2. Write SKILL.md (the thin orchestrator; no personal data).
3. Build a **fictional example profile** for demos and evals.
4. **Onboard the first real user** (Avani) through Onboarding mode. This doubles as the test of onboarding.
5. **Evaluate** against 5–6 real JDs across role families, for both the fictional and the real profile. Hard gates are checked automatically; soft scores by human review.
6. Iterate, then onboard 1–2 more people with different backgrounds (e.g. mid-career, a different industry) to prove the method generalises.
7. Wire into JobOS through the interface contract.
