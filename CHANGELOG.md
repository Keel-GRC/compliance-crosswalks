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
a generation date would not have separated them either. For those, only the commit exists,
and the `controlCount` / mapping count / framework count in each heading below is the only
release identifier there is:

```bash
jq '.meta.controlCount' crosswalks.json
jq '[.controls[].crosswalks | to_entries[].value | length] | add' crosswalks.json
```

Entries are newest first. A **BREAKING** heading means an existing key or value changed or
disappeared — additions alone are never marked breaking.

---

## 2026-08-27 — 274 controls, 1621 mappings, 21 frameworks — **BREAKING**

**Version:** `sha256-ca3d7036612697061adace59787e549ad43a8c574b5d727ed22c370687a9b101`

Previous **published** state: **274 controls, 1621 mappings, 21 frameworks**
(`sha256-af054babc9b9858b4db1f00a02939d469703738591c6344c90c19c081d636ffa`, 2026-08-23).
The entry directly below this one, `sha256-27b58db1…`, never reached `main`; its single
change is carried in this release. Cumulative figures against what you are actually holding
are at the end of this entry.

**This entry landed ahead of the data.** This file is written by hand; the dataset reaches
this repository in a separate sync commit and `keelgrc.com/open-data/` in a separate site
deploy. If `jq -r '.meta.version' crosswalks.json` does not return `sha256-ca3d7036…`, the
copy you are holding predates this entry.

### What changed, in total

| | |
|---|---|
| Controls added | 0 |
| Controls removed | 0 |
| Frameworks added or removed | 0 |
| Control–framework edges added or removed | 0 |
| Clause references added, withdrawn or re-pointed | 0 |
| Control **names** changed | 3 |
| Control descriptions reworded | 27 |
| Edges (control × framework pairs) | 726 → 726 |
| Mappings | 1621 → 1621 |
| CSV rows withdrawn / arrived | 0 / 0 |
| CSV rows whose `control_name` changed | 31 of 1621 |

### Why this is BREAKING, and for whom

Only display text moved. But `control_name` is **column 2 of `crosswalks.csv`**, and 31 of
its 1621 rows now carry a different value.

- **A CSV consumer that joins, pivots, groups or filters on `control_name` breaks**, as
  does anything that pinned a control name as a label or a dictionary key.
- **A JSON consumer keying on `control.key` does not.** No key, type, edge or clause
  reference changed, and every control is present in the same order.

The rule this file states is that a BREAKING heading means an existing key or value changed
or disappeared. Values changed, so the flag is on. Under-declaring is the only direction
that costs a consumer anything.

### BREAKING — three control names

| Control key | Was | Now | CSV rows |
|---|---|---|---|
| `child-data-minimization` | Minimised collection in children’s activities | Minimized collection in children’s activities | 8 |
| `childrens-ads-monetization` | Children’s advertising & monetisation controls | Children’s advertising & monetization controls | 19 |
| `special-category-data-handling` | Special category and criminal offence data | Special category and criminal offense data | 4 |

Where those 31 rows sit, by framework:

| Control key | Frameworks |
|---|---|
| `child-data-minimization` | `google-play-families` 5, `apple-kids` 1, `coppa` 1, `gdpr` 1 |
| `childrens-ads-monetization` | `google-play-families` 13, `apple-kids` 3, `amazon-child-directed` 2, `coppa` 1 |
| `special-category-data-handling` | `gdpr` 4 |

### What this release is — US spelling in Keel's own words

Every change is a word-for-word substitution of a British spelling with its US form. No
sentence gained or lost a word: across the 3 names and 27 descriptions there are **32
substitutions**, and every field is the same length in words as before.

| Was | Now | Occurrences |
|---|---|---|
| judgement | judgment | 9 |
| offence / offences | offense / offenses | 8 |
| enquiries | inquiries | 2 |
| enrolment | enrollment | 2 |
| licences | licenses | 2 |
| monetisation | monetization | 2 |
| minimisation, Minimised | minimization, Minimized | 2 |
| prioritised, practised, authorised, analyses, neighbour | prioritized, practiced, authorized, analyzes, neighbor | 5 |

