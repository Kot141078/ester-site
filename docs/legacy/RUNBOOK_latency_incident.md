# Runbook: Высокая латентность
1) Посмотреть Grafana: p95/p99, ошибочные коды.
2) Снять k6 short‑profile (`make bench` с 60s).
3) Проверить провайдера LLM и очередь ingest.
4) Включить флаг `feature_flags.yaml: kg_repair=true` при деградации KG.
5) При необходимости — scale out (docker-compose.prod.yml репликас нет, но можно поднять второй узел и поставить DNS RR).
