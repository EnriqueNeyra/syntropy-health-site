# health.syntropylabs.io

Static public site for [Syntropy Health](https://github.com/EnriqueNeyra/syntropy-health), served by GitHub Pages.

| Path | Purpose |
|---|---|
| `/` | Landing page |
| `/docs/` | How it works — architecture, relay design, data handled (also used for EHR app-program review) |
| `/privacy/`, `/terms/` | Privacy policy and terms of use |
| `/callback/` | **OAuth redirect URI** registered with EHR vendors |

## The callback relay

Every Syntropy Health instance runs at its own private address, but OAuth needs one fixed HTTPS redirect URI.
`/callback/` is that URI. It is a static page with a strict Content-Security-Policy that:

1. reads the instance address from the OAuth `state` (base64url JSON, field `d`),
2. accepts it only if it is a loopback, private-network (10/8, 172.16/12, 192.168/16, 100.64/10), Tailscale (`*.ts.net`),
   mDNS (`*.local`) or other internal host — otherwise falls back to `http://localhost:8000`,
3. forwards the full query string (one-time `code` + `state`, or an `error`) and fragment to `<instance>/callback`.

It makes no network requests and stores nothing. The authorization code is useless without the PKCE verifier that exists only on
the instance.

Wearable providers that require a client secret (Oura, WHOOP) use the Cloudflare Worker in the main repository
(`cloudflare-worker/`) instead.

## Pages

All pages are self-contained: one local stylesheet (`assets/site.css`), no external fonts, scripts, analytics or trackers.
