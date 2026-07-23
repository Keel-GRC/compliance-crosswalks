# Keel compliance crosswalks

An open dataset of canonical, framework-agnostic security and quality controls, each
**crosswalked** to the clauses it satisfies across multiple compliance frameworks. This
is the "collect once, comply everywhere" mapping that powers [Keel](https://keelgrc.com),
published as open data.

- **42 controls** mapped across **10 frameworks**, **216 control-to-clause mappings**.
- Frameworks covered: ISO/IEC 27001, SOC 2, NIST Cybersecurity Framework, PCI DSS,
  HIPAA, ISO 9001, ESG Essentials, NIST SP 800-171, CIS Controls, GDPR.

> Counts reflect the current dataset in this repo and update when it is regenerated.

## Files

| File | Format | Shape |
|------|--------|-------|
| [`crosswalks.json`](./crosswalks.json) | JSON | Metadata wrapper + one object per control with its `crosswalks` map (`framework key -> [clause refs]`). |
| [`crosswalks.csv`](./crosswalks.csv) | CSV | Tidy long format, one row per `(control, framework, clause)`: `control_key, control_name, framework_key, framework_name, clause_ref`. |

## Schema (JSON)

```jsonc
{
  "meta": {
    "name": "Keel compliance crosswalks",
    "license": "CC-BY-4.0",
    "attribution": "Keel GRC LLC (https://keelgrc.com)",
    "controlCount": 42,
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
truth), so the open data can never drift from what the product actually maps. Keel
regenerates and republishes it as the library grows.

## License

Licensed under [Creative Commons Attribution 4.0 International](https://creativecommons.org/licenses/by/4.0/)
(CC BY 4.0). You may share and adapt the data, including commercially, with attribution
to **Keel GRC LLC (https://keelgrc.com)**. See [LICENSE](./LICENSE).