**Clause references and quoted requirement text were deliberately not touched.** ISO and
GDPR are published in British English, so a British spelling inside a quotation of a
standard is correct as published and rewriting it would falsify the quotation. This pass
changed only text written in Keel's own voice. Every `clause_ref` value is byte-identical.

### The 27 descriptions

`ai-biometric-emotion-notice`, `ai-independent-expert-review`, `ai-policy`,
`ai-prohibited-practices-screening`, `breach-notification`, `capacity-management`,
`childrens-ads-monetization`, `childrens-privacy-program`, `data-classification`,
`dpia-process`, `dpo-designation`, `environmental-obligations-register`, `eu-representative`,
`grievance-mechanism`, `hr-security`, `incident-response`, `internal-audit-program`,
`management-system-communication`, `penetration-testing`, `personal-data-use-limitation`,
`privacy-notice`, `security-performance-measurement`, `security-testing-in-development`,
`special-category-data-handling`, `storage-media-management`, `user-lifecycle`,
`web-filtering`.

`crosswalks.csv` does not carry descriptions, so these 27 are invisible to a CSV consumer.

### Cumulative, from the state actually on `main`

Against `sha256-af054bab…` (2026-08-23), this release and the unpublished one below it
together change **3 control names and 28 descriptions**, and **31 of 1621 CSV rows** in
`control_name`. Controls, frameworks, edges, clause references and row identity are
unchanged: 274 / 21 / 726 / 1621.

---

## 2026-08-26 — 274 controls, 1621 mappings, 21 frameworks — additive — **not published here**

**Version:** `sha256-27b58db157d87a6404b8f4485b90540e4712b2db090795a6cf5409add66df5a3`

**This state never reached this repository's `main`.** It was generated and committed in
the producer repo on 2026-08-26 and never published here; why the automated sync did not
publish it is being diagnosed separately. This repository therefore sat at the 2026-08-23
state while a newer one existed, and nothing reported that.

**If you take the dataset from `keelgrc.com/open-data/` rather than from this repository,
you may be holding this state.** The two channels are generated from the same source in the
same run but published by different mechanisms, and when the sync to this repository does
not fire they diverge. That is what happened here, and it is why this entry exists at all —
the change below is real, it is downloadable from the site, and skipping it would leave the
ordered record wrong for half the audience.

Previous published state: `sha256-af054bab…` (2026-08-23).

### What changed, in total

| | |
|---|---|
| Controls added or removed | 0 |
| Frameworks added or removed | 0 |
| Control–framework edges added or removed | 0 |
| Clause references added, withdrawn or re-pointed | 0 |
| Control **names** changed | 0 |
| Control descriptions reworded | 1 |
| `crosswalks.csv` | byte-identical |

One description, on `childrens-social-safety`, re-authored against Google Play's live
Families policy wording after the policy it was written from was replaced:

> **Was:** … Apps whose main focus is chatting with strangers do not target children at all.
>
> **Now:** … Apps whose main focus is chatting with people the user does not know, or
> chatting with people anonymously, do not target children at all.

The clause references on that control did not move.

---

## 2026-08-23 — 274 controls, 1621 mappings, 21 frameworks — **BREAKING**

**Version:** `sha256-af054babc9b9858b4db1f00a02939d469703738591c6344c90c19c081d636ffa`
— the state currently on `main`, commit `aa2cf66`.

**This release renamed two controls and shipped with no changelog entry and no breaking
flag.** It went out on 2026-08-23 and nothing was said. This entry is written after the
fact, on 2026-08-27; it is a correction of the record, not a note filed at the time.

Previous published state: **274 controls, 1621 mappings, 21 frameworks**
(`sha256-438d65c19447e920607cc7a202b7eea552477880bd5c14bf4d8f6229685f3d25`, 2026-08-22,
commit `4bc17b5`) — **which has no entry either; see the stub below it.**

### What changed, in total

