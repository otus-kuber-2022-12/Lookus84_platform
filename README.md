Лекция №4: Сетевое взаимодействие Pod,взаимодействие Pod,сервисысервисы // ДЗ

    Добавление проверок Pod
Откройте файл с описанием Pod из предыдущего ДЗ ( kubernetes- intro/web-pod.yml )
Добавьте в описание пода readinessProbe (можно добавлять его сразу после указания образа контейнера):
...
spec:
containers:
- name: web
image: thatsme/web:1.2
# --- BEGIN ---
readinessProbe: # Добавим проверку готовности
httpGet: # веб-сервера

Запустите наш под командой kubectl apply -f web-pod.yml
Теперь выполните команду kubectl get pod/web и убедитесь, что под перешел в состояние Running
$ kubectl apply -f web-pod.yml
pod/web created
$ kubectl get pod/web
NAME READY STATUS RESTARTS AGE
web 0/1 Running 0 5m47s

Из листинга выше видно, что проверка готовности контейнера
завершается неудачно. Это неудивительно - веб-сервер в контейнере слушает порт 8000 (по условиям первого ДЗ).
Пока мы не будем исправлять эту ошибку, а добавим другой вид проверок: livenessProbe .
Самостоятельно добавьте в манифест проверку состояния веб-сервера. Например, так:
Запустите Pod с новой конфигурацией

Вопрос для самопроверки:
. Почему следующая конфигурация валидна, но не имеет смысла?
. Бывают ли ситуации, когда она все-таки имеет смысл?



Создание Deployment
Скорее всего, в процессе изменения конфигурации Pod, вы столкнулись с неудобством обновления конфигурации пода через kubectl (и уже нашли ключик --force ).
В любом случае, для управления несколькими однотипными подами такой способ не очень подходит. Создадим Deployment, который упростит обновление конфигурации пода и управление группами подов.
Для начала, создайте новую папку kubernetes-networks в вашем репозитории
В этой папке создайте новый файл web-deploy.yaml

Начнем заполнять наш файл-манифест для Deployment:
Теперь в блок template: можно перенести конфигурацию Pod из web-pod.yaml , убрав строки apiVersion: v1 и kind: Pod .
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

Поскольку мы не исправили ReadinessProbe , то поды, входящие в наш Deployment, не переходят в состояние Ready из-за неуспешной проверки
На предыдущем слайде видно, что это влияет на состояние всего Deployment (строчка Available в блоке Conditions)
Теперь самое время исправить ошибку! Поменяйте в файле web-deploy.yaml следующие параметры:
Увеличьте число реплик до 3 ( replicas: 3 )
Исправьте порт в readinessProbe на порт 8000
Примените изменения командой kubectl apply -f web-deploy.yaml

Теперь проверьте состояние нашего Deployment командой kubectl describe deploy/web
Убедитесь, что условия (Conditions) Available и Progressing выполняются (в столбце Status значение true ) 
Добавьте в манифест ( web-deploy.yaml ) блок strategy (можно сразу перед шаблоном пода)

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
  
  
Попробуйте разные варианты деплоя с крайними значениями maxSurge и maxUnavailable (оба 0, оба 100%, 0 и 100%)
За процессом можно понаблюдать с помощью kubectl get events --watch или установить и использовать его ( kubespy trace deploy )

---выполнено в рамках предыдущего ДЗ в задании со *

Создание Service

Для того, чтобы наше приложение было доступно внутри кластера (а тем более - снаружи), нам потребуется объект типа Service . Начнем с самого распространенного типа сервисов - ClusterIP .
ClusterIP выделяет для каждого сервиса IP-адрес из особого диапазона (этот адрес виртуален и даже не настраивается на сетевых интерфейсах)
Когда под внутри кластера пытается подключиться к виртуальному IP- адресу сервиса, то нода, где запущен под меняет адрес получателя в сетевых пакетах на настоящий адрес пода.
Нигде в сети, за пределами ноды, виртуальный ClusterIP не встречается.

