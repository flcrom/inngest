# api-docs.inngest.com

This is an API docs site using TanStack Start and Fumadocs. It generates API
pages and public OpenAPI assets from specifications in this repository.

## Source and generated artifacts

The source of truth is:

- `docs/openapi/v3/api/v1/spec.yaml` for REST v1.
- `proto/api/v2/`, `tools/convert-openapi/`, and
  `docs/api_v2_examples.json` for REST v2. The converter appends TODO stubs to
  the examples file when an operation or response does not have an entry.
- `content/docs/index.mdx`, `content/docs/authentication.mdx`, and
  `content/docs/v1/index.mdx` for hand-written pages.

The files under `public/api-specs/`, the generated endpoint pages under
`content/docs/v1/` (except `index.mdx`), and all of `content/docs/v2/` are
derived artifacts. They are committed so API changes and the exact public spec
can be reviewed together. Do not edit them directly.

From the repository root, install the site dependencies once and regenerate the
complete derived set with:

```sh
pnpm --dir docs/api-docs install --frozen-lockfile
make api-docs
```

`make docs` only creates the intermediate OpenAPI files. `make api-docs` also
filters the public specs and generates the endpoint MDX consumed by the site.
CI runs `make api-docs-check` and fails if regenerating changes a committed file
or creates an untracked generated file.

## Development

Generate the artifacts first, then start the dev server from this directory:

```sh
pnpm run dev
```

The production build intentionally does not regenerate documentation:

```sh
pnpm build
```

This ensures local, CI, and deployment builds all use the reviewed, committed
artifacts rather than producing different documentation during deployment.

## Deployment

The `API Docs` workflow checks generation consistency and builds changes on pull
requests and relevant pushes to `main`. After that workflow succeeds for a
`main` push, `API Docs deploy` deploys the exact checked commit to the production
Vercel project. This is intentionally independent of the OSS release workflow:
the site includes hand-written documentation and Cloud API contracts that do
not share the CLI release cadence.

`API Docs deploy` also supports manual preview and production dispatches for
review and recovery. It expects `VERCEL_TOKEN`, `VERCEL_ORG_ID`, and
`VERCEL_PROJECT_ID` repository secrets.
