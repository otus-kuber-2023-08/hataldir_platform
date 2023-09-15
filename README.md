# hataldir_platform
hataldir Platform repository

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

![скриншот](kube1.png)

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
