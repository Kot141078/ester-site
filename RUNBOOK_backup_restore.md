# Runbook: Backup/Restore
1) Проверить `/ready` (ok=true) и `/ops/backup/verify` (200).
2) Если нужно восстановление: `POST /ops/backup/restore { id: <last-good> }`.
3) Проверить `/providers/status` и быстрый RAG‑запрос.
4) Зафиксировать tick в журнале событий.
