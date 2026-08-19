# Roundtrip docs

Docs site for Roundtrip, an endpoint-testing CLI that also verifies what
actually landed in your database. Built on [Mintlify](https://mintlify.com).

## Access model

Every page falls into one of three tiers. Which tier a page is in is decided
by two things working together: the page's own frontmatter, and the
navigation group it's listed under in [docs.json](docs.json).

| Tier | Who sees it | How it's set | Where it lives |
|---|---|---|---|
| Public | Signed-out visitors | `public: true` in page frontmatter, or `"public": true` on its navigation group | root — [index.mdx](index.mdx), [quickstart.mdx](quickstart.mdx) |
| Logged in | Any authenticated user | No `public` or `groups` field at all (this is the default) | [account/](account/) |
| Group-gated | Authenticated users in a specific group | `groups: ["name"]` in page frontmatter | [customers/](customers/) → `customer`, [internal/](internal/) → `internal` |

**Group-level `public` overrides page frontmatter.** If a page is listed
under a navigation group with `"public": true`, it's public — even if that
page's own frontmatter has no `public` field and even if it has a `groups`
field. Keep gated pages out of public groups; when in doubt, gate at the page
level with frontmatter rather than the group level, since a page-level
`groups` field can't be silently overridden by a config change elsewhere.

## Folder structure

Folders mirror access tier, not topic, so you can tell what a page's
audience is without opening it:

```
index.mdx, quickstart.mdx   public
account/                    logged in, no group required
customers/                  "customer" group
internal/                   "internal" group
```

When adding a page, put it in the folder for its intended audience first,
then set matching frontmatter (or add it to the matching group in
`docs.json`). New pages don't appear in the sidebar automatically — they
must be added to a `pages` array in `docs.json`.

## Preview commands

Install the CLI once:

```bash
npm i -g mint
```

```bash
mint dev
```

Local preview at `localhost:3000`. This does not enforce production
authentication, so use `--groups` to mock membership and check how a gated
tier will actually render before publishing:

```bash
mint dev --groups customer
mint dev --groups internal
```

Before pushing, also run:

```bash
mint broken-links   # catch dead internal links, e.g. after moving a page
mint validate        # validate docs.json and the build
```