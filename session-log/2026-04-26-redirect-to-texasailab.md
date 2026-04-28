# 2026-04-26 — Cutover atxailab.com → texasailab.com

**Branch:** feat/migrate-to-texas
**Status:** shipped to main, Vercel auto-deployed, 301s verified on production

## Summary

Flipped atxailab.com to a 301 mirror of texasailab.com using Vercel `redirects` (statusCode 301, not `permanent`). Same pattern used on wilco-ai-lab. The Austin pages now live under the flattened `/austin/...` URL structure on texasailab.com, so every legacy `/ai-training-*` slug maps to its new home; everything else falls through to `/austin/`.

## Files touched

- `vercel.json` — added (was missing). Contains the full redirect map.
- `sitemap.xml` — deleted. The site no longer serves content; the new sitemap lives on texasailab.com.
- `robots.txt` — kept allow-all, dropped the now-broken `Sitemap:` line that pointed to the deleted file.
- `session-log/2026-04-26-redirect-to-texasailab.md` — this entry.

## Redirect map

| From | To |
| --- | --- |
| `/blog/:slug/` | `https://texasailab.com/articles/:slug/` |
| `/blog/`, `/blog` | `https://texasailab.com/articles/` |
| `/ai-training-austin/` | `https://texasailab.com/austin/` |
| `/ai-training-east-austin/` | `https://texasailab.com/austin/east-austin/` |
| `/ai-training-lakeway/` | `https://texasailab.com/austin/lakeway/` |
| `/ai-training-south-austin/` | `https://texasailab.com/austin/south-austin/` |
| `/ai-training-west-austin/` | `https://texasailab.com/austin/west-austin/` |
| `/author/tj-larkin/`, `/author/` | `https://texasailab.com/author/tj-larkin/` |
| `/about/` | `https://texasailab.com/about/` |
| `/contact/`, `/newsletter/` | `https://texasailab.com/contact/` |
| `/services/` | `https://texasailab.com/#services` |
| `/events/` | `https://texasailab.com/austin/#events` |
| `/`, `/:path*` | `https://texasailab.com/austin/` |

All redirects are 301 (not `permanent`) so we can adjust later without permanent-cache concerns from edge servers.

## Verification

Run on production after Vercel auto-deploy:

```
curl -sI https://www.atxailab.com/ | grep -iE 'HTTP|location'
curl -sI https://www.atxailab.com/blog/ai-101-what-every-business-owner-needs-to-know/ | grep -iE 'HTTP|location'
curl -sI https://www.atxailab.com/ai-training-lakeway/ | grep -iE 'HTTP|location'
```

Expect 301 with Location pointing to texasailab.com on each.

## Trade-offs / surprises

- The `/services/` redirect uses a hash anchor rather than a real path because the canonical "services" content on texasailab.com is a section on the homepage, mirroring wilco-ai-lab's pattern.
- Static HTML files (`index.html`, city pages) remain in the repo but are no longer served, since `redirects` fire before file resolution. Left in place to keep the diff surgical; they can be removed in a future cleanup if desired.
