# FerroMagnet feed

Indicators of C2 and offensive-security-tooling infrastructure observed by
[FerroMagnet](https://github.com/AliceGrey/ferromagnet). Updated automatically; each
commit is one refresh in which at least one file changed.

| File | Contents |
|---|---|
| `ip_port.txt` | one `ip:port` per line, grouped by family, with a trailing comment |
| `ip.txt` | one address per line, grouped by family |
| `iocs.csv` | every active indicator with family, kind, confidence and timestamps |
| `iocs.json` | the same, as a document with `generated_at` and `count` |

Confidence is the strength of the match that produced the indicator (0-100).
`last_verified` is set when a live check confirmed the service still matched.
Indicators expire when the service stops being observed and are then removed.
