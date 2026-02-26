# Iteration H — CRDT-память и Merkle-синхронизация

## Миссия
Гарантировать целостность и консистентность общей БЗ между узлами без конфликтов и потери данных. Вставлено drop-in: CRDT LWW-Set, Merkle-дерево для сверки, CAS (content-addressable), безопасный P2P-протокол, автосинк и бэкап.

---

## Definition of Done (DoD) — статус

1. **CRDT слой** — LWW-Element-Set:
   - Операции `add/remove`, метки `Dot(peer, ts)`.
   - Merge идемпотентен, коммутативен, ассоциативен.
   - Тесты: `tests/crdt/test_lww_set.py` — зелёные.

2. **Merkle сверка**:
   - Обмен корнем/уровнями, `state(level, offset, limit)`.
   - Ветвевой срез: `/p2p/state_branch?start&end`.
   - Клиенты/диагностика: `tools/p2p_verify_merkle.py`, `tools/p2p_diff_bisect.py`.
   - Бенчмарк: `tools/bench_merkle.py` (локально цель `< 200ms` на 10k ключей, зависит от CPU).

3. **CAS**:
   - `merkle/cas.py` — `blake3` → fallback на `blake2b`.
   - Дедупликация: `tests/merkle/test_cas_dedup.py`.

4. **P2P протокол**:
   - Ручки: `/p2p/state`, `/p2p/state_branch`, `/p2p/pull`, `/p2p/push`, `/p2p/pull_by_ids`, `/p2p/snapshot/(export|import)`.
   - HMAC-подпись `X-HMAC-Signature`.
   - RBAC роль `replicator` при `ESTER_RBAC_STRICT=1`.

5. **Автосинхронизация**:
   - Планировщик: `scheduler/sync_job.py` (`sync_once()` и loop).
   - systemd: `systemd/ester-p2p-sync.(service|timer)`.
   - UI/OPS: `/ops/p2p`, `/ops/p2p/diff`.

6. **Резерв**:
   - Снапшот/импорт: `/p2p/snapshot/export|import`, `tools/p2p_snapshot.py`, выборочный `tools/p2p_snapshot_select.py`.
   - systemd: `systemd/ester-crdt-snapshot.(service|timer)`.

---

## Запуск

```bash
export ESTER_PEER_ID=peer-$(hostname)
export ESTER_P2P_HMAC="shared_secret"
export ESTER_P2P_STATIC="http://10.0.0.11:5000,http://10.0.0.12:5000"
export ESTER_RBAC_STRICT=1
export JWT_SECRET="change_me"

python app.py  # либо gunicorn 'app:create_app()'
