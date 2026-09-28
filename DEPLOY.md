# Deploy clockfilter.com

Static HTML and CSS, published from the root of the public `clockfilter/website` repository on `main`. No build step, JavaScript, tracking, or third-party assets are required.

## GitHub Pages

1. Push to `main`.
2. Open Settings → Pages. Select **Deploy from a branch**, `main`, `/ (root)`.
3. Set the custom domain to `clockfilter.com` before changing DNS. Keep the root CNAME file containing `clockfilter.com`.
4. After GitHub issues the certificate, turn on **Enforce HTTPS**.

## GoDaddy DNS — website records only

Open GoDaddy → Domain Portfolio → clockfilter.com → DNS → DNS Records. Keep the nameservers `ns71.domaincontrol.com` and `ns72.domaincontrol.com`. Do not change nameservers or use a setup wizard that replaces the zone.

Record the current DNS values before editing. Delete only the existing apex (`@`) A records `13.248.243.5` and `76.223.105.230`. Delete the `www` CNAME only if it points to `clockfilter.com`. If records differ, inspect them before proceeding.

Add the following records, using GoDaddy’s default TTL:

| Type | Name | Value |
| --- | --- | --- |
| A | @ | 185.199.108.153 |
| A | @ | 185.199.109.153 |
| A | @ | 185.199.110.153 |
| A | @ | 185.199.111.153 |
| AAAA | @ | 2606:50c0:8000::153 |
| AAAA | @ | 2606:50c0:8001::153 |
| AAAA | @ | 2606:50c0:8002::153 |
| AAAA | @ | 2606:50c0:8003::153 |
| CNAME | www | clockfilter.github.io |

These addresses were confirmed on 2026-09-28 against [GitHub’s custom domain documentation](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site). DNS propagation and certificate issuance may take up to 24 hours. GitHub redirects the configured www variant to the apex domain.

### Preserve all email and verification records

Do not add, edit, or delete any of these:

- The five MX records: `aspmx.l.google.com`, `alt1.aspmx.l.google.com`, `alt2.aspmx.l.google.com`, `alt3.aspmx.l.google.com`, `alt4.aspmx.l.google.com`.
- The single apex SPF TXT: `v=spf1 include:dc-aa8e722993._spfm.clockfilter.com ~all`.
- TXT at `dc-aa8e722993._spfm` (currently `v=spf1 include:_spf.google.com ~all`).
- TXT at `google._domainkey`.
- TXT at `_dmarc`.
- The apex TXT beginning `google-site-verification=`.

Do not add a second SPF record. Leave every other record unchanged.

## Verify after DNS propagates

```sh
curl -fsSL https://clockfilter.com
curl -fsSL https://clockfilter.com/about
curl -fsSL https://clockfilter.com/contact
curl -I https://www.clockfilter.com
dig MX clockfilter.com +short
dig TXT clockfilter.com +short
dig TXT dc-aa8e722993._spfm.clockfilter.com +short
dig A clockfilter.com +short
dig AAAA clockfilter.com +short
dig CNAME www.clockfilter.com +short
```

All three HTML responses must include `CLOCK FILTER, LLC`, contain no placeholder page, and require no sign-in. Contact must contain the visible `mailto:ryan@clockfilter.com` link. Confirm all five MX hosts remain present and exactly one apex SPF record still resolves through the existing `_spfm` host to `_spf.google.com`. Compare DKIM, DMARC, Google verification, and nameservers with the pre-change snapshot. Verify Enforce HTTPS is enabled.

## Local preview

Run `python3 -m http.server 8000` at the repository root and open `http://localhost:8000`. About and Contact use directory index files so their clean paths work without a routing framework.
