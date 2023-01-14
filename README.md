Лекция №2: Знакомство с Kubernetes, основные понятия и архитектура // ДЗ

    kubernetes-intro

Знакомство с Kubernetes
Задание:

Знакомство с решениями для запуска локального Kubernetes кластера, создание первого pod Цель:

В данном дз студенты научатся формировать локальное окружение, запустят локальную версию kubernetes при помощи minikube, научатся использовать CLI утилиту kubectl для управления kubernetes.

Описание/Пошаговая инструкция выполнения домашнего задания:

Все действия описаны в методическом указании.

Критерии оценки:

0 б. - задание не выполнено 1 б. - задание выполнено 2 б. - выполнены все дополнительные задания
Выполнено:

    Настройка локального окружения. Запуск первого контейнера. Работа с kubectl

    Установка kubectl (Ubuntu)

    https://kubernetes.io/ru/docs/tasks/tools/install-kubectl/

sudo apt-get update && sudo apt-get install -y apt-transport-https
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubectl

kubectl version --client --short
Flag --short has been deprecated, and will be removed in the future. The --short output will become the default.
Client Version: v1.26.0
Kustomize Version: v4.5.7

#Настройка автозаполнения для kubectl
source <(kubectl completion zsh)  # настройка автодополнения в текущую сессию zsh
echo "[[ $commands[kubectl] ]] && source <(kubectl completion zsh)" >> ~/.zshrc # add autocomplete permanently to your zsh shell

    Установка Minikube

    https://minikube.sigs.k8s.io/docs/start/

curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

#Start your cluster
minikube start

😄  minikube v1.28.0 на Ubuntu 22.04
    ▪ KUBECONFIG=:/home/dpp/.kube/admin-dev-k8.energochain.config:/home/dpp/.kube/dev-k8.energochain.config:/home/dpp/.kube/test-k8.energochain.config
❗  Kubernetes 1.25.0 has a known issue with resolv.conf. minikube is using a workaround that should work for most use cases.
❗  For more information, see: https://github.com/kubernetes/kubernetes/issues/112135
🆕  Доступен Kubernetes 1.25.3. Для обновления, укажите: --kubernetes-version=v1.25.3
✨  Используется драйвер docker на основе существующего профиля
👍  Запускается control plane узел minikube в кластере minikube
🚜  Скачивается базовый образ ...
🔄  Перезагружается существующий docker container для "minikube" ...
🐳  Подготавливается Kubernetes v1.25.0 на Docker 20.10.17 ...
🔎  Компоненты Kubernetes проверяются ...
    ▪ Используется образ gcr.io/k8s-minikube/storage-provisioner:v5
    ▪ Используется образ k8s.gcr.io/ingress-nginx/controller:v1.2.1
    ▪ Используется образ k8s.gcr.io/ingress-nginx/kube-webhook-certgen:v1.1.1
    ▪ Используется образ k8s.gcr.io/ingress-nginx/kube-webhook-certgen:v1.1.1
🔎  Verifying ingress addon...
🌟  Включенные дополнения: default-storageclass, storage-provisioner, ingress
🏄  Готово! kubectl настроен для использования кластера "minikube" и "default" пространства имён по умолчанию

~ kubectl cluster-info
Kubernetes control plane is running at https://192.168.49.2:8443
CoreDNS is running at https://192.168.49.2:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

    Установка Kubernetes Dashboard

minikube dashboard --url

🔌  Enabling dashboard ...
    ▪ Используется образ docker.io/kubernetesui/dashboard:v2.7.0
    ▪ Используется образ docker.io/kubernetesui/metrics-scraper:v1.0.8
💡  Some dashboard features require the metrics-server addon. To enable all features please run:

	minikube addons enable metrics-server	


🤔  Verifying dashboard health ...
🚀  Launching proxy ...
🤔  Verifying proxy health ...
http://127.0.0.1:45263/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/

Kubernetes Dashboard

При установке кластера с использованием Minikube будет создана виртуальная машина в которой будут работать все системные компоненты кластера Kubernetes. Можем убедиться в этом, зайдем на ВМ по SSH и посмотрим запущенные Docker контейнеры:

minikube ssh
docker@minikube:~$ docker ps