ClusterIP удобны в тех случаях, когда:
Нам не надо подключаться к конкретному поду сервиса
Нас устраивается случайное расределение подключений между подами
Нам нужна стабильная точка подключения к сервису, независимая от подов, нод и DNS-имен
Например:
Подключения клиентов к кластеру БД (multi-read) или хранилищу
Простейшая (не совсем, use IPVS, Luke) балансировка нагрузки внутри кластера

    
    Итак, создадим манифест для нашего сервиса в папке kubernetes-networks . Файл web-svc-cip.yaml 
    
    kubectl apply -f web-svc-cip.yaml 
service/web-svc-cip created

    Проверим результат (отметьте назначенный CLUSTER-IP):
    
    kubectl get services
    NAME          TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
kubernetes    ClusterIP   10.96.0.1       <none>        443/TCP   12d
web-svc-cip   ClusterIP   10.99.234.248   <none>        80/TCP    83s
    
    
    Подключимся к ВМ Minikube (команда minikube ssh и затем sudo -i ): 
    minikube ssh
docker@minikube:~$ 

    Сделайте curl http://<CLUSTER-IP>/index.html - работает!
    oot@minikube:~# curl http://10.99.234.248/index.html
<html>
<head/>
<body>
<!-- IMAGE BEGINS HERE -->
<font size="-3">
<pre><font color=white>0....


Сделайте ping <CLUSTER-IP> - пинга нет
root@minikube:~# ping 10.99.234.248
PPING 10.99.234.248 (10.99.234.248) 56(84) bytes of data.
^C
--- 10.99.234.248 ping statistics ---
22 packets transmitted, 0 received, 100% packet loss, time 21500ms

Сделайте arp -an , ip addr show - нигде нет ClusterIP
root@minikube:~# ip addr show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:a0:ab:88:5f brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
4: veth921da0e@if3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default 
    link/ether 86:bf:7a:78:cd:50 brd ff:ff:ff:ff:ff:ff link-netnsid 1
6: veth919e5fc@if5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default 
    link/ether a6:3c:56:16:07:6d brd ff:ff:ff:ff:ff:ff link-netnsid 2
8: vethefff5d7@if7: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default 
    link/ether ea:ec:32:c8:04:c2 brd ff:ff:ff:ff:ff:ff link-netnsid 3
10: veth9d9c03b@if9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default 
    link/ether 0e:08:dc:3d:8d:31 brd ff:ff:ff:ff:ff:ff link-netnsid 4
12: eth0@if13: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:c0:a8:31:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.168.49.2/24 brd 192.168.49.255 scope global eth0
       valid_lft forever preferred_lft forever
13: veth65118eb@if11: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default 
    link/ether 5e:de:cf:44:3c:f2 brd ff:ff:ff:ff:ff:ff link-netnsid 5



Сделайте iptables --list -nv -t nat - вот где наш кластерный IP!

root@minikube:~# iptables --list -nv -t nat

Chain KUBE-SERVICES (2 references)
 pkts bytes target     prot opt in     out     source               destination         
    0     0 KUBE-SVC-JD5MR3NA4I4DYORP  tcp  --  *      *       0.0.0.0/0            10.96.0.10           /* kube-system/kube-dns:metrics cluster IP */ tcp dpt:9153
    0     0 KUBE-SVC-NPX46M4PTMTKRN6Y  tcp  --  *      *       0.0.0.0/0            10.96.0.1            /* default/kubernetes:https cluster IP */ tcp dpt:443
    1    60 KUBE-SVC-6CZTMAROCN3AQODZ  tcp  --  *      *       0.0.0.0/0            10.99.234.248        /* default/web-svc-cip cluster IP */ tcp dpt:80
    0     0 KUBE-SVC-TCOU7JCQXEZGVUNU  udp  --  *      *       0.0.0.0/0            10.96.0.10           /* kube-system/kube-dns:dns cluster IP */ udp dpt:53
    0     0 KUBE-SVC-ERIFXISQEP7F7OF4  tcp  --  *      *       0.0.0.0/0            10.96.0.10           /* kube-system/kube-dns:dns-tcp cluster IP */ tcp dpt:53
  887 53220 KUBE-NODEPORTS  all  --  *      *       0.0.0.0/0            0.0.0.0/0            /* kubernetes service nodeports; NOTE: this must be the last rule in this chain */ ADDRTYPE match dst-type LOCAL



