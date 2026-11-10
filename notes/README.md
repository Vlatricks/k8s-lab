Наблюдения поведения кластера
Удаление пода: kubectl delete pod -n staging <имя пода>, и сразу смотри kubectl get pods -n staging -w. Появится новый.
Изменение числа реплик: kubectl scale deployment skillbox-app -n staging --replicas=4.
Сломанный образ: kubectl set image deployment/skillbox-app -n staging skillbox-app=skillbox-app:nope, посмотри kubectl describe pod (причина ErrImagePull), потом откати: kubectl rollout undo deployment/skillbox-app -n staging.
