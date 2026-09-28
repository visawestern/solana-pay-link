# Solana Pay Link + Verify — "tip-jar в одну ссылку" (hack/ pack, round 79)

Submission package for Colosseum Crypto World's Fair (Sep 14 — Oct 12 2026), Solana track.
Static client-only product. Nothing registered/published in this round.

## Проблема / Решение

Получать оплату в SOL/USDC сложно: адрес + сумма + memo теряются в чатах, плательщик
ошибается, получатель не может доказать "оплатили или нет".

Решение: одна shareable Solana Pay ссылка + live-верификация оплаты через RPC.
Флоу: вводишь recipient + сумму → генерируешь `solana:<addr>?amount=&spl-token=&reference=&label=&message=&memo=`
→ шаришь в X/Discord → плательщик платит из Phantom/Solflare → жмёшь Verify → бейдж
PAID/UNPAID за секунды.

## Фичи (v2, round 76, все F1–F10 закрыты)

Генератор (`paylink_v2/index.html`, 341 строка):
- SOL (native, без spl-token) + USDC (spl-token, mint per network: mainnet `EPjF...`, devnet `4zMM...UX8b8`).
- amount строкой в целые единицы без float (SOL ≤9, USDC ≤6 знаков, `0` разрешён, кап целой части 12 цифр).
- N `reference=` (comma-separated, каждый base58→32B), капы label ≤50, message/memo ≤200,
  варнинг "memo ON-CHAIN PUBLIC", хинт "recipient = wallet, НЕ ATA", QR на чистом canvas + copy-button,
  Check address через `getAccountInfo`.

Verify (`verify_v2/index.html`, 567 строк):
- recipient + reference(s) + expected amount → `getSignaturesForAddress(limit 20, finalized)`
  с `before`-пагинацией до MAX_PAGES=5 (100 сигн.) + "Search deeper (next 20)".
- `getTransaction(jsonParsed, maxSupportedTransactionVersion 0, finalized)` на подпись.
- reference обязан быть 32B non-signer read-only ключом (включая LUT `loadedAddresses`),
  привязанным к transfer-ix проверяемой валюты; USDC net-diff первичен, иначе fallback только
  к своему destination / recipient-owned token account (последний ix); SOL — последний transfer;
  `meta.err != null` пропускается; бейджи CONFIRMED/MISMATCH/PENDING + `[finalized]`.

## Стек

Static HTML+JS, ноль зависимостей, ноль бэкенда, ноль ключей/подписей/кустодии.
Только чтение chain + формирование URL. Отправка — в кошельке пользователя.
RPC: `https://api.mainnet-beta.solana.com` (демо) / `https://api.devnet.solana.com` (тесты),
read-only POST: getBalance, getSignaturesForAddress, getParsedTransaction/getTransaction,
getTokenAccountsByOwner, getAccountInfo. Вывод только через `textContent`.

## Как запустить локально

```bash
python3 -m http.server 8766 --directory paylink_v2 &
curl -o /dev/null -w "%{http_code}\n" http://localhost:8766/index.html  # → 200
python3 -m http.server 8767 --directory verify_v2 &
curl -o /dev/null -w "%{http_code}\n" http://localhost:8767/index.html  # → 200
python3 paylink_verify_test_v2.py  # → passed=30 failed=0
```

## Тесты

`paylink_verify_test_v2.py` (stdlib only, мок-харнесс v2 JS-логики): **passed=30 failed=0**.
Покрывает: URL-генерацию (SOL/USDC/minimal/encoding), валидацию адресов/reference,
amount-границы (F5: sub-lamport/sub-micro reject, zero-transfer ok), match reference
(F2 incl. LUT readonly/writable, signer/string-form), F1 чужой-destination reject,
F3 err-skip, F4 пагинация (страница 2), F6 multi-ref match-any, F8 devnet-mint,
F7 капы + F9 last-wins. Прогон этого раунда — см. лог чекеров ниже.

## Честные лимиты

- **devnet e2e pending — faucet dry.** Полный цикл "airdrop → 0.05 SOL + USDC-dev с memo →
  Verify зелёный" НЕ пройден: devnet-воды нет в этом раунде, `solana airdrop` не выполнялся.
  Верификация — только мок-харнесс 30/30 + read-only RPC-чекеры.
- **mainnet не тестирован.** Отправок с mainnet-SOL не было, mainnet-платежей не проверяли;
  mainnet-чтение — только `check_solana.py` (баланс/подписи/USDC тестового адреса `GBVu...`).
- RPC-флейки возможны (429 на public mainnet-beta) — митигация: переключатель devnet,
  кэш, повтор; Actions/Blinks `actions.json` — только дока, отдельного хостинга нет;
  программы (Anchor/native) в v1 нет осознанно (rent/deposit 0, аудит 0).
- Pre-existing code: `demo/`, `check_solana.py`, `solana_wallet_gen.py` — будет disclosed
  в сабмите (требование Colosseum).

## Балансы и статус (этот раунд, оба чекера)

- `check_solana.py`: `SOL:0.0,TXS:0,TOKENS:0,EARNED:False` → NOT_YET
- `check_balances.py`: `BTC:0,EVM:0,EARNED:False` → NOT_YET
- Регистрации/сабмиты/посты/деплои: ноль.