CONTAINER ID   IMAGE                  COMMAND                  CREATED              STATUS              PORTS                                      NAMES
717ca55f8739   5185b96f0bec           "/coredns -conf /etc…"   About a minute ago   Up About a minute                                              k8s_coredns_coredns-565d847f94-nkfqg_kube-system_27de7ed3-1de2-469d-acf6-16096734f13e_4
7ab54a934dbb   6e38f40d628d           "/storage-provisioner"   About a minute ago   Up About a minute                                              k8s_storage-provisioner_storage-provisioner_kube-system_3234523b-d4dc-4d52-9b91-206454baf384_13
2ad2dd7c0473   75bdf78d9d67           "/usr/bin/dumb-init …"   About a minute ago   Up About a minute                                              k8s_controller_ingress-nginx-controller-5959f988fd-8ph26_ingress-nginx_350e4ece-48cc-4a81-adc1-52e2f1cbdd83_4
4c88007b00e6   k8s.gcr.io/pause:3.6   "/pause"                 About a minute ago   Up About a minute                                              k8s_POD_coredns-565d847f94-nkfqg_kube-system_27de7ed3-1de2-469d-acf6-16096734f13e_4
63178d97ba2d   k8s.gcr.io/pause:3.6   "/pause"                 About a minute ago   Up About a minute                                              k8s_POD_storage-provisioner_kube-system_3234523b-d4dc-4d52-9b91-206454baf384_4
83bcf54aa33d   647cf9d55bae           "java -jar ./app.jar"    About a minute ago   Up About a minute                                              k8s_app_werf-guide-app-7646f8cbfc-njr9w_werf-guide-app_74bc20f4-7c36-409a-8169-fd56465d5be5_3
c2875128ea7f   k8s.gcr.io/pause:3.6   "/pause"                 About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 0.0.0.0:443->443/tcp   k8s_POD_ingress-nginx-controller-5959f988fd-8ph26_ingress-nginx_350e4ece-48cc-4a81-adc1-52e2f1cbdd83_4
a92faaf0a0a7   07655ddf2eeb           "/dashboard --insecu…"   About a minute ago   Up About a minute                                              k8s_kubernetes-dashboard_kubernetes-dashboard-57bbdc5f89-q2q79_kubernetes-dashboard_13060019-ecc8-472a-a82b-b68e495ecbe4_1
49170ced1ff1   115053965e86           "/metrics-sidecar"       About a minute ago   Up About a minute                                              k8s_dashboard-metrics-scraper_dashboard-metrics-scraper-b74747df5-tjzpr_kubernetes-dashboard_81994d2e-c2ec-467a-a5c1-eddd8f9ea213_1
b6bf4ca9c5ae   k8s.gcr.io/pause:3.6   "/pause"                 About a minute ago   Up About a minute                                              k8s_POD_werf-guide-app-7646f8cbfc-njr9w_werf-guide-app_74bc20f4-7c36-409a-8169-fd56465d5be5_3
c9af539976df   k8s.gcr.io/pause:3.6   "/pause"                 About a minute ago   Up About a minute                                              k8s_POD_kubernetes-dashboard-57bbdc5f89-q2q79_kubernetes-dashboard_13060019-ecc8-472a-a82b-b68e495ecbe4_1
a44625f54c54   k8s.gcr.io/pause:3.6   "/pause"                 About a minute ago   Up About a minute                                              k8s_POD_dashboard-metrics-scraper-b74747df5-tjzpr_kubernetes-dashboard_81994d2e-c2ec-467a-a5c1-eddd8f9ea213_1
165940e0a42c   58a9a0c6d96f           "/usr/local/bin/kube…"   About a minute ago   Up About a minute                                              k8s_kube-proxy_kube-proxy-vbmhg_kube-system_b60a9cb6-5ba1-4dd8-a90b-5e3f0e56f3c0_4
db652765bc87   k8s.gcr.io/pause:3.6   "/pause"                 About a minute ago   Up About a minute                                              k8s_POD_kube-proxy-vbmhg_kube-system_b60a9cb6-5ba1-4dd8-a90b-5e3f0e56f3c0_4
435338bdd0af   4d2edfd10d3e           "kube-apiserver --ad…"   About a minute ago   Up About a minute                                              k8s_kube-apiserver_kube-apiserver-minikube_kube-system_16de73f898f4460d96d28cf19ba8407f_4
6a9f14e7d944   1a54c86c03a6           "kube-controller-man…"   About a minute ago   Up About a minute                                              k8s_kube-controller-manager_kube-controller-manager-minikube_kube-system_77bee6e3b99b4016b81d7d949c58a789_4
ab2cce812a4c   bef2cf311509           "kube-scheduler --au…"   About a minute ago   Up About a minute                                              k8s_kube-scheduler_kube-scheduler-minikube_kube-system_b564e2b429a630c74cbb01d3c4b7a228_4
c28c49248bac   a8a176a5d5d6           "etcd --advertise-cl…"   About a minute ago   Up About a minute                                              k8s_etcd_etcd-minikube_kube-system_bd495b7643dfc9d3194bd002e968bc3d_4
abf08ec566fb   k8s.gcr.io/pause:3.6   "/pause"                 About a minute ago   Up About a minute                                              k8s_POD_kube-scheduler-minikube_kube-system_b564e2b429a630c74cbb01d3c4b7a228_4
e01b4238da81   k8s.gcr.io/pause:3.6   "/pause"                 About a minute ago   Up About a minute                                              k8s_POD_kube-controller-manager-minikube_kube-system_77bee6e3b99b4016b81d7d949c58a789_4
ceecbe7d5e70   k8s.gcr.io/pause:3.6   "/pause"                 About a minute ago   Up About a minute                                              k8s_POD_kube-apiserver-minikube_kube-system_16de73f898f4460d96d28cf19ba8407f_4
d51f3d3a5a21   k8s.gcr.io/pause:3.6   "/pause"                 About a minute ago   Up About a minute                                              k8s_POD_etcd-minikube_kube-system_bd495b7643dfc9d3194bd002e968bc3d_4

