# Spryker Search Toolkit

[![CI](https://github.com/andrebarthelmeshellmuth/spryker-search-toolkit/actions/workflows/ci.yml/badge.svg?branch=main)](https://github.com/andrebarthelmeshellmuth/spryker-search-toolkit/actions/workflows/ci.yml)
[![Bundles](https://img.shields.io/badge/bundles-7%20packages-2a6b2a)](#whats-included)
[![License](https://img.shields.io/badge/license-MIT-blue)](LICENSE)

This is a `type: metapackage` with no PHP source of its own, so it skips the PHP-version and
PHPStan badges every bundled package carries — there's no code for either to describe here. The
CI badge instead reflects the one check that does apply: `composer validate --strict` on this
package's own `composer.json`.

A single `composer require` for the whole Spryker search toolkit. This package carries no code of
its own — it exists purely to pull in [search-debug](https://github.com/spryker-community/search-debug),
[search-ranking](https://github.com/andrebarthelmeshellmuth/spryker-search-ranking),
[search-ranking-optimizer](https://github.com/andrebarthelmeshellmuth/spryker-search-ranking-optimizer),
[search-feedback](https://github.com/spryker-community/search-feedback),
[search-variant-facets](https://github.com/andrebarthelmeshellmuth/spryker-search-variant-facets),
[search-index-alias](https://github.com/andrebarthelmeshellmuth/spryker-search-index-alias), and
[search-analyzer-config](https://github.com/andrebarthelmeshellmuth/spryker-search-analyzer-config)
at version floors that are known to work with each other.

Each member package stays independently installable and focused on one concern (explainability,
ranking mechanism, tuning, cross-facet indexing, no-downtime index management, analyzer
configuration). search-index-alias, search-variant-facets, and search-analyzer-config are search
tooling that isn't about relevance scoring — a no-downtime settings/mapping change via alias swap,
correct cross-facet indexing, and Zed-editable analyzer settings (decompound/synonyms/stopwords),
respectively — landing as sibling packages under this same toolkit rather than bolted onto the
relevance packages above.

*Part of the [Search Relevance](https://search-relevance.dev/) project — explore the interactive ranking-formula walkthrough there.*

> **Community extensions.** search-debug and search-feedback are maintained by the community in
> Spryker's [community GitHub org](https://github.com/spryker-community); this bundle and the other
> members are still hosted on the maintainer's own GitHub account while they move over. None of them are
> part of the commercial Spryker product or covered by Spryker's commercial support.

## Contents

- [Quick summary](#quick-summary)
- [What's included](#whats-included)
- [Status](#status)
- [Installation](#installation)
- [Versioning](#versioning)
- [License](#license)

## Quick summary

One screenshot per bundled package, and the top thing (or two, or three) it actually does for you.

### [search-debug](https://github.com/spryker-community/search-debug)

![The SRP score overlay: matched tokens with a magnifying-glass link, the raw text-match score, per-field score contributions (type, store, locale, is-active), and the final score used for ranking](docs/screenshots/search-debug-srp-overlay.png)

- Explains exactly how a product earned its position on the results page: which database field
  matched, what the analyzer chain turned that field's value into, and how each piece contributed
  to the final score.
- The overlay widget itself is open for extension — other packages (search-ranking included)
  register their own contribution rows into it rather than needing a competing UI.

### [search-ranking](https://github.com/andrebarthelmeshellmuth/spryker-search-ranking)

![The metrics list: ID, name, weight, formula, active/inactive status, and edit/delete actions for every configured business signal](docs/screenshots/search-ranking-metrics-list.png)

- Lets you define weighted business metrics per store, so ranking stops being pure text
  relevance — margin, stock, conversion rate, or whatever signal matters, as long as each product
  carries that data.
- Blends those metrics into Elasticsearch/OpenSearch's `function_score` query alongside text
  relevance, with a relevance-weight knob to shift the balance between the two.

### [search-ranking-optimizer](https://github.com/andrebarthelmeshellmuth/spryker-search-ranking-optimizer)

![The Automated Weight Optimization page: the latest run's baseline vs. winning nDCG@10 score, the winning relevanceWeight and per-metric weights, a restart-on-plateau run's own restart history (population/generations/why it stopped/best score per restart), when it was applied, and a form to queue a new run against a chosen store/locale/algorithm/termination mode](docs/screenshots/search-ranking-optimizer-automated-weight-optimization.png)

- Lets admins rate how good a query's results actually are, building up a relevance-judgment set
  from real feedback instead of guesswork.
- Feeds those ratings into [blackbox-optimizer](https://github.com/andrebarthelmeshellmuth/blackbox-optimizer)
  (CMA-ES or Differential Evolution) to automatically tune search-ranking's formula weights,
  scored against nDCG@10.

### [search-feedback](https://github.com/spryker-community/search-feedback)

![The storefront search results page with a "Not happy with these results?" box below the product grid: a Topic dropdown (Relevance/Missing results/Wrong order/Filters-facets/Other), a free-text body field, and a Send Feedback button](docs/screenshots/search-feedback-yves-ticket-form.png)

- Lets a storefront user file a ticket about a specific search-results page straight from the
  page they're looking at, without needing to describe what they searched for.
- The Zed admin who picks it up can replay the *exact* result set the ticket-filer saw — not "run
  this search now," but "run it as it looked back then" — and, if search-debug is installed, dig
  into exactly how each of those historical scores was calculated.

### [search-variant-facets](https://github.com/andrebarthelmeshellmuth/spryker-search-variant-facets)

![Illustrative mockup: selecting Color=Red and Size=40 should only return products where one single variant is both red and size 40. Shoe #1 has a matching Red/Size-40 variant; Shoe #2 only has Green/Size-40 and Red/Size-42 separately, so no single variant satisfies both filters and it's correctly excluded](docs/screenshots/search-variant-facets-yves-mockup.webp)

- Fixes a real gap in Spryker core's facet indexing: core can return an abstract product even
  when no single one of its concretes/variants actually matches every selected facet value at
  once (an OR-across-concretes leak). This package makes cross-facet selections match only
  concretes that genuinely carry the full combination.

### [search-index-alias](https://github.com/andrebarthelmeshellmuth/spryker-search-index-alias)

![A ready rollout flagged "for next deploy": the cross-scope Pending deploy flips panel above the filter lists it, the active-rollout line carries a "flagged for next deploy" badge, and the action bar shows Flip (immediate) alongside Unflag (cancel the flag) instead of a plain Flip-only bar](docs/screenshots/search-index-alias-deploy-flip.png)

- Introduces no-downtime index switches: rebuild a search index in the background, verify it,
  then flip an alias to it instead of updating an index in place.
- A flip can be flagged to happen automatically as part of the next deployment, instead of
  requiring someone to trigger it by hand.

### [search-analyzer-config](https://github.com/andrebarthelmeshellmuth/spryker-search-analyzer-config)

![The analyzer configuration overview page](docs/screenshots/search-analyzer-config-overview.png)

- Zed-editable per-store-index analyzer configuration — decompound words, synonyms, stopwords,
  stemmer language, a do-not-decompound brand list — instead of a hand-edited mapping JSON.
- Changes are previewed against a throwaway index before being materialized via a
  search-index-alias rebuild, so a bad synonym/decompound edit never touches the live index.

## What's included

| Package | Role |
|---|---|
| [search-debug](https://github.com/spryker-community/search-debug) | Per-product Elasticsearch/OpenSearch score and token overlay — explains why a result ranked where it did. |
| [search-ranking](https://github.com/andrebarthelmeshellmuth/spryker-search-ranking) | The ranking mechanism: business-signal metrics, normalization, and `function_score` ranking. |
| [search-ranking-optimizer](https://github.com/andrebarthelmeshellmuth/spryker-search-ranking-optimizer) | The tuning layer on top: calibration, relevance judgments, rank evaluation, and weight optimization. |
| [search-feedback](https://github.com/spryker-community/search-feedback) | SRP feedback ticketing: lets an authorized storefront admin file a ticket about a set of search results. |
| [search-variant-facets](https://github.com/andrebarthelmeshellmuth/spryker-search-variant-facets) | Fixes Spryker core's OR-across-concretes facet indexing: cross-facet selections match only concretes that actually carry the combination. |
| [search-index-alias](https://github.com/andrebarthelmeshellmuth/spryker-search-index-alias) | Zed blue/green search-index management via aliases: rebuild and flip an index with no storefront downtime. |
| [search-analyzer-config](https://github.com/andrebarthelmeshellmuth/spryker-search-analyzer-config) | Zed-editable per-store-index analyzer configuration (decompound words, synonyms, stopwords, stemmer language), materialized via a search-index-alias rebuild. |

## Status

Stable. Every member package has reached a stable release. This bundle declares the oldest version of
each that it has been verified against:

| Package | Minimum verified |
|---|---|
| search-debug | `^1.3.4` |
| search-ranking | `^2.3.5` |
| search-ranking-optimizer | `^2.0.0` |
| search-feedback | `^1.4.4` |
| search-variant-facets | `^1.0.1` |
| search-index-alias | `^2.0.0` |
| search-analyzer-config | `^1.0.0` |

Read these as **floors, not pins**. They are caret constraints, so Composer resolves each member to the
newest release sharing that major — installing today gives you a newer set than the versions named
above, and that resolved combination is whatever the member packages' own semver guarantees make it,
not a combination this bundle has separately tested. That is the intended trade-off: pinning exact
versions here would block members from shipping their own patch releases to you. What this bundle
guarantees is the *floor* — that nothing older than the table above is ever selected, and that the
majors listed are mutually compatible. If you need a byte-exact reproducible set, commit your
`composer.lock`; that is the tool for it, and it is the reason this bundle does not try to be one.

One caveat is unchanged, and it is about distribution, not maturity: the packages are moving into
Spryker's community GitHub org (`github.com/spryker-community`) and onto Packagist one at a time.
search-debug and search-feedback are already there and install from Packagist; the other five members and
this bundle are not yet, so installation still needs the manual VCS repository step below for them.

`search-ranking-optimizer` also transitively requires
[`andrebarthelmeshellmuth/blackbox-optimizer`](https://github.com/andrebarthelmeshellmuth/blackbox-optimizer),
which is likewise not yet on Packagist — see the note under [Installation](#installation).

### Search engine compatibility

Every member package is verified against **OpenSearch 1.3.4, 2.11, 3.5.0 and Elasticsearch 8.11** — a
range spanning three Lucene generations (8.10 → 10.3) and the Apache-2.0 fork point. **The OpenSearch 3.5
upgrade required no code change in any member package**; it was run end-to-end on a demoshop upgraded from
1.3.4 (full re-export/reindex, every package's `check-installation` re-run on 3.5). The migration friction
is entirely core Spryker (the search-schema packages, ticket SC-25160) and project/deployment level — the
k-NN engine name, the static `index.knn` setting, the HTTP request-size limit for embedding-carrying
documents, and a neural-search mapping-transformer trap. [Migrating to OpenSearch
3.x](docs/opensearch-3.x-migration.md) is the shared reference (capability delta + the upgrade-time trap)
and links each member package's own tailored note.

## Installation

Once every member package (and blackbox-optimizer) is published to Packagist, installing the whole
toolkit will be:

```
composer require spryker-community/search-toolkit
```

**Today**, add a VCS repository entry to your own project's `composer.json` for every package that is
not yet on Packagist: the five members and this bundle below, **and** `blackbox-optimizer`, even though
`search-ranking-optimizer`'s own `composer.json` already declares a repository for it — Composer
only reads `repositories` from the *root* project, never from a dependency's own `composer.json`,
so every non-Packagist package anywhere in the graph has to be re-declared by whoever sits at the
root. search-debug and search-feedback need no entry: Composer resolves them from Packagist. (This
mirrors what this project's own demoshop does — see its `composer.json`.)

```json
{
    "repositories": [
        { "type": "vcs", "url": "https://github.com/andrebarthelmeshellmuth/spryker-search-ranking" },
        { "type": "vcs", "url": "https://github.com/andrebarthelmeshellmuth/spryker-search-ranking-optimizer" },
        { "type": "vcs", "url": "https://github.com/andrebarthelmeshellmuth/spryker-search-variant-facets" },
        { "type": "vcs", "url": "https://github.com/andrebarthelmeshellmuth/spryker-search-index-alias" },
        { "type": "vcs", "url": "https://github.com/andrebarthelmeshellmuth/spryker-search-analyzer-config" },
        { "type": "vcs", "url": "https://github.com/andrebarthelmeshellmuth/spryker-search-toolkit" },
        { "type": "vcs", "url": "https://github.com/andrebarthelmeshellmuth/blackbox-optimizer" }
    ],
    "require": {
        "spryker-community/search-toolkit": "^1.3.0"
    }
}
```

then `composer update`.

## Versioning

This package has no code, so its version tracks *compatible combinations*, not behavior. Its
`require` floors are bumped whenever a member package ships a new major or minor release that this
bundle has been verified against — and, as a patch bump, whenever a new member package joins the
bundle at all (adding `search-feedback` was one such patch bump, even though nothing existing
changed).

`1.0.0` marks the point where every member package is itself at a stable release, rather than any
change in what this bundle does. Note that two of the floors it raised were not merely stale but
wrong: the previous `^1.3.0` for search-ranking excluded its entire 2.x line, and `^0.9.1` for
search-ranking-optimizer excluded its 1.0.0 — so pre-1.0.0 tags of this bundle resolve to a set that
no longer reflects the packages' current releases.

`1.2.0` adds `search-variant-facets` and `search-index-alias` as new bundled members and raises
`search-ranking-optimizer`'s floor to its `2.0.0` line, alongside patch-level floor bumps for
search-debug, search-ranking, and search-feedback.

`1.3.0` adds `search-analyzer-config` as a new bundled member and raises `search-index-alias`'s
floor to its `2.0.0` line (a real breaking change in that package — the default rebuild mode
flipped to `fromSchema=true` — but this bundle's own contract doesn't change, hence a minor bump
here, not a major one), alongside patch-level floor bumps for search-debug, search-ranking,
search-feedback, and search-variant-facets. Use `^1.3.0`.

## License

MIT, see [LICENSE](LICENSE).
