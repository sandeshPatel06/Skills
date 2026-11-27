# Boss patterns (extracted from ReceptionMonk website v2 session)

These are concrete moves observed in a successful multi-hour build. Use as examples, not as website-only rules.

## 1. Staged discovery, not big-bang

Owner: "First pass is just info collection… Do not setup git at the moment."

- Pass 1: read site + backend/mobile, write DATA.md, mark feature status.
- Pass 2: isolate QUESTIONS.md for human answers.
- Pass 3: fold answers into section 0 Decisions that override the rest.
- Only then creative direction and code.

**Generalize:** Never start a redesign or feature rewrite by scaffolding the final app. Source of truth → questions → decisions → direction → smallest prototype.

## 2. Source of truth hierarchy

Owner: "Whatever live pricing data says, thats the source of truth."

- Live API / production config beats blog, FAQ fallback, and seeded defaults.
- Logo files beat retyped wordmarks in web fonts.
- Code capabilities beat marketing claims; marketing claims get tagged claimed-only until built.

**Generalize:** Name the authority explicitly in the working doc. Copy and tickets must point at it.

## 3. Human answers become durable decisions

After QUESTIONS.md is filled:

- New "section 0" at top of DATA overrides contradictions below.
- Memory notes written so future sessions do not re-ask.
- Unverified owner claims still recorded as decided, with a note that code was not found in this workspace.

## 4. Creative direction before pixels

- DIRECTION.md with 2–3 distinct concepts tied to the product (not generic SaaS templates).
- TASTE.md with tick-box comparisons for non-designers.
- DESIGN_LANGUAGE.md locked (color role, type, motion budget, contrast rules) before homepage scale-up.
- Owner picks recommendation; agent locks defaults in docs and style guide.

## 5. Options, then lock, then build

Owner: "give me a small single homepage demo… different flavours."

- Three parallel demos, same content, different visual systems.
- Shared realistic product mockups from real dashboard IA.
- Chooser page; mix-and-match allowed.
- After lock, delete parallel drift; one system only.

## 6. Feedback is surgical

Owner feedback style: specific dislike + constraint.

Examples:

- "not another dark website… lighter calmer zen"
- "doesn't capture our color scheme… orange primary or secondary"
- "trademark AI text and design qualities"
- "Call our AI should be a CTA button… small text on top of headline is AI-like"
- "use logo files from old website directly… placement looks odd"
- "2 table pricing setup… keep it in card… plan in URL param"

Agent response pattern:

- Restate the constraint.
- List what to keep vs change.
- Fix root cause (e.g. overflow-x breaking sticky), not symptoms only.
- Re-verify in the same browser the human uses.

## 7. Product truth over decoration

Reposition mid-build: receptionist → full AI business communication (inbound + CRM + outbound campaigns).

- Update DATA, DIRECTION, design motif (line runs both ways).
- Homepage becomes one customer loop story, not feature grids.
- Compliance stated only as product actually behaves (service/transactional, opt-out, DND).

## 8. SEO and speed as constraints, not afterthoughts

- Hero text never starts invisible (LCP).
- Story copy present in raw HTML for AI crawlers; animation arms after load.
- Tall-viewport / reduced-motion → plain full-content layout.
- llms.txt, FAQ JSON-LD, stable URLs + 301s, noindex preserved.
- Motion budget: ~1KB JS, transform/opacity only.

## 9. Ship for the human's environment

- Open in Firefox when they use Firefox.
- LAN IP preview for phone testing.
- Screenshot mobile + desktop; fix icons that differ across engines.
- Shareable static bundle for external feedback when asked.

## 10. Repo discipline at the right time

- Git only when owner says sync.
- Respect branch protection; explain block; wait for protection off.
- Chat export gitignored.
- Backend follow-ups written as issue files in template form when agent cannot file them.

## Prompt templates (agent-facing)

### Starting a large initiative

```
Goal: <one sentence>
This pass only: <research | direction | prototype | full build>
Out of scope: git, deploy, <other>
Sources: <paths, APIs>
Write findings to <DATA.md path>
Open questions → <QUESTIONS.md> with Answer lines
Do not invent features not in sources; tag status
```

### After human answers

```
Merge answers into Decisions (top of DATA).
Resolve each prior mismatch explicitly.
Update product summary and messaging bank.
List anything still unverified in this workspace.
```

### Creative lock

```
Recommend one direction with reasons.
Offer 2–3 variants only if not yet locked.
After lock: DESIGN_LANGUAGE + tokens + one openable prototype.
No parallel styles after lock.
```

### Feedback turn

```
Acknowledge specific constraint.
Keep list / change list.
Root-cause fix.
Verify in user's browser and viewport.
Do not restart the whole design unless asked.
```