Проверим, что Kubernetes обладает некоторой устойчивостью к отказам, удалим все контейнеры:

docker rm -f $(docker ps -a -q)

Эти же компоненты, но уже в виде pod можно увидеть в namespace kube-system

kubectl get pods -n kube-system    

NAME                               READY   STATUS    RESTARTS         AGE
coredns-565d847f94-nkfqg           1/1     Running   5                87d
etcd-minikube                      1/1     Running   5                87d
kube-apiserver-minikube            1/1     Running   5                87d
kube-controller-manager-minikube   1/1     Running   5                87d
kube-proxy-vbmhg                   1/1     Running   5                87d
kube-scheduler-minikube            1/1     Running   5                87d
storage-provisioner                1/1     Running   15 (2m58s ago)   87d

Можно устроить еще одну проверку на прочность и удалить все pod с системными компонентами (все поды из namespace kube-system):

kubectl delete pod --all -n kube-system

pod "coredns-565d847f94-nkfqg" deleted
pod "etcd-minikube" deleted
pod "kube-apiserver-minikube" deleted
pod "kube-controller-manager-minikube" deleted
pod "kube-proxy-vbmhg" deleted
pod "kube-scheduler-minikube" deleted
pod "storage-provisioner" deleted

Проверим, что кластер находится в рабочем состоянии

kubectl get cs

Warning: v1 ComponentStatus is deprecated in v1.19+
NAME                 STATUS    MESSAGE                         ERROR
scheduler            Healthy   ok                              
controller-manager   Healthy   ok                              
etcd-0               Healthy   {"health":"true","reason":""}  

Задание

Разберитесь почему все pod в namespace kube-system восстановились после удаления. Укажите причину в описании PR

kubectl get pods -n kube-system 

NAME                               READY   STATUS    RESTARTS        AGE
coredns-565d847f94-jmkj9           1/1     Running   1 (29s ago)     11h
etcd-minikube                      1/1     Running   6 (29s ago)     11h
kube-apiserver-minikube            1/1     Running   6 (6h56m ago)   11h
kube-controller-manager-minikube   1/1     Running   6 (6h56m ago)   11h
kube-proxy-9mg4f                   1/1     Running   1 (6h56m ago)   11h
kube-scheduler-minikube            1/1     Running   6 (6h56m ago)   11h
storage-provisioner                1/1     Running   0               12s

kubectl describe po coredns-565d847f94-jmkj9 -n kube-system

Name:                 coredns-565d847f94-jmkj9
Namespace:            kube-system
Priority:             2000000000
Priority Class Name:  system-cluster-critical
Service Account:      coredns
Node:                 minikube/192.168.49.2
Start Time:           Fri, 30 Dec 2022 17:20:43 +0300
Labels:               k8s-app=kube-dns
                      pod-template-hash=565d847f94
