# k8s-lab

Практическое изучение Kubernetes: переношу приложение из проекта DevOps (деплой на ВМ через Ansible) в Kubernetes и шаг за шагом довожу до платформы с мониторингом, логами и CI/CD.

## План

- [x] 1. Локальный кластер (minikube), основы `kubectl`
- [x] 2. Деплой приложения: Deployment, Service, пробы, лимиты
- [x] 3. Namespaces `staging` и `production`, Ingress
- [x] 4. Различия окружений через Kustomize
- [x] 5. Rolling update и откат
- [ ] 6. Мониторинг (kube-prometheus-stack) и логи (Fluent Bit)
- [ ] 7. CI/CD: деплой в кластер из пайплайна, GitOps
- [ ] 8. k3s на ВМ через Ansible

## Быстрый старт

```bash
minikube start --driver=docker --cpus=2 --memory=2200
minikube addons enable ingress

# собрать образ приложения и загрузить его в кластер
docker build -t skillbox-app:local <путь к папке с Dockerfile>
minikube image load skillbox-app:local

kubectl apply -k overlays/staging
kubectl apply -k overlays/production
```

Адреса `staging.skillbox.local` и `production.skillbox.local` нужно прописать в `/etc/hosts` на `minikube ip`.

## Структура

- `base/`: общие манифесты (Deployment, Service, Ingress)
- `overlays/staging`, `overlays/production`: различия окружений (namespace, число реплик, адрес)
- `monitoring/`: настройки Prometheus и Grafana (Helm)
- `logging/`: настройки сбора логов (Fluent Bit)
- `notes/`: заметки о том, что сломалось и как починил
