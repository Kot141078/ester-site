# Iteration F — Производительность и отказоустойчивость

## Как запускать
1) Прогреть сервис (и завести JWT, если надо).
2) `make bench` — k6 профили (read/replicate). Итоги: `artifacts/perf/*`.
3) `make recovery` — e2e backup→restore (нужен JWT и доступ к /ops/backup/*).
4) `make replay` — прогон реигры журнала (если включена ручка `/ops/replay_journal`).
5) (опц.) `make chaos` — убьёт процесс на :5000 и проверит автоподъём (systemd/Docker).

## ENV
- `ESTER_BASE_URL` — базовый URL API (default: `http://127.0.0.1:5000`).
- `ESTER_JWT` — JWT для защищённых ручек.
- `K6_RPS` — целевой RPS (default: 10).
- `K6_DURATION` — длина теста (default: `2m`).
- `K6_VUS` — число виртуальных пользователей (default: 20).
- `ESTER_BACKUP_BODY` — тело для restore (опц., JSON).
- `ESTER_JOURNAL_DIR` — локальный журнал событий/очередей (default: `data/journal`).

## Пороговые метрики
- Ошибки: `<1%`.
- Время отклика: p95 `<2s`, p99 `<5s`.

## Примечания
- Тесты `recovery/*` отмечены как опциональные: запускаются при `ESTER_RECOVERY_ENABLE=1`.
- Отсутствие некоторых роутов (например, `/replication/push`) допустимо — 404 учитывается.
