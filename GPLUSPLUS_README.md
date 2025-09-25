# G++ Guide

## Auto-rollback через Argo Rollouts
1. Включите в values: `rollouts.analysis.enabled=true`.
2. Для canary/blue-green чарт подключает `AnalysisTemplate` и использует его в стратегии:
   - Canary: блок `analysis` внутри `strategy.canary`.
   - Blue-Green: `prePromotionAnalysis` перед промоушеном.

## Adaptive SLO
Если `rollouts.analysis.slo.adaptive.enabled=true`, используется эвристический baseline p99 за последние `baselineRange`
(см. комментарий в шаблоне). При сомнениях отключайте адаптив.

## Multi-cluster DR
- `scripts/mc_sync.sh` — синхронный деплой в `primary` и `secondary`.
- `scripts/failover_dns_weighted.sh` — переключение веса DNS (external-dns аннотации под своего провайдера).

## ChatOps
- `scripts/chatops_rollouts.sh` — обёртка над `kubectl argo rollouts`.
- `scripts/autorollback_watch.sh` — внешний вотчер на метрики для аварийного abort.
