# Changelog

Notable changes to the published dataset (`crosswalks.json`, `crosswalks.csv`).

**This dataset gained a version field in the 2026-08-17 release below**, and every entry
from that one forward names its version. Check which release you are holding:

```bash
jq -r '.meta.version' crosswalks.json
```

`meta.version` is a content digest, derived on every generator run. Equal versions mean the
same data; a different version means something changed. It carries **no order** — it cannot
tell you which of two releases is newer, and this file is where that lives.

**Every state published before 2026-08-17 is unidentifiable from inside the file.** Eleven
of them reached `main` with no version field — four on 2026-08-13 and four on 2026-07-23, so
a generation date would not have separated them either. For those, only the commit exists. For those, the `controlCount` /
mapping count / framework count in each heading below is the only release identifier there
is:

```bash
jq '.meta.controlCount' crosswalks.json
jq '[.controls[].crosswalks | to_entries[].value | length] | add' crosswalks.json
```

Entries are newest first. A **BREAKING** heading means an existing key or value changed or
disappeared — additions alone are never marked breaking.

---

## 2026-08-17 — 100 controls, 615 mappings, 21 frameworks — **BREAKING**

**Version:** `sha256-8beea155f2bc90a2525219e3086b5973cc775703291442085caf536e5273af9c`
— the first published state that has one.

Previous published state: **94 controls, 572 mappings, 21 frameworks** (`c6a0277`). This
entry describes that transition and no other. It is the first entry in this file, so
everything before it is commit history only.

### What changed, in total

| | |
|---|---|
| Controls added | 6 |
| Controls removed | 0 |
| Frameworks added or removed | 0 |
| Control–framework edges **removed entirely** | 4 |
| Control–framework edges **added** | 16 |
| Control–framework edges with **changed clause refs** | 64 |
| Edges (control × framework pairs) | 355 → 367 |
| Mappings | 572 → 615 |

Mappings per framework, previously published → now. The eleven frameworks not listed
(`ai-governance-essentials`, `amazon-child-directed`, `apple-kids`, `coppa`,
`esg-essentials`, `eu-ai-act`, `google-play-families`, `nist-csf`, `soc-2`, `sox`,
`us-employment-federal`) are unchanged.

| Framework key | Was | Now | |
|---|---|---|---|
| `cis-controls` | 28 | 37 | +9 |
| `gdpr` | 31 | 33 | +2 |
| `hipaa` | 16 | 24 | +8 |
| `iso-27001` | 32 | 34 | +2 |
| `iso-42001` | 35 | 37 | +2 |
| `iso-9001` | 21 | 30 | +9 |
| `nist-800-171` | 20 | 23 | +3 |
| `nist-800-53` | 53 | 55 | +2 |
| `nist-ai-rmf` | 11 | 15 | +4 |
| `pci-dss` | 27 | 29 | +2 |

**Every framework's count went up, and four of them still lost an edge outright.** These
are net figures. Do not read a rising count as "nothing was withdrawn here" — the four
withdrawals are listed below by name.

### BREAKING — four keys now return `undefined`, not `[]`

These four control–framework edges are **gone from the `crosswalks` map**, not emptied. A
consumer reading `control.crosswalks["gdpr"]` for `risk-assessment` now gets `undefined`,
not `[]`. Code that does `.length` on the result will throw; code that iterates will skip
it. In the CSV, the corresponding rows are simply absent.

| Control | Framework | Was | The duty is now carried by |
|---|---|---|---|
| `risk-assessment` | `gdpr` | `["Art.35"]` | `dpia-process` → `["Art.35(1)", "Art.35(7)"]` |
| `risk-assessment` | `iso-9001` | `["6.1"]` | `risk-opportunity-planning` → `["6.1.1", "6.1.2"]` |
| `risk-assessment` | `cis-controls` | `["18"]` | `penetration-testing` → `["18.1"…"18.5"]` |
| `security-awareness-training` | `iso-9001` | `["7.2", "7.3"]` | `7.3` → `management-system-awareness`; `7.2` was already on `competence-management` and still is |

