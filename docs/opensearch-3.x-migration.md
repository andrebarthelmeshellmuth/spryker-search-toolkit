# Migrating to OpenSearch 3.x

Every member package was re-verified end-to-end on a Spryker demoshop upgraded from **OpenSearch 1.3.4 to
3.5.0** (Lucene 10.3.2): full re-export/reindex, every member's `check-installation` re-run on 3.5.

**No member package needs a code change for OpenSearch 3.x.** The verified range now spans three Lucene
generations (8.10 → 10.3) and the Apache-2.0 fork point:
OpenSearch 1.3.4 · 2.11 · **3.5.0** · Elasticsearch 8.11.

Each member ships its own migration note with the specifics that matter for *that* package:

| package | what its note covers |
|---|---|
| [search-ranking](https://github.com/andrebarthelmeshellmuth/spryker-search-ranking/blob/main/docs/opensearch-3.x-migration.md) | The canonical write-up: full capability delta, and the core-/project-level environment work a k-NN `knn_vector` field needs on 3.x (`nmslib` → `lucene`, `index.knn` as a static setting, `http.max_content_length`). |
| [search-ranking-optimizer](https://github.com/andrebarthelmeshellmuth/spryker-search-ranking-optimizer/blob/main/docs/opensearch-3.x-migration.md) | `_rank_eval` + reconstructed `function_score` are version-stable; the `evaluate-hybrid` raw-`knn` path (alpha ≠ 1.0) is the only k-NN touchpoint. |
| [search-debug](https://github.com/andrebarthelmeshellmuth/spryker-search-debug/blob/main/docs/opensearch-3.x-migration.md) | The `_explanation` tree keeps the `sum of:` → `function score` / `match on required clause` shape `ExplanationParser` reads; Lucene wording drift is prefix-matched. |
| [search-index-alias](https://github.com/andrebarthelmeshellmuth/spryker-search-index-alias/blob/main/docs/opensearch-3.x-migration.md) | Blue-green rebuild APIs are all long-stable; the neural-search empty-`properties` trap surfaces on *every* rebuild; `index.knn` static-setting and `nmslib` notes for k-NN scopes. |
| [search-analyzer-config](https://github.com/andrebarthelmeshellmuth/spryker-search-analyzer-config/blob/main/docs/opensearch-3.x-migration.md) | The staged `analysis` block and live `_analyze` preview use only shared primitives; materializing goes through a rebuild, so the empty-`properties` trap applies. |
| [search-variant-facets](https://github.com/andrebarthelmeshellmuth/spryker-search-variant-facets/blob/main/docs/opensearch-3.x-migration.md) | `nested`/`reverse_nested`, `inner_hits`, and the `variant-facet` object mapping are all standard; `check-installation` re-confirms the mapping on 3.5. |
| [search-feedback](https://github.com/andrebarthelmeshellmuth/spryker-search-feedback/blob/main/docs/opensearch-3.x-migration.md) | Issues no live query — only reconstructs a stored `Elastica\Response`, which is engine-version-agnostic. |

## Shared reference — capability delta 1.3.x → 3.5

Probed directly against a live OpenSearch 1.3.14 and a live 3.5.0 (not assumed):

| capability | 1.3.x | 3.5 |
|---|---|---|
| `function_score` + `script_score` (painless), `rank_feature`, `distance_feature`, `_rank_eval`, `_explain`, `_analyze`, completion suggester | ✅ | ✅ |
| `pinned` query | ❌ | ❌ (Elastic-licensed, never in OpenSearch) |
| **`hybrid` query** | ❌ | ✅ — neural-search plugin's fusion query type; OpenSearch ≥ 2.10 |
| **`_search/pipeline` endpoint** | ❌ | ✅ — search pipelines; OpenSearch ≥ 2.8 |
| `_plugins/_ml` (ML Commons) | ✅ | ✅ — endpoint present on both; 3.x adds in-cluster model serving on top |
| `neural` query | ❌ | ❌ — the parser rejects a bare clause on both; it needs a registered `model_id` |
| `_plugins/_ltr` (Learning To Rank) | ❌ | ❌ — third-party plugin, in neither stock image |

The two genuine additions are `hybrid` query and `_search/pipeline`. No member package uses either.

## Shared reference — the upgrade-time trap

OpenSearch 3.x bundles the neural-search plugin, whose `SemanticMappingTransformer` runs on **every index
create**. It rejects any mapping that declares:

```json
"some-field": { "type": "object", "properties": {} }
```

with `class java.util.ArrayList cannot be cast to class java.util.Map` — PHP's `json_decode($json, true)`
turns the empty `{}` into `[]`, and Spryker PUTs `"properties": []`. Spryker Cloud Commerce fixed this in
five core packages (ticket SC-25160); other packages (e.g. `spryker-feature/self-service-portal`'s
`ssp_asset.json`) may still carry one. Spryker merges schema fragments with `array_replace_recursive`,
which cannot delete a key, so a project-level override has to make `properties` **non-empty** rather than
remove it — one inert, never-populated field:

```json
{ "type": "object", "properties": { "_os3_object_guard": { "type": "boolean", "index": false } } }
```

This is independent of every member package; it is a property of the merged schema being created.
