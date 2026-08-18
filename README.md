# Spryker Search Toolkit

A single `composer require` for the whole Spryker search toolkit. This package carries no code of
its own — it exists purely to pull in [search-debug](https://github.com/andrebarthelmeshellmuth/spryker-search-debug),
[search-ranking](https://github.com/andrebarthelmeshellmuth/spryker-search-ranking),
[search-ranking-optimizer](https://github.com/andrebarthelmeshellmuth/spryker-search-ranking-optimizer), and
[search-feedback](https://github.com/andrebarthelmeshellmuth/spryker-search-feedback)
at version floors that are known to work with each other.

Each member package stays independently installable and focused on one concern (explainability,
ranking mechanism, tuning). Future search tooling that isn't about relevance scoring — for example
a no-downtime settings/mapping change via alias swap — is expected to land as its own sibling
package under this same toolkit, not bolted onto the relevance packages above.

*Part of the [Search Relevance](https://search-relevance.dev/) project — explore the interactive ranking-formula walkthrough there.*

> **Not an official Spryker project.** `spryker-community/*` is an independent, community-built
> package namespace with no affiliation to, sponsorship by, or endorsement from Spryker Systems GmbH.
> The name describes what these packages are (community contributions for Spryker Commerce OS), not who
> maintains them. The matching Packagist namespace is held by an unrelated GitHub organization, which is
> why installation goes through a VCS repository entry rather than a plain `composer require` — see
> [Installation](#installation).

## Contents

- [What's included](#whats-included)
- [Status](#status)
- [Installation](#installation)
- [Versioning](#versioning)
- [License](#license)

## What's included

| Package | Role |
|---|---|
| [search-debug](https://github.com/andrebarthelmeshellmuth/spryker-search-debug) | Per-product Elasticsearch/OpenSearch score and token overlay — explains why a result ranked where it did. |
| [search-ranking](https://github.com/andrebarthelmeshellmuth/spryker-search-ranking) | The ranking mechanism: business-signal metrics, normalization, and `function_score` ranking. |
| [search-ranking-optimizer](https://github.com/andrebarthelmeshellmuth/spryker-search-ranking-optimizer) | The tuning layer on top: calibration, relevance judgments, rank evaluation, and weight optimization. |
| [search-feedback](https://github.com/andrebarthelmeshellmuth/spryker-search-feedback) | SRP feedback ticketing: lets an authorized storefront admin file a ticket about a set of search results. |

## Status

Stable. Every member package has reached a stable release. This bundle declares the oldest version of
each that it has been verified against:

| Package | Minimum verified |
|---|---|
| search-debug | `^1.2.1` |
| search-ranking | `^2.3.0` |
| search-ranking-optimizer | `^1.0.0` |
| search-feedback | `^1.4.0` |

Read these as **floors, not pins**. They are caret constraints, so Composer resolves each member to the
newest release sharing that major — installing today gives you a newer set than the versions named
above, and that resolved combination is whatever the member packages' own semver guarantees make it,
not a combination this bundle has separately tested. That is the intended trade-off: pinning exact
versions here would block members from shipping their own patch releases to you. What this bundle
guarantees is the *floor* — that nothing older than the table above is ever selected, and that the
majors listed are mutually compatible. If you need a byte-exact reproducible set, commit your
`composer.lock`; that is the tool for it, and it is the reason this bundle does not try to be one.

One caveat is unchanged, and it is about distribution, not maturity: all four member packages, and
this bundle, live under the `spryker-community` vendor namespace, which is not yet on Packagist (the
name is held by an unrelated GitHub organization; resolving this is tracked separately). Until that's
resolved, installation requires the manual VCS repository step below.

`search-ranking-optimizer` also transitively requires
[`andrebarthelmeshellmuth/blackbox-optimizer`](https://github.com/andrebarthelmeshellmuth/blackbox-optimizer),
which is likewise not yet on Packagist — see the note under [Installation](#installation).

## Installation

Once every member package (and blackbox-optimizer) is published to Packagist, installing the whole
toolkit will be:

```
composer require spryker-community/search-toolkit
```

**Today**, add a VCS repository entry per package to your own project's `composer.json`. This is
required for all five packages below **and** for `blackbox-optimizer`, even though
`search-ranking-optimizer`'s own `composer.json` already declares a repository for it — Composer
only reads `repositories` from the *root* project, never from a dependency's own `composer.json`,
so every non-Packagist package anywhere in the graph has to be re-declared by whoever sits at the
root. (This mirrors what this project's own demoshop does — see its `composer.json`.)

```json
{
    "repositories": [
        { "type": "vcs", "url": "https://github.com/andrebarthelmeshellmuth/spryker-search-debug" },
        { "type": "vcs", "url": "https://github.com/andrebarthelmeshellmuth/spryker-search-ranking" },
        { "type": "vcs", "url": "https://github.com/andrebarthelmeshellmuth/spryker-search-ranking-optimizer" },
        { "type": "vcs", "url": "https://github.com/andrebarthelmeshellmuth/spryker-search-feedback" },
        { "type": "vcs", "url": "https://github.com/andrebarthelmeshellmuth/spryker-search-toolkit" },
        { "type": "vcs", "url": "https://github.com/andrebarthelmeshellmuth/blackbox-optimizer" }
    ],
    "require": {
        "spryker-community/search-toolkit": "^1.1.0"
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
no longer reflects the packages' current releases. Use `^1.1.0`.

## License

MIT, see [LICENSE](LICENSE).
