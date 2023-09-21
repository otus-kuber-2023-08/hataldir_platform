# hataldir_platform
hataldir Platform repository

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
