# G+++ Guide

## Что включили
- **Golden paths**: `/ops/probe/golden` → web-provider в Argo Rollouts Analysis.
- **Recording Rules**: дешёвые метрики p95/p99 и error-rate для быстрых запросов аналайзера.
- **Auto-Failover**: Alertmanager → DR Webhook → внешние аннотации для external-dns (вес secondary=100).
- **Mirror-traffic**: Istio VirtualService `mirrorPercentage` → тёплый прогрев канарейки.

## Как включить
1. Обнови приложение: зарегистрируй blueprint `bp_probe` в `app.py`:
   ```python
   from routes.probe_routes import bp_probe
   app.register_blueprint(bp_probe)
   ```
2. В Helm values включи:
   ```yaml
   probes:
     golden: { enabled: true }
   recordingRules:
     enabled: true
   ```
3. Если используешь Istio mirror:
   ```yaml
   istio:
     enabled: true
     host: ester.local
     mirror: { enabled: true, percent: 5 }
   ```
4. DR Webhook:
   ```yaml
   failover:
     webhook:
       enabled: true
       token: "<SECRET>"
       targetKind: "Service"        # или Ingress
       targetName: "ester"
       targetNamespace: "ester"
       setIdentifier: "ester-secondary"
       weight: "100"
   ```
   В Alertmanager receiver — webhook c заголовком `X-Auth-Token: <SECRET>`.

## Smoke / Проверки
- `GET /ops/probe/golden` → `{value: 1}` при зелёном состоянии.
- Argo: `kubectl argo rollouts get rollout ester -n ester` — в `analysis.templates` есть `*-golden` (добавлен к SLO).
- DR: 
  ```bash
  curl -H 'X-Auth-Token: <SECRET>' \
       -H 'Content-Type: application/json' \
       --data @scripts/alert_test_payload.json \
       http://<dr-webhook-svc>:8080/alert
  ```
  Должен пропатчиться `Service/Ingress` аннотациями external-dns (вес/идентификатор).

## Примечания по совместимости
- Порт берём из `.Values.port`, сервис — из `.Values.service.port`.
- Канареечный сервис `{{ fullname }}-canary` уже создаётся чартом и используется в Rollout/VirtualService.

_Эстер остаётся «память + мышление + сеть». Это — бронепластина для прод-контура._
