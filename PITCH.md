# PITCH — Solana Pay Link + Verify (200 слов)

Getting paid in crypto is still broken. A creator posts a wallet address in chat, a
freelancer pastes "send 0.05 SOL pls", the memo gets lost, the amount is wrong, and nobody
can prove payment happened. Wallets solved sending — nobody solved requesting.

Solana Pay Link + Verify is a tip-jar in one link. Paste a recipient, type an amount in SOL
or USDC, add label, message, memo — get a shareable Solana Pay URL with QR and copy button.
Share it anywhere: X, Discord, stream overlay. The payer opens it in Phantom or Solflare and
pays. The recipient hits Verify and gets a live on-chain verdict in seconds: PAID or UNPAID,
with amount and reference proof from RPC.

No backend, no keys, no custody — one static page that only builds URLs and reads the chain.
Every link is distribution: each share spreads the product. Creators, streamers, freelancers,
hackathon teams collecting tips all need this weekly.

We ship narrow but working: generator plus live Verify with finalized commitment, pagination,
and strict reference binding. Next is metrics per link and an optional escrow program once
traction proves it. Request payments like you share links — that is the pitch.

(200 words)

## Ответы на критерии судей (venture-style)

- **Insight.** Боль не "нет кошелька", а "нет способа запросить точную оплату и доказать её".
  Адрес+сумма+memo теряются в чатах; инсайт — получателю нужен request-инструмент, а не ещё
  один send-инструмент. Валидация: флоу "ссылка → оплата → Verify зелёный за 3 сек" демоабилен
  за 2 мин (Д8–Д9 план mvp70).
- **Product + Execution.** v2 закрывает все F1–F10 (pv_review): строгий reference-binding
  (32B non-signer read-only + transfer-ix, LUT), finalized + err-skip, пагинация 5×20,
  строковые amounts без float, multi-ref, капы, mint per network, last-wins. Доказательство
  execution: мок-харнесс 30/30 + static без бэкенда (деплой 0, аудит 0). Стоп-условие честное:
  если Verify не зелёный на devnet — ужаться до Monitor+Generator (mvp70 Д6→Д11).
- **Market + GTM.** TAM: все, кто принимает SOL/USDC (криейторы, стримеры, фрилансеры,
  OSS-донаты, хакатон-команды). Distribution из коробки: каждая ссылка — шаринг в X/Discord,
  Blinks-совместимый JSON. Demand: еженедельная боль получения оплаты. Traction-план: метрики
  Verify/неделя, число сгенерированных ссылок. Monetization позже (аналитика ссылок / escrow v2);
  сейчас — захват distribution. Запасной вариант тем же кодом: Public Goods ($5k), цель —
  Solana-трек $10k.
- **Viability / Fit (дополнительно).** Соло за ~11 дней (Sep 27 — Oct 7, буфер до Oct 12):
  нулевая стоимость (devnet $0, mainnet не нужен, программы нет). Founder-fit: переиспользован
  проверенный `demo/`-паттерн + RPC-методы из `check_solana.py`. Риски названы: R1 429-RPC,
  R2 faucet-лимиты (нужно всего 0.5–2 SOL devnet), R3 scope-creep (freeze без программы),
  R4 venture-фильтр (GTM + шарибельность + public repo + disclose).