| | |
|---|---|
| Controls added | 0 |
| Controls removed | 0 |
| Frameworks added or removed | 0 |
| Control–framework edges added or removed | 0 |
| Clause references added, withdrawn or re-pointed | 0 |
| Control **names** changed | 2 |
| Control descriptions reworded | 4 |
| Edges (control × framework pairs) | 726 → 726 |
| Mappings | 1621 → 1621 |
| CSV rows withdrawn / arrived | 0 / 0 |
| CSV rows whose `control_name` changed | 14 of 1621 |

### BREAKING — two control names changed, unannounced

| Control key | Was | Now | CSV rows |
|---|---|---|---|
| `penetration-testing` | Penetration testing programme | Penetration testing program | 8 |
| `childrens-privacy-program` | Children’s online privacy programme | Children’s online privacy program | 6 |

The 8 `penetration-testing` rows are `cis-controls` 6, `nist-csf` 1, `pci-dss` 1. The 6
`childrens-privacy-program` rows are `amazon-child-directed` 2, `google-play-families` 2,
`apple-kids` 1, `coppa` 1.

By this file's own rule this was breaking on the day it shipped and should have carried the
flag then. A CSV consumer joining on `control_name` for either control broke on 2026-08-23
with no warning and no way to find out from here why. A JSON consumer keying on
`control.key` was unaffected — both keys are unchanged, as is every edge and clause
reference.

### The four descriptions

`information-security-policy`, `penetration-testing`, `security-privacy-officers` and
`childrens-ads-monetization`.

Every change in this release, in the names and the descriptions alike, is the single
substitution `programme`/`programmes` → `program`/`programs`: 2 in the names and 5 across
the four descriptions, with no other word added, removed or reordered.

### Nothing else moved

274 control keys, in the same order. `meta` carries the same eight keys. The 21
`meta.frameworks` entries are identical, key and display name. Every control object still
has exactly `key`, `name`, `description`, `crosswalks`. The CSV header and column order are
unchanged, and no row was added or withdrawn.

---

## 2026-08-22 — 274 controls, 1621 mappings, 21 frameworks — **BREAKING** — write-up outstanding

**Version:** `sha256-438d65c19447e920607cc7a202b7eea552477880bd5c14bf4d8f6229685f3d25`,
commit `4bc17b5`.

**This release has no write-up. This stub exists so the gap is visible rather than
implied.** It is by far the largest change this dataset has had — it more than doubled the
control count — it withdrew a published CSV row, and it went out on 2026-08-22 unannounced.
The counted figures below are derived from the two published files; the narrative of what
the 111 re-pointed edges did is not established here.

Previous published state: **123 controls, 680 mappings, 21 frameworks**
(`sha256-0c4035fb…`, 2026-08-18 — the entry below).

| | |
|---|---|
| Controls added | 151 |
| Controls removed | 0 |
| Frameworks added or removed | 0 |
| Control–framework edges added | 89 |
| Control–framework edges removed | 0 |
| Edges with **changed clause refs** | 111 |
| Control descriptions reworded | 72 |
| Control **names** changed | 0 |
| Edges (control × framework pairs) | 389 → 726 |
| Mappings | 680 → 1621 |
| CSV rows withdrawn / arrived | 1 / 942 |

The one withdrawn row is `product-service-safety`, `iso-9001`, `8.3.5`. The clause was not
dropped from the dataset — it moved to `design-development-outputs` — but a consumer
reading `product-service-safety` → `iso-9001` sees `["8.5.5"]` where it saw
`["8.3.5","8.5.5"]`, which is a value that changed.

To diff it yourself:

```bash
git diff f81f464 4bc17b5 -- crosswalks.csv
```

---

## 2026-08-18 — 123 controls, 680 mappings, 21 frameworks — additive

**Version:** `sha256-0c4035fbf3a780b06f32d1049236fbdb83f06a11d522110101cf1303d4b9a348`

