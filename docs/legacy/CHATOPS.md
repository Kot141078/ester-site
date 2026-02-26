# ChatOps

## Команды (через bastion/runner)
- `/rollout status` → `chatops_rollouts.sh status`
- `/rollout abort` → `chatops_rollouts.sh abort`
- `/rollout promote` → `chatops_rollouts.sh promote`
- `/dr failover primary 100 secondary 0` → `failover_dns_weighted.sh`

Интеграция с Telegram: webhook → GitHub Action/Runner → запуск скрипта с проверкой подписи и маппингом команд.
