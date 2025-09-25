# Observability & Release One-Shot (Итерация G)

## 1) Локальная наблюдаемость

```bash
docker compose -f docker-compose.observability.yml up -d
# или
make observability-up
```

Проверки:
- Prometheus http://localhost:9090 → Targets: `ester` **UP**
- Grafana http://localhost:3000 (admin/admin) → добавлен datasource Prometheus (provisioned)
- `/metrics` отдаёт 200: `curl -s -o /dev/null -w "%{http_code}\n" http://localhost:5000/metrics`

## 2) Helm-чарт (ServiceMonitor/Rules/VirtualService)

```bash
helm upgrade --install ester ./charts/ester -n ester --create-namespace   -f values.observability.yaml
# или
make helm-apply
```

Ожидаемые объекты:
```
kubectl get servicemonitor -n ester
kubectl get prometheusrule -n ester
kubectl get virtualservice -n ester
```

## 3) Тесты perf/smoke

```bash
pytest -m perf -k metrics -q
pytest -m smoke -q
# или
make test-perf
make test-smoke
```

## 4) Релиз предпросмотра

```bash
bash scripts/release_preview.sh
# или
make release-preview
```

Артефакты:
- `CHANGELOG.md`
- `sbom-v0.1-preview.spdx.json` (если установлен syft)
- `ester-0.1-preview.tar.gz`

Выгрузка:
- через `storage.uploader` (если модуль доступен);
- либо WebDAV (переменные `WEBDAV_URL`, `WEBDAV_USER`, `WEBDAV_PASSWORD`);
- либо S3 (CLI `aws`, переменная `S3_BUCKET`, опционально `S3_PREFIX`).

## Примечания

- Значения порогов SLO настраиваются в `values.observability.yaml`.
- Для Istio укажите свой `istio.host` и, при необходимости, `istio.gateway`.
- В кластере должны быть CRD `ServiceMonitor`/`PrometheusRule` (Prometheus Operator).