Previous published state: **110 controls, 657 mappings, 21 frameworks**
(`sha256-d1cdf31e736bbe0eb056958b01e473cd98a074772ab1fefb5366d29955d5606a`, 2026-08-17).

**Nothing was removed, renamed, retyped or re-pointed.** Every key that existed still
exists, with the same type; every clause reference published on 2026-08-17 is still
published, on the same control, spelled the same way. **Zero CSV rows were withdrawn.** If
you pinned against the previous version, this release adds rows and adds keys and takes
nothing away — the opposite of the release below it, which is why the check is stated
rather than assumed.

### What changed, in total

| | |
|---|---|
| Controls added | 13 |
| Controls removed | 0 |
| Frameworks added or removed | 0 |
| Control–framework edges **removed entirely** | 0 |
| Clause references **withdrawn** from an existing key | 0 |
| Clause references **re-pointed** | 0 |
| Control–framework edges added | 19 (14 on the new controls, 5 on existing ones) |
| Control descriptions reworded | 7 |
| Control **names** changed | 0 |
| Edges (control × framework pairs) | 370 → 389 |
| Mappings | 657 → 680 |
| CSV rows withdrawn / arrived | 0 / 23 |

Mappings per framework, previously published → now. **Nineteen of the twenty-one
frameworks are byte-for-byte unchanged**; only these two moved, and both only upward.

| Framework key | Was | Now | |
|---|---|---|---|
| `iso-27001` | 36 | 58 | +22 |
| `iso-9001` | 29 | 30 | +1 |

Controls mapping to each: `iso-27001` 29 → 47, `iso-9001` 14 → 15.

### What this release is — ISO/IEC 27001's management clauses

The previous dataset carried **35 ISO/IEC 27001 Annex A references and exactly one
reference to clauses 4–10** — the management-system requirements that establish, run and
improve the ISMS, as opposed to the Annex A control catalogue. Twenty-two of those twenty-three
scored requirements had no control mapped to them. They now all do:

| Clause 4–10 references carried | Was | Now |
|---|---|---|
| ISO/IEC 27001 clauses 4–10 | 1 (`7.3`) | 23 (`4.1`–`10.2`) |
| ISO/IEC 27001 Annex A | 35 | 35 |

Thirteen of the twenty-three were closed by a **new control**, each mapping to one
management-system requirement:

| New control key | Maps to |
|---|---|
| `organizational-context` | `iso-27001` `4.1` |
| `interested-parties-requirements` | `iso-27001` `4.2` |
| `management-system-scope` | `iso-27001` `4.3` |
| `management-system-processes` | `iso-27001` `4.4` |
| `leadership-commitment` | `iso-27001` `5.1` |
| `security-objectives` | `iso-27001` `6.2` |
| `management-system-change-planning` | `iso-27001` `6.3`, `iso-9001` `6.3` |
| `management-system-resources` | `iso-27001` `7.1` |
| `management-system-communication` | `iso-27001` `7.4` |
| `operational-planning-control` | `iso-27001` `8.1` |
| `risk-treatment-plan` | `iso-27001` `8.3` |
| `security-performance-measurement` | `iso-27001` `9.1` |
| `continual-improvement` | `iso-27001` `10.1` |

The remaining nine were closed on controls you already have, by adding a key or a reference
— never by moving one (`7.3` is the twenty-third, and was already carried by
`management-system-awareness`):

| Control | Change |
|---|---|
| `risk-assessment` | gains `iso-27001` → `["6.1","8.2"]` (the key did not exist) |
| `security-privacy-officers` | gains `iso-27001` → `["5.3"]` (the key did not exist) |
| `competence-management` | gains `iso-27001` → `["7.2"]` (the key did not exist) |
| `management-review` | gains `iso-27001` → `["9.3"]` (the key did not exist) |
| `nonconformity-capa` | gains `iso-27001` → `["10.2"]` (the key did not exist) |
| `information-security-policy` | `iso-27001` gains `5.2` |
| `document-control` | `iso-27001` gains `7.5` |
| `internal-audit-program` | `iso-27001` gains `9.2` |

