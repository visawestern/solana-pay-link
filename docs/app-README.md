# hack/app — static pack (round 198)

Local static pages, zero backend/keys/custody. Nothing published.

## Pages

- `index.html` — landing, links to all subpages (relative `./pay.html ./verify.html ./monitor.html ./bolt11.html ./index.html`)
- `pay.html` — Solana Pay URL generator (copy of `paylink_v2/index.html`)
- `verify.html` — on-chain payment check (copy of `verify_v2/index.html`)
- `monitor.html` — live BTC/EVM balance demo (copy of `demo/index.html`)
- `bolt11.html` — local Lightning BOLT11 invoice decoder (copy of `bolt11/index.html`, round 198; own bech32, no libs/CDN; signature NOT cryptographically verified — presence only; payee only from `n`-field)

Run: `python3 -m http.server 8766 --directory hack/app` → curl each page → expect 200.

## Log round 198 (2026-09-27)

- `bolt11/index.html` → `hack/app/bolt11.html` copied; nav added (`Home/Pay/Verify/Monitor/BOLT11`); landing `hack/app/index.html` links to `bolt11.html` (nav + Pages list + hint).
- Pages 200: `index.html=200, pay.html=200, verify.html=200, monitor.html=200, bolt11.html=200` (via `python3 -m http.server 8766 --directory hack/app`, curl).
- Links: index → `./pay.html ./verify.html ./monitor.html ./bolt11.html ./index.html` ok; `bolt11.html` nav → `./index.html ./pay.html ./verify.html ./monitor.html ./bolt11.html` ok.
- `check_balances.py`: `BTC:0,EVM:0,EARNED:False` → NOT_YET
- `check_solana.py`: `SOL:0.0,TXS:0,TOKENS:0,EARNED:False` → NOT_YET
- Registrations/submits/posts/deploys: zero.
