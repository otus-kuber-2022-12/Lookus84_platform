Лекция №4: Сетевое взаимодействие Pod,взаимодействие Pod,сервисысервисы // ДЗ

    Добавление проверок Pod
Откройте файл с описанием Pod из предыдущего ДЗ ( kubernetes-
intro/web-pod.yml )
Добавьте в описание пода readinessProbe (можно добавлять его сразу
после указания образа контейнера):
...
spec:
containers:
- name: web
image: thatsme/web:1.2
# --- BEGIN ---
readinessProbe: # Добавим проверку готовности
httpGet: # веб-сервера

Запустите наш под командой kubectl apply -f web-pod.yml
Теперь выполните команду kubectl get pod/web и убедитесь, что под
перешел в состояние Running
$ kubectl apply -f web-pod.yml
pod/web created
$ kubectl get pod/web
NAME READY STATUS RESTARTS AGE
web 0/1 Running 0 5m47s

Из листинга выше видно, что проверка готовности контейнера
завершается неудачно. Это неудивительно - веб-сервер в контейнере
слушает порт 8000 (по условиям первого ДЗ).
Пока мы не будем исправлять эту ошибку, а добавим другой вид
проверок: livenessProbe .
Самостоятельно добавьте в манифест проверку состояния веб-
сервера. Например, так:
Запустите Pod с новой конфигурацией

Вопрос для самопроверки:
. Почему следующая конфигурация валидна, но не имеет смысла?
. Бывают ли ситуации, когда она все-таки имеет смысл?



Создание Deployment
Скорее всего, в процессе изменения конфигурации Pod, вы столкнулись
с неудобством обновления конфигурации пода через kubectl (и уже
нашли ключик --force ).
В любом случае, для управления несколькими однотипными подами
такой способ не очень подходит. Создадим Deployment, который упростит
обновление конфигурации пода и управление группами подов.
Для начала, создайте новую папку kubernetes-networks в вашем
репозитории
В этой папке создайте новый файл web-deploy.yaml

Начнем заполнять наш файл-манифест для Deployment:
Теперь в блок template: можно перенести конфигурацию Pod из
web-pod.yaml , убрав строки apiVersion: v1 и kind: Pod .
❗Будьте внимательны с отступами!
apiVersion: apps/v1
kind: Deployment
metadata:
name: web # Название нашего объекта Deployment
spec:
replicas: 1 # Начнем с одного пода
selector: # Укажем, какие поды относятся к нашему Deploy

Для начала удалим старый под из кластера:
kubectl delete pod/web --grace-period=0 --force

И приступим к деплою:
cd kubernetes-networks/
kubectl apply -f web-deploy.yaml

Посмотрим, что получилось:
kubectl describe deployment web

Поскольку мы не исправили ReadinessProbe , то поды, входящие в наш
Deployment, не переходят в состояние Ready из-за неуспешной проверки
На предыдущем слайде видно, что это влияет на состояние всего
Deployment (строчка Available в блоке Conditions)
Теперь самое время исправить ошибку! Поменяйте в файле web-
deploy.yaml следующие параметры:
Увеличьте число реплик до 3 ( replicas: 3 )
Исправьте порт в readinessProbe на порт 8000
Примените изменения командой kubectl apply -f web-deploy.yaml

Теперь проверьте состояние нашего Deployment командой kubectl
describe deploy/web
Убедитесь, что условия (Conditions) Available и Progressing
выполняются (в столбце Status значение true )
Добавьте в манифест ( web-deploy.yaml ) блок strategy (можно
сразу перед шаблоном пода)

Name:                   web
Namespace:              default
CreationTimestamp:      Sun, 22 Jan 2023 11:14:14 +0300
Labels:                 <none>
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=web
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=web
  Init Containers:
   html-gen:
    Image:      busybox:musl
    Port:       <none>
    Host Port:  <none>
    Command:
      sh
      -c
      wget -O- https://bit.ly/otus-k8s-index-gen | sh
    Environment:  <none>
    Mounts:
      /app from app (rw)
  Containers:
   web:
    Image:        lookus84/otustestui:0.2
    Port:         <none>
    Host Port:    <none>
    Liveness:     tcp-socket :8000 delay=0s timeout=1s period=10s #success=1 #failure=3
    Readiness:    http-get http://:8000/index.html delay=0s timeout=1s period=10s #success=1 #failure=3
    Environment:  <none>
    Mounts:
      /app from app (rw)
  Volumes:
   app:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:     
    SizeLimit:  <unset>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   web-6bbbc7d4f5 (3/3 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  17m   deployment-controller  Scaled up replica set web-6bbbc7d4f5 to 3
  
  
Попробуйте разные варианты деплоя с крайними значениями maxSurge
и maxUnavailable (оба 0, оба 100%, 0 и 100%)
За процессом можно понаблюдать с помощью kubectl get events --
watch или установить и использовать его ( kubespy trace
deploy )

---выполнено в рамках предыдущего ДЗ в задании со *

Создание Service

Для того, чтобы наше приложение было доступно внутри кластера (а тем более - снаружи), нам потребуется объект типа Service . Начнем с самого
распространенного типа сервисов - ClusterIP .
ClusterIP выделяет для каждого сервиса IP-адрес из особого
диапазона (этот адрес виртуален и даже не настраивается на сетевых
интерфейсах)
Когда под внутри кластера пытается подключиться к виртуальному IP-
адресу сервиса, то нода, где запущен под меняет адрес получателя в
сетевых пакетах на настоящий адрес пода.
Нигде в сети, за пределами ноды, виртуальный ClusterIP не
встречается.