Five of those keys are *new keys on existing controls*: a consumer that cached
`control.crosswalks` for `risk-assessment` and saw no `iso-27001` entry will now see one.
That is an addition, not a change of shape — nothing that returned an array returns
anything else.

**7 control descriptions were reworded**, to state the management-system duty the control
now carries: `information-security-policy`, `risk-assessment`, `security-privacy-officers`,
`competence-management`, `internal-audit-program`, `management-review`,
`nonconformity-capa`. No key, name or type changed, so this is not breaking; but if you
display or index `description`, the text is not the text you had.

### One requirement the last release left uncovered is now covered — by a new control, not a near-miss

The 2026-08-17 entry published a table of requirements that lost their last control and
were deliberately left uncovered. **One of them is covered again: `iso-9001` `6.3`.**

It is not the withdrawn mapping coming back. `change-management` — the control that used to
claim it, and whose subject is IT change control over systems and software — still does not
claim it, and its `iso-9001` key is still absent. `6.3` is now carried by the new
`management-system-change-planning`, whose subject is planned change to the management
system itself. The withdrawal note said this leaf would stay unmapped until such a control
was authored; this is that control.

**Every other requirement in that table is still uncovered**, including `gdpr` `Art.25(1)`.
No mapping in this release points at any of them.

### Nothing else moved

`meta` carries the same eight keys in the same order. The 21 `meta.frameworks` entries are
identical, key and display name. Every control object still has exactly `key`, `name`,
`description`, `crosswalks`. The CSV header and column order are unchanged.

---

## 2026-08-17 — 110 controls, 657 mappings, 21 frameworks — **BREAKING**

**Version:** `sha256-d1cdf31e736bbe0eb056958b01e473cd98a074772ab1fefb5366d29955d5606a`
— the first published state that has one.

Previous published state: **94 controls, 572 mappings, 21 frameworks** (`c6a0277`,
2026-08-14). This entry describes that transition and no other. It is the first entry in
this file, so everything before it is commit history only.

**An earlier draft of this entry described a 100-control / 615-mapping release. No such
release exists.** That state lived only on a pull-request branch and never reached `main`,
so no consumer can be holding it — the digest it carried,
`sha256-8beea155…`, identifies nothing published. The single published step is 94 → 110,
and it is the one described here.

### What changed, in total

| | |
|---|---|
| Controls added | 16 |
| Controls removed | 0 |
| Frameworks added or removed | 0 |
| Control–framework edges **removed entirely** | 22 |
| Control–framework edges **added** | 6 (plus every edge on the 16 new controls) |
| Control–framework edges with **changed clause refs** | 84 |
| Control descriptions reworded | 24 |
| Control **names** changed | 0 |
| Edges (control × framework pairs) | 355 → 370 |
| Mappings | 572 → 657 |
| CSV rows withdrawn / arrived | 124 / 209 |

Mappings per framework, previously published → now. The four frameworks not listed
(`ai-governance-essentials`, `amazon-child-directed`, `apple-kids`, `eu-ai-act`) are
unchanged.

| Framework key | Was | Now | |
|---|---|---|---|
| `cis-controls` | 28 | 37 | +9 |
| `coppa` | 40 | 38 | -2 |
| `esg-essentials` | 54 | 49 | -5 |
| `gdpr` | 31 | 32 | +1 |
| `google-play-families` | 36 | 35 | -1 |
| `hipaa` | 16 | 93 | +77 |
| `iso-27001` | 32 | 36 | +4 |
| `iso-42001` | 35 | 36 | +1 |
| `iso-9001` | 21 | 29 | +8 |
| `nist-800-171` | 20 | 21 | +1 |
| `nist-800-53` | 53 | 58 | +5 |
| `nist-ai-rmf` | 11 | 15 | +4 |
| `nist-csf` | 20 | 18 | -2 |
| `pci-dss` | 27 | 29 | +2 |
| `soc-2` | 33 | 31 | -2 |
| `sox` | 44 | 34 | -10 |
| `us-employment-federal` | 35 | 30 | -5 |

