---
name: doc-align
description: Use when the user wants CLAUDE.md, wiki, CONTEXT.md, memory or other doc files checked for correct and current facts, or when docs may have gone stale.
---

# doc-align

Agents trust doc files. A wrong fact in a doc gives wrong answers. Find these facts. Fix only what the user approves.

Argument: repo path (default: current directory). Never read `.env` or credential files.

## Phase 1: Map
1. Find `CLAUDE.md`, `AGENTS.md`, `CONTEXT.md`, `README*`, `docs/`, wiki folders and the project memory folder.
2. Follow pointers in these files. Read the repo's own "where things go" rules.
3. If you find no doc file, say "nothing to audit" and stop.
4. Show the map: file and purpose. Wait for user corrections.

## Phase 2: Mechanical checks
Use Grep, Glob and git. Cite `file:line` for each finding.
- Missing paths.
- Missing hashes: run `git cat-file -e <hash>` in the repo that owns the claim. No git: label "unverified".
- `verified:` date or file age over 60 days.
- File over 1,500 words.
- `@` import of a large file.
- Same fact with different values in two files.
- Orphan: no other doc links to the file.
- Fact in the wrong place, by the repo's own rules.
- Secret-like strings (tokens, keys, passwords). Report file:line and kind only. NEVER print the value.

## Phase 3: Claim checks
Start one subagent per doc group. Each subagent compares claims with code and git history. Each finding needs `file:line` evidence.
Label a claim you cannot verify "unverified". Never label it "error".
Claims about people, customers, decisions or plans that no file or git history can prove (for example "the customer prefers X"): give each one its own row, label "unverified", and propose "confirm with the owner".
Keep doc-vs-code findings apart from other code remarks. Report only doc facts.

## Phase 4: Report and approve
1. Print one table, ranked by risk: file, line, problem, evidence, proposed fix.
2. Ask which fixes to apply: one by one or as a group.
3. Edit ONLY approved items. Keep the file encoding (UTF-8 with BOM, Hebrew).
4. Follow the user's CLAUDE.md for branches and worktrees.

## Common mistakes
| Mistake | Rule |
|---|---|
| Treat an orphan doc or README as true | Check if another doc links to it. Report orphans. |
| Check code against docs only | Also compare docs with each other: values, placement, secrets. |
| Skip the secret scan | Scan every doc. Report file:line and kind. Never the value. |
| Start the audit with no map | Show the map. Wait for corrections. |
| Give a plain list | Give the ranked table with a proposed fix per row. |
| Edit as you go | Edit only after approval. |
| Write "unverifiable" in prose | Use the label "unverified". |
| Skip a claim about a person or customer because it is not code | It gets its own "unverified" row. |
| Add unused deps or style remarks | Report doc facts only. |
