# DNS & domains (GitHub Pages + GoDaddy)

If a brand link opens a **GoDaddy parking / landing page**, the HTML links are not wrong — **DNS or domain forwarding** for that name is still pointing at GoDaddy instead of GitHub Pages.

## Correct outcome

| Domain | Should resolve to |
|--------|-------------------|
| `tyneside.software` | GitHub Pages (`185.199.108.153` … `185.199.111.153`) |
| `tyneside.cleaning` | same |
| `tyneside.charity` | same |
| `tyneside.group` | same |
| `tyneside.games` | same |

Site HTML always links with **`https://tyneside.<tld>/`**. There are no GoDaddy URLs in the sites.

## GoDaddy checklist (each domain)

In GoDaddy → **DNS** for that domain:

1. **Turn OFF** Domain Forwarding / Masking / “Forwarding with masking”.
2. **Turn OFF** any Parking / “Coming soon” / holding page.
3. **Apex (`@`)** — four **A** records (only these; remove old GoDaddy A/park records):

   | Type | Name | Value |
   |------|------|--------|
   | A | `@` | `185.199.108.153` |
   | A | `@` | `185.199.109.153` |
   | A | `@` | `185.199.110.153` |
   | A | `@` | `185.199.111.153` |

4. **`www`** — one **CNAME**:

   | Type | Name | Value |
   |------|------|--------|
   | CNAME | `www` | `tyneside-software.github.io` |

5. Wait for DNS (often minutes; can be up to 48h). Then hard-refresh the browser.

6. In GitHub → each site repo → **Settings → Pages**:
   - Custom domain set (matches `CNAME` file in repo)
   - **Enforce HTTPS** checked (needs cert “Approved”)

## Status notes (as of 2026-07-14)

- Nameservers are still **GoDaddy** (`*.domaincontrol.com`) — that is fine **if** DNS records point at GitHub as above.
- HTTPS enforced on: software, cleaning, charity, games.
- **tyneside.group**: certificate state was `dns_changed` / re-issuing; `www.tyneside.group` HTTPS may fail until the cert is approved again. Apex `https://tyneside.group/` can still serve content while that settles.

## Verify

```text
https://tyneside.software/
https://tyneside.cleaning/
https://tyneside.charity/
https://tyneside.group/
https://tyneside.games/
```

Each should show the Tyneside site (nav + brand), **not** a GoDaddy “Get this domain” / park page.

## Related repos

Pages sources: `Tyneside-Software/tyneside.*` on branch `main`.
