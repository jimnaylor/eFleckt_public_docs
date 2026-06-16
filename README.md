# eFleckt_public_docs

Canonical public-facing documents for [eFleckt](https://efleckt.com). This repo is the **single source of truth** for the documents listed below. Both the iOS app and the marketing website consume from here — do not edit copies elsewhere.

## What's here

| File | Purpose |
|---|---|
| `privacy-policy.md` | GDPR / CCPA-aware privacy policy. Discloses processors, data retention, user rights, and Article 9 special-category data handling. |
| `terms-of-service.md` | User terms of service. Governs the contractual relationship between Naylormade, LLC and the user. |

## Consumers

| Surface | How it consumes |
|---|---|
| iOS app ([`eFleckt_flutter_claude`](https://github.com/jimnaylor/eFleckt_flutter_claude)) | `docs/legal/` is a git submodule pointing at `main` of this repo. Pulled at build time; bundled into the app binary. |
| Marketing website ([`landingPage_eFleckt`](https://github.com/jimnaylor/landingPage_eFleckt)) | `LegalPage.tsx` fetches via `https://raw.githubusercontent.com/jimnaylor/eFleckt_public_docs/main/<filename>`. Server-side renderable; no auth required (this repo is public). |

## How to update

1. Open a PR in this repo with the doc changes
2. Get review (legal + product), merge to `main`
3. Bump the corresponding version constant in `eFleckt_flutter_claude` if user re-consent is required:
   - `tosCurrentVersion` (in `lib/models/account_data.dart`) for `terms-of-service.md`
   - `privacyPolicyCurrentVersion` (same file) for `privacy-policy.md`
4. Bump the iOS app's submodule pointer in `eFleckt_flutter_claude` to pick up the new content (`git submodule update --remote docs/legal && git add docs/legal && git commit`)
5. The marketing site fetches `main` directly — change goes live on next deploy (or sooner if the page is fetched fresh)

Version bumps trigger a re-prompt for existing users in-app. The marketing site does not version-gate — it always serves the current copy on `main`. Both are intentional.

## Why this repo exists

The previous design had `landingPage_eFleckt/LegalPage.tsx` fetching from `raw.githubusercontent.com/jimnaylor/eFleckt_flutter_claude/main/docs/legal/` — but that repo is private, so every fetch 404'd and silently fell back to a bundled web-side copy. The result: iOS and web copies drifted independently for ~6 weeks before being caught (filed as [eFleckt_flutter_claude#397](https://github.com/jimnaylor/eFleckt_flutter_claude/issues/397)).

Carving the canonical legal docs into this small public repo eliminates the drift mechanism: both consumers pull from the same `main` branch of a public repo. The Flutter codebase stays private; only the documents that are public-by-purpose (the legal policies that already live on the website) are public.

## Editing discipline

- **Do not edit copies in `eFleckt_flutter_claude/docs/legal/` or `landingPage_eFleckt/*.md` directly.** Those are no longer canonical — the Flutter copy is a submodule pointer, the web fetches from this repo.
- **Bump effective dates** in the doc body when content changes meaningfully.
- **Cross-check both docs** when one changes — terms and privacy reference each other in places.
- **Consult counsel** for material changes to either doc before merging.

## License

No open-source license. Copyright Naylormade, LLC. All rights reserved. These are bespoke legal documents specific to the eFleckt product; they are not template policies and are not licensed for reuse.
