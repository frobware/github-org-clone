# github-org-clone

Clone all repositories from a GitHub organisation with a single command.

## Quick start

```bash
./github-org-clone openshift | sh
```

This fetches the repo list from GitHub, then outputs git commands to stdout. Pipe to `sh` to run them.

By default:
- Clones to `~/src/github.com/<org>/<repo>`
- Only clones public repositories
- Only clones source repositories (repos the org owns, not forks)
- Skips empty repositories

The default path follows the convention `~/src/<forge>/<org>/<repo>`, so you can organise clones from different forges (github.com, gitlab.com, etc.) in one place.

## Trying it out

To test without writing to `~/src`, use a temporary directory:

```bash
./github-org-clone --basedir /tmp/test --shallow frobware | sh
```

This clones to `/tmp/test/github.com/frobware/...` instead. The `--shallow` flag makes it faster.

## Keeping repos up to date

Run the same command again:

```bash
./github-org-clone openshift | sh
```

If a repo already exists locally, it outputs a `git fetch` instead of `git clone`. Running periodically keeps everything in sync.

## Parallel cloning

For large organisations, clone in parallel:

```bash
./github-org-clone openshift | parallel --bar --eta -j 8
```

The `--bar` flag shows a progress bar, `--eta` shows estimated time remaining.

The included `dop` wrapper provides `--bar` and `--eta` by default, plus per-job logging and error summaries:

```bash
./github-org-clone openshift | ./dop -j 8
```

## Including forks

By default, only source repos are cloned. To clone forks instead:

```bash
./github-org-clone --forks openshift | sh
```

## Listing repositories

See what's in an org without cloning:

```bash
./github-org-clone --list openshift
```

Output shows repo name, parent (if it's a fork), and archived status:

```
release-tests       -                      -
appliance           -                      -
some-fork           upstream/original      -
old-project         -                      archived
```

For scripting, get JSON:

```bash
./github-org-clone --list --json openshift
```

## Cloning specific repos

Clone only certain repos from an org:

```bash
./github-org-clone openshift release api console | sh
```

## Options

```
Filtering:
  -s, --source        Only source repos, not forks (default)
  -f, --forks         Only forked repos
  -a, --no-archived   Skip archived repos
  --private           Also include private repos (default: public only)

Listing:
  -l, --list          List repos and exit
  --json              JSON output (with --list)

Cloning:
  -b, --basedir DIR   Base directory (default: ~/src, or CLONE_BASEDIR env var)
  --shallow           Shallow clones (--depth 1)

Other:
  -h, --help          Show help
  -v, --version       Show version
```

## More examples

Clone to a different directory:

```bash
./github-org-clone --basedir /tmp/test openshift | sh
```

Shallow clone for a quick local copy:

```bash
./github-org-clone --shallow openshift | sh
```

Preview what would happen:

```bash
./github-org-clone openshift | head
```

Skip archived repos:

```bash
./github-org-clone --no-archived openshift | sh
```

## Requirements

- `gh` (GitHub CLI) - authenticated
- `jq`
- `git`
- `parallel` or `dop` (optional, for parallel execution)
