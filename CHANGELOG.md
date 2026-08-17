# Changelog

Notable changes to the published dataset (`crosswalks.json`, `crosswalks.csv`).

**This dataset carries no version field.** `meta` has `name`, `description`, `source`,
`license`, `attribution`, `controlCount` and `frameworks` — and nothing a consumer can pin
to or compare. Until that changes, the headings below are the only release identifier
there is, so each one states the exact `controlCount` / mapping count / framework count it
describes. Check which one you are holding:

```bash
jq '.meta.controlCount' crosswalks.json
jq '[.controls[].crosswalks | to_entries[].value | length] | add' crosswalks.json
```

Entries are newest first. A **BREAKING** heading means an existing key or value changed or
disappeared — additions alone are never marked breaking.

---

## 2026-08-17 — 96 controls, 591 mappings, 21 frameworks — **BREAKING**

Previous published state: 94 controls, 572 mappings, 21 frameworks.

This entry covers **three separate changes** to Keel's control library that are published
here together. They were produced independently and should not be read as one act — a
mapping that was withdrawn because it was **wrong** is a different event from a mapping
that was **re-pointed** to a finer clause, and both are in this diff. The three parts are
separated below.

### What changed, in total

| | |
|---|---|
| Controls added | 2 (`records-of-processing`, `dpia-process`) |
| Controls removed | 0 |
| Frameworks added or removed | 0 |
| Control–framework edges **removed entirely** | 4 |
| Control–framework edges with **changed clause refs** | 62 |
| Mappings | 572 → 591 |

Mappings per framework, previously published → now. Frameworks not listed are unchanged.

| Framework key | Was | Now | |
|---|---|---|---|
| `cis-controls` | 28 | 31 | +3 |
| `gdpr` | 31 | 33 | +2 |
| `hipaa` | 16 | 21 | +5 |
| `iso-9001` | 21 | 27 | +6 |
| `nist-800-171` | 20 | 19 | −1 |
| `nist-ai-rmf` | 11 | 15 | +4 |

**A framework whose count went up still lost mappings.** These are net figures across all
three changes below; ISO 9001 gains six on balance while two of its edges disappeared
entirely. Do not read a rising count as "nothing was withdrawn here".

### BREAKING — four keys now return `undefined`, not `[]`

These four control–framework edges are **gone from the `crosswalks` map**, not emptied. A
consumer reading `control.crosswalks["gdpr"]` for `risk-assessment` now gets `undefined`,
not `[]`. Code that does `.length` on the result will throw; code that iterates will skip
it. In the CSV, the corresponding rows are simply absent.

| Control | Framework | Was | Reason |
|---|---|---|---|
| `risk-assessment` | `gdpr` | `["Art.35"]` | withdrawn — see *Correction* |
| `risk-assessment` | `iso-9001` | `["6.1"]` | withdrawn — see *Correction* |
| `security-awareness-training` | `iso-9001` | `["7.2", "7.3"]` | withdrawn — see *Correction* |
| `risk-assessment` | `cis-controls` | `["18"]` | dropped — see *Refinement* |

The first three were removed because **they were wrong**, not because they were pruned for
tidiness. If you asserted those mappings, the assertion was on a claim this dataset should
not have been making. The fourth pointed at a CIS Controls top-level heading with no leaf
safeguard that could be argued for it, and a wrong leaf is worse than a header.

---

### 1. Correction — twelve clause references we could not defend

Mappings withdrawn because they did not hold against the clause text. Eleven references
were withdrawn outright and one was re-pointed. This is the part of the diff that is a
**correction to a published claim**, and it is the reason this entry is marked breaking.

Net effect visible against the previously published files:

| Control | Framework | Was | Now |
|---|---|---|---|
| `risk-assessment` | `gdpr` | `["Art.35"]` | *(removed)* |
| `risk-assessment` | `iso-9001` | `["6.1"]` | *(removed)* |
| `security-awareness-training` | `iso-9001` | `["7.2", "7.3"]` | *(removed)* |
| `access-control-policy` | `cis-controls` | `["5", "6"]` | `["6.8"]` |
| `ai-risk-management` | `nist-ai-rmf` | `["MAP-4", "MEASURE-1", "MANAGE-1"]` | `["MEASURE-1.1", "MANAGE-1.2", "MANAGE-1.3"]` |
| `data-classification` | `gdpr` | `["Art.5", "Art.30"]` | `["Art.5(1)"]` |
| `data-retention-disposal` | `gdpr` | `["Art.5", "Art.17"]` | `["Art.5(1)"]` |
| `malware-protection` | `cis-controls` | `["9", "10"]` | `["10.1", "10.2"]` |

A ninth edge, `security-awareness-training` × `hipaa`, was refined and then corrected back
to `["164.308(a)(5)"]` — its published value is unchanged, and it is listed here only so
the count of nine corrected edges reconciles.

What was wrong, by class:

- **ISO 9001 clause 6.1 is not information-security risk.** `risk-assessment` and
  `security-awareness-training` were mapped to quality-management-system clauses on a
  conflation between QMS risk and security risk.
- **A HIPAA "training" specification that is actually security reminders**, not a training
  programme.
- **Two mappings justified by an internal artifact model rather than by the standard.** An
  earlier pass argued `risk-assessment` → GDPR Art.35 and `data-classification` → Art.30
  from how Keel stores DPIA and RoPA records. Those duties are now carried by the two new
  controls below, where they belong, and the borrowed mappings are gone.
- Four further minor drops of the same character.

Coverage falls in GDPR, ISO 9001, CIS Controls and NIST AI RMF as a result of this part,
which is the correct direction: these were claims we could not defend. The net per-framework
table above nets this loss against the refinement in part 2, so it does not show up there.

### 2. Refinement — 57 further edges re-pointed from framework headings to scored leaves

Separate from the correction, and **not** a statement that the previous mappings were
false. These edges pointed at a *heading* — a row with children — rather than at the
specific requirement underneath it. For example `access-control-policy` × `gdpr` moves from
`"Art.32"` to `"Art.32(1)"`, and `asset-inventory` × `cis-controls` from `["1", "2"]` to
`["1.1", "2.1"]`.

Every re-pointing was argued against the target leaf individually. Where no leaf could be
argued, the edge was dropped rather than pointed at a plausible-looking child — that is the
`risk-assessment` × `cis-controls` removal above.

**This is still breaking for you if you match clause refs as strings.** `"Art.32"` no
longer appears for these controls; `"Art.32(1)"` does. If you join this dataset to a
framework clause list, expect the join keys to have moved to a finer grain across GDPR,
ISO 9001, HIPAA, CIS Controls, NIST 800-171 and NIST AI RMF.

This work predates the correction above. It was produced first and is published here in the
same commit only because the two were released together.

### 3. Addition — two GDPR controls (additive, not breaking)

| Control | Crosswalks |
|---|---|
| `records-of-processing` — Record of processing activities (RoPA) | `gdpr`: `["Art.30(1)"]` |
| `dpia-process` — Data protection impact assessment (DPIA) process | `gdpr`: `["Art.35(1)", "Art.35(7)"]` |

These carry the Art.30 and Art.35 duties that the *Correction* section removed from
`data-classification` and `risk-assessment`. The duties did not leave the dataset; they
moved to controls that are actually about them. **If you were reading Art.30 or Art.35
coverage off the old edges, read it off these two control keys instead** — the coverage is
equivalent or finer, but the key you look it up by has changed.

### Diffing this yourself

```bash
git diff HEAD~1 -- crosswalks.csv | grep '^-[^-]'   # every mapping that went away
git diff HEAD~1 -- crosswalks.csv | grep '^+[^+]'   # every mapping that arrived
```

---

## Before this file existed

This changelog starts on 2026-08-17. Earlier changes are in the commit history; the sync
commits carry the control and mapping counts in their subject lines, and the producer
commit SHA in the body.

```bash
git log --oneline -- crosswalks.json
```

Note that the automated sync (`sync-open-data`, in the producer repo) writes only
`crosswalks.json` and `crosswalks.csv`. It does **not** write this file, so an entry here
is added by hand and a sync can land without one.
