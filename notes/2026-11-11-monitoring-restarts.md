# kube-prometheus-stack не поместился в ВМ на 4 ГБ

**Симптом:** после `helm install` поды мониторинга (Grafana, operator, Prometheus, kube-state-metrics) постоянно перезапускались, часть оставалась `0/1` или `2/3 Ready`.

**Как искал причину:**
- `kubectl describe pod ... | grep -A8 "Last State"`: не `OOMKilled`, а `Completed` с кодом 0 и `Error` с кодом 1.
- `kubectl get events -n monitoring`: везде `Liveness probe failed` и `Readiness probe failed` с таймаутами.
- `free -m`: всего 3915 МБ, доступно 900 МБ, в swap уже больше 1 ГБ.
- `kubectl top nodes` отвечал `ServiceUnavailable`: metrics-server тоже не успевал подниматься.

**Причина:** нехватка памяти. ВМ уходила в swap, процессы отвечали медленнее таймаута проб, Kubernetes их перезапускал, а перезапуски добавляли нагрузку.

**Решение:** памяти хоста (7 ГБ) не хватает, чтобы поднять ВМ выше 4 ГБ, поэтому полный kube-prometheus-stack заменил облегчённой связкой: чарт `prometheus` (без Alertmanager, pushgateway и node-exporter, хранение 6 часов, без постоянного диска) и отдельный чарт `grafana` с лимитами.

**Вывод:** у готовых стеков есть минимальные требования к ресурсам. Перед установкой смотреть `free -m` и ставить только нужные компоненты.
