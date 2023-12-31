# Домашнее задание 8

Monitoring

Созданы deployment и service для nginx, в конфиге которого прописан location /basic_status.

Созданы deployment и service для nginx-exporter.

Скачаны CRD для prometheus-operator, создан ServiceMonitor.


# Домашнее задание 7

CRD

Создано Custom Resource Definition. Пример из методички устарел, версия api теперь v1, в манифест также необходимо добавить раздел schema.

Применены все манифесты, в базу добавлены данные.

kubectl exec -it $MYSQLPOD -- mysql -potuspassword -e "select * from test;" otus-database
mysql: [Warning] Using a password on the command line interface can be insecure.
+----+-------------+
| id | name        |
+----+-------------+
|  1 | some data   |
|  2 | some data-2 |
+----+-------------+

Далее необходимо удалить базу, но команда kubectl delete mysqls.otus.homework mysql-instance запускает джоб бекапа, который зависает. 
Соответственно восстановления тоже не происходит.
Скорее всего причина в устаревшем образе zhenkins/mysql-operator:v0.1



# Домашнее задание 6

Helm

Создан кластер в Yandex cloud. Установлена утилита yc; yc и kubectl настроены на использование созданного кластера.

Установлен Helm. Добавлен репозиторий https://charts.helm.sh/stable, указанный в методичке был недоступен.

Установлен ingress-nginx из https://kubernetes.github.io/ingress-nginx, указанный в методичке был недоступен.

Установлен cert-manager версии 1.13.1 вместо указанной в методичке 0.16.1 с помощью helm, а также crd для него.
Создан issuer.

Установлен chartmuseum из https://chartmuseum.github.io/charts, указанный в методичке был недоступен.

Установлен Harbor из https://github.com/goharbor/harbor-helm.

Создан helm-чарт hipster-shop с помощью приложенного в ДЗ  манифеста all-hipster-shop.yaml. Из чарта развернуто приложение.

Из общего темплейта выделен отдельный темплейт frontend (deployment, service, написанный новый ingress)

В темплейте frontend часть параметров превращена в переменные и вынесена в values.yaml.

Установлен плагин helm-secrets, создан PGP ключ, создан и затем зашифрован манифест secrets.yaml

Установлен kubecfg, сервисы shippingservice и paymentservice вынесены в services.jsonnet


# Домашнее задание 5

Volumes

Установлен minio, созданы statefulset и сервис.

Создан persistent volume типа hostPath размером 1 Gb, claim к нему на 500 mb и под, к которому он маунтится.
Из контейнера на volume создан текстовый файл.
Затем первый под удален и создан второй, с тем же volume. Проверно, что текстовый файл после удаления первого пода сохранился.
 

# Домашнее задание 4

Создание аккаунтов и назначение прав.

1. Созданы аккаунты bob и dave, bob назначен администратором кластера.

2. Создан namespace prometheus, в нем создан аккаунт carol, всем аккаунтам в prometheus назначены права get, list, watch на весь кластер.

3. Создан namespace dev, в нем созданы аккаунты jane и ken, jane назначена администратором dev, аккаунту ken выданы права view в dev.

# Домашнее задание 3

Задание выполняется снова на minukube.

В описание пода web добавлены readinessProbe с неправильным портом и livenessProbe.

Конфигурация с livenessProbe, проверяющей наличие процесса в выводе ps хуже,чем проверка доступности порта, так как несмотря на наличие процесса порт все равно может быть недоступен по разным причинам.  
Такая проверка может иметь смысл для процессов, которые не слушают порты.

Создан Deployment, исправлен порт в readinessProbe, количество реплик установлено в 3.

Создан Service типа ClusterIP - web-svc-cip, проверено как он выглядит в правилах фаерволла.

Для включения ipvs изменена конфигурация minukube без перезагрузки, но с пересозданием правил фаерволла.

Установлен MetalLB, настроен с помощью ConfigMap.

