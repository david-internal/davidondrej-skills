---
name: check-domain-availability
description: Check whether domains are available/unregistered from the terminal, free, with no API key, signup, or credit card. Uses RDAP over curl, with per-TLD endpoints and a whois caveat. Use when a user asks whether a domain is available, wants to check domains for names, find open domains, or during brand/product naming.
---

# Check Domain Availability

Goal: tell which domains are free, fast and free, no account needed.

## Method

Use RDAP over `curl` (free, keyless). Per domain:
- HTTP `404` = AVAILABLE
- HTTP `200` = taken
- anything else = unknown (retry / rate-limited)

RDAP is more reliable than the `whois` binary and is safer for loops.

## RDAP endpoints by TLD

| TLD | Endpoint (append `<name>.<tld>`) |
|-----|-----|
| .com / .net | `https://rdap.verisign.com/com/v1/domain/` |
| .ai / .io | `https://rdap.identitydigital.services/rdap/domain/` |
| .dev / .app | `https://pubapi.registry.google/rdap/domain/` |
| .co | no RDAP - socket whois to `whois.registry.co` |
| other gTLD | look up base in IANA bootstrap (below) |

Find any gTLD's RDAP base:
```bash
curl -s https://data.iana.org/rdap/dns.json | python3 -c "import sys,json;d=json.load(sys.stdin);print(next((u for s in d['services'] if 'TLD' in s[0] for u in s[1] if u.startswith('https')),'NONE'))"
```

## Batch script (com / ai / io / dev / app / co)

```bash
set +e +o pipefail
names=(foo bar baz)
rstat(){ c=$(curl -s -o /dev/null -w "%{http_code}" --max-time 12 "$1"); [ "$c" = 404 ] && echo FREE || { [ "$c" = 200 ] && echo taken || echo "?$c"; }; }
cofree(){ python3 - "$1" <<'PY'
import socket,sys
n=sys.argv[1]
try:
    s=socket.create_connection(("whois.registry.co",43),timeout=8); s.settimeout(8)
    s.sendall((n+".co\r\n").encode()); d=b""
    while True:
        try: c=s.recv(4096)
        except Exception: break
        if not c: break
        d+=c
    s.close(); t=d.decode(errors="ignore").lower()
except Exception: t=""
free=any(k in t for k in ["not found","does not exist","no match","no data found"])
taken=any(k in t for k in ["domain name:","registry expiry","registrar:","creation date"])
print("FREE" if free else ("taken" if taken else "?"))
PY
}
printf "%-12s %-5s %-5s %-5s %-5s %-5s %-5s\n" NAME .com .ai .io .dev .app .co
for n in "${names[@]}"; do
  printf "%-12s %-5s %-5s %-5s %-5s %-5s %-5s\n" "$n" \
    "$(rstat "https://rdap.verisign.com/com/v1/domain/$n.com")" \
    "$(rstat "https://rdap.identitydigital.services/rdap/domain/$n.ai")" \
    "$(rstat "https://rdap.identitydigital.services/rdap/domain/$n.io")" \
    "$(rstat "https://pubapi.registry.google/rdap/domain/$n.dev")" \
    "$(rstat "https://pubapi.registry.google/rdap/domain/$n.app")" \
    "$(cofree "$n")"
  sleep 0.4
done
```

## Gotchas

- If your shell uses `errexit` and `pipefail`, start with `set +e +o pipefail` so checks do not abort early.
- Do NOT use the `whois` binary in loops: it can throttle or fail after multiple queries, producing misleading results.
- `.co` and many ccTLDs have no public RDAP. Use socket whois to `whois.registry.co` in the script; avoid the `whois` binary for loops.
- `.ai`/`.io` RDAP may reject some non-browser or no-User-Agent clients. Use `curl` as in the script.
- `/dev/tcp` may be unavailable in some shells, and `nc` to port 43 may be blocked. Prefer RDAP where available.
- If `.dev` returns `?429`, slow down and recheck.