**No clause lost coverage in this release** — but the key you look it up by moved in all
four cases. If you were reading GDPR Art.35, ISO 9001 6.1/7.3 or CIS 18 coverage off those
control keys, read it off the control keys in the right-hand column instead. The mapping is
equivalent or finer; the join key is not the same.

The withdrawals themselves were made because the edges did not hold. `risk-assessment` was
mapped to ISO 9001 clause 6.1 on a conflation of quality-management-system risk with
information-security risk, and to GDPR Art.35 from how Keel stores DPIA records rather than
from the Article's own duty. `security-awareness-training` × ISO 9001 `7.2` duplicated a
claim `competence-management` already made correctly.

### BREAKING — 64 edges re-pointed from framework headings to scored leaves

Sixty-four edges changed their clause refs. This is the large, mechanical half of the diff
and the part most likely to break you.

Before this release the dataset contained **83 clause references that pointed at a section
*header*** — a row with children — rather than at the specific requirement underneath it.
It now contains **zero**. Of those 83: 80 sat in 63 of the re-pointed edges, and 3 sat in
edges withdrawn outright (`Art.35`, ISO 9001 `6.1`, CIS `18`, above).

Representative examples:

| Control | Framework | Was | Now |
|---|---|---|---|
| `access-control-policy` | `gdpr` | `["Art.32"]` | `["Art.32(1)"]` |
| `asset-inventory` | `cis-controls` | `["1", "2"]` | `["1.1", "2.1"]` |
| `nonconformity-capa` | `iso-9001` | `["8.7", "10.2"]` | `["8.7.1", "8.7.2", "10.2.1", "10.2.2"]` |
| `ai-risk-management` | `nist-ai-rmf` | `["MAP-4", "MEASURE-1", "MANAGE-1"]` | `["MEASURE-1.1", "MANAGE-1.2", "MANAGE-1.3"]` |
| `individual-privacy` | `gdpr` | `["Art.12", "Art.15", "Art.16", "Art.17", "Art.21"]` | `["Art.12(1)", "Art.12(2)", "Art.15(1)", "Art.16", "Art.17(1)", "Art.21(1)"]` |

**This is breaking for you if you match clause refs as strings.** `"Art.32"` no longer
appears for these controls; `"Art.32(1)"` does. If you join this dataset to a framework
clause list, expect the join keys to have moved to a finer grain across GDPR, ISO 9001,
HIPAA, CIS Controls, NIST SP 800-171 and NIST AI RMF.

A header is not merely imprecise. In Keel a header row is structural and is not scored, so
an edge aimed at one displays as coverage while contributing nothing to a readiness score —
a claim the product's own numbers contradict. That is why these moved rather than being
left as harmless approximations.

### Reversed before publication — `security-awareness-training` × `hipaa`

**This is a value that moved and moved back, and it is recorded rather than netted out.**

| State | Value |
|---|---|
| Published `main` (94/572) | `["164.308(a)(5)"]` |
| Earlier revision of this pull request | `["164.308(a)(5)"]` — described in that revision as a *correction*, on a strict reading of "Security reminders" |
| **This release** | `["164.308(a)(5)(ii)(A)"]` |

An earlier draft of this changelog argued that `164.308(a)(5)(ii)(A)` could not be defended
and that the header `164.308(a)(5)` was the right target. **That argument is withdrawn.**
It was right about the standard's wording and wrong about everything else: it was the only
header-targeting edge left in the dataset, it left all four specifications under
`164.308(a)(5)` with no control mapped to them, and it produced exactly the
coverage-without-score contradiction described in the section above.

The full `164.308(a)(5)` family is now mapped at leaf grain, three of the four newly:

| Clause | Control |
|---|---|
| `164.308(a)(5)(ii)(A)` | `security-awareness-training` (reinstated) |
| `164.308(a)(5)(ii)(B)` | `malware-protection` (new edge) |
| `164.308(a)(5)(ii)(C)` | `logging-monitoring` (new edge) |
| `164.308(a)(5)(ii)(D)` | `credential-management` (new control) |

