# Demo fixture claims

Cross-package registry of who's using which piece of the b2b-demo-marketplace demoshop's shared import
data, so two packages' fixtures don't silently collide when applied together. This is bookkeeping only —
each package's actual fixture patches/scripts live in that package's own repo (a `fixtures/` folder,
documented in its README), never committed to the demoshop itself (it's Spryker's official upstream, not
a fork any of these packages own — see each package's README "Testing"/"Installation" section for why).

**Update this file whenever a package's fixture claims a new product, attribute key, or shared-file slot**
— check here first before reusing something another package has already claimed.

## Why this exists

`data/import/common/common/product_concrete.csv` gives each concrete exactly **two** attribute slots
(`attribute_key_1`/`value_1`, `attribute_key_2`/`value_2`). Two packages independently deciding to use the
same product's same slot for different data would silently overwrite each other with no error — the CSV
importer has no way to detect that as a conflict. `product_search_attribute.csv`'s `position` column and
`product_attribute_key.csv`'s key registry have similar "shared, ordered, no built-in conflict detection"
shapes.

## Claims

### spryker-community/search-variant-facets

- **`STL-7010`** (concretes `STL-7010-1`..`-6`): `attribute_key_1` = `limitrange`, `attribute_key_2` =
  `packaging_unit` — **both slots claimed**, do not add a third attribute to these concretes without
  extending the CSV format. `STL-7010-3`/`STL-7010-4` have `is_searchable.*` deliberately set to `0`
  (intentional gap in the 2×3 variant matrix, used to prove the cross-facet-AND fix) — don't "fix" this
  back to `1` without checking [search-variant-facets' README](https://github.com/andrebarthelmeshellmuth/spryker-search-variant-facets#testing-and-ci)
  first.
- **`HP-ECO-45K`** (concretes `HP-ECO-45K-1`..`-3`): `attribute_key_1` = `poweroutput` (pre-existing, not
  this package's own), `attribute_key_2` = `leadtime_days` (added) — **both slots claimed**.
- `product_search_attribute.csv`: positions **4** (`limitrange`), **5** (`packaging_unit`), **6**
  (`leadtime_days`) — next free position is **7**.
- `product_attribute_key.csv`: registered `limitrange`, `packaging_unit`, `leadtime_days` (all
  `is_super=1`).

### Shared: the `SearchAdmin--1` customer

`spryker-community/search-debug`, `spryker-community/search-feedback`,
`spryker-community/search-ranking`, and `spryker-community/search-ranking-optimizer` each need a real,
loggable-in customer holding their own package's permission, for their own Presentation suite and for
manual verification. All four now claim the **same** customer/company-user/role
(`customer_reference`/`company_user_key` = `SearchAdmin--1`, `company_role_key` = `test-company_Admin`,
login `search-admin@test-company.example` / `change123`) rather than four separate accounts — each
package's own `fixtures/apply.php` creates these shared rows **only if missing**, then adds its own
row to `company_role_permission.csv` and its own slice of `glossary.csv`. Running any subset of the four
scripts, in any order, is safe: whichever runs first creates the shared account, the rest just add their
own permission grant to it. **Do not add a fifth distinct customer for a new package's own permission
need — extend this same shared account instead**, and register the new permission-plugin key here.

Per-package rows already registered on `test-company_Admin`:
- `SeeSearchDebugInfoPermissionPlugin` (search-debug)
- `SubmitSearchFeedbackTicketPermissionPlugin`, `ViewSearchFeedbackTicketReplayPermissionPlugin`
  (search-feedback)
- `SeeSearchRankingRandomImpactPermissionPlugin` (search-ranking)
- `RateSearchRelevancePermissionPlugin` (search-ranking-optimizer)

### spryker-community/search-ranking

- `search_ranking_metric.csv` / `search_ranking_product_metric.csv` — standalone new files, own
  `data_entity` registrations in `full_EU.yml` **and** `catalog_setup_import_config_EU.yml`. No
  shared-file edits, no product/slot claims — safe to add to without checking this list. (Referenced
  products, e.g. `C2235`, are read-only lookups by `abstract_sku`, not attribute-slot edits.) Both files
  are bundled verbatim in this package's own `fixtures/` dir and dropped into place by its
  `fixtures/apply.php`, alongside the shared customer claim above.

### spryker-community/search-ranking-optimizer

- Ground-truth judgments fixture: not yet built (see that package's own roadmap memory). When it lands,
  record its claims here — likely standalone new files like search-ranking's, but confirm before assuming
  no shared-file edits are needed.
- Shared customer claim: see above.

### spryker-community/search-debug, spryker-community/search-feedback

- No fixture data claims beyond the shared customer above.
