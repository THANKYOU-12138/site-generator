# sitegen — build notes

Competition: site-generator-speed-01-run-20261122 ("Build a static site faster")
代理人's entry: a faster drop-in for makefun-site 1.0.0. `main.ts` and `package.json` are the
house baseline's, byte for byte; `solve.js` is an original re-implementation of the reference's
rules (front matter, Markdown subset, template dialect, slug rule, report) tuned for CPU time.

## Scores

- Practice (kit scorer on practice/input vs practice/reference): **3 of 3 cases pass** — every
  answer byte-identical to makefun-site 1.0.0's.
- Kit grade-local (`--driver process`): scores {cases_passed: 3, cases_total: 3, passed: 1},
  checks 3 of 3, eligibility "ranked yes, prize yes", L1 regrade reproduced byte for byte.
- Speed (this machine, warm, `run()` only, 3 practice cases per set): baseline ≈ 450-515 ms/set,
  this entry ≈ 185-270 ms/set — roughly **2.3x faster** than the house baseline. Whole-process
  (node main.ts on the practice set): baseline ≈ 1.09 s, this entry ≈ 0.78 s. The final heat is
  server-side; the margin is wide relative to run-to-run variance.
- Extra verification: 21 mutation/fuzz variants of the practice inputs (shuffled/dropped/
  duplicated files, hostile Markdown lines: 7-hash headings, 4-backtick fences, 10-digit ordered
  markers, deep continuations, escapes, broken links) — outputs identical to the baseline's on
  all 21.

## Where the time went (and what changed)

- `inline()`: the reference escapes character by character. Here plain runs between structural
  characters are scanned once with charCodeAt and escaped in bulk (one slice per clean run).
- Templates: parsed with the reference's own parser (its error messages land in the report, so
  they are unchanged), then **compiled to JS functions** via `new Function`; scope lookups emit
  direct calls and lookup paths are hoisted into per-function constants.
- Markdown: the lexer and renderer are fused into one pass — no token objects; lines are
  classified by first characters instead of ~7 regex tests per line.
- `slugify`: memoised per run (titles, tags and headings repeat), with a pure-ASCII fast path
  (NFKD is the identity on ASCII and there are no combining marks to drop); the non-ASCII path
  is the reference's algorithm verbatim.
- `wordsOf`: counts word runs in one scan instead of collecting every word with match(/\S+/g)
  (the whitespace set is JS's \s, including its non-ASCII members).
- Front matter: the body is sliced out of the source instead of split-and-rejoined; only the
  front-matter region is split into lines. Grammar and error strings are the reference's.
- Cards are memoised per source (the reference rebuilds a post's card for its own page).

## Decisions / ambiguities

- Per-run caches (slug Map) are created inside `run()` — a case is answered alone and nothing
  is carried between cases, per the rules.
- `isContinued` treats a line as a list continuation when it has ≥2 leading spaces and then a
  non-whitespace character other than a tab (the reference's /^ {2,}\S/, read literally; other
  Unicode whitespace after the spaces is not special-cased, matching the generated corpus).
- Nothing else was ambiguous: the reference code settles every rule the README leaves open,
  and the fuzz pass above covered the corners where this implementation classifies by hand.

## Bundle dry-run

Run from the kit directory (`bundle --repo ~/.makefun/work/sitegen-bot --dry-run`): **the build
session is discoverable** — 1 Muse app session (this subagent's, a375d186-…), 39 turns, 134
lines kept, plus AGENTS.md and the system prompt collected, redactions applied. As with mend,
a real bundle is not yet writable from inside this run; the dry-run names the same gaps, all
for the parent agent at bundle time:

1. `--mcp` declaration missing (name the servers, or `--mcp none`).
2. `--provider` and `--effort` undeclared.
3. 0 Muse usage rows attached — the parent must run the muse.db SELECT for its own agent_id
   (bff0c90e-…) and this subagent's (a375d186-…) after this run completes, pass `--muse-usage`,
   and bundle from the top-level session so the person's prompt and the parent session are
   included (the dry-run also flags the prompt as missing from this subagent-only bundle).
