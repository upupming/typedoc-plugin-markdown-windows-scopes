# typedoc-plugin-markdown: `excludeScopesInPaths` puts Windows path separators in URLs

Minimal reproduction: one scoped package (`@scope/ui`) with a namespace, rendered with `entryPointStrategy: "packages"`, `router: "module"` and `excludeScopesInPaths: true`.

```sh
npm install
npx typedoc
```

The `Repro` workflow runs those two commands on `ubuntu-latest` and `windows-latest`.

## Linux and macOS

```
docs/packages.md:9:- [helpers](ui/namespaces/helpers.md)
docs/ui/namespaces/helpers.md:5:[@scope/ui](../../packages.md) / helpers
docs/ui/namespaces/helpers.md:17:Formats [Options.value](../../packages.md#value).
```

## Windows

```
docs/packages.md:9:- [helpers](ui\namespaces\helpers.md)
docs/ui/namespaces/helpers.md:5:[@scope/ui](packages.md) / helpers
docs/ui/namespaces/helpers.md:17:Formats [Options.value](packages.md#value).
```

The page is written to the right file, but its URL is `ui\namespaces\helpers.md`: a single path segment. Links to the page then carry a backslash, and links from the page are resolved against `docs/` instead of `docs/ui/namespaces/`, so every relative link between pages is wrong.

## Cause

[`removeFirstScopedDirectory()`](https://github.com/typedoc2md/typedoc-plugin-markdown/blob/main/packages/typedoc-plugin-markdown/src/libs/utils/remove-first-scoped-directory.ts) rewrites the URL with `path.sep`, so on Windows the `/` of a URL becomes `\`.
