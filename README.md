# typedoc-plugin-markdown: `excludeScopesInPaths` breaks paths on Windows

Minimal reproduction for typedoc-plugin-markdown, with `entryPointStrategy: "packages"`, `router: "module"` and `excludeScopesInPaths: true` for one scoped package (`@scope/ui`) that has a namespace.

```sh
npm install
npx typedoc
```

On Linux and macOS the namespace gets its own page and the links between the pages are correct:

```
docs/README.md
docs/ui/namespaces/helpers.md

docs/README.md:9:- [helpers](ui/namespaces/helpers.md)
```

On Windows the same run writes the page to `docs/ui\namespaces\helpers.md` and links to `ui\namespaces\helpers.md`, so the path is one segment and every relative link between pages resolves to the wrong place.

The `Repro` workflow runs the same commands on `ubuntu-latest` and `windows-latest`.

## Cause

`removeFirstScopedDirectory()` rewrites the URL with `path.sep`, so on Windows the `/` of a URL becomes `\`:

https://github.com/typedoc2md/typedoc-plugin-markdown/blob/main/packages/typedoc-plugin-markdown/src/libs/utils/remove-first-scoped-directory.ts
