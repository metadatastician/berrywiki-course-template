<!--
SPDX-FileCopyrightText: 2026 Jonathan D.A. Jewell
SPDX-License-Identifier: CC-BY-SA-4.0
-->

# Course wiki starter

A **pre-structured module wiki** you can press *Use this template* on and fill
in. It is not an application. There is nothing to install and nothing to run.

Every page is plain Markdown. If you never touch any tooling, you still have a
properly structured course wiki — which is the point.

## What you get

```
wiki/
  Home.md
  Syllabus.md
  Weeks.md
  Weeks--Week-01.md … Weeks--Week-12.md
  Talk--Week-01.md
  Assignments.md
  Assignments--Coursework-1.md
  Assignments--Coursework-2.md
  Groups.md
  Resources.md
  Staff.md
  Staff--Answer-Keys.md
  Staff--Drafts.md
  _Sidebar.md        ← generated, never hand-edited
```

24 pages, every one of them a stub with the headings already in place.

## The same tree, two ways

**In GitHub's own wiki reader**, the pages are a flat list and `_Sidebar.md`
renders natively as the navigation:

```
Notebook
- Home
- Syllabus
- Weeks
  - Week 01 — Introduction and orientation
  - Talk — Week 01
  - Week 02 — Core concepts
  …
- Assignments
  - Coursework 1
  - Coursework 2
- Groups
- Resources
- Staff
  - Answer Keys
  - Drafts
```

**In [BerryWiki](https://github.com/metadatastician/berrywiki)** — a local,
zero-JavaScript companion for GitHub Wikis — the same files open as a
hierarchical notebook with that tree in a pane, plus backlinks and structure
diagnostics. That is strictly optional. This template is designed to read
correctly without it.

## Where the hierarchy actually lives

Not in the filenames. Each page carries a small HTML comment at the top that
GitHub does not render:

```markdown
<!-- berrywiki
id: 0199b1c0-0000-7000-8000-000000000002
parent: 0199b1c0-0000-7000-8000-000000000001
position: 10
kind: page
tags: []
archived: false
-->

# Syllabus
```

`parent` is the tree. The `--` in a filename is only a naming convention, which
is why `Talk--Week-01.md` can sit beside `Weeks--Week-01.md` in the tree despite
the different prefix — its `parent` says so.

**Regenerate `_Sidebar.md` rather than editing it.** With BerryWiki installed:

```sh
berrywiki sidebar wiki --write
```

Without it, edit `_Sidebar.md` by hand if you must — nothing breaks, it is just
a Markdown file — but it will be overwritten the first time you do run that
command.

## Making it your wiki

Two ways, and the first is the one most people want.

**Keep it in the repository.** A folder in a repository can be reviewed in a
pull request. A GitHub Wiki cannot. For a module that more than one person
teaches, that is usually the better arrangement, and the CI check below works
on it directly.

**Publish it to the repository's GitHub Wiki.** Create the wiki once through the
web UI (Settings → Features → Wikis, then add any page), then:

```sh
git subtree push --prefix wiki https://github.com/<you>/<repo>.wiki.git master
```

The wiki is an ordinary git repository, so this is an ordinary push. Nothing is
transformed on the way in — what is in `wiki/` is what the wiki gets.

## The structure check

`.github/workflows/wiki.yml` runs `berrywiki check` over `wiki/` on every push
and pull request. It finds broken links, missing parents, cycles and duplicate
ids, and fails the build on the last of those.

The point, for a module wiki, is finding out that Week 7 links to a page you
deleted *before* a student does.

Warnings — a broken link, a missing parent — do not fail the build by default,
because a wiki that is still being written has them. Set `strict: 'true'` in the
workflow once your tree is clean and you want it to stay that way.

**Before you rely on it, pin it.** The workflow ships with
`uses: metadatastician/berrywiki@main`; replace `@main` with a commit SHA. The
Action builds its CLI from whatever revision you pinned it at, so a pinned
workflow also restores a cached binary instead of rebuilding.

If you do not want any of this, delete `.github/workflows/wiki.yml`. The wiki
is unaffected.

## The `Staff` subtree is not access control

`Staff.md` and its children carry an `access:` key in their metadata. **Nothing
enforces it.** It is a declaration of intent that is preserved as unknown
metadata and read by no code today.

What actually keeps those pages away from students is the repository being
**private** — a GitHub Classroom repository is private by default. If you make
your repository public, everything in it is public, the answer keys included.
Move them out before you do.

## Deleting pages

Delete any page you do not want. Delete a page whose children you are keeping
and the structure check will report the children as having a missing parent — so
delete subtrees together, or re-point the children's `parent` first.

## Licence

The template content is **CC-BY-SA-4.0** (see [`LICENSE`](LICENSE)), so you can
adapt it for your own institution. Attribution and the same licence apply to
what you redistribute *of this scaffolding*; the course material you write into
it is your own.

BerryWiki itself is MPL-2.0 for code and CC-BY-SA-4.0 for its prose.
