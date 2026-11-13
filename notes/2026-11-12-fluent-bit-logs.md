# Сбор логов подов через Fluent Bit (DaemonSet)

**Что сделал:** поставил Fluent Bit чартом `fluent/fluent-bit` в namespace `logging`. Он читает логи контейнеров из `staging` и `production` (`/var/log/containers/*_staging_*.log` и `*_production_*.log`) и пишет в stdout в формате JSON.

**Как проверил:**
- `curl` к `production.skillbox.local`, затем `kubectl logs -n logging daemonset/fluent-bit`.
- Тестовый под `logtest`, который печатает `hello-N`.

**Что увидел:** каждая строка лога приложения (`GET / ... curl/7.81.0`) пришла с блоком `kubernetes`: `namespace_name`, `pod_name`, `container_name`, `labels`, `container_image`. Из обычного файла на диске получилась структурированная запись, по которой можно фильтровать по окружению и поду.

**Наблюдение:** в строках от `logtest` вместо `hello-1` пришло `hello-`: `$i` подставилась локальной оболочкой ещё до запуска (команда была в двойных кавычках). В одинарных кавычках цикл раскрывается уже внутри контейнера.

**Ограничения:** вывод идёт в stdout. EFK на отдельной ВМ не подключал: при 7 ГБ памяти на компьютере ВМ с Elasticsearch и ВМ с minikube вместе не запустить. Чтобы отправлять логи в Elasticsearch, достаточно заменить блок `[OUTPUT]` в `logging/fluent-bit-values.yaml` на `Name es` с адресом сервера.

**Вывод:** сначала разобрать механику (DaemonSet, метаданные Kubernetes), потом менять хранилище.
