# Campaign Tracking Protocol

This portfolio is the durable index for vulnerability-research campaigns in
`crook3dfingers/*` that use the `vuln-research` GitHub topic.

## When To Update This Portfolio

Update this repo whenever a campaign is created, renamed, archived,
published, materially changes status, or gets a new headline finding suitable
for public indexing.

This includes campaigns researched locally under:

```text
/home/ubuntu/vuln_research/projects/<slug>
```

## New Campaign Checklist

Use this checklist before calling a campaign scaffold complete.

1. Choose a short slug, for example `gpsd`, `dds`, or `baseband-shannon`.
2. Create or clone the campaign repo as `crook3dfingers/vuln-research-<slug>`.
3. Place the local checkout at `projects/<slug>` unless there is a clear
   reason to use another path.
4. Add the GitHub topic:

   ```sh
   gh repo edit crook3dfingers/vuln-research-<slug> --add-topic vuln-research
   ```

5. Scaffold the standard campaign layout:

   ```text
   <repo>/
   ├── AUTHORIZATION.md
   ├── README.md
   ├── evidence/
   ├── submissions/
   ├── notes/
   ├── harnesses/
   ├── scratch/
   └── corpus/
   ```

6. Add a `.gitignore` that excludes generated binaries, build trees, crash
   dumps, large corpora, emulator images, firmware blobs, secrets, and local
   tool state.
7. Add or update one row in `README.md` under "Active / recent campaigns".
8. Verify the row links to `https://github.com/crook3dfingers/vuln-research-<slug>`.
9. Keep status and headline text accurate, concise, and disclosure-safe.
10. Commit the portfolio update separately from campaign evidence changes
    unless the user asks for a combined commit.

## README Row Format

Use this table shape:

```md
| **Campaign name** (target family) | [`vuln-research-<slug>`](https://github.com/crook3dfingers/vuln-research-<slug>) | Current status | Public-safe headline |
```

Status examples:

- `Scoped, scaffold-only`
- `Audit in progress`
- `Harnessing in progress`
- `Runtime confirmation in progress`
- `Disclosure prep`
- `Vendor reported`
- `Public advisory published`
- `Archived`

Headline guidance:

- OK: target class, bug class, proof status, disclosure stage.
- Not OK: exploit recipe, weaponized chain details, secrets, unreleased vendor
  correspondence, private crash corpus, or sensitive infrastructure details.

## Standard Campaign Files

`AUTHORIZATION.md` should capture:

- Research owner and date opened
- Scope and out-of-scope targets
- Legal/authorization basis
- Threat model
- Disclosure timeline
- Contact/vendor notes

`README.md` should capture:

- Target family and repo purpose
- Current status
- Setup notes
- Finding/evidence index
- How to reproduce only safe, authorized test cases

`evidence/` should use one directory per hypothesis or finding, for example
`evidence/H-0007/` or `evidence/F-0003/`.

`submissions/` should hold disclosure-ready advisories, vendor reports, and
public explanation material.

`notes/`, `harnesses/`, `scratch/`, and `corpus/` are campaign-local working
areas. Keep bulky or sensitive generated output out of git.

## Verification Commands

Useful checks:

```sh
gh repo view crook3dfingers/vuln-research-<slug> --json name,visibility,repositoryTopics,url
git -C projects/<slug> status --short --branch
git -C vuln-research-portfolio status --short --branch
```

To list all portfolio-discoverable repos:

```sh
gh repo list crook3dfingers --limit 200 --topic vuln-research
```