Создан сервис типа LoadBalancer - web-svc-lb, найден его внешний IP, добавлен маршрут к этой подсети через ip minikube, проверена доступность.

Установлен ingress-nginx, создан сервис web-svc, и ингресс-прокси web, привязаны к созданному ранее Deployment.



# Домашнее задание 2

Установлен kind, создан кластер с 3 worker нодами.

Создан ReplicaSet frontend на основе Pod из предыдущей ДЗ. В манифест добавлен раздел Selector.

Проведено скалирование количества подов командой kubectl scale и изменением поля replicas в манифесте.

Образу frontend добавлены теги 0.01 и 0.02
Проведена попытка обновления версии путем изменения тега обраща в манифесте. ReplicaSet не обновляет работающие поды таким образом, необходимо удалять их и запускать заново, 
либо использовать Deployment.

Создан образ контейнера paymentservice для другого сервиса из Hipster Store с тегами 0.01 и 0.02. Создан и запущен для него ReplicaSet по аналогии с frontend (с другими переменными 
окружения)

Из ReplicaSet paymentservice создан Deployment и запущен.

Изменена версия образа в манифесте и проведено обновление работающих подов. Также проведен откат обновления (kubectl rollout undo).

Из ReplicaSet frontend создан Deployment. В него добавлен дополнительный раздел readinessProbe.

Изменен url в описании Probe на некорректный, проведена попытка обновления Deployment - cоздался только один под с ошибкой.




# Домашнее задание 1

Склонирован репозиторий, подготовлен к выполнению домашних заданий, настроена ssh аутентификация на github, создан ключ. 
Установлен minikube.

## Удаление всех подов в kube-system.

Удалены поды:
coredns-5d78c9869d-tcb8h
etcd-minikube                      
kube-apiserver-minikube            
kube-controller-manager-minikube   
kube-proxy-t9c8j
kube-scheduler-minikube            

Все они перезапустились. Механизмы перезапуска:
coredns - создается с помощью RepilicaSet по имени coredns-5d78c9869d, которая следит за количеством реплик пода.
kube-proxy - создается с помощью DaemonSet kube-proxy.
Остальные поды - Static Pods, они управляются и перезапускаются непосредственно демоном kubelet.

## Создание веб-сервера.

Создан Dockerfile для запуска веб-сервера (использован nginx, в контейнер копируется default.conf с указанным корневым каталогом app). Образ контейнера скомпилирован и загружен на Docker Hub.

Написан манифест web-pod.yaml, создающий под web с контейнером web из вышесозданного образа, запущен. 

В манифест добавлено создание второго контейнера типа InitContainer, создающего в каталоге /app файл index.html (указанный в методичке обаз busybox:1.31.0 создать файл не смог, использован busybox:latest)
Также для работы контейнера init в оба контейнера примонтирован volume с типом emptyDir (временный, существующий только во время жизни пода).

Манифест запущен, порт проброшен с помощью kubectl port-forward, работа веб-сервера проверена.

![скриншот](https://github.com/otus-kuber-2023-08/hataldir_platform/blob/kubernetes-intro/kubernetes-intro/kube1.png)

## Cоздание сервиса frontend из приложения Hipster-shop

Склонирован репозиторий Hipster-shop, в каталоге src/frontend создан образ контейнера, залит на Docker Hub.
Запущен под frontend, из него сформирован манифест. Запущенный из этого манифеста под находится в статусе Error со следующими ошибками:

panic: environment variable "PRODUCT_CATALOG_SERVICE_ADDR" not set

goroutine 1 [running]:
main.mustMapEnv(0xc000120840, {0xc5d36a, 0x1c})
        /src/main.go:208 +0xb4
main.main()
        /src/main.go:124 +0x557

## Задание со *

Из реального манифеста Hipster-shop frontend скопированы необходимые переменные окружения, под frontend запущен.