**Seven of the seventeen went down, and that is the point of this release** — see
"Mappings were removed on purpose" below. Do not read a rising count as "nothing was
withdrawn here" either: `iso-27001`, `iso-9001`, `iso-42001` and `nist-800-171` all rose
*and* lost a scored requirement outright.

### BREAKING — 22 keys now return `undefined`, not `[]`

These 22 control–framework edges are **gone from the `crosswalks` map**, not emptied. A
consumer reading `control.crosswalks["gdpr"]` for `risk-assessment` now gets `undefined`,
not `[]`. Code that does `.length` on the result will throw; code that iterates will skip
it. In the CSV, the corresponding rows are simply absent.

The right-hand column is derived, not editorial: it names the control that carries each
withdrawn clause reference **in this release**, or `— nobody` where nothing does.

| Control | Framework | Was | That clause is now on |
|---|---|---|---|
| `risk-assessment` | `iso-27001` | `["A.5.7"]` | `A.5.7` → `vulnerability-management` |
| `risk-assessment` | `nist-csf` | `["ID.RA-01"]` | `ID.RA-01` → `vulnerability-management` |
| `risk-assessment` | `iso-9001` | `["6.1"]` | — nobody (a section header; its leaves `6.1.1`/`6.1.2` are on `risk-opportunity-planning`) |
| `risk-assessment` | `esg-essentials` | `["G.6"]` | — nobody |
| `risk-assessment` | `cis-controls` | `["18"]` | — nobody (a header; `18.1`–`18.5` are on `penetration-testing`) |
| `risk-assessment` | `gdpr` | `["Art.35"]` | — nobody (a header; `Art.35(1)`/`Art.35(7)` are on `dpia-process`) |
| `risk-assessment` | `sox` | `["P6","P7","P9"]` | `P9` → `change-management`; `P6`, `P7` → nobody |
| `malware-protection` | `nist-csf` | `["PR.PS-05"]` | — nobody |
| `change-management` | `iso-9001` | `["6.3"]` | — nobody |
| `security-awareness-training` | `iso-9001` | `["7.2","7.3"]` | `7.2` → `competence-management`; `7.3` → `management-system-awareness` |
| `asset-inventory` | `soc-2` | `["CC6.1"]` | `CC6.1` → `access-control-policy`, `mfa` |
| `data-classification` | `coppa` | `["312.8(b)(2)"]` | `312.8(b)(2)` → `risk-assessment` |
| `secure-development` | `gdpr` | `["Art.25"]` | — nobody (a header; `Art.25(1)` is now uncovered, see below) |
| `data-retention-disposal` | `sox` | `["P13"]` | `P13` → `information-completeness-accuracy` |
| `nonconformity-capa` | `esg-essentials` | `["S.16"]` | `S.16` → `product-service-safety` |
| `customer-requirements` | `esg-essentials` | `["S.7"]` | `S.7` → `stakeholder-engagement` |
| `individual-privacy` | `us-employment-federal` | `["us.privacy.electronic-monitoring"]` | — nobody |
| `fraud-risk-assessment` | `esg-essentials` | `["G.3"]` | `G.3` → `anti-bribery` |
| `ai-system-inventory` | `iso-42001` | `["A.4.2"]` | — nobody |
| `ai-system-inventory` | `eu-ai-act` | `["HOBL-5"]` | `HOBL-5` → `human-oversight`, `ai-monitoring-incidents` |
| `ai-impact-assessment` | `eu-ai-act` | `["HREQ-1"]` | `HREQ-1` → `ai-risk-management` |
| `neutral-age-screen` | `coppa` | `["312.5(c)(8)"]` | `312.5(c)(8)` → `childrens-consent-exceptions` |

### BREAKING — 84 edges re-pointed, and every section header is gone

Eighty-four edges changed their clause refs. This is the large, mechanical half of the diff
and the part most likely to break you.

The previously published dataset contained **83 mappings pointing at a section *header***
— a row with children — across **68 distinct header refs**. It now contains **zero**.