Annotations:          <none>
Status:               Running
IP:                   172.17.0.6
IPs:
  IP:           172.17.0.6
Controlled By:  ReplicaSet/coredns-565d847f94
...

core-dns управляется контроллером ReplicaSet, т.е. при удалении poda, ReplicaSet обнаруживает его отсутствие и создаёт новый.

kubectl describe po etcd-minikube -n kube-system

Name:                 etcd-minikube
Namespace:            kube-system
Priority:             2000001000
Priority Class Name:  system-node-critical
Node:                 minikube/192.168.49.2
Start Time:           Fri, 30 Dec 2022 15:45:11 +0300
Labels:               component=etcd
                      tier=control-plane
Annotations:          kubeadm.kubernetes.io/etcd.advertise-client-urls: https://192.168.49.2:2379
                      kubernetes.io/config.hash: bd495b7643dfc9d3194bd002e968bc3d
                      kubernetes.io/config.mirror: bd495b7643dfc9d3194bd002e968bc3d
                      kubernetes.io/config.seen: 2022-12-30T14:13:03.898281376Z
                      kubernetes.io/config.source: file
Status:               Running
IP:                   192.168.49.2
IPs:
  IP:           192.168.49.2
Controlled By:  Node/minikube
...

kubectl describe po kube-apiserver-minikube -n kube-system

Name:                 kube-apiserver-minikube
Namespace:            kube-system
Priority:             2000001000
Priority Class Name:  system-node-critical
Node:                 minikube/192.168.49.2
Start Time:           Fri, 30 Dec 2022 15:45:11 +0300
Labels:               component=kube-apiserver
                      tier=control-plane
Annotations:          kubeadm.kubernetes.io/kube-apiserver.advertise-address.endpoint: 192.168.49.2:8443
                      kubernetes.io/config.hash: 16de73f898f4460d96d28cf19ba8407f
                      kubernetes.io/config.mirror: 16de73f898f4460d96d28cf19ba8407f
                      kubernetes.io/config.seen: 2022-12-30T14:13:03.898314768Z
                      kubernetes.io/config.source: file
Status:               Running
IP:                   192.168.49.2
IPs:
  IP:           192.168.49.2
Controlled By:  Node/minikube
...

kubectl describe po kube-controller-manager-minikube -n kube-system

Name:                 kube-controller-manager-minikube
Namespace:            kube-system
Priority:             2000001000
Priority Class Name:  system-node-critical
Node:                 minikube/192.168.49.2
Start Time:           Tue, 04 Oct 2022 17:15:16 +0300
Labels:               component=kube-controller-manager
                      tier=control-plane
Annotations:          kubernetes.io/config.hash: 77bee6e3b99b4016b81d7d949c58a789
                      kubernetes.io/config.mirror: 77bee6e3b99b4016b81d7d949c58a789
                      kubernetes.io/config.seen: 2022-12-30T14:13:03.898319030Z
                      kubernetes.io/config.source: file
Status:               Running
IP:                   192.168.49.2
IPs:
  IP:           192.168.49.2
Controlled By:  Node/minikube
...

etcd-minikube,kube-apiserver,kube-controller-manager-minikube,kube-scheduler-minikube управляются Node/minikube, т.е. самим minikube.

kubectl describe po kube-proxy-9mg4f -n kube-system

Name:                 kube-proxy-9mg4f
Namespace:            kube-system
Priority:             2000001000
Priority Class Name:  system-node-critical
Service Account:      kube-proxy
Node:                 minikube/192.168.49.2
Start Time:           Fri, 30 Dec 2022 17:20:46 +0300
Labels:               controller-revision-hash=55c79b8759
                      k8s-app=kube-proxy
                      pod-template-generation=1
Annotations:          <none>
Status:               Running
IP:                   192.168.49.2
IPs:
  IP:           192.168.49.2
Controlled By:  DaemonSet/kube-proxy
...

kube-proxy управляется контроллером DaemonSet, т.е. при удалении poda, DaemonSet обнаруживает его отсутствие и создаёт новый.
2. Dockerfile

