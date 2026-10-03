# FerroMagnet feed

Indicators of C2 and offensive-security-tooling infrastructure observed by FerroMagnet.
Only indicators that a live check confirmed are published. Updated automatically; each
commit is one refresh in which at least one file changed. TLP:CLEAR.

| File | Contents |
|---|---|
| `ip_port.txt` | one `ip:port` per line, grouped by family, with a trailing comment |
| `ip.txt` | one address per line, grouped by family |
| `iocs.csv` | every indicator: family, kind, confidence, times, ip, port, STIX pattern, cluster |
| `iocs.json` | the same, as a document with `generated_at` and `count` |
| `misp/` | a MISP feed: `manifest.json`, one event per family, `hashes.csv` |
| `stix/bundle.json` | STIX 2.1: indicators, addresses, a malware per family, relationships |
| `opencti/csv-feed.json` | an OpenCTI CSV feed (with its mapper) for `iocs.csv` |

Confidence (0-100) is the strength of the evidence behind the indicator: the match that
produced it, lowered to the strength of the latest live confirmation when that is weaker.
`last_verified` is when a live check last confirmed the service. For most families that
is the fingerprint seen again; for listeners a scanner cannot capture (the .NET RATs:
DcRat, VenomRAT, QuasarRAT, PureRAT, which answer only a client that already speaks their
TLS version) it is the discovery certificate plus the port still answering, at a lower
confidence.
`expires_at` is when the indicator lapses unless it is observed again; expired indicators
are removed from every file (MISP keeps them as deleted attributes for 30 days).
IPv6 `ip:port` values are bracketed: `[2001:db8::1]:443`.
An address used by several families is listed once under each of them (one actor often
runs several tools on one host); in `stix/bundle.json` it is one indicator that indicates
each family.
`cluster_id` (`FM-C-0042`; MISP comment `cluster FM-C-0042`, STIX `x_ferromagnet_cluster`)
groups addresses that share operator-chosen infrastructure (a generated certificate, an
SSH host key, names in a certificate) at overlapping times. It is an opaque, stable label
for grouping indicators by operator, not an attribution, and it says nothing about what
was shared. Addresses with no such link have no cluster id.

## MISP

Sync Actions > Feeds > Add feed: provider `FerroMagnet`, source format **MISP Feed**, URL
`https://raw.githubusercontent.com/AliceGrey/ferromagnet-feed/main/misp/`, input source Network, enabled, caching enabled. Each family is one event
(stable uuid) that is updated in place; `first_seen`/`last_seen` are set on every
attribute, confidence is an `estimative-language:likelihood-probability` tag and in the
comment, and indicators that lapse are sent as deleted attributes.

For a plain block list instead: source format **Simple CSV Parsed Feed**, URL
`https://raw.githubusercontent.com/AliceGrey/ferromagnet-feed/main/iocs.csv`, value column `2`; or **Freetext Parsed Feed** on `https://raw.githubusercontent.com/AliceGrey/ferromagnet-feed/main/ip_port.txt`.
Only the values survive those formats (MISP drops per-row metadata).

## OpenCTI

Integrations > CSV feeds > Import (OpenCTI 6.6 or later), choose `opencti/csv-feed.json`,
then check the preview and start the feed. It polls `https://raw.githubusercontent.com/AliceGrey/ferromagnet-feed/main/iocs.csv` hourly and creates,
per row, an Indicator (pattern, confidence and score, valid from/until), the IPv4/IPv6
address, the family as a Malware, and `indicates`/`based-on` relationships, authored by
`FerroMagnet` and marked TLP:CLEAR.

`stix/bundle.json` uses OpenCTI's deterministic ids, so importing it (Data > Import, or
any STIX 2.1 pipeline) updates objects in place instead of duplicating them.
