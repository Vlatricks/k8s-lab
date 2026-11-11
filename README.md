# k8s-lab

Практическое изучение Kubernetes: переношу приложение из проекта DevOps (деплой на ВМ через Ansible) в Kubernetes и шаг за шагом довожу до платформы с мониторингом, логами и CI/CD.

## План

- [x] 1. Локальный кластер (minikube), основы `kubectl`
- [x] 2. Деплой приложения: Deployment, Service, пробы, лимиты
- [ ] 3. Namespaces `staging` и `production`, Ingress
- [ ] 4. Различия окружений через Helm или Kustomize
- [ ] 5. Rolling update и откат
- [ ] 6. Мониторинг (kube-prometheus-stack) и логи (Fluent Bit)
- [ ] 7. CI/CD: деплой в кластер из пайплайна, GitOps
- [ ] 8. k3s на ВМ через Ansible

## Быстрый старт

```bash
minikube start --driver=docker --cpus=2 --memory=2200

# собрать образ приложения и загрузить его в кластер
docker build -t skillbox-app:local <путь к папке с Dockerfile>
minikube image load skillbox-app:local

kubectl apply -f manifests/
kubectl get pods -n staging
kubectl port-forward -n staging svc/skillbox-app 8080:80
```

## Структура

- `manifests/`: манифесты Kubernetes
- `notes/`: заметки о том, что сломалось и как починил