Chain KUBE-SVC-6CZTMAROCN3AQODZ (1 references)
 pkts bytes target     prot opt in     out     source               destination         
    1    60 KUBE-MARK-MASQ  tcp  --  *      *      !10.244.0.0/16        10.99.234.248        /* default/web-svc-cip cluster IP */ tcp dpt:80
    0     0 KUBE-SEP-XP4XVLODH7BOHN5Q  all  --  *      *       0.0.0.0/0            0.0.0.0/0            /* default/web-svc-cip -> 172.17.0.2:8000 */ statistic mode random probability 0.25000000000
    0     0 KUBE-SEP-Z6QHC4C2JAQDF7MX  all  --  *      *       0.0.0.0/0            0.0.0.0/0            /* default/web-svc-cip -> 172.17.0.4:8000 */ statistic mode random probability 0.33333333349
    0     0 KUBE-SEP-C5Q7WHV7ALQOOLAZ  all  --  *      *       0.0.0.0/0            0.0.0.0/0            /* default/web-svc-cip -> 172.17.0.5:8000 */ statistic mode random probability 0.50000000000
    1    60 KUBE-SEP-SLOPQOZW34M3DWKM  all  --  *      *       0.0.0.0/0            0.0.0.0/0            /* default/web-svc-cip -> 172.17.0.6:8000 */

Нужное правило находится в цепочке KUBE-SERVICES

Затем мы переходим в цепочку KUBE-SVC-..... - здесь находятся правила "балансировки" между цепочками KUBE-SEP-.....
SVC - очевидно Service

В цепочках KUBE-SEP-..... находятся конкретные правила
перенаправления трафика (через DNAT)
SEP - Service Endpoint

hain KUBE-SEP-Z6QHC4C2JAQDF7MX (1 references)
 pkts bytes target     prot opt in     out     source               destination         
    0     0 KUBE-MARK-MASQ  all  --  *      *       172.17.0.4           0.0.0.0/0            /* default/web-svc-cip */
    0     0 DNAT       tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            /* default/web-svc-cip */ tcp to:172.17.0.4:8000
...........................

Подробное описание можно почитать https://msazure.club/kubernetes-services-and-iptables/ или перейти на IPVS, там чуть понятнее ))

-------------------------------------------
Итак, с версии 1.0.0 Minikube поддерживает работу kube-proxy в режиме IPVS. Попробуем включить его "наживую".

Включим IPVS для kube-proxy , исправив ConfigMap (конфигурация Pod, хранящаяся в кластере)
Выполните команду kubectl --namespace kube-system edit configmap/kube-proxy

Теперь найдите в файле конфигурации kube-proxy строку mipvs -> mode: ""
---------------------
ipvs:
      excludeCIDRs: null
      minSyncPeriod: 0s
      scheduler: ""
      strictARP: false
      syncPeriod: 0s
      tcpFinTimeout: 0s
      tcpTimeout: 0s
      udpTimeout: 0s
    kind: KubeProxyConfiguration
    metricsBindAddress: 0.0.0.0:10249
    mode: ""
----------------------------

      strictARP: true
    kind: KubeProxyConfiguration
    metricsBindAddress: 0.0.0.0:10249
    mode: "ipvs"
    
    Теперь удалим Pod с kube-proxy , чтобы применить новую конфигурацию (он входит в DaemonSet и будет запущен автоматически) kubectl --namespace kube-system delete pod --selector='k8s-app=kube-proxy'
    
    (kubernetes-networks)kubectl --namespace kube-system delete pod --selector='k8s-app=kube-proxy'
pod "kube-proxy-cb4rt" deleted


Описание работы и настройки IPVS https://github.com/kubernetes/kubernetes/blob/master/pkg/proxy/ipvs/README.md . Причины включения strictARP описаны тут 
    https://github.com/metallb/metallb/issues/153
    
    После успешного рестарта kube-proxy выполним команду minikube ssh и проверим, что получилось
    
    Выполним команду iptables --list -nv -t nat в ВМ Minikube 