Representative examples:

| Control | Framework | Was | Now |
|---|---|---|---|
| `access-control-policy` | `gdpr` | `["Art.32"]` | `["Art.32(1)"]` |
| `asset-inventory` | `cis-controls` | `["1", "2"]` | `["1.1", "2.1"]` |
| `nonconformity-capa` | `iso-9001` | `["8.7", "10.2"]` | `["8.7.1", "8.7.2", "10.2.1", "10.2.2"]` |
| `encryption` | `hipaa` | `["164.312(a)(1)", "164.312(e)(1)"]` | `["164.312(a)(2)(iv)", "164.312(e)(2)(ii)"]` |
| `ai-risk-management` | `nist-ai-rmf` | `["MAP-4", "MEASURE-1", "MANAGE-1"]` | `["MEASURE-1.1", "MANAGE-1.2", "MANAGE-1.3"]` |
| `individual-privacy` | `gdpr` | `["Art.12", "Art.15", "Art.16", "Art.17", "Art.21"]` | `["Art.12(1)", "Art.12(2)", "Art.15(1)", "Art.16", "Art.17(1)", "Art.21(1)"]` |

**This is breaking for you if you match clause refs as strings.** `"Art.32"` no longer
appears for any control; `"Art.32(1)"` does. If you join this dataset to a framework clause
list, expect the join keys to have moved to a finer grain across GDPR, HIPAA, ISO 9001,
ISO 27001, CIS Controls, NIST SP 800-171, NIST SP 800-53 and NIST AI RMF.

A header is not merely imprecise. In Keel a header row is structural and is not scored, so
an edge aimed at one displays as coverage while contributing nothing to a readiness score —
a claim the product's own numbers contradict. That is why these moved rather than being
left as harmless approximations.

**24 control descriptions were also reworded.** No key, name or type changed, so this is not
breaking; but if you display or index `description`, the text is not the text you had.

### Mappings were removed on purpose — 17 requirements now have no control

Between the two published states, Keel audited the semantic fit of every non-HIPAA edge in
its control library: **598 edges read, 47 judged false and 169 partial**. A false mapping is
the worst kind of defect in this dataset, because it renders as coverage — a green tick
somebody shows an auditor — and no count goes down when you add one.

**Nothing was backfilled to hold the numbers up.** Against the previously published state,
**17 scored requirements lost their last control** and were left uncovered:

| Framework key | Requirements with no control |
|---|---|
| `esg-essentials` | `G.6` |
| `google-play-families` | `families/requirements/bluetooth-cdm` |
| `iso-27001` | `A.5.2` |
| `iso-42001` | `A.4.2` |
| `iso-9001` | `6.3` |
| `nist-800-171` | `3.13.11` |
| `nist-csf` | `PR.PS-05` |
| `soc-2` | `CC1.5`, `CC3.1` |
| `sox` | `P2`, `P6`, `P7` |
| `us-employment-federal` | `us.hiring-onboarding.i9`, `us.leave.pump-act`, `us.privacy.electronic-monitoring`, `us.privacy.polygraph`, `us.wage-hour.recordkeeping` |

