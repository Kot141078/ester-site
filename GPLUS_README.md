# G+ Deploy Guide

## 1) Helm install
```bash
bash scripts/helm_install.sh
```

## 2) Включить canary или blue-green (Argo Rollouts)
```bash
bash scripts/helm_canary_flip.sh       # canary
bash scripts/helm_bluegreen_switch.sh  # blue-green
```

## 3) Istio (опц.)
Добавь `--set istio.enabled=true --set istio.host=ester.example.com` и убедись, что `VirtualService` создан чартом. Argo Rollouts будет менять веса маршрута `ester-route`.

## 4) SLO и алерты
PrometheusRule создаётся чартом. Alertmanager конфиг — `alertmanager/alertmanager.yml.tmpl` (Telegram, PagerDuty, e-mail).

## 5) Unleash
* Сайдкар по умолчанию в Pod (`.Values.unleash.enabled=true`).
* Standalone-режим: включи `--set unleash.standalone=true` — поднимется отдельный `Deployment` и `Service` на `:4242`.

## 6) OPA Gatekeeper
Установи Gatekeeper (CRD/контроллер), затем применяй манифесты из `k8s/gatekeeper/`.

## 7) LAN распространение
Bare-metal: `docker-compose -f lan/docker-compose.lan.yml up -d` или `systemd-ester-lan-peer.service`.
K8s: для обнаружения используй headless Service + Endpoints или mesh (Istio/Linkerd).

## 8) Безопасность
`NetworkPolicy` ограничивает исходящий трафик (53/443); `seccomp`/`AppArmor`/`drop ALL`; `runAsNonRoot`.

## 9) Самовосстановление
K8s перезапускает pod (liveness/readiness + HPA + PDB). Для bare-metal — `systemd` `Restart=always`.

«Эстер» — не просто чат; это **память + мышление + сеть**.
