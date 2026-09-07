# alanwtom/homebrew-tap

Homebrew casks for my own software.

```bash
brew install --cask alanwtom/tap/current
```

That one command taps this repository and installs the app; there is no need to
`brew tap` first.

## What's here

**[Current](https://current.alantom.dev)** — a native macOS BitTorrent client.
Apple Silicon, macOS 26 or later.

## Why a personal tap and not homebrew-cask

`brew audit --cask --new` passes on everything except notability: their audit
requires the upstream repository to have 75 stars, 30 forks or 30 watchers, and
Current is newer than that. Their written policy names no numbers — the number
lives in the tool.

So this tap exists to make `brew install` work today. When the app clears one of
those thresholds the identical cask can be submitted upstream, and the shorter
`brew install --cask current` will start working. Nothing here would need to
change.

## What you're trusting when you install from here

Worth stating plainly, because a tap is a place people run installs from:

- **The cask pins a SHA-256.** Homebrew refuses the install if the downloaded
  disk image doesn't match it byte for byte, so a swapped file fails rather
  than installs.
- **It downloads from the GitHub release asset**, not from a download page. A
  release asset is immutable; a page always serves the newest build, so its
  checksum would go stale and — worse — a page that got taken over could serve
  anything under a checksum nobody re-checked.
- **The app is signed with a Developer ID and notarised by Apple**, and macOS
  verifies both before it will run, independently of Homebrew.
- **Nothing in this repository can write to this repository.** There is one
  workflow, it has `contents: read`, and every action it uses is pinned to a
  commit hash rather than a tag. `brew tap-new` scaffolds a daily autobump and
  a bottle publisher that both need write access; a tap has no business
  carrying those, so they were deleted.
- **CI re-audits every cask against its live download** on each push. If an
  upstream release were ever re-cut under an existing tag, the checksum
  mismatch fails here instead of on someone's laptop.

## Updating a cask for a new release

Two lines change — the version and the checksum:

```bash
shasum -a 256 Current.dmg
```

`brew bump-cask-pr --version=1.1.2 alanwtom/tap/current` does both and opens the
PR. Deliberately not automated on a schedule: a bot that can bump a cask is a
bot that can rewrite a URL and a checksum together, which is exactly the change
an attacker would want to make quietly.
