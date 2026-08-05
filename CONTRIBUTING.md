# Contributing to search-toolkit

This repository is a composer metapackage — it has no source code of its own, just a
`composer.json` requiring compatible versions of:

- [search-debug](https://github.com/andrebarthelmeshellmuth/spryker-search-debugger)
- [search-ranking](https://github.com/andrebarthelmeshellmuth/spryker-search-ranking)
- [search-ranking-optimizer](https://github.com/andrebarthelmeshellmuth/spryker-search-ranking-optimizer)

If you're looking to fix a bug or add a feature, it almost certainly belongs in one of those three
repos instead — please open your PR there.

What does belong here:
- Bumping a `require` version constraint when a sub-package cuts a new release.
- Fixing `composer.json` metadata (description, keywords, `support` links, etc).

## Before opening a PR

```
composer validate --no-check-publish --strict
```

This is the only CI check — there's no code to test or lint.

## Making a change

- Branch from and target `main`; branches are merged via squash.
- Keep version constraints in sync with what's actually been released — don't point at an
  unreleased version.

## Reporting issues

Use the issue template. If the issue is actually about the behavior of search-debug,
search-ranking, or search-ranking-optimizer rather than this metapackage's version wiring, please
open it in that package's repo instead. For security issues, see [SECURITY.md](SECURITY.md).

## License

By contributing, you agree your contribution is licensed under this project's [MIT license](LICENSE).
