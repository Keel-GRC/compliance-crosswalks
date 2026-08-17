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

**There is no version field.** `meta` carries no `version`, `schemaVersion` or generation
timestamp, so nothing in the data itself tells you which release you are holding or that it
changed shape since you last read it. Eleven distinct states of this dataset have been
published so far and none of them can be told apart from inside the file — only by commit.
Until that is fixed, pin a commit if you need stability, and read
[`CHANGELOG.md`](./CHANGELOG.md) — its headings state the exact `controlCount` and mapping
count each entry describes, which is the only release identifier this dataset currently has.

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
