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
