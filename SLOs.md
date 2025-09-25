# SLO Ester

## Доступность
- **Availability (HTTP success)**: ≥99.5% в месяц (ошибка = 5xx).
- **Error budget**: 0.5%.

## Латентность
- **p95** < 2s, **p99** < 5s для READ‑ручек (5‑мин окно).

## Бэкапы
- Последний успешный бэкап < 48h.

## Наблюдаемость
- Дашборд: LAT, ERR, RPS, BACKUP_AGE. Алерты: MWMB burn‑rate, p95/p99, backup stale.