Что-то поменялось, но старые цепочки на месте (хотя у них теперь 0 references) 😕
kube-proxy настроил все по-новому, но не удалил мусор
Запуск kube-proxy --cleanup в нужном поде - тоже не помогает

 kubectl --namespace kube-system exec kube-proxy-k5dg6 -- kube-proxy --cleanup
I0122 09:53:01.124630    3402 server.go:231] "Warning, all flags other than --config, --write-config-to, and --cleanup are deprecated, please begin using a config file ASAP"

Полностью очистим все правила iptables 
Создадим в ВМ с Minikube файл /tmp/iptables.cleanup

iptables-restore /tmp/iptables.cleanup
root@minikube:~# iptables-restore /tmp/iptables.cleanup
root@minikube:~# 

Теперь надо подождать (примерно 30 секунд), пока kube-proxy восстановит правила для сервисов

Итак, лишние правила удалены и мы видим только актуальную конфигурацию kube-proxy периодически делает полную синхронизацию правил в своих цепочках)

Как посмотреть конфигурацию IPVS? Ведь в ВМ нет утилиты ipvsadm?

В ВМ выполним команду toolbox - в результате мы окажется в контейнере с Fedora.  -- не получилось установить, тк нет toolbox внутри ВМ  minikube

Теперь установим ipvsadm :  dnf install -y ipvsadm && dnf clean all


Выполним ipvsadm --list -n и среди прочих сервисов найдем наш:

Теперь выйдем из контейнера toolbox и сделаем ping кластерного IP:
ping -c1 10.106.18.171
PING 10.106.18.171 (10.106.18.171): 56 data bytes
64 bytes from 10.106.18.171: seq=0 ttl=64 time=0.277 ms

Итак, все работает. Но почему пингуется виртуальный IP?
Все просто - он уже не такой виртуальный. Этот IP теперь есть на интерфейсе kube-ipvs0 

ip addr show kube-ipvs0
57: kube-ipvs0: <BROADCAST,NOARP> mtu 1500 qdisc noop state DOWN group default
link/ether 16:0b:8d:8c:fe:ac brd ff:ff:ff:ff:ff:ff

Также, правила в iptables построены по-другому. Вместо  цепочки правил для каждого сервиса, теперь используются хэш-таблицы (ipset). Можете посмотреть их, установив утилиту ipset в toolbox 

---------------------------------------
Работа с LoadBalancer и IngressIngress
----------------------------------------

Установка MetalLB
MetalLB позволяет запустить внутри кластера L4-балансировщик, который будет принимать извне запросы к сервисам и раскидывать их между подами. Установка его проста:

kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.9.3/manifests/namespace.yaml
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.9.3/manifests/metallb.yaml

--- не запускались поды.  Причина: не могли скачать образ.  выполнил: sudo nano /etc/resolv.conf и прописал там nameserver 8.8.8.8 и nameserver 8.8.4.4.
затем sudo systemctl daemon-reload и sudo systemctl restart docker.  После этого образы скачались.

 kubectl create secret generic -n metallb-system memberlist --from-literal=secretkey="$(openssl rand -base64 128)"
 
 !!!!!В продуктиве так делать не надо. Сначала стоит скачать файл и разобраться, что там внутри!!!!
 
 Проверим, что создалис нужные объекты:
  (kubernetes-networks)kubectl --namespace metallb-system get all
NAME                              READY   STATUS    RESTARTS   AGE
pod/controller-69bcb9c949-fhlgr   1/1     Running   0          18m
pod/speaker-vhrng                 1/1     Running   0          18m

NAME                     DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
daemonset.apps/speaker   1         1         1       1            1           beta.kubernetes.io/os=linux   18m

NAME                         READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/controller   1/1     1            1           18m

NAME                                    DESIRED   CURRENT   READY   AGE
replicaset.apps/controller-69bcb9c949   1         1         1       18m



Теперь настроим балансировщик с помощью ConfigMap Создайте манифест metallb-config.yaml в папке kubernetes-networks
 
 В конфигурации мы настраиваем:
Режим L2 (анонс адресов балансировщиков с помощью ARP)
Создаем пул адресов 172.17.255.1 - 172.17.255.255 - они будут назначаться сервисам с типом LoadBalancer
Теперь можно применить наш манифест: kubectl apply -f metallb-config.yaml
Контроллер подхватит изменения автоматически.

(kubernetes-networks)kubectl apply -f metallb-config.yaml
configmap/config created

Сделайте копию файла web-svc-cip.yaml в web-svc-lb.yaml и откройте его в редакторе.
Измените имя сервиса и его тип на LoadBalancer. 

Примените манифест
(kubernetes-networks)kubectl apply -f web-svc-lb.yaml 
service/web-svc-cip configured

Теперь посмотрите логи пода-контроллера MetalLB (подставьте правильное имя!)

Если мы попробуем открыть URL http://<our_LB_address>/index.html , то... ничего не выйдет.
Это потому, что сеть кластера изолирована от нашей основной ОС (а ОС не знает ничего о подсети для балансировщиков)
Чтобы это поправить, добавим статический маршрут
В реальном окружении это решается добавлением нужной подсети на интерфейс сетевого оборудования
Или использованием L3-режима (что потребует усилий от сетевиков, но более предпочтительно)

Найдите IP-адрес виртуалки с Minikube. Например так: 
minikube ssh
inet 192.168.64.4/24 brd 192.168.64.255 scope global dynamic eth0$ 

Добавьте маршрут в вашей ОС на IP-адрес Minikube:
sudo route add 172.17.255.0/24 192.168.64.4

Добавление маршрута может иметь другой синтаксис
(например, ip route add 172.17.255.0/24 via 192.168.64.4 в ОС Linux) или вообще не сработать (в зависимости от VM Driver в Minkube).

В этом случае, не надо расстраиваться - работу наших сервисов и манифестов можно проверить из консоли Minikube, просто будет не так эффектно.
P.S. - Самый простой способ найти IP виртуалки с minikube - minikube ip

Если все получилось, то можно открыть в браузере URL с IP-адресом нашего балансировщика и посмотреть, как космические корабли бороздят просторы вселенной.

Если пообновлять страничку с помощью Ctrl-F5 (т.е. игнорируя кэш), то будет видно, что каждый наш запрос приходит на другой под. Причем, порядок смены подов - всегда один и тот же.
Так работает IPVS - по умолчанию он использует rr (Round-Robin) балансировку.
К сожалению, выбрать алгоритм на уровне манифеста сервиса нельзя. Но когда-нибудь, эта полезная фича появится.

----------------------
Создание Ingress
-----------------------
Теперь, когда у нас есть балансировщик, можно заняться Ingress-контроллером и прокси:неудобно, когда на каждый Web-сервис надо выделять свой IP-адрес а еще хочется балансировку по HTTP-заголовкам (sticky sessions)
Для нашего домашнего задания возьмем почти "коробочный" ingress-nginx от проекта Kubernetes. Это "достаточно хороший" Ingress для умеренных нагрузок, основанный на OpenResty и пачке Lua-скриптов.

Установка начинается с основного манифеста:
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/baremetal/deploy.yaml

(kubernetes-networks)kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/baremetal/deploy.yaml
namespace/ingress-nginx created
serviceaccount/ingress-nginx created
serviceaccount/ingress-nginx-admission created
role.rbac.authorization.k8s.io/ingress-nginx created
role.rbac.authorization.k8s.io/ingress-nginx-admission created
clusterrole.rbac.authorization.k8s.io/ingress-nginx created
clusterrole.rbac.authorization.k8s.io/ingress-nginx-admission created
rolebinding.rbac.authorization.k8s.io/ingress-nginx created
rolebinding.rbac.authorization.k8s.io/ingress-nginx-admission created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx-admission created
configmap/ingress-nginx-controller created
service/ingress-nginx-controller created
service/ingress-nginx-controller-admission created
deployment.apps/ingress-nginx-controller created
job.batch/ingress-nginx-admission-create created
job.batch/ingress-nginx-admission-patch created
ingressclass.networking.k8s.io/nginx created
validatingwebhookconfiguration.admissionregistration.k8s.io/ingress-nginx-admission created