(GDPR `Art.25(1)` is uncovered too, but was uncovered in the previous published state as
well — the edge it inherited pointed at the `Art.25` header. Measured against Keel's own
pre-audit library rather than against `c6a0277`, the audit orphaned **18** requirements
across **11** frameworks; 17 across 10 is the number visible from *this* file's history.)

A further **68 header refs** also lost their last mapping. Those are not a coverage loss:
headers are structural and were never scored. Do not add the two numbers.

**A requirement with no honest control is an honest gap, and it is published as one.** If
you are using this dataset to plan coverage, an absent clause is information — it says Keel
does not currently claim to satisfy it. Substituting the nearest-looking control to keep a
percentage up is exactly the defect the audit removed.

### Addition — `meta.version`, the one schema change here, and it is additive

`meta` gains a `version` key. Nothing was removed, renamed or retyped to make room for it;
the other seven keys are byte-for-byte where they were.

```json
"meta": {
  "version": "sha256-d1cdf31e736bbe0eb056958b01e473cd98a074772ab1fefb5366d29955d5606a",
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
which means the state that removes 22 keys and moves 84 sets of join keys is pinnable, and
every unpinnable state is now behind you.

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

### Addition — sixteen new controls

| Control | Crosswalks |
|---|---|
| `credential-management` — Password & credential management | `iso-27001`: `["A.5.17"]`, `pci-dss`: `["8.3"]`, `hipaa`: `["164.308(a)(5)(ii)(D)"]`, `cis-controls`: `["5.2"]`, `nist-800-171`: `["3.5.7"…"3.5.10"]`, `nist-800-53`: `["IA-5", "IA-5(1)"]` |
| `penetration-testing` — Penetration testing programme | `pci-dss`: `["11.4"]`, `cis-controls`: `["18.1"…"18.5"]` |
| `security-privacy-officers` — Named security & privacy officers | `hipaa`: `["164.308(a)(2)", "164.530(a)(2)"]` |
| `disciplinary-process` — Disciplinary & sanctions process | `iso-27001`: `["A.6.4"]`, `hipaa`: `["164.308(a)(1)(ii)(C)", "164.530(e)(2)"]`, `nist-800-53`: `["PS-8"]` |
| `data-segregation` — Segregation of regulated data inside a larger organization | `hipaa`: `["164.308(a)(4)(ii)(A)"]` |
| `endpoint-security` — Workstation & endpoint security | `iso-27001`: `["A.7.7", "A.8.1"]`, `hipaa`: `["164.310(b)", "164.310(c)", "164.312(a)(2)(iii)"]`, `nist-800-53`: `["AC-11", "AC-12"]` |
| `data-integrity` — Data integrity verification | `hipaa`: `["164.312(c)(2)", "164.312(e)(2)(i)"]`, `nist-800-53`: `["SI-7"]` |
| `risk-opportunity-planning` — Risk & opportunity planning | `iso-9001`: `["6.1.1", "6.1.2"]`, `iso-42001`: `["6.1.1"]` |
| `management-system-awareness` — Management-system awareness | `iso-9001`: `["7.3"]`, `iso-27001`: `["7.3"]`, `iso-42001`: `["7.3"]` |
| `records-of-processing` — Record of processing activities (RoPA) | `gdpr`: `["Art.30(1)"]` |
| `dpia-process` — Data protection impact assessment (DPIA) process | `gdpr`: `["Art.35(1)", "Art.35(7)"]` |
| `breach-notification` — Breach assessment & notification | `hipaa`: 12 refs across `164.404`–`164.414` |
| `privacy-notice` — Privacy notice & transparency | `hipaa`: `["164.520(b)"…"164.520(e)", "164.530(i)(4)"]` |
| `individual-rights-requests` — Individual rights request handling | `hipaa`: 14 refs across `164.522`–`164.530` |
| `disclosure-accounting` — Record & accounting of disclosures | `hipaa`: `["164.528(b)", "164.528(c)", "164.528(d)"]` |
| `information-completeness-accuracy` — Completeness & accuracy of information used by controls | `soc-2`: `["CC2.1"]`, `sox`: `["P13"]` |

Adding a control is additive: no existing key changes. Ten of the sixteen carry HIPAA
duties, as that framework went from 16 mappings to 93.

`information-completeness-accuracy` is the one authored by the semantic-fit audit itself.
SOX `P13` previously had three controls claiming it and nothing that performed its duty —
a requirement three times covered and zero times satisfied.

### Diffing this yourself

```bash
git diff HEAD~1 -- crosswalks.csv | grep '^-[^-]'   # every mapping that went away
git diff HEAD~1 -- crosswalks.csv | grep '^+[^+]'   # every mapping that arrived
```

Or, for the requirements nobody covers, join `clause_ref` against the framework's own
requirement list — this dataset publishes only the mappings that exist, so a gap is visible
as an absence rather than as a row.

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
