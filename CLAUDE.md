# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

The public legal/support site for the FogTrail iOS app: five hand-written static pages (`index`, `privacy`, `terms`, `safety`, `support`) plus one `style.css`. No build step, no framework, no tests, no dependencies. It lives inside the app folder (`~/Desktop/Live Apps/FogTrail/FogTrail-site/`) but is its **own git repo** (`origin` = `github.com/rodelrepulle/fogtrail-site`, branch `main`). The app repo's `CLAUDE.md` is the source of truth for the app itself.

## Deploy

GitHub Pages serves `main` at `https://rodelrepulle.github.io/fogtrail-site/<page>.html`. **Pushing is publishing** — there is no staging. After a push, wait about a minute and `curl` the live page to confirm the change landed (a push can succeed while Pages lags).

```bash
git -c credential.helper= -c "credential.helper=!gh auth git-credential" push origin main
```

The plain `git push` can fail with 403 because the macOS keychain (`osxkeychain`) holds a stale credential; `gh`'s login is the one to use. If `gh` itself 403s, its token is a fine-grained PAT lacking *Contents: Read and write* — re-run `gh auth login -h github.com -p https -w`.

## These pages are contracts, not marketing

- **The app links to them.** `LegalLinks.site` in the app (`Sources/FogTrail/Domain/LegalLinks.swift`, pinned by `LegalLinksTests`) hard-codes this base URL. Never rename or remove a page, and never change the host, without changing the app and shipping a build.
- **Apple requires them to be true.** The privacy page is a submission requirement for a HealthKit app (App Review 5.1.3), and App Store Connect's App Privacy answers (web UI only — not in the API) must match it. The code is fresher than the prose: before editing a claim about what is stored, sent or collected, check the app (Phase C now adds accepted friends, selected place delivery, optional statistics/route sync and optional APNs token registration; signing in alone does not enable walking sync) and `Sources/FogTrail/PrivacyInfo.xcprivacy`.
- **Keep the same facts in every page that states them.** The age rule (13+, parent/guardian under 19), the sign-in doors (Apple gives name only; Google also gives an email), the locked-screen recording, and the contact address each appear on several pages. Change one, grep for the rest. The walking-safety page once said the app only works "on screen" while Support said it records in a pocket.
- **Bump `Last updated:` on any content change.** It sits under the title in `privacy`, `terms` and `safety`, and went stale twice.
- **Before a build that adds friends, chat or sync ships, rewrite Privacy, Terms and Safety first** (the app's `Roadmap.md` requires it), and re-answer age-rating questions from their current Apple definitions (user-generated names/hints exist even without chat; selected-recipient inbox is distinct from a public/amplifying feed). The Phase C pages were rewritten October 8–9, 2026 and published October 9, 2026; see `../marketing/appstore/phase-c-release-checklist.md`.

## Conventions

- Contact is `support@cairnstudio.si` (mailto links carry a `?subject=` of `FogTrail Support|Privacy|Terms`). It appears in `support`, `privacy` and `terms`; `index` and `safety` show no address.
- Pages share one skeleton — `<div class="wrap">`, a `header.brand`, `h1.title`, `p.updated`, `.card` / `.card.warn` callouts and a closing `nav.links`. `style.css` uses a dark theme driven by `:root` variables; reuse them rather than adding colours.
- Commit messages are `docs: …` (conventional commits), as in the existing history.
- A dated site copy that is not a git clone deploys nothing — this one is a clone; check `git remote get-url origin` if in doubt.