kubectl get all -n ingress-nginx 
NAME                                            READY   STATUS      RESTARTS   AGE
pod/ingress-nginx-admission-create-7j79w        0/1     Completed   0          97s
pod/ingress-nginx-admission-patch-d8rzv         0/1     Completed   0          97s
pod/ingress-nginx-controller-6c56945c75-rvz2b   1/1     Running     0          97s

NAME                                         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
service/ingress-nginx-controller             NodePort    10.102.132.32    <none>        80:30644/TCP,443:32115/TCP   97s
service/ingress-nginx-controller-admission   ClusterIP   10.103.129.156   <none>        443/TCP                      97s

NAME                                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/ingress-nginx-controller   1/1     1            1           97s

NAME                                                  DESIRED   CURRENT   READY   AGE
replicaset.apps/ingress-nginx-controller-6c56945c75   1         1         1       97s

NAME                                       COMPLETIONS   DURATION   AGE
job.batch/ingress-nginx-admission-create   1/1           20s        97s
job.batch/ingress-nginx-admission-patch    1/1           20s        97s


После установки основных компонентов, в рекомендуется применить манифест, который создаст NodePort -сервис. Но у нас есть MetalLB, мы можем сделать круче.
Можно сделать просто minikube addons enable ingress , но мы не ищем легких путей.

Создадим файл nginx-lb.yaml c конфигурацией LoadBalancer -сервиса (работаем в каталоге kubernetes-networks )


Теперь применим созданный манифест и посмотрим на IP-адрес, назначенный ему MetalLB
Теперь можно сделать пинг на этот IP-адрес и даже curl

(kubernetes-networks)kubectl apply -f nginx-lb.yaml 
service/ingress-nginx created

(kubernetes-networks)kubectl get svc -n ingress-nginx 
NAME                                 TYPE           CLUSTER-IP       EXTERNAL-IP    PORT(S)                      AGE
ingress-nginx                        LoadBalancer   10.96.192.73     172.17.255.2   80:31921/TCP,443:32720/TCP   13s
ingress-nginx-controller             NodePort       10.102.132.32    <none>         80:30644/TCP,443:32115/TCP   9m39s
ingress-nginx-controller-admission   ClusterIP      10.103.129.156   <none>         443/TCP                      9m39s


Подключение приложение Web к Ingress
Наш Ingress-контроллер не требует ClusterIP для балансировки трафика
Список узлов для балансировки заполняется из ресурса Endpoints нужного сервиса (это нужно для "интеллектуальной" балансировки, привязки сессий и т.п.)
Поэтому мы можем использовать headless-сервис для нашего веб-приложения.
Скопируйте web-svc-cip.yaml в web-svc-headless.yaml
измените имя сервиса на web-svc
добавьте параметр clusterIP: None

Теперь примените полученный манифест и проверьте, что ClusterIP для сервиса web-svc действительно не назначен
(kubernetes-networks)kubectl apply -f web-svc-headless.yaml 
service/web-svc created


kubectl get svc
NAME          TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)        AGE
kubernetes    ClusterIP      10.96.0.1      <none>         443/TCP        160m
web-svc       ClusterIP      None           <none>         80/TCP         9s
web-svc-cip   LoadBalancer   10.109.20.86   172.17.255.1   80:30843/TCP   144m


Теперь настроим наш ingress-прокси, создав манифест с ресурсом Ingress (файл назовите web-ingress.yaml ):

Примените манифест и проверьте, что корректно заполнены Address и Backends
kubectl describe ingress/web

(kubernetes-networks)kubectl describe ingress/web
Name:             web
Labels:           <none>
Namespace:        default
Address:          
Default backend:  web-svc:80 (172.17.0.2:8000,172.17.0.3:8000,172.17.0.6:8000)
Rules:
  Host        Path  Backends
  ----        ----  --------
  *           
              /web   web-svc:80 (172.17.0.2:8000,172.17.0.3:8000,172.17.0.6:8000)
Annotations:  nginx.ingress.kubernetes.io/rewrite-target: /
Events:       <none>




 