Для выполнения домашней работы необходимо создать Dockerfile, в котором будет описан образ:

    Запускающий web-сервер на порту 8000 (можно использовать любой способ);
    Отдающий содержимое директории /app внутри контейнера (например, если в директории /app лежит файл homework.html , то при запуске контейнера данный файл должен быть доступен по URL http://localhost:8000/homework.html);
    Работающий с UID 1001.

FROM nginxinc/nginx-unprivileged

ENV UID=1001 \
    GID=1001 \
    USER=nginx \
    GROUP=nginx


WORKDIR /app/
COPY ./app .

COPY ./default.conf /etc/nginx/conf.d/default.conf

EXPOSE 8000/tcp
USER ${UID}:${GID}

CMD ["nginx", "-g", "daemon off;"]


Собираем образ...

cd web
docker build -t lookus84/otustestui:0.2 --no-cache .

запускаем...

docker run -d -p 8000:8000 lookus84/otustestui:0.2

94ef674acfda3e9e1c54832509e2cc438dc05a11ea60ed3023bf0eb3d27ba7a5

проверяем работу...

curl http://localhost:8000/homework.html

<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>HomeWork</title>
</head>
<body>
  <h2>Test page for HomeWork 1</h2>
</body>
</html>

docker exec -it 94ef674acfda /bin/sh
$ id
uid=1001 gid=1001 groups=1001
$ ls
homework.html


и выкладываем в Docker Hub:

ocker push lookus84/otustestui:0.2
The push refers to repository [docker.io/lookus84/otustestui]
bb8b24ba389b: Pushed 
68ddc87bbdbb: Pushed 
af1fce0ff6f7: Pushed 
0913aa865b99: Mounted from nginxinc/nginx-unprivileged 
243ddba48292: Mounted from nginxinc/nginx-unprivileged 
9d7d72cbba5c: Mounted from nginxinc/nginx-unprivileged 
c5f6e2c835e2: Mounted from nginxinc/nginx-unprivileged 
dd3488faea7b: Mounted from nginxinc/nginx-unprivileged 
891438588426: Mounted from nginxinc/nginx-unprivileged 
67a4178b7d47: Mounted from nginxinc/nginx-unprivileged 
0.2: digest: sha256:05a2a09c2b9beae5b08a4ebbb54faa13856daf179e403a4201fae138b09be8c7 size: 2400


3. Манифест pod

Напишем манифест web-pod.yaml для создания pod web c меткой app со значением web, содержащего один контейнер с названием web.

kubectl apply -f web-pod.yaml

kubectl get pods

NAME   READY   STATUS    RESTARTS   AGE
web    1/1     Running   0          47s

kubectl get pods -o yaml

kubectl describe pod web

...
Events:
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  2m22s  default-scheduler  Successfully assigned default/web to minikube
  Normal  Pulling    2m14s  kubelet            Pulling image "deron73/my-nginx-image:0.3"
  Normal  Pulled     2m5s   kubelet            Successfully pulled image "deron73/my-nginx-image:0.3" in 9.258574705s
  Normal  Created    2m5s   kubelet            Created container web
  Normal  Started    2m4s   kubelet            Started container web

Шатаем pod, указав в манифесте несуществующий тег образа web

kubectl apply -f web-pod.yaml

Events:
  Type     Reason     Age                From               Message
  ----     ------     ----               ----               -------
  Normal   Scheduled  101s               default-scheduler  Successfully assigned default/web to minikube
  Normal   Pulled     100s               kubelet            Container image "busybox:1.34.1" already present on machine
  Normal   Created    100s               kubelet            Created container init-web
  Normal   Started    100s               kubelet            Started container init-web
  Normal   Pulling    98s                kubelet            Pulling image "lookus84/otustestui:0.2"
  Normal   Pulled     74s                kubelet            Successfully pulled image "lookus84/otustestui:0.2" in 24.315160307s
  Normal   Created    74s                kubelet            Created container web
  Normal   Started    74s                kubelet            Started container web
  Normal   Killing    18s                kubelet            Container web definition changed, will be restarted
  Normal   Pulling    17s                kubelet            Pulling image "lookus84/otustestui:0.8"
  Warning  Failed     16s                kubelet            Failed to pull image "lookus84/otustestui:0.8": rpc error: code = Unknown desc = Error response from daemon: manifest for lookus84/otustestui:0.8 not found: manifest unknown: manifest unknown
  Warning  Failed     16s                kubelet            Error: ErrImagePull
  Normal   BackOff    15s                kubelet            Back-off pulling image "lookus84/otustestui:0.8"
  Warning  Failed     15s                kubelet            Error: ImagePullBackOff
  Warning  BackOff    10s (x2 over 11s)  kubelet            Back-off restarting failed container


4. Init контейнеры & Volumes

Добавим в наш pod init container, генерирующий страницу index.html Для того, чтобы файлы, созданные в init контейнере, были доступны основному контейнеру в pod нам понадобится использовать volume типа emptyDir.

...
    volumeMounts:
    - name: app
      mountPath: /app
  initContainers:
  - name: init-web
    image: busybox:1.34.1
    imagePullPolicy: IfNotPresent
    volumeMounts:
    - name: app
      mountPath: /app
    command: ['sh', '-c', 'wget -O- https://tinyurl.com/otus-k8s-intro | sh']
  volumes:
    - name: app
      emptyDir: {}

Перезапускам pod

kubectl delete -f web-pod.yaml

pod "web" deleted

kubectl apply -f web-pod.yaml

pod/web created

kubectl get pods -w

NAME   READY   STATUS            RESTARTS   AGE
web    0/1     PodInitializing   0          3s
web    0/1     Running           0          8s
web    0/1     Running           0          10s
web    1/1     Running           0          11s

Проверка работы приложения

kubectl port-forward --address 0.0.0.0 pod/web 8000:8000

Проверка работы приложения
6. Hipster Shop

Начнем с микросервиса frontend. Его исходный код доступен по адресу https://github.com/GoogleCloudPlatform/microservices-demo/tree/master/src/frontend Склонируем и соберем собственный образ для frontend (используя готовый Dockerfile)

cd ../../microservices-demo/src/frontend
docker build -t lookus84/hipster-frontend:0.2 .
docker push lookus84/hipster-frontend:0.2

Запустим через ad-hoc режим:

kubectl run frontend --image lookus84/hipster-frontend:0.2 --restart=Never

Один из распространенных кейсов использования ad-hoc режима - генерация манифестов средствами kubectl:

kubectl run frontend --image lookus84/hipster-frontend:0.2 --restart=Never --dry-run=client -o yaml > frontend-pod.yaml

Hipster Shop | Задание со star Выяснение причины, по которой pod frontend находится в статусе Error

kubectl get pods

NAME       READY   STATUS    RESTARTS   AGE
frontend   0/1     Error     0          3m20s
web        1/1     Running   0          30m

kubectl logs frontend
lookus@ubuntu20:~/otus/12-2022/Lookus84_platform/kubernetes-intro$ (kubernetes-intro)kubectl logs frontend
panic: environment variable "PRODUCT_CATALOG_SERVICE_ADDR" not set

goroutine 1 [running]:
main.mustMapEnv(0xc000284c60, 0xb03c4a, 0x1c)
	/go/src/github.com/GoogleCloudPlatform/microservices-demo/src/frontend/main.go:248 +0x10e
main.main()
	/go/src/github.com/GoogleCloudPlatform/microservices-demo/src/frontend/main.go:106 +0x3e9


Добавим небходимый блок env в frontend-pod-healthy.yaml из https://raw.githubusercontent.com/GoogleCloudPlatform/microservices-demo/main/kubernetes-manifests/frontend.yaml и проверяем:

    env:
      - name: PORT
        value: "8080"
      - name: PRODUCT_CATALOG_SERVICE_ADDR
        value: "productcatalogservice:3550"
      - name: CURRENCY_SERVICE_ADDR
        value: "currencyservice:7000"
      - name: CART_SERVICE_ADDR
        value: "cartservice:7070"
      - name: RECOMMENDATION_SERVICE_ADDR
        value: "recommendationservice:8080"
      - name: SHIPPING_SERVICE_ADDR
        value: "shippingservice:50051"
      - name: CHECKOUT_SERVICE_ADDR
        value: "checkoutservice:5050"
      - name: AD_SERVICE_ADDR
        value: "adservice:9555"

kubectl delete pod frontend
kubectl apply -f frontend-pod-healthy.yaml
kubectl get pods

NAME       READY   STATUS    RESTARTS   AGE
frontend   1/1     Running   0          70s
web        1/1     Running   0          39m