Net effect for anyone tracking only published releases: the value changed once, from
`["164.308(a)(5)"]` to `["164.308(a)(5)(ii)(A)"]`. It is one of the 64 re-pointed edges
counted above.

### Addition — `meta.version`, the one schema change here, and it is additive

`meta` gains a `version` key. Nothing was removed, renamed or retyped to make room for it;
the other seven keys are byte-for-byte where they were, and the `controls` array is
untouched by this part of the release.

```json
"meta": {
  "version": "sha256-8beea155f2bc90a2525219e3086b5973cc775703291442085caf536e5273af9c",
  "name": "Keel compliance crosswalks",
  …
}
```

It is a **content digest** of this exact published document, derived by the generator on
every run. Nobody increments it, so it cannot go stale. Equal versions mean the same data;
a different version means something in the document changed.

**It deliberately carries no order.** A digest cannot tell you whether the copy you hold is
older or newer than another — do not build a staleness check on it. This file is the
ordered record, and every entry from this one forward states its version.

**This release is the first identifiable published state.** The eleven before it have no
version, so a consumer holding one cannot name it from inside the file. That ordering is
worth stating plainly: the field arrives *in* the breaking release rather than after it,
which means the state that removes four keys and moves 64 join keys is pinnable, and every
unpinnable state is now behind you.

`crosswalks.csv` is unchanged by this addition and gains no version column — a sixth column
would break every CSV parser to carry a value the JSON already carries.

**Could an added key break you?** Only if you validate this document against a schema that
rejects unknown properties. **This dataset publishes no schema** — there is no JSON Schema,
Frictionless `datapackage.json` or OpenAPI document here or in the producer repo, and the
`## Schema (JSON)` section of [`README.md`](./README.md) is illustrative prose, not a
machine-readable contract. So nothing we ship forbids extra keys. But we cannot see your
validator: if you have written one of your own with `additionalProperties: false`, or a
strict deserializer in a typed language that errors on unknown fields (rather than ignoring
them, which most default to), this addition will fail it. That is the one way a purely
additive change bites, and it is worth thirty seconds of checking rather than an assumption.

### Addition — six new controls (additive, not breaking)

| Control | Crosswalks |
|---|---|
| `credential-management` — Credential management | `iso-27001`: `["A.5.17"]`, `pci-dss`: `["8.3"]`, `hipaa`: `["164.308(a)(5)(ii)(D)"]`, `cis-controls`: `["5.2"]`, `nist-800-171`: `["3.5.7"…"3.5.10"]`, `nist-800-53`: `["IA-5", "IA-5(1)"]` |
| `penetration-testing` — Penetration testing | `pci-dss`: `["11.4"]`, `cis-controls`: `["18.1"…"18.5"]` |
| `risk-opportunity-planning` — Risk and opportunity planning | `iso-9001`: `["6.1.1", "6.1.2"]`, `iso-42001`: `["6.1.1"]` |
| `management-system-awareness` — Management system awareness | `iso-9001`: `["7.3"]`, `iso-27001`: `["7.3"]`, `iso-42001`: `["7.3"]` |
| `records-of-processing` — Record of processing activities (RoPA) | `gdpr`: `["Art.30(1)"]` |
| `dpia-process` — Data protection impact assessment (DPIA) process | `gdpr`: `["Art.35(1)", "Art.35(7)"]` |

Four of these exist to carry duties that had fallen to **zero** coverage: ISO 9001 6.1.1,
6.1.2 and 7.3, and CIS Controls 18.1–18.5. The other two carry the GDPR Art.30 and Art.35
duties withdrawn from `data-classification` and `risk-assessment`, on controls that are
actually about them.

Fifteen of the sixteen added edges belong to these six controls. The sixteenth is
`malware-protection` × `hipaa` → `["164.308(a)(5)(ii)(B)"]`.

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
