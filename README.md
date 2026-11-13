# k8s-lab

![CI](https://github.com/Vlatricks/k8s-lab/actions/workflows/ci.yml/badge.svg)

Практическое изучение Kubernetes: переношу приложение из проекта DevOps (деплой на ВМ через Ansible) в Kubernetes и шаг за шагом довожу до платформы с мониторингом, логами и CI/CD.

## План

- [x] 1. Локальный кластер (minikube), основы `kubectl`
- [x] 2. Деплой приложения: Deployment, Service, пробы, лимиты
- [x] 3. Namespaces `staging` и `production`, Ingress
- [x] 4. Различия окружений через Kustomize
- [x] 5. Rolling update и откат
- [x] 6. Мониторинг (Prometheus) и логи (Fluent Bit, вывод в stdout)
- [x] 7. CI: проверка манифестов и smoke-test в kind (GitHub Actions)
- [ ] 8. GitOps (Argo CD), по желанию
- [ ] 9. k3s на ВМ через Ansible

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
- `.github/workflows/`: CI (проверка манифестов и smoke-test в kind)
- `notes/`: заметки о том, что сломалось и как починил
