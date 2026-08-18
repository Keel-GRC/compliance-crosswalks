# Keel compliance crosswalks

An open dataset of canonical, framework-agnostic security and quality controls, each
**crosswalked** to the clauses it satisfies across multiple compliance frameworks. This
is the "collect once, comply everywhere" mapping that powers [Keel](https://keelgrc.com),
published as open data.

**The authoritative counts live in the data, not in this file.** Coverage grows as Keel's
control library does, and a number written in prose here is wrong the week after it is
written — which is exactly what happened: this README claimed 45 controls across 15
frameworks while the files beside it already held 64 across 16. Read them from the data
instead:

```bash
jq '.meta | {controlCount, frameworks: (.frameworks | length)}' crosswalks.json
jq '[.controls[].crosswalks | to_entries[].value | length] | add' crosswalks.json  # mappings
tail -n +2 crosswalks.csv | wc -l                                                  # same, from the CSV
```

The framework list is likewise in `meta.frameworks`, so it cannot fall behind the mappings.

## Files

| File | Format | Shape |
|------|--------|-------|
| [`crosswalks.json`](./crosswalks.json) | JSON | Metadata wrapper + one object per control with its `crosswalks` map (`framework key -> [clause refs]`). |
| [`crosswalks.csv`](./crosswalks.csv) | CSV | Tidy long format, one row per `(control, framework, clause)`: `control_key, control_name, framework_key, framework_name, clause_ref`. |
| [`CHANGELOG.md`](./CHANGELOG.md) | Markdown | Notable changes, including which ones **break** a consumer. Hand-written — the automated sync does not update it. |

## Schema (JSON)

```jsonc
{
  "meta": {
    "version": "sha256-<64 hex>",  // content digest of this exact published state
    "name": "Keel compliance crosswalks",
    "license": "CC-BY-4.0",
    "attribution": "Keel GRC LLC (https://keelgrc.com)",
    "controlCount": <integer>,   // read this, don't trust a number in prose
    "frameworks": [{ "key": "iso-27001", "name": "ISO/IEC 27001" }, ...]
  },
  "controls": [
    {
      "key": "access-control-policy",
      "name": "Access control policy",
      "description": "...",
      "crosswalks": { "iso-27001": ["A.5.15"], "soc-2": ["CC6.1", "CC6.3"], ... }
    }
  ]
}
```

### `meta.version` — pin from here forward

`meta.version` is a **content digest** of the exact state you are holding:

```bash
jq -r '.meta.version' crosswalks.json
# sha256-<64 hex>   — the real value is in the file; no copy of it is written in this README,
#                     because a digest pasted into prose is stale the next time the data moves
```

Record it. Equal versions mean the same data; a different version means something in this
document changed. It is derived by the generator on every run, so it cannot be forgotten or
left stale.

**It does not tell you which of two versions is newer.** A digest has no order. Ordering
lives in [`CHANGELOG.md`](./CHANGELOG.md), which is newest-first and names the version of
each entry from this release onward.

**Everything published before this release is unidentifiable from inside the file.** Eleven
distinct states of `crosswalks.json` reached `main` without a version field, and none of
them can be told apart except by commit — four landed on 2026-08-13 and four more on
2026-07-23, so a generation date would not have separated them either. If you are holding one of those, the
only identifier available is the commit, plus the `controlCount` / mapping count each
`CHANGELOG.md` heading states.

To recompute the digest yourself: delete `meta.version` from the document and hash the
serialization the generator writes — `sha256( JSON.stringify(doc, null, 2) + "\n" )`, UTF-8
in, hex out. In Node that reproduces the published value exactly. Across languages it
depends on that runtime's JSON escaping and indentation, so **the guarantee offered here is
equality comparison of the field, not byte-exact recomputation elsewhere.**

`crosswalks.csv` carries no version: a sixth column would change the shape every CSV
consumer parses, and a comment line would break naive parsers. The CSV is generated from
the same controls in the same run, so the JSON's version identifies both.

Clause references have also moved to a finer grain over time (`Art.32` → `Art.32(1)`). If
you join on `clause_ref`, do not assume the key is stable across releases.

## About the mappings

Each control is deliberately framework-agnostic: one control, one place, mapped to the
clauses it satisfies in each framework. A clause reference (for example `A.5.15` for
ISO/IEC 27001, or `CC6.1` for SOC 2) is a factual pointer to that framework's published
requirement; this dataset does not reproduce any copyrighted standard text. Framework
names are kept version-free on purpose, since the clause references carry the specificity.

The mappings are a practical starting point for "if I do this once, which frameworks does
it count toward," not a certification or a substitute for an audit. Use them to plan
coverage; confirm against the authoritative standard for your scope.

### An absent clause is deliberate, and it is information

**This dataset publishes only the mappings that hold, so gaps appear as absences rather
than as rows.** A requirement of a framework with no control mapped to it means Keel does
not currently claim to satisfy it — not that the requirement was overlooked. Some of those
gaps were created on purpose: a 2026-08-17 semantic-fit audit withdrew mappings that did
not survive reading, and **nothing was substituted in to keep a coverage figure up.** The
release notes in [`CHANGELOG.md`](./CHANGELOG.md) name every requirement left uncovered by
that pass.

So do not infer coverage from silence in either direction. If you are computing "what
fraction of framework X does this dataset cover", the honest denominator is the framework's
own requirement list, which this dataset does not ship — join against the standard, and
expect the answer to be well under 100%.

## Regenerating

This dataset is generated from Keel's canonical control library (the single source of
truth), so the open data can never drift from what the product actually maps.

That guarantee used to stop at the repository boundary: a generator check confirmed the
copy inside Keel matched the library, and nothing confirmed that *this* repo matched
either. It didn't — the published files sat nineteen controls behind, with a whole
framework missing, for long enough that the invitation below to check the mappings was
not really answerable. `crosswalks.json` and `crosswalks.csv` are now pushed here
automatically whenever the library changes, and the job refuses to publish a copy the
generator would not reproduce.

## Found a mapping you disagree with?

Open an issue. A crosswalk is a judgment call about whether one control genuinely
satisfies a given clause, and a wrong mapping is worth more to us than a polite one —
it is the reason this is published rather than described.

## License

Licensed under [Creative Commons Attribution 4.0 International](https://creativecommons.org/licenses/by/4.0/)
(CC BY 4.0). You may share and adapt the data, including commercially, with attribution
to **Keel GRC LLC (https://keelgrc.com)**. See [LICENSE](./LICENSE).
