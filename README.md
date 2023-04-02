
Лекция №2: Знакомство с Kubernetes, основные понятия и архитектура // ДЗ

kubernetes-intro

Знакомство с Kubernetes Задание:

Знакомство с решениями для запуска локального Kubernetes кластера, создание первого pod Цель:

В данном дз студенты научатся формировать локальное окружение, запустят локальную версию kubernetes при помощи minikube, научатся использовать CLI утилиту kubectl для управления kubernetes.

Описание/Пошаговая инструкция выполнения домашнего задания:

Все действия описаны в методическом указании.

Критерии оценки:

0 б. - задание не выполнено 1 б. - задание выполнено 2 б. - выполнены все дополнительные задания Выполнено:

Настройка локального окружения. Запуск первого контейнера. Работа с kubectl

Установка kubectl (Ubuntu)

https://kubernetes.io/ru/docs/tasks/tools/install-kubectl/

sudo apt-get update && sudo apt-get install -y apt-transport-https curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add - echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list sudo apt-get update sudo apt-get install -y kubectl

kubectl version --client --short Flag --short has been deprecated, and will be removed in the future. The --short output will become the default. Client Version: v1.26.0 Kustomize Version: v4.5.7

#Настройка автозаполнения для kubectl source <(kubectl completion zsh) # настройка автодополнения в текущую сессию zsh echo "[[ $commands[kubectl] ]] && source <(kubectl completion zsh)" >> ~/.zshrc # add autocomplete permanently to your zsh shell

Установка Minikube

https://minikube.sigs.k8s.io/docs/start/

curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 sudo install minikube-linux-amd64 /usr/local/bin/minikube

#Start your cluster minikube start

smile minikube v1.28.0 на Ubuntu 22.04 black_small_square KUBECONFIG=:/home/dpp/.kube/admin-dev-k8.energochain.config:/home/dpp/.kube/dev-k8.energochain.config:/home/dpp/.kube/test-k8.energochain.config exclamation Kubernetes 1.25.0 has a known issue with resolv.conf. minikube is using a workaround that should work for most use cases. exclamation For more information, see: kubernetes/kubernetes#112135 new Доступен Kubernetes 1.25.3. Для обновления, укажите: --kubernetes-version=v1.25.3 sparkles Используется драйвер docker на основе существующего профиля +1 Запускается control plane узел minikube в кластере minikube tractor Скачивается базовый образ ... arrows_counterclockwise Перезагружается существующий docker container для "minikube" ... whale Подготавливается Kubernetes v1.25.0 на Docker 20.10.17 ... mag_right Компоненты Kubernetes проверяются ... black_small_square Используется образ gcr.io/k8s-minikube/storage-provisioner:v5 black_small_square Используется образ k8s.gcr.io/ingress-nginx/controller:v1.2.1 black_small_square Используется образ k8s.gcr.io/ingress-nginx/kube-webhook-certgen:v1.1.1 black_small_square Используется образ k8s.gcr.io/ingress-nginx/kube-webhook-certgen:v1.1.1 mag_right Verifying ingress addon... star2 Включенные дополнения: default-storageclass, storage-provisioner, ingress surfer Готово! kubectl настроен для использования кластера "minikube" и "default" пространства имён по умолчанию

~ kubectl cluster-info Kubernetes control plane is running at https://192.168.49.2:8443 CoreDNS is running at https://192.168.49.2:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

Установка Kubernetes Dashboard

minikube dashboard --url

electric_plug Enabling dashboard ... black_small_square Используется образ docker.io/kubernetesui/dashboard:v2.7.0 black_small_square Используется образ docker.io/kubernetesui/metrics-scraper:v1.0.8 bulb Some dashboard features require the metrics-server addon. To enable all features please run:

minikube addons enable metrics-server	

thinking Verifying dashboard health ... rocket Launching proxy ... thinking Verifying proxy health ... http://127.0.0.1:45263/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/

Kubernetes Dashboard

При установке кластера с использованием Minikube будет создана виртуальная машина в которой будут работать все системные компоненты кластера Kubernetes. Можем убедиться в этом, зайдем на ВМ по SSH и посмотрим запущенные Docker контейнеры:

minikube ssh docker@minikube:~$ docker ps

CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES 717ca55f8739 5185b96f0bec "/coredns -conf /etc…" About a minute ago Up About a minute k8s_coredns_coredns-565d847f94-nkfqg_kube-system_27de7ed3-1de2-469d-acf6-16096734f13e_4 7ab54a934dbb 6e38f40d628d "/storage-provisioner" About a minute ago Up About a minute k8s_storage-provisioner_storage-provisioner_kube-system_3234523b-d4dc-4d52-9b91-206454baf384_13 2ad2dd7c0473 75bdf78d9d67 "/usr/bin/dumb-init …" About a minute ago Up About a minute k8s_controller_ingress-nginx-controller-5959f988fd-8ph26_ingress-nginx_350e4ece-48cc-4a81-adc1-52e2f1cbdd83_4 4c88007b00e6 k8s.gcr.io/pause:3.6 "/pause" About a minute ago Up About a minute k8s_POD_coredns-565d847f94-nkfqg_kube-system_27de7ed3-1de2-469d-acf6-16096734f13e_4 63178d97ba2d k8s.gcr.io/pause:3.6 "/pause" About a minute ago Up About a minute k8s_POD_storage-provisioner_kube-system_3234523b-d4dc-4d52-9b91-206454baf384_4 83bcf54aa33d 647cf9d55bae "java -jar ./app.jar" About a minute ago Up About a minute k8s_app_werf-guide-app-7646f8cbfc-njr9w_werf-guide-app_74bc20f4-7c36-409a-8169-fd56465d5be5_3 c2875128ea7f k8s.gcr.io/pause:3.6 "/pause" About a minute ago Up About a minute 0.0.0.0:80->80/tcp, 0.0.0.0:443->443/tcp k8s_POD_ingress-nginx-controller-5959f988fd-8ph26_ingress-nginx_350e4ece-48cc-4a81-adc1-52e2f1cbdd83_4 a92faaf0a0a7 07655ddf2eeb "/dashboard --insecu…" About a minute ago Up About a minute k8s_kubernetes-dashboard_kubernetes-dashboard-57bbdc5f89-q2q79_kubernetes-dashboard_13060019-ecc8-472a-a82b-b68e495ecbe4_1 49170ced1ff1 115053965e86 "/metrics-sidecar" About a minute ago Up About a minute k8s_dashboard-metrics-scraper_dashboard-metrics-scraper-b74747df5-tjzpr_kubernetes-dashboard_81994d2e-c2ec-467a-a5c1-eddd8f9ea213_1 b6bf4ca9c5ae k8s.gcr.io/pause:3.6 "/pause" About a minute ago Up About a minute k8s_POD_werf-guide-app-7646f8cbfc-njr9w_werf-guide-app_74bc20f4-7c36-409a-8169-fd56465d5be5_3 c9af539976df k8s.gcr.io/pause:3.6 "/pause" About a minute ago Up About a minute k8s_POD_kubernetes-dashboard-57bbdc5f89-q2q79_kubernetes-dashboard_13060019-ecc8-472a-a82b-b68e495ecbe4_1 a44625f54c54 k8s.gcr.io/pause:3.6 "/pause" About a minute ago Up About a minute k8s_POD_dashboard-metrics-scraper-b74747df5-tjzpr_kubernetes-dashboard_81994d2e-c2ec-467a-a5c1-eddd8f9ea213_1 165940e0a42c 58a9a0c6d96f "/usr/local/bin/kube…" About a minute ago Up About a minute k8s_kube-proxy_kube-proxy-vbmhg_kube-system_b60a9cb6-5ba1-4dd8-a90b-5e3f0e56f3c0_4 db652765bc87 k8s.gcr.io/pause:3.6 "/pause" About a minute ago Up About a minute k8s_POD_kube-proxy-vbmhg_kube-system_b60a9cb6-5ba1-4dd8-a90b-5e3f0e56f3c0_4 435338bdd0af 4d2edfd10d3e "kube-apiserver --ad…" About a minute ago Up About a minute k8s_kube-apiserver_kube-apiserver-minikube_kube-system_16de73f898f4460d96d28cf19ba8407f_4 6a9f14e7d944 1a54c86c03a6 "kube-controller-man…" About a minute ago Up About a minute k8s_kube-controller-manager_kube-controller-manager-minikube_kube-system_77bee6e3b99b4016b81d7d949c58a789_4 ab2cce812a4c bef2cf311509 "kube-scheduler --au…" About a minute ago Up About a minute k8s_kube-scheduler_kube-scheduler-minikube_kube-system_b564e2b429a630c74cbb01d3c4b7a228_4 c28c49248bac a8a176a5d5d6 "etcd --advertise-cl…" About a minute ago Up About a minute k8s_etcd_etcd-minikube_kube-system_bd495b7643dfc9d3194bd002e968bc3d_4 abf08ec566fb k8s.gcr.io/pause:3.6 "/pause" About a minute ago Up About a minute k8s_POD_kube-scheduler-minikube_kube-system_b564e2b429a630c74cbb01d3c4b7a228_4 e01b4238da81 k8s.gcr.io/pause:3.6 "/pause" About a minute ago Up About a minute k8s_POD_kube-controller-manager-minikube_kube-system_77bee6e3b99b4016b81d7d949c58a789_4 ceecbe7d5e70 k8s.gcr.io/pause:3.6 "/pause" About a minute ago Up About a minute k8s_POD_kube-apiserver-minikube_kube-system_16de73f898f4460d96d28cf19ba8407f_4 d51f3d3a5a21 k8s.gcr.io/pause:3.6 "/pause" About a minute ago Up About a minute k8s_POD_etcd-minikube_kube-system_bd495b7643dfc9d3194bd002e968bc3d_4

Проверим, что Kubernetes обладает некоторой устойчивостью к отказам, удалим все контейнеры:

docker rm -f $(docker ps -a -q)

Эти же компоненты, но уже в виде pod можно увидеть в namespace kube-system

kubectl get pods -n kube-system

NAME READY STATUS RESTARTS AGE coredns-565d847f94-nkfqg 1/1 Running 5 87d etcd-minikube 1/1 Running 5 87d kube-apiserver-minikube 1/1 Running 5 87d kube-controller-manager-minikube 1/1 Running 5 87d kube-proxy-vbmhg 1/1 Running 5 87d kube-scheduler-minikube 1/1 Running 5 87d storage-provisioner 1/1 Running 15 (2m58s ago) 87d

Можно устроить еще одну проверку на прочность и удалить все pod с системными компонентами (все поды из namespace kube-system):

kubectl delete pod --all -n kube-system

pod "coredns-565d847f94-nkfqg" deleted pod "etcd-minikube" deleted pod "kube-apiserver-minikube" deleted pod "kube-controller-manager-minikube" deleted pod "kube-proxy-vbmhg" deleted pod "kube-scheduler-minikube" deleted pod "storage-provisioner" deleted

Проверим, что кластер находится в рабочем состоянии

kubectl get cs

Warning: v1 ComponentStatus is deprecated in v1.19+ NAME STATUS MESSAGE ERROR scheduler Healthy ok
controller-manager Healthy ok
etcd-0 Healthy {"health":"true","reason":""}

Задание

Разберитесь почему все pod в namespace kube-system восстановились после удаления. Укажите причину в описании PR

kubectl get pods -n kube-system

NAME READY STATUS RESTARTS AGE coredns-565d847f94-jmkj9 1/1 Running 1 (29s ago) 11h etcd-minikube 1/1 Running 6 (29s ago) 11h kube-apiserver-minikube 1/1 Running 6 (6h56m ago) 11h kube-controller-manager-minikube 1/1 Running 6 (6h56m ago) 11h kube-proxy-9mg4f 1/1 Running 1 (6h56m ago) 11h kube-scheduler-minikube 1/1 Running 6 (6h56m ago) 11h storage-provisioner 1/1 Running 0 12s

kubectl describe po coredns-565d847f94-jmkj9 -n kube-system

Name: coredns-565d847f94-jmkj9 Namespace: kube-system Priority: 2000000000 Priority Class Name: system-cluster-critical Service Account: coredns Node: minikube/192.168.49.2 Start Time: Fri, 30 Dec 2022 17:20:43 +0300 Labels: k8s-app=kube-dns pod-template-hash=565d847f94 Annotations: Status: Running IP: 172.17.0.6 IPs: IP: 172.17.0.6 Controlled By: ReplicaSet/coredns-565d847f94 ...

core-dns управляется контроллером ReplicaSet, т.е. при удалении poda, ReplicaSet обнаруживает его отсутствие и создаёт новый.

kubectl describe po etcd-minikube -n kube-system

Name: etcd-minikube Namespace: kube-system Priority: 2000001000 Priority Class Name: system-node-critical Node: minikube/192.168.49.2 Start Time: Fri, 30 Dec 2022 15:45:11 +0300 Labels: component=etcd tier=control-plane Annotations: kubeadm.kubernetes.io/etcd.advertise-client-urls: https://192.168.49.2:2379 kubernetes.io/config.hash: bd495b7643dfc9d3194bd002e968bc3d kubernetes.io/config.mirror: bd495b7643dfc9d3194bd002e968bc3d kubernetes.io/config.seen: 2022-12-30T14:13:03.898281376Z kubernetes.io/config.source: file Status: Running IP: 192.168.49.2 IPs: IP: 192.168.49.2 Controlled By: Node/minikube ...

kubectl describe po kube-apiserver-minikube -n kube-system

Name: kube-apiserver-minikube Namespace: kube-system Priority: 2000001000 Priority Class Name: system-node-critical Node: minikube/192.168.49.2 Start Time: Fri, 30 Dec 2022 15:45:11 +0300 Labels: component=kube-apiserver tier=control-plane Annotations: kubeadm.kubernetes.io/kube-apiserver.advertise-address.endpoint: 192.168.49.2:8443 kubernetes.io/config.hash: 16de73f898f4460d96d28cf19ba8407f kubernetes.io/config.mirror: 16de73f898f4460d96d28cf19ba8407f kubernetes.io/config.seen: 2022-12-30T14:13:03.898314768Z kubernetes.io/config.source: file Status: Running IP: 192.168.49.2 IPs: IP: 192.168.49.2 Controlled By: Node/minikube ...

kubectl describe po kube-controller-manager-minikube -n kube-system

Name: kube-controller-manager-minikube Namespace: kube-system Priority: 2000001000 Priority Class Name: system-node-critical Node: minikube/192.168.49.2 Start Time: Tue, 04 Oct 2022 17:15:16 +0300 Labels: component=kube-controller-manager tier=control-plane Annotations: kubernetes.io/config.hash: 77bee6e3b99b4016b81d7d949c58a789 kubernetes.io/config.mirror: 77bee6e3b99b4016b81d7d949c58a789 kubernetes.io/config.seen: 2022-12-30T14:13:03.898319030Z kubernetes.io/config.source: file Status: Running IP: 192.168.49.2 IPs: IP: 192.168.49.2 Controlled By: Node/minikube ...

etcd-minikube,kube-apiserver,kube-controller-manager-minikube,kube-scheduler-minikube управляются Node/minikube, т.е. самим minikube.

kubectl describe po kube-proxy-9mg4f -n kube-system

Name: kube-proxy-9mg4f Namespace: kube-system Priority: 2000001000 Priority Class Name: system-node-critical Service Account: kube-proxy Node: minikube/192.168.49.2 Start Time: Fri, 30 Dec 2022 17:20:46 +0300 Labels: controller-revision-hash=55c79b8759 k8s-app=kube-proxy pod-template-generation=1 Annotations: Status: Running IP: 192.168.49.2 IPs: IP: 192.168.49.2 Controlled By: DaemonSet/kube-proxy ...

kube-proxy управляется контроллером DaemonSet, т.е. при удалении poda, DaemonSet обнаруживает его отсутствие и создаёт новый. 2. Dockerfile

Для выполнения домашней работы необходимо создать Dockerfile, в котором будет описан образ:

Запускающий web-сервер на порту 8000 (можно использовать любой способ);
Отдающий содержимое директории /app внутри контейнера (например, если в директории /app лежит файл homework.html , то при запуске контейнера данный файл должен быть доступен по URL http://localhost:8000/homework.html);
Работающий с UID 1001.

FROM nginxinc/nginx-unprivileged

ENV UID=1001
GID=1001
USER=nginx
GROUP=nginx

WORKDIR /app/ COPY ./app .

COPY ./default.conf /etc/nginx/conf.d/default.conf

EXPOSE 8000/tcp USER

{GID}

CMD ["nginx", "-g", "daemon off;"]

Собираем образ...

cd web docker build -t lookus84/otustestui:0.2 --no-cache .

запускаем...

docker run -d -p 8000:8000 lookus84/otustestui:0.2

94ef674acfda3e9e1c54832509e2cc438dc05a11ea60ed3023bf0eb3d27ba7a5

проверяем работу...

curl http://localhost:8000/homework.html

<!doctype html>
<title>HomeWork</title>
Test page for HomeWork 1

docker exec -it 94ef674acfda /bin/sh $ id uid=1001 gid=1001 groups=1001 $ ls homework.html

и выкладываем в Docker Hub:

ocker push lookus84/otustestui:0.2 The push refers to repository [docker.io/lookus84/otustestui] bb8b24ba389b: Pushed 68ddc87bbdbb: Pushed af1fce0ff6f7: Pushed 0913aa865b99: Mounted from nginxinc/nginx-unprivileged 243ddba48292: Mounted from nginxinc/nginx-unprivileged 9d7d72cbba5c: Mounted from nginxinc/nginx-unprivileged c5f6e2c835e2: Mounted from nginxinc/nginx-unprivileged dd3488faea7b: Mounted from nginxinc/nginx-unprivileged 891438588426: Mounted from nginxinc/nginx-unprivileged 67a4178b7d47: Mounted from nginxinc/nginx-unprivileged 0.2: digest: sha256:05a2a09c2b9beae5b08a4ebbb54faa13856daf179e403a4201fae138b09be8c7 size: 2400

    Манифест pod

Напишем манифест web-pod.yaml для создания pod web c меткой app со значением web, содержащего один контейнер с названием web.

kubectl apply -f web-pod.yaml

kubectl get pods

NAME READY STATUS RESTARTS AGE web 1/1 Running 0 47s

kubectl get pods -o yaml

kubectl describe pod web

... Events: Type Reason Age From Message

Normal Scheduled 2m22s default-scheduler Successfully assigned default/web to minikube Normal Pulling 2m14s kubelet Pulling image "deron73/my-nginx-image:0.3" Normal Pulled 2m5s kubelet Successfully pulled image "deron73/my-nginx-image:0.3" in 9.258574705s Normal Created 2m5s kubelet Created container web Normal Started 2m4s kubelet Started container web

Шатаем pod, указав в манифесте несуществующий тег образа web

kubectl apply -f web-pod.yaml

Events: Type Reason Age From Message

Normal Scheduled 101s default-scheduler Successfully assigned default/web to minikube Normal Pulled 100s kubelet Container image "busybox:1.34.1" already present on machine Normal Created 100s kubelet Created container init-web Normal Started 100s kubelet Started container init-web Normal Pulling 98s kubelet Pulling image "lookus84/otustestui:0.2" Normal Pulled 74s kubelet Successfully pulled image "lookus84/otustestui:0.2" in 24.315160307s Normal Created 74s kubelet Created container web Normal Started 74s kubelet Started container web Normal Killing 18s kubelet Container web definition changed, will be restarted Normal Pulling 17s kubelet Pulling image "lookus84/otustestui:0.8" Warning Failed 16s kubelet Failed to pull image "lookus84/otustestui:0.8": rpc error: code = Unknown desc = Error response from daemon: manifest for lookus84/otustestui:0.8 not found: manifest unknown: manifest unknown Warning Failed 16s kubelet Error: ErrImagePull Normal BackOff 15s kubelet Back-off pulling image "lookus84/otustestui:0.8" Warning Failed 15s kubelet Error: ImagePullBackOff Warning BackOff 10s (x2 over 11s) kubelet Back-off restarting failed container

    Init контейнеры & Volumes

Добавим в наш pod init container, генерирующий страницу index.html Для того, чтобы файлы, созданные в init контейнере, были доступны основному контейнеру в pod нам понадобится использовать volume типа emptyDir.

... volumeMounts: - name: app mountPath: /app initContainers:

    name: init-web image: busybox:1.34.1 imagePullPolicy: IfNotPresent volumeMounts:
        name: app mountPath: /app command: ['sh', '-c', 'wget -O- https://tinyurl.com/otus-k8s-intro | sh'] volumes:
        name: app emptyDir: {}

Перезапускам pod

kubectl delete -f web-pod.yaml

pod "web" deleted

kubectl apply -f web-pod.yaml

pod/web created

kubectl get pods -w

NAME READY STATUS RESTARTS AGE web 0/1 PodInitializing 0 3s web 0/1 Running 0 8s web 0/1 Running 0 10s web 1/1 Running 0 11s

Проверка работы приложения

kubectl port-forward --address 0.0.0.0 pod/web 8000:8000

Проверка работы приложения 6. Hipster Shop

Начнем с микросервиса frontend. Его исходный код доступен по адресу https://github.com/GoogleCloudPlatform/microservices-demo/tree/master/src/frontend Склонируем и соберем собственный образ для frontend (используя готовый Dockerfile)

cd ../../microservices-demo/src/frontend docker build -t lookus84/hipster-frontend:0.2 . docker push lookus84/hipster-frontend:0.2

Запустим через ad-hoc режим:

kubectl run frontend --image lookus84/hipster-frontend:0.2 --restart=Never

Один из распространенных кейсов использования ad-hoc режима - генерация манифестов средствами kubectl:

kubectl run frontend --image lookus84/hipster-frontend:0.2 --restart=Never --dry-run=client -o yaml > frontend-pod.yaml

Hipster Shop | Задание со star Выяснение причины, по которой pod frontend находится в статусе Error

kubectl get pods

NAME READY STATUS RESTARTS AGE frontend 0/1 Error 0 3m20s web 1/1 Running 0 30m

kubectl logs frontend lookus@ubuntu20:~/otus/12-2022/Lookus84_platform/kubernetes-intro$ (kubernetes-intro)kubectl logs frontend panic: environment variable "PRODUCT_CATALOG_SERVICE_ADDR" not set

goroutine 1 [running]: main.mustMapEnv(0xc000284c60, 0xb03c4a, 0x1c) /go/src/github.com/GoogleCloudPlatform/microservices-demo/src/frontend/main.go:248 +0x10e main.main() /go/src/github.com/GoogleCloudPlatform/microservices-demo/src/frontend/main.go:106 +0x3e9

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

kubectl delete pod frontend kubectl apply -f frontend-pod-healthy.yaml kubectl get pods

NAME READY STATUS RESTARTS AGE frontend 1/1 Running 0 70s web 1/1 Running 0 39m

--------------------------------------------------------------------------------------------------------

Лекция №4: Сетевое взаимодействие Pod,взаимодействие Pod,сервисысервисы // ДЗ

Добавление проверок Pod

Откройте файл с описанием Pod из предыдущего ДЗ ( kubernetes- intro/web-pod.yml ) Добавьте в описание пода readinessProbe (можно добавлять его сразу после указания образа контейнера): ... spec: containers:

    name: web image: thatsme/web:1.2

--- BEGIN ---

readinessProbe: # Добавим проверку готовности httpGet: # веб-сервера

Запустите наш под командой kubectl apply -f web-pod.yml Теперь выполните команду kubectl get pod/web и убедитесь, что под перешел в состояние Running $ kubectl apply -f web-pod.yml pod/web created $ kubectl get pod/web NAME READY STATUS RESTARTS AGE web 0/1 Running 0 5m47s

Из листинга выше видно, что проверка готовности контейнера завершается неудачно. Это неудивительно - веб-сервер в контейнере слушает порт 8000 (по условиям первого ДЗ). Пока мы не будем исправлять эту ошибку, а добавим другой вид проверок: livenessProbe . Самостоятельно добавьте в манифест проверку состояния веб-сервера. Например, так: Запустите Pod с новой конфигурацией

Вопрос для самопроверки: . Почему следующая конфигурация валидна, но не имеет смысла? . Бывают ли ситуации, когда она все-таки имеет смысл?

Создание Deployment Скорее всего, в процессе изменения конфигурации Pod, вы столкнулись с неудобством обновления конфигурации пода через kubectl (и уже нашли ключик --force ). В любом случае, для управления несколькими однотипными подами такой способ не очень подходит. Создадим Deployment, который упростит обновление конфигурации пода и управление группами подов. Для начала, создайте новую папку kubernetes-networks в вашем репозитории В этой папке создайте новый файл web-deploy.yaml

Начнем заполнять наш файл-манифест для Deployment: Теперь в блок template: можно перенести конфигурацию Pod из web-pod.yaml , убрав строки apiVersion: v1 и kind: Pod . exclamationБудьте внимательны с отступами! apiVersion: apps/v1 kind: Deployment metadata: name: web # Название нашего объекта Deployment spec: replicas: 1 # Начнем с одного пода selector: # Укажем, какие поды относятся к нашему Deploy

Для начала удалим старый под из кластера: kubectl delete pod/web --grace-period=0 --force

И приступим к деплою: cd kubernetes-networks/ kubectl apply -f web-deploy.yaml

Посмотрим, что получилось: kubectl describe deployment web

Поскольку мы не исправили ReadinessProbe , то поды, входящие в наш Deployment, не переходят в состояние Ready из-за неуспешной проверки На предыдущем слайде видно, что это влияет на состояние всего Deployment (строчка Available в блоке Conditions) Теперь самое время исправить ошибку! Поменяйте в файле web-deploy.yaml следующие параметры: Увеличьте число реплик до 3 ( replicas: 3 ) Исправьте порт в readinessProbe на порт 8000 Примените изменения командой kubectl apply -f web-deploy.yaml

Теперь проверьте состояние нашего Deployment командой kubectl describe deploy/web Убедитесь, что условия (Conditions) Available и Progressing выполняются (в столбце Status значение true ) Добавьте в манифест ( web-deploy.yaml ) блок strategy (можно сразу перед шаблоном пода)

Name: web Namespace: default CreationTimestamp: Sun, 22 Jan 2023 11:14:14 +0300 Labels: Annotations: deployment.kubernetes.io/revision: 1 Selector: app=web Replicas: 3 desired | 3 updated | 3 total | 3 available | 0 unavailable StrategyType: RollingUpdate MinReadySeconds: 0 RollingUpdateStrategy: 25% max unavailable, 25% max surge Pod Template: Labels: app=web Init Containers: html-gen: Image: busybox:musl Port: Host Port: Command: sh -c wget -O- https://bit.ly/otus-k8s-index-gen | sh Environment: Mounts: /app from app (rw) Containers: web: Image: lookus84/otustestui:0.2 Port: Host Port: Liveness: tcp-socket :8000 delay=0s timeout=1s period=10s #success=1 #failure=3 Readiness: http-get http://:8000/index.html delay=0s timeout=1s period=10s #success=1 #failure=3 Environment: Mounts: /app from app (rw) Volumes: app: Type: EmptyDir (a temporary directory that shares a pod's lifetime) Medium:
SizeLimit: Conditions: Type Status Reason

Available True MinimumReplicasAvailable Progressing True NewReplicaSetAvailable OldReplicaSets: NewReplicaSet: web-6bbbc7d4f5 (3/3 replicas created) Events: Type Reason Age From Message

Normal ScalingReplicaSet 17m deployment-controller Scaled up replica set web-6bbbc7d4f5 to 3

Попробуйте разные варианты деплоя с крайними значениями maxSurge и maxUnavailable (оба 0, оба 100%, 0 и 100%) За процессом можно понаблюдать с помощью kubectl get events --watch или установить и использовать его ( kubespy trace deploy )

---выполнено в рамках предыдущего ДЗ в задании со *

Создание Service

Для того, чтобы наше приложение было доступно внутри кластера (а тем более - снаружи), нам потребуется объект типа Service . Начнем с самого распространенного типа сервисов - ClusterIP . ClusterIP выделяет для каждого сервиса IP-адрес из особого диапазона (этот адрес виртуален и даже не настраивается на сетевых интерфейсах) Когда под внутри кластера пытается подключиться к виртуальному IP- адресу сервиса, то нода, где запущен под меняет адрес получателя в сетевых пакетах на настоящий адрес пода. Нигде в сети, за пределами ноды, виртуальный ClusterIP не встречается.

ClusterIP удобны в тех случаях, когда: Нам не надо подключаться к конкретному поду сервиса Нас устраивается случайное расределение подключений между подами Нам нужна стабильная точка подключения к сервису, независимая от подов, нод и DNS-имен Например: Подключения клиентов к кластеру БД (multi-read) или хранилищу Простейшая (не совсем, use IPVS, Luke) балансировка нагрузки внутри кластера

Итак, создадим манифест для нашего сервиса в папке kubernetes-networks . Файл web-svc-cip.yaml 

kubectl apply -f web-svc-cip.yaml 

service/web-svc-cip created

Проверим результат (отметьте назначенный CLUSTER-IP):

kubectl get services
NAME          TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE

kubernetes ClusterIP 10.96.0.1 443/TCP 12d web-svc-cip ClusterIP 10.99.234.248 80/TCP 83s

Подключимся к ВМ Minikube (команда minikube ssh и затем sudo -i ): 
minikube ssh

docker@minikube:~$

Сделайте curl http://<CLUSTER-IP>/index.html - работает!
oot@minikube:~# curl http://10.99.234.248/index.html

0....

Сделайте ping  - пинга нет
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

0     0 KUBE-SVC-JD5MR3NA4I4DYORP  tcp  --  *      *       0.0.0.0/0            10.96.0.10           /* kube-system/kube-dns:metrics cluster IP / tcp dpt:9153
0     0 KUBE-SVC-NPX46M4PTMTKRN6Y  tcp  --  *      *       0.0.0.0/0            10.96.0.1            / default/kubernetes:https cluster IP / tcp dpt:443
1    60 KUBE-SVC-6CZTMAROCN3AQODZ  tcp  --  *      *       0.0.0.0/0            10.99.234.248        / default/web-svc-cip cluster IP / tcp dpt:80
0     0 KUBE-SVC-TCOU7JCQXEZGVUNU  udp  --  *      *       0.0.0.0/0            10.96.0.10           / kube-system/kube-dns:dns cluster IP / udp dpt:53
0     0 KUBE-SVC-ERIFXISQEP7F7OF4  tcp  --  *      *       0.0.0.0/0            10.96.0.10           / kube-system/kube-dns:dns-tcp cluster IP / tcp dpt:53
887 53220 KUBE-NODEPORTS  all  --  *      *       0.0.0.0/0            0.0.0.0/0            / kubernetes service nodeports; NOTE: this must be the last rule in this chain */ ADDRTYPE match dst-type LOCAL

Chain KUBE-SVC-6CZTMAROCN3AQODZ (1 references)
pkts bytes target     prot opt in     out     source               destination

1    60 KUBE-MARK-MASQ  tcp  --  *      *      !10.244.0.0/16        10.99.234.248        /* default/web-svc-cip cluster IP / tcp dpt:80
0     0 KUBE-SEP-XP4XVLODH7BOHN5Q  all  --  *      *       0.0.0.0/0            0.0.0.0/0            / default/web-svc-cip -> 172.17.0.2:8000 / statistic mode random probability 0.25000000000
0     0 KUBE-SEP-Z6QHC4C2JAQDF7MX  all  --  *      *       0.0.0.0/0            0.0.0.0/0            / default/web-svc-cip -> 172.17.0.4:8000 / statistic mode random probability 0.33333333349
0     0 KUBE-SEP-C5Q7WHV7ALQOOLAZ  all  --  *      *       0.0.0.0/0            0.0.0.0/0            / default/web-svc-cip -> 172.17.0.5:8000 / statistic mode random probability 0.50000000000
1    60 KUBE-SEP-SLOPQOZW34M3DWKM  all  --  *      *       0.0.0.0/0            0.0.0.0/0            / default/web-svc-cip -> 172.17.0.6:8000 */

Нужное правило находится в цепочке KUBE-SERVICES

Затем мы переходим в цепочку KUBE-SVC-..... - здесь находятся правила "балансировки" между цепочками KUBE-SEP-.....
SVC - очевидно Service

В цепочках KUBE-SEP-..... находятся конкретные правила
перенаправления трафика (через DNAT)
SEP - Service Endpoint

hain KUBE-SEP-Z6QHC4C2JAQDF7MX (1 references)
pkts bytes target     prot opt in     out     source               destination

0     0 KUBE-MARK-MASQ  all  --  *      *       172.17.0.4           0.0.0.0/0            /* default/web-svc-cip /
0     0 DNAT       tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            / default/web-svc-cip */ tcp to:172.17.0.4:8000
...........................

Подробное описание можно почитать https://msazure.club/kubernetes-services-and-iptables/ или перейти на IPVS, там чуть понятнее ))


Итак, с версии 1.0.0 Minikube поддерживает работу kube-proxy в режиме IPVS. Попробуем включить его "наживую".

Включим IPVS для kube-proxy , исправив ConfigMap (конфигурация Pod, хранящаяся в кластере)
Выполните команду kubectl --namespace kube-system edit configmap/kube-proxy

Теперь найдите в файле конфигурации kube-proxy строку mipvs -> mode: ""

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

  strictARP: true
kind: KubeProxyConfiguration
metricsBindAddress: 0.0.0.0:10249
mode: "ipvs"

Теперь удалим Pod с kube-proxy , чтобы применить новую конфигурацию (он входит в DaemonSet и будет запущен автоматически) kubectl --namespace kube-system delete pod --selector='k8s-app=kube-proxy'

(kubernetes-networks)kubectl --namespace kube-system delete pod --selector='k8s-app=kube-proxy'


pod "kube-proxy-cb4rt" deleted

Описание работы и настройки IPVS https://github.com/kubernetes/kubernetes/blob/master/pkg/proxy/ipvs/README.md . Причины включения strictARP описаны тут
metallb/metallb#153

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
root@minikube:# iptables-restore /tmp/iptables.cleanup
root@minikube:#

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


Работа с LoadBalancer и IngressIngress

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


Создание Ingress

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
service/ingress-nginx-controller             NodePort    10.102.132.32            80:30644/TCP,443:32115/TCP   97s
service/ingress-nginx-controller-admission   ClusterIP   10.103.129.156           443/TCP                      97s

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
ingress-nginx-controller             NodePort       10.102.132.32             80:30644/TCP,443:32115/TCP   9m39s
ingress-nginx-controller-admission   ClusterIP      10.103.129.156            443/TCP                      9m39s

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
kubernetes    ClusterIP      10.96.0.1               443/TCP        160m
web-svc       ClusterIP      None                    80/TCP         9s
web-svc-cip   LoadBalancer   10.109.20.86   172.17.255.1   80:30843/TCP   144m

Теперь настроим наш ingress-прокси, создав манифест с ресурсом Ingress (файл назовите web-ingress.yaml ):

Примените манифест и проверьте, что корректно заполнены Address и Backends
kubectl describe ingress/web

(kubernetes-networks)kubectl describe ingress/web
Name:             web
Labels:           
Namespace:        default
Address:

Default backend:  web-svc:80 (172.17.0.2:8000,172.17.0.3:8000,172.17.0.6:8000)
Rules:
Host        Path  Backends




          /web   web-svc:80 (172.17.0.2:8000,172.17.0.3:8000,172.17.0.6:8000)




Annotations:  nginx.ingress.kubernetes.io/rewrite-target: /
Events:       

kind delete cluster

=======================================================================================================================

Лекция 5. ДЗ: Volumes, Storages, StatefulSetStatefulSet

Подготовка В корне вашего репозитория создайте каталог kubernetes-volumes . Все файлы этого ДЗ помещайте в этот каталог.

Установка и запуск kind kind - инструмент для запуска Kuberenetes при помощи Docker контейнеров.

Запуск В этом ДЗ мы развернем StatefulSet c - локальным S3 хранилищем

Применение StatefulSet Закомитьте конфигурацию под именем minio-statefulset.yaml . kubectl может брать конфигурацию по HTTP: В результате применения конфигурации должно произойти следующее:

Запуститься под с MinIO (kubernetes-volumes)kubectl get pod NAME READY STATUS RESTARTS AGE minio-0 1/1 Running 0 5m51s

Создаться PVC (kubernetes-volumes)kubectl get pvc NAME STATUS VOLUME CAPACITY ACCESS MODES STORAGECLASS AGE data-minio-0 Bound pvc-92b38749-a57e-4708-bf69-7c64dfeeb29c 10Gi RWO standard 6m20s

Динамически создаться PV на этом PVC с помощью дефолотного StorageClass (kubernetes-volumes)kubectl get pv NAME CAPACITY ACCESS MODES RECLAIM POLICY STATUS CLAIM STORAGECLASS REASON AGE pvc-92b38749-a57e-4708-bf69-7c64dfeeb29c 10Gi RWO Delete Bound default/data-minio-0 standard 5m33s

Применение Headless Service Для того, чтобы наш StatefulSet был доступен изнутри кластера, создадим Headless Service Закомитьте конфигурацию под именем minio-headless-service.yaml

Проверка работы MinIO Проверить работу Minio можно с помощью консольного клиента . Также для проверки ресурсов k8s помогут команды: mc kubectl get statefulsets kubectl get pods kubectl get pvc kubectl get pv kubectl describe <resource_name>

Создадим внутри пода в директории, в которую смонтирован каталог файловой системы хоста, файл 1.txt

kubernetes-volumes)kubectl exec -it minio-0 -- /bin/sh @minio-0:/data$ touch 1.txt

Проверим, какой каталог смонтирован в под minio (kubernetes-volumes)kubectl get pv -o yaml apiVersion: v1 items:

    apiVersion: v1 kind: PersistentVolume metadata: annotations: pv.kubernetes.io/provisioned-by: rancher.io/local-path creationTimestamp: "2023-01-22T13:59:57Z" finalizers:
        kubernetes.io/pv-protection name: pvc-92b38749-a57e-4708-bf69-7c64dfeeb29c resourceVersion: "1052" uid: 56d5c9fd-c641-446c-9929-71bb5d49a1c2 spec: accessModes:
        ReadWriteOnce capacity: storage: 10Gi claimRef: apiVersion: v1 kind: PersistentVolumeClaim name: data-minio-0 namespace: default resourceVersion: "1034" uid: 92b38749-a57e-4708-bf69-7c64dfeeb29c hostPath: path: /var/local-path-provisioner/pvc-92b38749-a57e-4708-bf69-7c64dfeeb29c_default_data-minio-0 type: DirectoryOrCreate nodeAffinity: required: nodeSelectorTerms:
            matchExpressions:
                key: kubernetes.io/hostname operator: In values:
                    kind-control-plane persistentVolumeReclaimPolicy: Delete storageClassName: standard volumeMode: Filesystem status: phase: Bound kind: List metadata: resourceVersion: "" selfLink: ""

Посмотрим, создался ли этот файл внутри контейнера kind

docker ps CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES ae5aa00976a5 kindest/node:v1.25.3 "/usr/local/bin/entr…" 42 minutes ago Up 42 minutes 127.0.0.1:34559->6443/tcp kind-control-plane

(kubernetes-volumes)docker exec -it ae5aa00976a5 /bin/sh
cd /var/local-path-provisioner/pvc-92b38749-a57e-4708-bf69-7c64dfeeb29c_default_data-minio-0
ls -la

total 12 drwxrwxrwx 3 root root 4096 Jan 22 14:11 . drwxr-xr-x 3 root root 4096 Jan 22 13:59 .. drwxr-xr-x 5 root root 4096 Jan 22 14:00 .minio.sys -rw-r--r-- 1 root root 0 Jan 22 14:11 1.txt

Задание со *.

Создадим секрет (kubernetes-volumes)kubectl apply -f miniosecret.yaml secret/mysecret created

(kubernetes-volumes)kubectl get secret NAME TYPE DATA AGE mysecret Opaque 2 3m25s
подключим его в стейтфуллсет

    image: minio/minio:RELEASE.2019-07-10T00-34-56Z
    envFrom:
    - secretRef:
        name: mysecret

(kubernetes-volumes)kubectl apply -f minio-statefulset-with-secret.yaml statefulset.apps/minio configured

посмотрим env в поде (kubernetes-volumes)kubectl exec -it minio-0 -- /bin/sh / # env KUBERNETES_SERVICE_PORT=443 KUBERNETES_PORT=tcp://10.96.0.1:443 HOSTNAME=minio-0 MINIO_ACCESS_KEY_FILE=access_key SHLVL=1 HOME=/root MINIO_ACCESS_KEY=minio MINIO_UPDATE=off TERM=xterm KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1 PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin KUBERNETES_PORT_443_TCP_PORT=443 KUBERNETES_PORT_443_TCP_PROTO=tcp MINIO_SECRET_KEY_FILE=secret_key KUBERNETES_SERVICE_PORT_HTTPS=443 KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443 MINIO_SECRET_KEY=minio123 KUBERNETES_SERVICE_HOST=10.96.0.1 PWD=/

Видим, что данные из секрета появились.

kind delete cluster


======================================================================================================================


Лекция №7: Шаблонизация манифестов. Helm и его аналоги (Jsonnet, Kustomize) // ДЗ

    Шаблонизация манифестов Kubernetes

kubernetes-templating
Задание:

Шаблонизация манифестов приложения, использование Helm, kustomize, helmfile, jsonnet. Установка community Helm charts Цель: В данном дз студенты научатся использовать менеджер Helm3, научатся писать свои helm манифесты. Научатся управлять релизами при помощи helm. Описание/Пошаговая инструкция выполнения домашнего задания: Все действия описаны в методическом указании.

Критерии оценки:

0 б. - задание не выполнено 1 б. - задание выполнено 2 б. - выполнены все дополнительные задания
Выполнено:

----------------------------------------------
1. Intro
-----------------------------------------------
Поднимем кластер k8s

terraform init
terraform apply --auto-approve

kubernetes-templating)yc managed-kubernetes cluster list
+----------------------+----------+---------------------+---------+---------+------------------------+---------------------+
|          ID          |   NAME   |     CREATED AT      | HEALTH  | STATUS  |   EXTERNAL ENDPOINT    |  INTERNAL ENDPOINT  |
+----------------------+----------+---------------------+---------+---------+------------------------+---------------------+
| cat12c5iknt2afmad8s4 | k8s-otus | 2023-02-05 11:16:19 | HEALTHY | RUNNING | https://158.160.56.207 | https://10.128.0.22 |
+----------------------+----------+---------------------+---------+---------+------------------------+---------------------+



(kubernetes-templating)kubectl cluster-info
Kubernetes control plane is running at https://158.160.56.207
CoreDNS is running at https://158.160.56.207/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy



(kubernetes-templating)snap install helm --classic
helm 3.7.0 from Snapcrafters installed



(kubernetes-templating)helm version
version.BuildInfo{Version:"v3.7.0", GitCommit:"eeac83883cb4014fe60267ec6373570374ce770b", GitTreeState:"clean", GoVersion:"go1.16.8"}

------- autocomplete zsh---------------
(kubernetes-templating)helm completion bash > ~/.helmrc; echo "source ~/.helmrc" >> ~/.bashrc
lookus@ubuntu20:~/otus/12-2022/Lookus84_platform/kubernetes-templating$ (kubernetes-templating)source ~/.bashrc
lookus@ubuntu20:~/otus/12-2022/Lookus84_platform/kubernetes-templating$ (kubernetes-templating)helm 
completion  (generate autocompletion scripts for the specified shell)                           pull        (download a chart from a repository and (optionally) unpack it in local directory)
create      (create a new chart with the given name)                                            repo        (add, list, remove, update, and index chart repositories)
dependency  (manage a chart's dependencies)                                                     rollback    (roll back a release to a previous revision)
env         (helm client environment information)                                               search      (search for a keyword in charts)
get         (download extended information of a named release)                                  show        (show information of a chart)
help        (Help about any command)                                                            status      (display the status of the named release)
history     (fetch release history)                                                             template    (locally render templates)
install     (install a chart)                                                                   test        (run tests for a release)
lint        (examine a chart for possible issues)                                               uninstall   (uninstall a release)
list        (list releases)                                                                     upgrade     (upgrade a release)
package     (package a chart directory into a chart archive)                                    verify      (verify that a chart at the given path has been signed and is valid)
plugin      (install, list, or uninstall Helm plugins)                                          version     (print the client version information)



Памятка по использованию Helm

    Создание release:

$ helm install <chart_name> --name=<release_name> --namespace=<namespace>
$ kubectl get secrets -n <namespace> | grep <release_name>
sh.helm.release.v1.<release_name>.v1 helm.sh/release.v1 1 115m

    Обновление release:

$ helm upgrade <release_name> <chart_name> --namespace=<namespace>
$ kubectl get secrets -n <namespace> | grep <release_name>
sh.helm.release.v1.<release_name>.v1 helm.sh/release.v1 1 115m
sh.helm.release.v1.<release_name>.v2 helm.sh/release.v1 1 56m

    Создание или обновление release:

$ helm upgrade --install <release_name> <chart_name> --namespace=<namespace>
$ kubectl get secrets -n <namespace> | grep <release_name>
sh.helm.release.v1.<release_name>.v1 helm.sh/release.v1 1 115m
sh.helm.release.v1.<release_name>.v2 helm.sh/release.v1 1 56m
sh.helm.release.v1.<release_name>.v3 helm.sh/release.v1 1 5s


Add helm repo

Добавим репозиторий helm/stable. По умолчанию в Helm 3 не установлен репозиторий stable.

helm repo add stable https://charts.helm.sh/stable

"stable" has been added to your repositories

helm repo list

stable                  https://charts.helm.sh/stable     


---------------------------------------------
2. Nginx-ingress
---------------------------------------------
kubectl create ns nginx-ingress

helm upgrade --install nginx-ingress stable/nginx-ingress --wait \
 --namespace=nginx-ingress \
 --version=1.41.3

*******************************************************************************************************
* DEPRECATED, please use https://github.com/kubernetes/ingress-nginx/tree/master/charts/ingress-nginx *
*******************************************************************************************************


(kubernetes-templating)helm list --all-namespaces
NAME         	NAMESPACE    	REVISION	UPDATED                                	STATUS	CHART               	APP VERSION
nginx-ingress	nginx-ingress	2       	2023-02-05 15:00:14.409056121 +0300 +03	failed	nginx-ingress-1.41.3	v0.34.1  

(kubernetes-templating)kubectl get po -n nginx-ingress
NAME                                             READY   STATUS             RESTARTS      AGE
nginx-ingress-controller-65845897bc-6kqrc        0/1     CrashLoopBackOff   6 (51s ago)   8m26s
nginx-ingress-default-backend-5974cfcb46-46vln   1/1     Running            0             8m26s


(kubernetes-templating)kubectl describe pod nginx-ingress-controller-65845897bc-6kqrc -n nginx-ingress

Events:
  Type     Reason     Age                     From               Message
  ----     ------     ----                    ----               -------
  Normal   Scheduled  9m21s                   default-scheduler  Successfully assigned nginx-ingress/nginx-ingress-controller-65845897bc-6kqrc to cl1vsvuvv574pibgkrir-ikaz
  Normal   Pulling    9m20s                   kubelet            Pulling image "us.gcr.io/k8s-artifacts-prod/ingress-nginx/controller:v0.34.1"
  Normal   Pulled     9m10s                   kubelet            Successfully pulled image "us.gcr.io/k8s-artifacts-prod/ingress-nginx/controller:v0.34.1" in 10.177563616s
  Normal   Created    8m18s (x2 over 9m10s)   kubelet            Created container nginx-ingress-controller
  Normal   Started    8m18s (x2 over 9m10s)   kubelet            Started container nginx-ingress-controller
  Normal   Pulled     8m18s                   kubelet            Container image "us.gcr.io/k8s-artifacts-prod/ingress-nginx/controller:v0.34.1" already present on machine
  Warning  Unhealthy  7m41s (x6 over 8m51s)   kubelet            Liveness probe failed: HTTP probe failed with statuscode: 500
  Normal   Killing    7m41s (x2 over 8m31s)   kubelet            Container nginx-ingress-controller failed liveness probe, will be restarted
  Warning  Unhealthy  4m11s (x30 over 8m51s)  kubelet            Readiness probe failed: HTTP probe failed with statuscode: 500


(kubernetes-templating)helm uninstall nginx-ingress --namespace=nginx-ingress
release "nginx-ingress" uninstalled


(kubernetes-templating)helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
"ingress-nginx" has been added to your repositories


(kubernetes-templating)helm repo update ingress-nginx
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "ingress-nginx" chart repository
Update Complete. ⎈Happy Helming!⎈


(kubernetes-templating)helm upgrade --install nginx-ingress-release ingress-nginx/ingress-nginx --namespace=nginx-ingress --version="4.4.2"
Release "nginx-ingress-release" does not exist. Installing it now.
NAME: nginx-ingress-release
LAST DEPLOYED: Sun Feb  5 15:09:24 2023
NAMESPACE: nginx-ingress
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The ingress-nginx controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace nginx-ingress get services -o wide -w nginx-ingress-release-ingress-nginx-controller'

An example Ingress that makes use of the controller:
  apiVersion: networking.k8s.io/v1
  kind: Ingress
  metadata:
    name: example
    namespace: foo
  spec:
    ingressClassName: nginx
    rules:
      - host: www.example.com
        http:
          paths:
            - pathType: Prefix
              backend:
                service:
                  name: exampleService
                  port:
                    number: 80
              path: /
    # This section is only required if TLS is to be enabled for the Ingress
    tls:
      - hosts:
        - www.example.com
        secretName: example-tls

If TLS is enabled for the Ingress, a Secret containing the certificate and key must also be provided:

  apiVersion: v1
  kind: Secret
  metadata:
    name: example-tls
    namespace: foo
  data:
    tls.crt: <base64 encoded cert>
    tls.key: <base64 encoded key>
  type: kubernetes.io/tls



(kubernetes-templating)kubectl get services -n nginx-ingress
NAME                                                       TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)                      AGE
nginx-ingress-release-ingress-nginx-controller             LoadBalancer   10.96.173.246   158.160.42.166   80:32601/TCP,443:31655/TCP   2m55s
nginx-ingress-release-ingress-nginx-controller-admission   ClusterIP      10.96.130.68    <none>           443/TCP                      2m55s


(kubernetes-templating)helm list --all-namespaces
NAME                 	NAMESPACE    	REVISION	UPDATED                                	STATUS  	CHART              	APP VERSION
nginx-ingress-release	nginx-ingress	1       	2023-02-05 15:09:24.113690199 +0300 +03	deployed	ingress-nginx-4.4.2	1.5.1   



(kubernetes-templating)helm uninstall nginx-ingress-release -n nginx-ingress
release "nginx-ingress-release" uninstalled

------------------------------------------------------------
3. Cert-manager
------------------------------------------------------------
helm repo add jetstack https://charts.jetstack.io
(kubernetes-templating)helm repo update jetstack
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "jetstack" chart repository
Update Complete. ⎈Happy Helming!⎈



#Install CustomResourceDefinitions
(kubernetes-templating)kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.11.0/cert-manager.crds.yaml
customresourcedefinition.apiextensions.k8s.io/clusterissuers.cert-manager.io created
customresourcedefinition.apiextensions.k8s.io/challenges.acme.cert-manager.io created
customresourcedefinition.apiextensions.k8s.io/certificaterequests.cert-manager.io created
customresourcedefinition.apiextensions.k8s.io/issuers.cert-manager.io created
customresourcedefinition.apiextensions.k8s.io/certificates.cert-manager.io created
customresourcedefinition.apiextensions.k8s.io/orders.acme.cert-manager.io created



kubernetes-templating)helm install \
cert-manager jetstack/cert-manager \
 --namespace cert-manager \
--create-namespace \
--version v1.11.0
NAME: cert-manager
LAST DEPLOYED: Sun Feb  5 15:28:36 2023
NAMESPACE: cert-manager
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
cert-manager v1.11.0 has been deployed successfully!

In order to begin issuing certificates, you will need to set up a ClusterIssuer
or Issuer resource (for example, by creating a 'letsencrypt-staging' issuer).

More information on the different types of issuers and how to configure them
can be found in our documentation:

https://cert-manager.io/docs/configuration/

For information on how to configure cert-manager to automatically provision
Certificates for Ingress resources, take a look at the `ingress-shim`
documentation:

https://cert-manager.io/docs/usage/ingress/


(kubernetes-templating)helm list --all-namespaces
NAME        	NAMESPACE   	REVISION	UPDATED                                	STATUS  	CHART               	APP VERSION
cert-manager	cert-manager	1       	2023-02-05 15:28:36.263234076 +0300 +03	deployed	cert-manager-v1.11.0	v1.11.0



--------------------------------------------------------------------
4. chartmuseum
--------------------------------------------------------------------
yc vpc address list   --  сетевые адреса публичных объектов яндекс клауд, ноды, ингресс....



kubernetes-templating)yc vpc address list
+----------------------+------+----------------+----------+------+
|          ID          | NAME |    ADDRESS     | RESERVED | USED |
+----------------------+------+----------------+----------+------+
| e9b37jqv2qmncb8m18cq |      | 130.193.36.248 | false    | true |
| e9bb8kli2mu5thgdrljp |      | 158.160.39.186 | false    | true |
| e9bqu92la7dviociv5u9 |      | 158.160.32.99  | false    | true |
+----------------------+------+----------------+----------+------+



(kubernetes-templating)kubectl --namespace nginx-ingress get services -o wide
NAME                                                       TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-release-ingress-nginx-controller             LoadBalancer   10.96.245.28   158.160.32.99   80:32415/TCP,443:30980/TCP   88m   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress-release,app.kubernetes.io/name=ingress-nginx
nginx-ingress-release-ingress-nginx-controller-admission   ClusterIP      10.96.162.97   <none>          443/TCP                      88m   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress-release,app.kubernetes.io/name=ingress-nginx



Создадим файл values.yaml для chartmuseum

---
ingress:
  enabled: true
  pathType: "ImplementationSpecific"
  annotations:
    kubernetes.io/ingress.class: nginx
    kubernetes.io/tls-acme: "true"
#    ingress.kubernetes.io/ssl-redirect: "true"
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
    cert-manager.io/acme-challenge-type: http01
  hosts:
    - name: chartmuseum.158.160.32.99.sslip.io
      path: /
      tls: true
      tlsSecret: chartmuseum.158.160.32.99.sslip.io
  ingressClassName: nginx
env:
  open:
    DISABLE_API: false


Запустим установку chartmuseum

kubectl create ns chartmuseum
kubectl apply -f cert-manager/acme-issuer.yaml

helm repo add chartmuseum https://chartmuseum.github.io/charts
helm repo update chartmuseum

#helm upgrade --install chartmuseum-release stable/chartmuseum --wait \
# --namespace=chartmuseum \
# --version=2.13.2 \
# -f chartmuseum/values.yaml
helm upgrade --install chartmuseum-release chartmuseum/chartmuseum  --wait \
 --namespace=chartmuseum \
  --version 3.1.0 \
  -f chartmuseum/values.yaml

Проверим, что release chartmuseum установился:


(kubernetes-templating)helm ls -n chartmuseum
NAME               	NAMESPACE  	REVISION	UPDATED                                	STATUS  	CHART            	APP VERSION
chartmuseum-release	chartmuseum	1       	2023-02-05 17:53:24.964247239 +0300 +03	deployed	chartmuseum-3.1.0	0.13.1    




Helm 3 хранит информацию в secrets

kubectl get secrets -n chartmuseum

(kubernetes-templating)kubectl get secrets -n chartmuseum
NAME                                        TYPE                                  DATA   AGE
chartmuseum-release                         Opaque                                0      3m2s
chartmuseum.1158.160.32.99.sslip.io         kubernetes.io/tls                     2      2m34s
default-token-g59w7                         kubernetes.io/service-account-token   3      3m7s
sh.helm.release.v1.chartmuseum-release.v1   helm.sh/release.v1                    1      3m2s


Проверим 

Chartmuseum доступен по URL https://chartmuseum.158.160.32.99.sslip.io/
имеет валидный сертификат.


==================================================
5. harbor
==================================================
Установка

helm repo add harbor https://helm.goharbor.io
helm repo update harbor
kubectl create ns harbor

Правим harbor/values.yaml в части 'ingress'

expose:
  type: ingress
  tls:
    enabled: true
    certSource: secret
    secret:
      secretName: harbor-ingress-tls
  ingress:
    hosts:
      core: harbor.158.160.47.10.sslip.io
    controller: nginx
    annotations:
      kubernetes.io/tls-acme: "true"
      cert-manager.io/cluster-issuer: letsencrypt-prod
      cert-manage.io/acme-challenge-type: http01
      kubernetes.io/ingress.class: nginx
externalURL: https://harbor.158.160.47.10.sslip.io/

notary:
  enabled: false



(kubernetes-templating)helm search repo harbor
NAME         	CHART VERSION	APP VERSION	DESCRIPTION                                       
harbor/harbor	1.11.0       	2.7.0      	An open source trusted cloud native registry th...



(kubernetes-templating)helm upgrade --install harbor harbor/harbor --wait --namespace=harbor --version=1.11.0 -f ./harbor/values.yaml
Release "harbor" does not exist. Installing it now.
NAME: harbor
LAST DEPLOYED: Sun Feb  5 18:27:08 2023
NAMESPACE: harbor
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Please wait for several minutes for Harbor deployment to complete.
Then you should be able to visit the Harbor portal at https://harbor.158.160.32.99.sslip.io/
For more details, please visit https://github.com/goharbor/harbor



lookus@ubuntu20:~/otus/12-2022/Lookus84_platform/kubernetes-templating$ (kubernetes-templating)helm ls -n harbor
NAME  	NAMESPACE	REVISION	UPDATED                                	STATUS  	CHART        	APP VERSION
harbor	harbor   	1       	2023-02-05 18:27:08.474920753 +0300 +03	deployed	harbor-1.11.0	2.7.0      



kubernetes-templating)kubectl get secrets -n harbor -l owner=helm
NAME                           TYPE                 DATA   AGE
sh.helm.release.v1.harbor.v1   helm.sh/release.v1   1      4m15s



Проверяем установку 

https://harbor.158.160.32.99.sslip.io/harbor/projects
сертификат валиден.


Задание со *

(kubernetes-templating)wget https://github.com/helmfile/helmfile/releases/download/v0.150.0/helmfile_0.150.0_linux_amd64.tar.gz
(kubernetes-templating)sudo tar xzvf helmfile_0.150.0_linux_amd64.tar.gz -C /bin
LICENSE
README.md
helmfile




(kubernetes-templating)sudo chmod +x /bin/helmfile

(kubernetes-templating)helm plugin install https://github.com/databus23/helm-diff
Downloading https://github.com/databus23/helm-diff/releases/latest/download/helm-diff-linux-amd64.tgz
Preparing to install into /home/lookus/.local/share/helm/plugins/helm-diff
helm-diff installed into /home/lookus/.local/share/helm/plugins/helm-diff/helm-diff

The Helm Diff Plugin

* Shows a diff explaining what a helm upgrade would change:



(kubernetes-templating)cd helmfile/


helmfile$ (kubernetes-templating)helmfile apply
Adding repo ingress-nginx https://kubernetes.github.io/ingress-nginx
"ingress-nginx" has been added to your repositories

Adding repo jetstack https://charts.jetstack.io
"jetstack" has been added to your repositories

Adding repo chartmuseum https://chartmuseum.github.io/charts
"chartmuseum" has been added to your repositories

Adding repo harbor https://helm.goharbor.io
"harbor" has been added to your repositories

Comparing release=nginx-ingress-release, chart=ingress-nginx/ingress-nginx
Comparing release=cert-manager, chart=jetstack/cert-manager
cert-manager, certificaterequests.cert-manager.io, CustomResourceDefinition (apiextensions.k8s.io) has been added:


===================================================
6. Создаем свой helm chart
===================================================


Мы рассмотрим третий вариант. Возьмем готовые манифесты и подготовим их к релизу на разные окружения.
Использовать будем демо-приложение , представляющее собой типичный набор микросервисов.
Стандартными средствами helm инициализируйте структуру директории с содержимым будущего helm chart


helm create hipster-shop

Мы будем создавать chart для приложения с нуля, поэтому удалим values.yaml и содержимое templates

rm ./hipster-shop/values.yaml
rm -rf ./hipster-shop/templates/*
wget https://raw.githubusercontent.com/express42/otus-platform-snippets/master/Module-04/05-Templating/manifests/all-hipster-shop.yaml \
-O ./hipster-shop/templates/all-hipster-shop.yaml

В целом, helm chart уже готов, можем попробовать установить его:

kubectl create ns hipster-shop
helm upgrade --install hipster-shop-release hipster-shop --namespace hipster-shop
helm ls -n hipster-shop


(kubernetes-templating)helm ls -n hipster-shop
NAME                	NAMESPACE   	REVISION	UPDATED                                	STATUS  	CHART             	APP VERSION
hipster-shop-release	hipster-shop	1       	2023-02-11 16:21:46.450005061 +0300 +03	deployed	hipster-shop-0.1.0	1.16.0     



kubectl get nodes -o wide



Сейчас наш helm chart hipster-shop совсем не похож на настоящий. При этом, все микросервисы устанавливаются из одного файла all-hipster-shop.yaml
Давайте исправим это и первым делом займемся микросервисом frontend. Скорее всего он разрабатывается отдельной командой, а исходный код хранится в отдельном репозитории.
Поэтому, было бы логично вынести все что связано с frontend в отдельный helm chart.
Создадим заготовку:
helm create kubernetes-templating/frontend


Аналогично чарту hipster-shop удалим файл values.yaml и файлы в директории templates , создаваемые по умолчанию.

rm -rf frontend/templates
rm -f frontend/values.yaml


Выделим из файла all-hipster-shop.yaml манифесты для установки микросервиса frontend. В директории templates чарта frontend создадим файлы:

    deployment.yaml - должен содержать соответствующую часть из файла all-hipster-shop.yaml
    service.yaml - должен содержать соответствующую часть из файла allhipster-shop.yaml
    ingress.yaml - создадим самостоятельно.

Переустановим 'hipster-shop'

helm upgrade --install hipster-shop-release hipster-shop --namespace hipster-shop

helm ls -n hipster-shop


Доступ к UI пропал и таких ресурсов больше нет

kubectl get svc -A | grep NodePort | wc -l 

0

Установим chart frontend в namespace hipster-shop и проверим что доступ к UI вновь появился:

helm upgrade --install frontend-release frontend --namespace hipster-shop

kubectl get svc -n hipster-shop | grep NodePort  

frontend                NodePort    10.96.211.214   <none>        80:30001/TCP   85s

kubectl get ingress -A

NAMESPACE      NAME       CLASS   HOSTS                         ADDRESS         PORTS     AGE
hipster-shop   frontend   nginx   shop.84.201.134.6.sslip.io   158.160.47.10   80, 443   30m



Создадим frontend/values.yaml, добавим .image.tag, изменим frontend/templates/deployment.yaml, перезапустим обновление чарта:

helm upgrade --install frontend-release frontend --namespace hipster-shop -f frontend/values.yaml

kubectl describe  pods -n hipster-shop -l app=frontend | grep -i image

Видим, что ничего не изменилось в части тэга образа.

    Image:          gcr.io/google-samples/microservices-demo/frontend:v0.1.3
    Image ID:       gcr.io/google-samples/microservices-demo/frontend@sha256:0c72f37ed9aac1e65bccafc0ce7675ab9d1b6a407cdcefb2b9a608eec83490d5

Аналогичным образом шаблонизируем следующие параметры frontend chart

    Количество реплик в deployment
    Port, targetPort и NodePort в service
    Опционально - тип сервиса. Ключ NodePort должен появиться в манифесте только если тип сервиса - NodePort
    Другие параметры, которые на наш взгляд стоит шаблонизировать




Проверяем шаблонизированные чарты:

helm template frontend  -f frontend/values.yaml

helm upgrade --install frontend-release frontend --namespace hipster-shop -f frontend/values.yaml \
  --dry-run

Включить созданный чарт frontend в зависимости нашего большого микросервисного приложения hipster-shop. Для начала, удалим release frontend из кластера:

helm delete frontend-release -n hipster-shop

Добавим chart frontend как зависимость в hipster-shop/Chart.yaml

dependencies:
  - name: frontend
    version: 0.1.0
    repository: "file://../frontend"
    
    
    
    
    В директории kubernetes-templating/hipster-shop/charts появился архив frontend-0.1.0.tgz содержащий chart frontend определенной версии и добавленный в chart hipster-shop как зависимость.
    

Обновим release hipster-shop и убедимся, что ресурсы frontend вновь созданы:   
helm upgrade hipster-shop-release -n hipster-shop hipster-shop


kubectl get all -A -l app=frontend
NAMESPACE      NAME                            READY   STATUS    RESTARTS   AGE
hipster-shop   pod/frontend-69c6ff75c7-wwtcv   1/1     Running   0          15m

NAMESPACE      NAME               TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
hipster-shop   service/frontend   NodePort   10.96.186.227   <none>        80:30001/TCP   15m

NAMESPACE      NAME                                  DESIRED   CURRENT   READY   AGE
hipster-shop   replicaset.apps/frontend-69c6ff75c7   1         1         1       15m



Осталось понять, как из CI-системы мы можем менять параметры helm chart, описанные в values.yaml. Для этого существует специальный ключ --set. Изменим NodePort для frontend в release, не меняя его в самом chart:

helm upgrade --install hipster-shop-release hipster-shop -n hipster-shop --set frontend.service.NodePort=31234

kubectl get svc -n hipster-shop -l app=frontend



kubectl get svc -n hipster-shop -l app=frontend
NAME       TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
frontend   NodePort   10.96.186.227   <none>        80:30001/TCP   18m





Создаем свой helm chart | Задание со star

Добавим чарт redis как зависимость в hipster-shop/Chart.yaml

- name: redis
  version: 17.6.0
  repository: https://charts.bitnami.com/bitnami

Обновим зависимости:

helm dep update hipster-shop

ll hipster-shop/charts


-rw-rw-r-- 1 lookus lookus  1780 фев  5 13:25 frontend-0.1.0.tgz
-rw-rw-r-- 1 lookus lookus 92518 фев  5 13:25 redis-17.6.0.tgz







7. Работа с helm-secrets | Необязательное задание

Разберемся как работает плагин helm-secrets. Для этого добавим в Helm chart секрет и научимся хранить его в зашифрованном виде.

(kubernetes-templating)helm plugin install https://github.com/jkroepke/helm-secrets --version v4.2.2
Installed plugin: secrets


Сгенерируем новый PGP ключ:

gpg --full-generate-key

Ответим на все вопросы. После этого проверим, что ключ появился:

gpg -k

gpg -k
gpg: checking the trustdb
gpg: marginals needed: 3  completes needed: 1  trust model: pgp
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
/home/lookus/.gnupg/pubring.kbx
-------------------------------
pub   rsa3072 2023-02-11 [SC]
      EDD8D890A29A740ED4E214DFFFA367A363791B45
uid           [ultimate] lookus (key for helm using) <lookus@gm.com>
sub   rsa3072 2023-02-11 [E]



Создадим новый файл secrets.yaml в директории ./frontend со следующим содержимым:

visibleKey: hiddenValue

И попробуем зашифровать его:


(kubernetes-templating)sops -e -i --pgp EDD8D890A29A740ED4E214DFFFA367A363791B45 ./frontend/secrets.yaml 
[PGP]	 WARN[0000] Deprecation Warning: GPG key fetching from a keyserver within sops will be removed in a future version of sops. See https://github.com/mozilla/sops/issues/727 for more information.



Проверим, что файл secrets.yaml изменился. Сейчас его содержание выглядим примерно так:

visibleKey: ENC[AES256_GCM,data:z/WbIWfXdxj8eto=,iv:+xeNpDSVQCJzybqqBvRt4YyY0f9U3tqhEmA08JM23Z4=,tag:WaKvDaEylz7wAF95v+3wQQ==,type:str]
sops:
    kms: []
    gcp_kms: []
    azure_kv: []
    hc_vault: []
    age: []
    lastmodified: "2023-02-11T15:21:15Z"
    mac: ENC[AES256_GCM,data:yDj6E+YNBMy5OdcoAiqFXDyhmOAUb6zsLiwVbEsoYDWJnoBRg8eOemEjZt/t9T7ACqCboVmo1cl5UBzI5G5hAeTudaNOOh3PMHodk6k21AqzriLpN9HlRXUsUSQxvt2sqITFZQkbTEgocrtbFLKQzyGdRarevemrdMZhkA6ytAE=,iv:F6fYLxNvNNDkKGNq1A4a9csEqa/6UcflToLH/0r9AO8=,tag:gj7b0+1zBHTdRrGykNkCXA==,type:str]
    pgp:
        - created_at: "2023-02-11T15:21:03Z"
          enc: |
            -----BEGIN PGP MESSAGE-----

            hQGMA/Ds2EQBrBRqAQwAix/3IZkocMjesOJwLgsphLtliRCXeD2jzZw/3y5e+QS9
            fsmnl69rREAoNMeTV08U++mjTnnn4ZDy42214lud4SAQonVEJLI7at9DHPmZI+OQ
            J5hymNQCKdSwTD/nVvU6knDD1BQ+5vZALbDuJsAyPc2xN+FOv8E/7jCm3zNxzGsJ
            KeyBMwD4iFuUVW3W/znPHf2IuWXy9EIYdUlm9+40jyhLKBYA2OfsAaSveHLSkrUT
            291cANeVUsghgcrP3INxnAtCHQ/XwgmtPyQScQHJeU3JuAK57XM8hSJjIdGaZbUv
            d4Q3ZlFYFe2nwWKE4/rGmdGAqbC7x0hU1uvm98IL2za4PZ1KOkIzAjI0FsPp5Rhv
            RMCxYTlffvBb2rDG83/xSWItQZNqKrv7gjqYDr+6rj1CJQ2Zh4NPAOFCWhQXWub3
            72+fXhG6YX+xLwswZ7ZkNv5K4jlUDhdYLSB8fEJCrdQF91abWyIquWKIXAz1gQqD
            vWFFBlPm/h3wIB3CgFrB0l4BijCyrIVflBQHSPhvYfqiCDv4u+W2PFbo4KZOsWBt
            bDvXgm3hTLOlmruEHi158OMN+UUrD0XW6oxJ+ZKehgluPq3zo0WnZccBtTL2FncT
            nQybbWAhuEixSo6hjBXO
            =+ktJ
            -----END PGP MESSAGE-----
          fp: EDD8D890A29A740ED4E214DFFFA367A363791B45
    unencrypted_suffix: _unencrypted
    version: 3.7.3
    
    
    
    В таком виде файл уже можно коммитить в Git, но для начала - научимся расшифровывать его. Можно использовать любой из инструментов:

# helm secrets
helm secrets decrypt ./frontend/secrets.yaml

(kubernetes-templating)helm secrets decrypt ./frontend/secrets.yaml
visibleKey: hiddenValue



(kubernetes-templating)sops -d ./frontend/secrets.yaml
visibleKey: hiddenValue



Создадим в директории ./frontend/templates еще один файл secret.yaml. Несмотря на похожее название его предназначение будет отличаться.

apiVersion: v1
kind: Secret
metadata:
  name: secret
type: Opaque
data:
  visibleKey: {{ .Values.visibleKey | b64enc | quote }}

Теперь, если мы передадим в helm файл secrets.yaml как values файл плагин helm-secrets поймет, что его надо расшифровать, а значение ключа visibleKey подставить в соответствующий шаблон секрета. Запустим установку:




(kubernetes-templating)helm secrets upgrade --install hipster-shop-release  ./frontend -n hipster-shop  -f ./frontend/values.yaml  -f ./frontend/secrets.yaml
[helm-secrets] Decrypt: ./frontend/secrets.yaml
Release "hipster-shop-release" has been upgraded. Happy Helming!
NAME: hipster-shop-release
LAST DEPLOYED: Sat Feb 11 18:27:58 2023
NAMESPACE: hipster-shop
STATUS: deployed
REVISION: 5
TEST SUITE: None

[helm-secrets] Removed: ./frontend/secrets.yaml.dec



kubernetes-templating)kubectl get secret secret -n hipster-shop -o yaml | grep visibleKey | awk '{print $2}' | base64 -d -
hiddenValue



Проверка

Поместим все получившиеся helm chart's в установленный harbor (https://harbor.158.160.47.10.sslip.io/) в публичный проект otus-kuber.

helm package frontend
helm package hipster-shop
helm plugin install https://github.com/chartmuseum/helm-push
helm registry login https://harbor.158.160.47.10.sslip.io/ u <user> -p <password>
helm repo add templating https://harbor.158.160.47.10.sslip.io/chartrepo/otus-kuber
helm cm-push -u <user> -p <password> templating/frontend-0.1.0.tgz templating 
helm cm-push -u <user> -p <password> templating/hipster-shop-0.1.0.tgz templating

bash ./repo.sh

helm search repo templating




8. Kubecfg

Вынесем манифесты описывающие service и deployment микросервисов paymentservice и shippingservice из файла all-hipster-shop.yaml в директорию ./kubecfg

tree -L 1 kubecfg

kubecfg
├── paymentservice-deployment.yaml
├── paymentservice-service.yaml
├── shippingservice-deployment.yaml
└── shippingservice-service.yaml

0 directories, 4 files

Обновим релиз

helm upgrade hipster-shop-release -n hipster-shop hipster-shop

Проверим, что микросервисы paymentservice и shippingservice исчезли из установки и магазин стал работать некорректно (при нажатии на кнопку Add to Cart)

kubectl get all -A -l app=paymentservice
kubectl get all -A -l app=shippingservice

No resources found
No resources found

Uh, oh!
Something has failed. Below are some details for debugging.
HTTP Status: 500 Internal Server Error

Установим kubecfg

wget https://github.com/vmware-archive/kubecfg/releases/download/v0.22.0/kubecfg-linux-amd64
install kubecfg-linux-amd64 ~/bin/kubecfg
rm -f kubecfg-linux-amd64
kubecfg version

kubecfg version: v0.22.0
jsonnet version: v0.17.0
client-go version: v0.0.0-master+$Format:%h$

Kubecfg предполагает хранение манифестов в файлах формата .jsonnet и их генерацию перед установкой. Пример такого файла можно найти в официальном репозитории Напишем по аналогии свой .jsonnet файл - services.jsonnet. Для начала в файле мы должны указать libsonnet библиотеку, которую будем использовать для генерации манифестов. В домашней работе воспользуемся готовой от от bitnami Импортируем ее:

local kube = import "https://github.com/bitnami-labs/kube-libsonnet/raw/52ba963ca44f7a4960aeae9ee0fbee44726e481f/kube.libsonnet";
...

Общая логика задачи следующая:

    Пишем общий для сервисов , включающий описание service и deployment
    Наследуемся от него, указывая параметры для конкретных сервисов: payment-shipping.jsonnet

    Рекомендуем не заглядывать в сниппеты в ссылках и попробовать самостоятельно разобраться с jsonnet В качестве подсказки можно использовать и готовый services.jsonnet , который должен выглядеть примерно следующим образом: services.jsonnet

Проверим, что манифесты генерируются корректно:

kubecfg show kubecfg/services.jsonnet

И установим их:

kubecfg update kubecfg/services.jsonnet --namespace hipster-shop

Проверим установку:

kubectl get all -A -l app=paymentservice
kubectl get all -A -l app=shippingservice


9. Kustomize | Самостоятельное задание

Отпилим еще один (любой) микросервис из all-hipster-shop.yaml и самостоятельно займитесь его kustomизацией. В минимальном варианте достаточно реализовать установку на два окружения - hipster-shop (namespace hipster-shop ) и hipster- shop-prod (namespace hipster-shop-prod ) из одних манифестов deployment и service Окружения должны отличаться:

    Набором labels во всех манифестах
    Префиксом названий ресурсов
    Image Tag, Memory Limits, Replicas

kubectl apply -k ./kustomize/overrides/dev
kubectl apply -k ./kustomize/overrides/prod


==================================================================================================
Лекция №8: Custom Resource Definitions. Operators // ДЗ

    Custom Resource Definitions. Operators

kubernetes-operators
Задание:

Описание собственного CRD, использование open-source операторов Цель:

В данном дз студенты разберутся что такое CRD и как их использовать. Создадут собственный Custom Resource Refinition и собственный Custom Recource. Так же студенты напишут свой собственный оператор для взаимодействия с Mysql сервером в рамках кластера kubernetes.

Описание/Пошаговая инструкция выполнения домашнего задания:

Все действия описаны в методическом указании.

Критерии оценки:

0 б. - задание не выполнено 1 б. - задание выполнено 2 б. - выполнены все дополнительные задания
Выполнено:
1. Подготовка

Запустим kubernetes кластер в minikube/создадим поддиректорию deploy

mkdir -p ./deploy
minikube start

2. Что должно быть в описании MySQL

Для создания pod с MySQL оператору понадобится знать:

    Какой образ с MySQL использовать
    Какую db создать
    Какой пароль задать для доступа к MySQL

3. CustomResource

Создадим CustomResource deploy/cr.yml со следующим содержимым:

apiVersion: otus.homework/v1
kind: MySQL
metadata:
  name: mysql-instance
spec:
  image: mysql:5.7
  database: otus-database
  password: # otuspassword  # Так делать не нужно, следует использовать secret
    valueFrom:
      secretKeyRef:
        name: mysql-secrets
        key: mysql-password
  storage_size: 1Gi
usless_data: "useless info"
---
apiVersion: v1
kind: Secret
metadata:
  name: mysql-secrets
type: Opaque
data:
  mysql-password: b3R1c3Bhc3N3b3Jk

echo -n otuspassword | base64

kubectl apply -f deploy/cr.yml

secret/mysql-secrets created
error: resource mapping not found for name: "mysql-instance" namespace: "" from "deploy/cr.yml": no matches for kind "MySQL" in version "otus.homework/v1"
ensure CRDs are installed first

4. CustomResourceDefinition

    CustomResourceDefinition - это ресурс для определения других ресурсов (далее CRD)

Создадим CRD deploy/crd.yml

apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: mysqls.otus.homework # имя CRD должно иметь формат plural.group
spec:
  scope: Namespaced     # Данный CRD будет работать в рамках namespace
  group: otus.homework  # Группа, отражается в поле apiVersion CR
  versions:             # Список версий
    - name: v1
      served: true      # Будет ли обслуживаться API-сервером данная версия
      storage: true     # Фиксирует версию описания, которая будет сохраняться в etcd
  names:                # различные форматы имени объекта CR
    kind: MySQL         # kind CR
    plural: mysqls
    singular: mysql
    shortNames:
      - ms

Создадим CRD:

kubectl apply -f deploy/crd.yml

The CustomResourceDefinition "mysqls.otus.homework" is invalid: spec.versions[0].schema.openAPIV3Schema: Required value: schemas are required

Определим schema.openAPIV3Schema

     schema:
        openAPIV3Schema:
          type: object
          properties:
            apiVersion:
              type: string # Тип данных поля ApiVersion
            kind:
              type: string # Тип данных поля kind
            metadata:
              type: object # Тип поля metadata
              properties: # Доступные параметры и их тип данных поля metadata (словарь)
                name:
                  type: string

kubectl apply -f deploy/crd.yml

customresourcedefinition.apiextensions.k8s.io/mysqls.otus.homework created

Создадим CR:

kubectl apply -f deploy/cr.yml

mysql.otus.homework/mysql-instance created

5. Взаимодействие с объектами CR CRD

kubectl get crd

NAME                   CREATED AT
mysqls.otus.homework   2023-03-21T13:34:29Z


kubectl get mysqls.otus.homework

NAME             AGE
mysql-instance   3m52s

kubectl describe mysqls.otus.homework mysql-instance

NName:         mysql-instance
Namespace:    default
Labels:       <none>
Annotations:  <none>
API Version:  otus.homework/v1
Kind:         MySQL
Metadata:
  Creation Timestamp:  2023-03-21T13:36:46Z
  Generation:          1
  Managed Fields:
    API Version:  otus.homework/v1
    Fields Type:  FieldsV1
    fieldsV1:
      f:metadata:
        f:annotations:
          .:
          f:kubectl.kubernetes.io/last-applied-configuration:
      f:spec:
        .:
        f:database:
        f:image:
        f:password:
        f:storage_size:
    Manager:         kubectl-client-side-apply
    Operation:       Update
    Time:            2023-03-21T13:36:46Z
  Resource Version:  5937
  UID:               24627edd-5bd1-412d-b8fa-a094088df2d6
Spec:
  Database:      otus-database
  Image:         mysql:5.7
  Password:      otuspassword
  storage_size:  1Gi
Events:          <none>

6. Validation

На данный момент мы никак не описали схему нашего CustomResource. Объекты типа mysql могут иметь абсолютно произвольные поля, нам бы хотелось этого избежать, для этого будем использовать validation. Для начала удалим CR mysql-instance:

kubectl delete mysqls.otus.homework mysql-instance

Добавим в спецификацию CRD ( spec ) параметры validation

  validation:
    openAPIV3Schema:
      type: object
      properties:
        apiVersion:
          type: string
        kind:
          type: string
        metadata:
          type: object
          properties:
            name:
              type: string
        spec:
          type: object
          properties:
            image: 
              type: string
            database:
              type: string
            password:
              type: string
            storage_size:
              type: string

Пробуем применить CRD и CR

kubectl apply -f deploy/crd.yml

customresourcedefinition.apiextensions.k8s.io/mysqls.otus.homework created

kubectl apply -f deploy/cr.yml

mysql.otus.homework/mysql-instance created

Эту строку мы убрали ранее, при первых попытках применения cr.yml из gist

usless_data: "useless info"

Задание по CRD:

Если сейчас из описания mysql убрать строчку из спецификации, то манифест будет принят API сервером. Для того, чтобы этого избежать, добавим описание обязательный полей в CustomResourceDefinition

    Примера в лекции нет, но все же есть в Extend the Kubernetes API with CustomResourceDefinitions

Вносим соответствующие правки и проверяем:

...
            spec:
              type: object
              required: ["image", "database", "password", "storage_size"]
...

kubectl delete -f deploy/crd.yml
kubectl apply -f deploy/crd.yml

8. Деплой оператора

Создадим в папке ./deploy:

    service-account.yml
    role.yml
    role-binding.yml
    deploy-operator.yml

Применим манифесты:

kubectl apply -f ./deploy/service-account.yml
kubectl apply -f ./deploy/role.yml
kubectl apply -f ./deploy/role-binding.yml
kubectl apply -f ./deploy/deploy-operator.yml
kubectl apply -f ./deploy/cr.yml

9. Проверим, что все работает

Проверяем что появились pvc:

kubectl get pvc -A

NAMESPACE   NAME                        STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
default     backup-mysql-instance-pvc   Bound    pvc-645ede01-3824-4f33-92cf-6eb45579df57   1Gi        RWO            standard       45s
default     mysql-instance-pvc          Bound    pvc-ecb46eaf-2ea6-4509-8ea9-af9d8905bdf1   1Gi        RWO            standard       46s


Заполним базу созданного mysql-instance:

export MYSQLPOD=$(kubectl get pods -l app=mysql-instance -o jsonpath="{.items[*].metadata.name}")

kubectl exec -it $MYSQLPOD -- mysql -u root -potuspassword -e "CREATE TABLE test ( id smallint \
  unsigned not null auto_increment, name varchar(20) not null, constraint pk_example primary key \
  (id) );" otus-database

kubectl exec -it $MYSQLPOD -- mysql -potuspassword -e "INSERT INTO test ( id, name ) VALUES ( \
  null, 'some data' );" otus-database

kubectl exec -it $MYSQLPOD -- mysql -potuspassword -e "INSERT INTO test ( id, name ) VALUES ( \
  null, 'some data-2' );" otus-database

Посмотри содержимое таблицы:

kubectl exec -it $MYSQLPOD -- mysql -potuspassword -e "select * from test;" otus-database

+----+-------------+
| id | name        |
+----+-------------+
|  1 | some data   |
|  2 | some data-2 |
+----+-------------+

Удалим mysql-instance:

kubectl delete mysqls.otus.homework mysql-instance

kubectl get pv

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM                               STORAGECLASS   REASON   AGE
backup-mysql-instance-pv                   1Gi        RWO            Retain           Available                                                               5m7s
pvc-645ede01-3824-4f33-92cf-6eb45579df57   1Gi        RWO            Delete           Bound       default/backup-mysql-instance-pvc   standard                5m6s


показывает, что PV для mysql в статусе Available. а не Bound, а

kubectl get jobs.batch

показывает

NAME                        COMPLETIONS   DURATION   AGE
backup-mysql-instance-job   1/1           5s         55s


kubectl describe jobs/backup-mysql-instance-job

Events:
  Type    Reason            Age   From            Message
  ----    ------            ----  ----            -------
  Normal  SuccessfulCreate  87s   job-controller  Created pod: backup-mysql-instance-job-2xlln
  Normal  Completed         82s   job-controller  Job completed


Создадим заново mysql-instance:

kubectl apply -f deploy/cr.yml

Немного подождем и

export MYSQLPOD=$(kubectl get pods -l app=mysql-instance -o jsonpath="{.items[*].metadata.name}")
kubectl exec -it $MYSQLPOD -- mysql -potuspassword -e "select * from test;" otus-database

+----+-------------+
| id | name        |
+----+-------------+
|  1 | some data   |
|  2 | some data-2 |
+----+-------------+

kubectl get jobs 

NAME                         COMPLETIONS   DURATION   AGE
backup-mysql-instance-job    1/1           5s         3m44s
restore-mysql-instance-job   1/1           45s        78s


kubectl describe jobs/restore-mysql-instance-job

vents:
  Type    Reason            Age   From            Message
  ----    ------            ----  ----            -------
  Normal  SuccessfulCreate  114s  job-controller  Created pod: restore-mysql-instance-job-f4759
  Normal  Completed         69s   job-controller  Job completed


Полезное:

Start

yc managed-kubernetes cluster start k8s-4otus

Stop

yc managed-kubernetes cluster stop k8s-4otus

==========================================================================================================================


Лекция №9: Мониторинг компонентов кластера и приложений, работающих в нем // ДЗ

    Мониторинг сервиса в кластере k8s

kubernetes-monitoring
Задание:

Мониторинг приложения в кластере

Цель: В данном дз студенту установят систему мониторинга Prometheus в кластер. Научатся конфигурировать Prometheus для мониторинга как самого кластера, так и приложений которые в нем работают.

Описание/Пошаговая инструкция выполнения домашнего задания: Все действия описаны в методическом указании.

Критерии оценки: 0 б. - задание не выполнено 1 б. - задание выполнено 2 б. - выполнены все дополнительные задания
Выполнено:

    Запускаем minikube

minikube delete && minikube start --kubernetes-version=v1.23.0 --memory=6g --bootstrapper=kubeadm --extra-config=kubelet.authentication-token-webhook=true --extra-config=kubelet.authorization-mode=Webhook --extra-config=scheduler.bind-address=0.0.0.0 --extra-config=controller-manager.bind-address=0.0.0.0

    Ставим prometheus-operator через kubectl apply из https://github.com/prometheus-operator/kube-prometheus

cd ./kube-prometheus
# Create the namespace and CRDs, and then wait for them to be available before creating the remaining resources
# Note that due to some CRD size we are using kubectl server-side apply feature which is generally available since kubernetes 1.22.
# If you are using previous kubernetes versions this feature may not be available and you would need to use kubectl create instead.
kubectl apply --server-side -f manifests/setup
kubectl wait \
--for condition=Established \
--all CustomResourceDefinition \
--namespace=monitoring
kubectl apply -f manifests/

    Готовим манифест для nginx и применяем:

kubectl apply -f ./nginx

    Проверяем эндпойнты:

kubectl --namespace monitoring port-forward svc/prometheus-k8s 9090


    Заходим в графану

kubectl --namespace monitoring port-forward svc/grafana 3000

http://localhost:3000/

    Добавляем Official dashboard for NGINX Prometheus exporter


Для изучения:
    https://github.com/prometheus-operator/kube-prometheus
    https://nginx.org/ru/docs/http/ngx_http_stub_status_module.html
    https://habr.com/ru/company/flant/blog/353410/



=============================================================================================================================================================

Лекция №10: Сервисы централизованного логирования для компонентов Kubernetes и приложений // ДЗ

    Сервисы централизованного логирования для Kubernetes

kubernetes-logging
Задание:

Сервисы централизованного логирования для Kubernetes

Цель: В домашнем задании развернем сервисы для централизованного логирования (EFK/Loki) внутри Kubernetes, научимся отправлять туда структурированные логи продукта и инфраструктурных компонентов, рассмотрим способы визуализировать информацию из логов

Описание/Пошаговая инструкция выполнения домашнего задания: Все действия описаны в методическом указании.

Критерии оценки: 0 б. - задание не выполнено 1 б. - задание выполнено 2 б. - выполнены все дополнительные задания
Выполнено:
1. Подготовка Kubernetes кластера

    Поднимаем кластер k8s в yandex-cloud со следующими параметрами:
        Как минимум 1 нода типа standard-v2 в группе узлов default-pool
        Как минимум 3 ноды типа standard-v2 в группе узлов infra-pool

cd terraform-k8s
terraform init
terraform plan
terraform apply

yc managed-kubernetes cluster list-node-groups k8s-otus
yc managed-kubernetes node-group list
+----------------------+--------------+----------------------+---------------------+---------+------+
|          ID          |     NAME     |  INSTANCE GROUP ID   |     CREATED AT      | STATUS  | SIZE |
+----------------------+--------------+----------------------+---------------------+---------+------+
| catmr74fadp5f6ena3r2 | default-pool | cl1gpea7ejjpk3v01ucd | 2023-03-23 12:00:08 | RUNNING |    1 |
| catsmep3537dpast19ae | infra-pool   | cl1mqfdgq0b7v43lj89u | 2023-03-23 12:00:08 | RUNNING |    3 |
+----------------------+--------------+----------------------+---------------------+---------+------+


В результате должна получиться следующая конфигурация кластера:

kubectl get nodes -o wide

NAME                        STATUS   ROLES    AGE   VERSION   INTERNAL-IP   EXTERNAL-IP      OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
cl1gpea7ejjpk3v01ucd-ugoj   Ready    <none>   26m   v1.23.6   10.128.0.12   158.160.60.148   Ubuntu 20.04.4 LTS   5.4.0-124-generic   containerd://1.6.7
cl1mqfdgq0b7v43lj89u-aqej   Ready    <none>   26m   v1.23.6   10.128.0.20   158.160.35.70    Ubuntu 20.04.4 LTS   5.4.0-124-generic   containerd://1.6.7
cl1mqfdgq0b7v43lj89u-ocoq   Ready    <none>   26m   v1.23.6   10.128.0.28   51.250.71.43     Ubuntu 20.04.4 LTS   5.4.0-124-generic   containerd://1.6.7
cl1mqfdgq0b7v43lj89u-omir   Ready    <none>   26m   v1.23.6   10.128.0.30   158.160.49.160   Ubuntu 20.04.4 LTS   5.4.0-124-generic   containerd://1.6.7


Выведем перечень всех нод из infra-pool

yc managed-kubernetes node-group list-nodes infra-pool
+--------------------------------+---------------------------+--------------------------------+--------------------------------+--------+
|         CLOUD INSTANCE         |      KUBERNETES NODE      |           RESOURCES            |              DISK              | STATUS |
+--------------------------------+---------------------------+--------------------------------+--------------------------------+--------+
| fhmgg1j0o9eo4iddmbhb           | cl1mqfdgq0b7v43lj89u-aqej | 2 100% core(s), 8.0 GB of      | 93.0 GB                        | READY  |
| RUNNING_ACTUAL                 |                           | memory                         | network-ssd-nonreplicated      |        |
| fhmemc58sg9e3qp2kvpj           | cl1mqfdgq0b7v43lj89u-ocoq | 2 100% core(s), 8.0 GB of      | 93.0 GB                        | READY  |
| RUNNING_ACTUAL                 |                           | memory                         | network-ssd-nonreplicated      |        |
| fhmhvt3jkj3flhrr3b5g           | cl1mqfdgq0b7v43lj89u-omir | 2 100% core(s), 8.0 GB of      | 93.0 GB                        | READY  |
| RUNNING_ACTUAL                 |                           | memory                         | network-ssd-nonreplicated      |        |
+--------------------------------+---------------------------+--------------------------------+--------------------------------+--------+


Пометим ноды infra-pool тейнтом node-role=infra:NoSchedule

kubectl taint nodes cl1mqfdgq0b7v43lj89u-aqej node-role=infra:NoSchedule
kubectl taint nodes cl1mqfdgq0b7v43lj89u-ocoq node-role=infra:NoSchedule
kubectl taint nodes cl1mqfdgq0b7v43lj89u-omir node-role=infra:NoSchedule

Проверим taints на нодах

kubectl get nodes -o json | jq '.items[].spec.taints'

null
[
  {
    "effect": "NoSchedule",
    "key": "node-role",
    "value": "infra"
  }
]
[
  {
    "effect": "NoSchedule",
    "key": "node-role",
    "value": "infra"
  }
]
[
  {
    "effect": "NoSchedule",
    "key": "node-role",
    "value": "infra"
  }
]

2. Установка HipsterShop

Для начала, установим в Kubernetes кластер уже знакомый нам HipsterShop.

kubectl create ns microservices-demo
kubectl apply -f https://raw.githubusercontent.com/express42/otus-platform-snippets/master/Module-02/Logging/microservices-demo-without-resources.yaml -n microservices-demo

Проверим, что все pod развернулись на ноде из default-pool:

kubectl get pods -n microservices-demo -o wide

NAME                                     READY   STATUS             RESTARTS      AGE   IP              NODE                        NOMINATED NODE   READINESS GATES
adservice-548889999f-ftrhh               0/1     ImagePullBackOff   0             17m   10.112.130.14   cl1gpea7ejjpk3v01ucd-ugoj   <none>           <none>
cartservice-75cc479cdd-wtpmj             1/1     Running            2 (15m ago)   17m   10.112.130.9    cl1gpea7ejjpk3v01ucd-ugoj   <none>           <none>
checkoutservice-699758c6d9-xhlbv         1/1     Running            0             17m   10.112.130.4    cl1gpea7ejjpk3v01ucd-ugoj   <none>           <none>
currencyservice-7fc9cfc9cf-trqt8         1/1     Running            0             17m   10.112.130.11   cl1gpea7ejjpk3v01ucd-ugoj   <none>           <none>
emailservice-6c8d49f789-6s2lm            1/1     Running            0             17m   10.112.130.3    cl1gpea7ejjpk3v01ucd-ugoj   <none>           <none>
frontend-5b8c8bf745-rrr24                1/1     Running            0             17m   10.112.130.6    cl1gpea7ejjpk3v01ucd-ugoj   <none>           <none>
loadgenerator-799c7664dd-6xr62           1/1     Running            4 (15m ago)   17m   10.112.130.10   cl1gpea7ejjpk3v01ucd-ugoj   <none>           <none>
paymentservice-557f767677-7d7sm          1/1     Running            0             17m   10.112.130.7    cl1gpea7ejjpk3v01ucd-ugoj   <none>           <none>
productcatalogservice-7b69d99c89-2rjb9   1/1     Running            0             17m   10.112.130.8    cl1gpea7ejjpk3v01ucd-ugoj   <none>           <none>
recommendationservice-7f78d66cc9-vlx6z   1/1     Running            0             17m   10.112.130.5    cl1gpea7ejjpk3v01ucd-ugoj   <none>           <none>
redis-cart-fd8d87cdb-jqq5r               1/1     Running            0             17m   10.112.130.13   cl1gpea7ejjpk3v01ucd-ugoj   <none>           <none>
shippingservice-64999cdc59-6c57k         1/1     Running            0             17m   10.112.130.12   cl1gpea7ejjpk3v01ucd-ugoj   <none>           <none>


3. Установка EFK стека | Helm charts

Начнем с "классического" набора инструментов (ElasticSearch, Fluent Bit, Kibana) и "классического" способа его установки в Kubernetes кластер (Helm). Рекомендуемый репозиторий с Helm chart для ElasticSearch и Kibana на текущий момент - https://github.com/elastic/helm-charts Добавим его:

helm repo add elastic https://helm.elastic.co

И установим нужные нам компоненты, для начала - без какой-либо дополнительной настройки:

kubectl create ns observability
# ElasticSearch
helm upgrade --install elasticsearch elastic/elasticsearch --namespace observability
# Kibana
helm upgrade --install kibana elastic/kibana --namespace observability
# Fluent Bit
helm upgrade --install fluent-bit stable/fluent-bit --namespace observability

И ловим 403 - No comments... Идем другим путем:

kubectl create ns observability
#helm repo remove elastic 
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update bitnami
# ElasticSearch
helm upgrade --install elasticsearch bitnami/elasticsearch --namespace observability
# Kibana
helm upgrade --install kibana bitnami/kibana --namespace observability

Смотрим, что получилось

kubectl get pods -n observability -o wide

Всё поставилось так же, на ту же первую ноду cl1gpea7ejjpk3v01ucd-ugoj

NAME                           READY   STATUS    RESTARTS        AGE   IP              NODE                        NOMINATED NODE   READINESS GATES
elasticsearch-coordinating-0   0/1     Running   22 (113s ago)   92m   10.112.130.15   cl1gpea7ejjpk3v01ucd-ugoj   <none>           <none>
elasticsearch-coordinating-1   0/1     Running   22 (103s ago)   92m   10.112.130.16   cl1gpea7ejjpk3v01ucd-ugoj   <none>           <none>
elasticsearch-data-0           0/1     Running   22 (100s ago)   92m   10.112.130.22   cl1gpea7ejjpk3v01ucd-ugoj   <none>           <none>
elasticsearch-data-1           0/1     Running   22 (119s ago)   92m   10.112.130.19   cl1gpea7ejjpk3v01ucd-ugoj   <none>           <none>
elasticsearch-ingest-0         0/1     Running   22 (113s ago)   92m   10.112.130.17   cl1gpea7ejjpk3v01ucd-ugoj   <none>           <none>
elasticsearch-ingest-1         0/1     Running   22 (2m3s ago)   92m   10.112.130.18   cl1gpea7ejjpk3v01ucd-ugoj   <none>           <none>
elasticsearch-master-0         0/1     Running   9 (18m ago)     92m   10.112.130.21   cl1gpea7ejjpk3v01ucd-ugoj   <none>           <none>
elasticsearch-master-1         1/1     Running   8 (9m48s ago)   92m   10.112.130.20   cl1gpea7ejjpk3v01ucd-ugoj   <none>           <none>


Создадим файл elasticsearch.values.yaml , будем указывать в этом файле нужные нам values. Для начала, обратимся к файлу values.yaml в и найдем там ключ tolerations.

Мы помним, что ноды из infra-pool имеют taint node-role=infra:NoSchedule . Давайте разрешим ElasticSearch запускаться на данных нодах

    elasticsearch.values.yaml

master:
  tolerations: &tolerations
    - key: node-role
      operator: Equal
      value: infra
      effect: NoSchedule

data:
  tolerations:
    *tolerations

coordinating:
  tolerations:
    *tolerations

ingest:
  tolerations:
    *tolerations

metrics:
  tolerations:
    *tolerations

Обновим установку:

helm upgrade --install elasticsearch bitnami/elasticsearch --namespace observability \
-f elasticsearch.values.yaml

Теперь ElasticSearch может запускаться на нодах из infra-pool, но это не означает, что он должен это делать. Исправим этот момент и добавим в elasticsearch.values.yaml NodeSelector, определяющий, на каких нодах мы можем запускать наши pod.

yc managed-kubernetes node-group list

master:
  nodeSelector: &nodeSelector
    yandex.cloud/node-group-id: catsmep3537dpast19ae

Корректируем соответственно elasticsearch.values.yaml для всех компонент ElasticSearch и перезапускаем деплой:

kubectl create ns observability
helm uninstall elasticsearch --namespace observability
helm upgrade --install elasticsearch bitnami/elasticsearch --namespace observability \
-f elasticsearch.values.yaml

Смотрим, что получилось:

kubectl get pods -n observability -o wide -l app.kubernetes.io/component=master

NAME                     READY   STATUS    RESTARTS   AGE   IP              NODE                        NOMINATED NODE   READINESS GATES
elasticsearch-master-0   1/1     Running   0          20m   10.112.129.12   cl1a1v5ptf3j9fo85vat-aven   <none>           <none>
elasticsearch-master-1   1/1     Running   0          22m   10.112.128.11   cl1a1v5ptf3j9fo85vat-ehif   <none>           <none>
elasticsearch-master-2   1/1     Running   0          24m   10.112.131.8    cl1a1v5ptf3j9fo85vat-upaz   <none>           <none>

4. Установка nginx-ingress | Самостоятельное задание

yc managed-kubernetes cluster list

helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update ingress-nginx

kubectl create ns nginx-ingress
helm upgrade --install nginx-ingress-release ingress-nginx/ingress-nginx \
 --namespace=nginx-ingress --version="4.4.2" \
 -f nginx.values.yaml

kubectl get pods -n nginx-ingress -o wide

NAME                                                              READY   STATUS    RESTARTS   AGE    IP              NODE                        NOMINATED NODE   READINESS GATES
nginx-ingress-release-ingress-nginx-controller-5bd9cd5dc8-9nnj8   1/1     Running   0          54s    10.112.131.12   cl1mqfdgq0b7v43lj89u-ocoq   <none>           <none>
nginx-ingress-release-ingress-nginx-controller-5bd9cd5dc8-clx67   1/1     Running   0          114s   10.112.128.11   cl1mqfdgq0b7v43lj89u-aqej   <none>           <none>
nginx-ingress-release-ingress-nginx-controller-5bd9cd5dc8-hpgtg   1/1     Running   0          84s    10.112.129.13   cl1mqfdgq0b7v43lj89u-omir   <none>           <none>


kubectl get svc -n nginx-ingress

kubectl get svc -n observability

# Kibana
helm upgrade --install kibana bitnami/kibana --namespace observability \
  --set "elasticsearch.hosts[0]=elasticsearch,elasticsearch.port=9200" -f kibana.values.yaml

Попробуем создать index pattern , и увидим, что в ElasticSearch пока что не обнаружено никаких данных: img.png)

Посмотрим в логи решения, которое отвечает за отправку логов (Fluent Bit) и увидим следующие строки:

kubectl logs -n observability -l app=fluent-bit --tail 3

[2023/02/25 19:55:40] [ warn] net_tcp_fd_connect: getaddrinfo(host='fluentd'): Name or service not known
[2023/02/25 19:55:40] [error] [out_fw] no upstream connections available
[2023/02/25 19:55:40] [ warn] [engine] failed to flush chunk '1-1677333615.980802731.flb', retry in 239 seconds: task_id=22, input=tail.0 > output=forward.0

5.Fluent Bit

Попробуем исправить проблему. Создадим файл fluentbit.values.yaml и добавим туда:

config:
  outputs: |
    [OUTPUT]
        Name  es
        Match *
        Host  elasticsearch
        Port  9200

nodeSelector: &nodeSelector
  yandex.cloud/node-group-id: catsmep3537dpast19ae

tolerations: &tolerations
  - key: node-role
    operator: Equal
    value: infra
    effect: NoSchedule

Запускаем деплой NotDeprecated версии чарта:

helm repo add fluent https://fluent.github.io/helm-charts
helm upgrade --install fluent-bit fluent/fluent-bit \
 --namespace observability -f fluentbit.values.yaml

Ловим в подах fluent ошибки 2х видов:

{"error":{"root_cause":[{"type":"illegal_argument_exception","reason":"Action/metadata line [1] contains an unknown parameter [_type]"}],"type":"illegal_argument_exception","reason":"Action/metadata line [1] contains an unknown parameter [_type]"},"status":400}

[error] [engine] chunk '1-1677401624.242975823.flb' cannot be retried: task_id=0, input=tail.0 > output=es.0 

Приводим fluentbit.values.yaml в соответствии с рекомендациями:

    https://docs.fluentbit.io/manual/pipeline/outputs/elasticsearch

    https://github.com/fluent/fluent-bit/issues/4386.you

к виду:

config:
  outputs: |
    [OUTPUT]
        Name  es
        Match *
        Host  elasticsearch
        Port  9200
        Suppress_Type_Name On
        Replace_Dots    On

nodeSelector: &nodeSelector
  yandex.cloud/node-group-id: catsmep3537dpast19ae

tolerations: &tolerations
  - key: node-role
    operator: Equal
    value: infra
    effect: NoSchedule

и производим передеплой fluent-bit:

helm upgrade --install fluent-bit fluent/fluent-bit \
--namespace observability -f fluentbit.values.yaml

Попробуем повторно создать 'index pattern' . В этот раз ситуация изменилась, и какие-то индексы в ElasticSearch уже есть: img_1.png

После установки можно заметить, что в ElasticSearch не попадают логи нашего приложения, т.к. fluentbit не продеплоился на все ноды. Исключаем секцию из fluentbit.values.yaml

nodeSelector: &nodeSelector
yandex.cloud/node-group-id: catsmep3537dpast19ae

И приводим к виду:

config:
  outputs: |
    [OUTPUT]
        Name es
        Match kube.*
        Host elasticsearch
        Port  9200
        Logstash_Format On
        Retry_Limit False
        Suppress_Type_Name On
        Replace_Dots    On

    [OUTPUT]
        Name es
        Match host.*
        Host elasticsearch
        Port  9200
        Logstash_Format On
        Logstash_Prefix node
        Retry_Limit False
        Suppress_Type_Name On
        Replace_Dots    On

tolerations: &tolerations
  - key: node-role
    operator: Equal
    value: infra
    effect: NoSchedule

и производим передеплой fluent-bit:

helm upgrade --install fluent-bit fluent/fluent-bit \
--namespace observability -f fluentbit.values.yaml

Проверяем:

kubectl get pod -n observability -l app.kubernetes.io/instance=fluent-bit -o wide

NAME               READY   STATUS    RESTARTS   AGE   IP              NODE                        NOMINATED NODE   READINESS GATES
fluent-bit-9zk24   1/1     Running   0          36s   10.112.130.31   cl1gpea7ejjpk3v01ucd-ugoj   <none>           <none>
fluent-bit-r285w   1/1     Running   0          36s   10.112.128.12   cl1mqfdgq0b7v43lj89u-aqej   <none>           <none>
fluent-bit-rx2t6   1/1     Running   0          36s   10.112.131.13   cl1mqfdgq0b7v43lj89u-ocoq   <none>           <none>
fluent-bit-t9d9b   1/1     Running   0          36s   10.112.129.14   cl1mqfdgq0b7v43lj89u-omir   <none>           <none>

Видим, что мы уже можем добавить индекс:  http://kibana.51.250.36.103.nip.io/app/discover#/


6. Мониторинг ElasticSearch

Помимо установки ElasticSearch, важно отслеживать его показатели и вовремя понимать, что пора предпринять какие-либо действия. Для мониторинга ElasticSearch будем использовать следующий

    Установим prometheus-operator в namespace observability

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm upgrade --install prometheus-operator prometheus-community/kube-prometheus-stack -n observability \
 -f prometheus.values.yaml

    Установим упомянутый выше exporter:

helm upgrade --install elasticsearch-exporter prometheus-community/prometheus-elasticsearch-exporter \
  --set es.uri=http://elasticsearch:9200 \
  --set serviceMonitor.enabled=true \
  --namespace=observability

Проверяем:

    http://prometheus.51.250.36.103.nip.io img_2.png

    http://grafana.51.250.36.103.nip.io img_4.png

Импортируем в Grafana один из популярных Dashboard c ID: 4358 exporter, содержащий визуализацию основных собираемых метрик: img_3.png

Проверим, что метрики действительно собираются корректно. Сделаем drain одной из нод infra-pool:

yc managed-kubernetes node-group list-nodes infra-pool

+--------------------------------+---------------------------+--------------------------------+--------------------------------+--------+
|         CLOUD INSTANCE         |      KUBERNETES NODE      |           RESOURCES            |              DISK              | STATUS |
+--------------------------------+---------------------------+--------------------------------+--------------------------------+--------+
| fhmgg1j0o9eo4iddmbhb           | cl1mqfdgq0b7v43lj89u-aqej | 2 100% core(s), 8.0 GB of      | 93.0 GB                        | READY  |
| RUNNING_ACTUAL                 |                           | memory                         | network-ssd-nonreplicated      |        |
| fhmemc58sg9e3qp2kvpj           | cl1mqfdgq0b7v43lj89u-ocoq | 2 100% core(s), 8.0 GB of      | 93.0 GB                        | READY  |
| RUNNING_ACTUAL                 |                           | memory                         | network-ssd-nonreplicated      |        |
| fhmhvt3jkj3flhrr3b5g           | cl1mqfdgq0b7v43lj89u-omir | 2 100% core(s), 8.0 GB of      | 93.0 GB                        | READY  |
| RUNNING_ACTUAL                 |                           | memory                         | network-ssd-nonreplicated      |        |
+--------------------------------+---------------------------+--------------------------------+--------------------------------+--------+


kubectl drain cl1mqfdgq0b7v43lj89u-aqej --ignore-daemonsets --delete-emptydir-data

Статус Cluster Health остался зеленым, но количество нод в кластере уменьшилось до двух штук. При этом, кластер сохранил полную работоспособность. img_5.png

Попробуем сделать drain второй ноды из infra-pool, и увидим что не PDB дает этого сделать.

    https://kubernetes.io/docs/tasks/run-application/configure-pdb/

kubectl drain cl1a1v5ptf3j9fo85vat-ehif --ignore-daemonsets --delete-emptydir-data

Вернем ноды в исходное состояние:

kubectl uncordon cl1mqfdgq0b7v43lj89u-aqej
kubectl uncordon cl1mqfdgq0b7v43lj89u-ocoq

После экспериментов у нас не рабоает один из экземпляров core-dns

kubectl describe pod/coredns-d64bfb745-wcrk4 -n kube-system

...
Events:
Type     Reason             Age                 From                Message
  ----     ------             ----                ----                -------
Normal   NotTriggerScaleUp  2m58s               cluster-autoscaler  pod didn't trigger scale-up:
Warning  FailedScheduling   59s (x3 over 3m8s)  default-scheduler   0/4 nodes are available: 1 node(s) didn't match pod topology spread constraints, 3 node(s) had taint {node-role: infra}, that the pod didn't tolerate.

Лечим:

kubectl taint nodes cl1mqfdgq0b7v43lj89u-aqej node-role=infra:NoSchedule-
kubectl taint nodes cl1mqfdgq0b7v43lj89u-ocoq node-role=infra:NoSchedule-
kubectl taint nodes cl1mqfdgq0b7v43lj89u-omir  node-role=infra:NoSchedule-
sleep 15
kubectl taint nodes cl1mqfdgq0b7v43lj89u-aqej node-role=infra:NoSchedule
kubectl taint nodes cl1mqfdgq0b7v43lj89u-ocoq node-role=infra:NoSchedule
kubectl taint nodes cl1mqfdgq0b7v43lj89u-omir node-role=infra:NoSchedule

7. EFK | nginx ingress

Попробуем найти в Kibana логи nginx-ingress (например, полнотекстовым поиском по слову nginx ) и обнаружим, что они отсутствуют. Приводим nginx.values.yaml к виду:

...
  config:
    log-format-escape-json: "true"
    log-format-upstream: '{"time_local": "$time_local", "proxy_protocol_addr": "$proxy_protocol_addr",
        "remote_addr": "$remote_addr", "proxy_add_x_forwarded_for": "$proxy_add_x_forwarded_for", 
        "remote_user": "$remote_user", "request" : "$request", "body_bytes_sent": "$body_bytes_sent", 
        "http_referer":  "$http_referer", "http_user_agent": "$http_user_agent", 
        "proxy_upstream_name": "$proxy_upstream_name", "upstream_addr": "$upstream_addr",  
        "upstream_response_length": "$upstream_response_length", "upstream_response_time": "$upstream_response_time", 
        "upstream_status": "$upstream_status"}'
...

Перезапускаем передеплой nginx-ingress-release, проверяем: img_6.png img_7.png

Теперь, когда мы научились собирать логи с nginx-ingress и смогли их структурировать, можно опробовать возможности Kibana для визуализации. Перейдем на вкладку Visualize и создадим новую визуализацию с типом TSVB. Для этого нам понадобится применить следующий KQL фильтр:

kubernetes.labels.app_kubernetes_io/name: ingress-nginx

Cоздадим визуализации для отображения запросов к nginx-ingress со статусами:

    200-299 KQL filter: upstream_status.keyword : 2*
    300-399 KQL filter: upstream_status.keyword : 3*
    400-499 KQL filter: upstream_status.keyword : 4*
    500+    KQL filter: upstream_status.keyword : 5*

img_8.png

Экспортируем получившиеся визуализации и Dashboard в файл export.ndjson img_9.png
8. Loki

Задачи:

    Установить Loki в namespace observability, используя любой из доступных способов. Должны быть установлены непосредственно Loki и Promtail

    Модифицировать конфигурацию prometheus-operator таким образом, чтобы datasource Loki создавался сразу после установки оператора

    Итоговый файл prometheus-operator.values.yaml выложить в репозиторий в директорию kubernetes-logging

    Установка

helm repo add grafana https://grafana.github.io/helm-charts
helm repo update grafana
helm upgrade --install loki grafana/loki-distributed -n observability -f loki.values.yaml
helm upgrade --install promtail grafana/promtail -n observability -f promtail.values.yaml

    Loki | Datasource img_10.png

    Loki | ingress-nginx img_11.png

Loki, аналогично ElasticSearch умеет разбирать JSON лог по ключам, но, к сожалению, фильтрация по данным ключам на текущий момент не работает: img_12.png

    Loki | Визуализация Создадим Dashboard, на котором одновременно выведем метрики ingress-nginx и его логи:

        Убедимся, что вместе с ingress-nginx устанавливается 'serviceMonitor', и Prometheus "видит" его: img_13.png

        Создадим в Grafana новый Dashboard.
            Добавим для него следующие переменные (взяты из официального Dashboard nginx-ingress):
            Создадим новую панель и добавьте туда следующую [query](взято из официального Dashboard для nginx-ingress)

        sum(rate(nginx_ingress_controller_requests{controller_pod=~"$controller",controller_class=~"$controller_class",namespace=~"$namespace",ingress=~"$ingress",status!~"[4-5].*"}[1m])) by (ingress) / sum(rate(nginx_ingress_controller_requests{controller_pod=~"$controller",controller_class=~"$controller_class",namespace=~"$namespace",ingress=~"$ingress"}[1m])) by (ingress)

            Аналогичным образом добавим панель, позволяющую оценить количество запросов к nginx-ingress в секунду
            Добавим панель с логами и укажем для нее следующие настройки Query:

img_15.png

Итоговый Dashboard:

img_16.png

Выгрузим из Grafana JSON с финальным Dashboard и поместим его в nginx-ingress.json
9. Host logging | Задание со star

На текущий момент мы лишены возможности централизованного просмотра логов с виртуальных машин, на которых запущен Kubernetes. Модернизируем конфигурацию nodes-logging/fluent-bit таким образом, чтобы данные логи отображались в ElasticSearch.

config:
  inputs: |
    [INPUT]
        Name systemd
        Tag node.*
        Path /journal
        Read_From_Tail On
...

Деплоим

helm upgrade --install fluent-bit fluent/fluent-bit \
--namespace observability -f nodes-logging/fluentbit.values.yaml

Проверяем: img_17.png
10. Audit logging | Задание со star

Еще один важный тип логов, который рекомендуется собирать и хранить логи аудита Получить их в GKE (Yandex) сложнее, чем в self-hosted кластерах из-за того, что доступ к master нодам, на которых запущен kube-apiserver, отсутствует. Поэтому нужно развернуть self-hosted кластер и настроить сбор аудит логов.

    https://kubernetes.io/docs/tasks/debug-application-cluster/audit/

Создадим файл с Audit policy audit-policy.yaml

# Log all requests at the Metadata level.
apiVersion: audit.k8s.io/v1
kind: Policy
rules:
  - level: Metadata

Запустим кластер с базе kind c дополнительными параметрами для kube-apiserver

--audit-policy-file=/etc/kubernetes/audit-policy.yaml \
--audit-log-path=/var/log/kubernetes/audit/audit.log

cd audit-logging/
kind create cluster --config kind-config.yaml

Проверим формирование логов аудита

docker exec kind-control-plane cat /var/log/kubernetes/kube-apiserver-audit.log | head -2

{"kind":"Event","apiVersion":"audit.k8s.io/v1","level":"Metadata","auditID":"8a263a99-cd22-4647-aaee-4f554138f234","stage":"RequestReceived","requestURI":"/apis/flowcontrol.apiserver.k8s.io/v1beta2/flowschemas?limit=500\u0026resourceVersion=0","verb":"list","user":{"username":"system:apiserver","uid":"337a90c2-2a6a-4d4d-867d-2455cea4b2ed","groups":["system:masters"]},"sourceIPs":["::1"],"userAgent":"kube-apiserver/v1.24.0 (linux/amd64) kubernetes/4ce5a89","objectRef":{"resource":"flowschemas","apiGroup":"flowcontrol.apiserver.k8s.io","apiVersion":"v1beta2"},"requestReceivedTimestamp":"2023-03-03T10:33:04.606133Z","stageTimestamp":"2023-03-03T10:33:04.606133Z"}

{"kind":"Event","apiVersion":"audit.k8s.io/v1","level":"Metadata","auditID":"6e876c1a-fd71-49ac-a25b-d915184ce1af","stage":"RequestReceived","requestURI":"/api/v1/services?limit=500\u0026resourceVersion=0","verb":"list","user":{"username":"system:node:kind-control-plane","groups":["system:nodes","system:authenticated"]},"sourceIPs":["fc00:f853:ccd:e793::3"],"userAgent":"kubelet/v1.24.0 (linux/amd64) kubernetes/4ce5a89","objectRef":{"resource":"services","apiVersion":"v1"},"requestReceivedTimestamp":"2023-03-03T10:33:04.607870Z","stageTimestamp":"2023-03-03T10:33:04.607870Z"}

Полезное:

    https://registry.tfpla.net/providers/yandex-cloud/yandex/latest/docs/resources/kubernetes_node_group#node_taints
    https://cloud.yandex.ru/docs/managed-kubernetes/api-ref/NodeGroup/
    https://kubernetes.io/docs/concepts/scheduling-eviction/topology-spread-constraints/#spread-constraints-for-pods
    https://blog.kubecost.com/blog/kubernetes-taints/
    https://docs.comcloud.xyz/
    https://kind.sigs.k8s.io/docs/user/auditing/

yc managed-kubernetes cluster stop k8s-4otus

yc managed-kubernetes cluster start k8s-4otus




===============================================================================================================================================

Лекция №14: GitOps и инструменты поставки // ДЗ

    GitOps и инструменты поставки

kubernetes-gitops
Задание:

Цель: В данном дз студенты познакомятся с такими инструментами как ArgoCD, Flux, Flagger. Научатся при помощи этих инструментов деплоить приложение в кластер.

Описание/Пошаговая инструкция выполнения домашнего задания: Все действия описаны в методическом указании.

Критерии оценки: 0 б. - задание не выполнено 1 б. - задание выполнено 2 б. - выполнены все дополнительные задания
Выполнено:
1. Подготовка GitLab репозитария

git clone https://github.com/GoogleCloudPlatform/microservices-demo
cd microservices-demo
git remote add gitlab git@gitlab.com:lookus84/microservices-demo.git
git remote remove origin
git push -uf gitlab main

2. Создание Helm чартов

Скопируем готовые чарты из демонстрационного репозитория (директория deploy/charts )

tree -L 1 deploy/charts

deploy/charts
├── adservice
├── cartservice
├── checkoutservice
├── currencyservice
├── emailservice
├── frontend
├── grafana-load-dashboards
├── loadgenerator
├── paymentservice
├── productcatalogservice
├── recommendationservice
└── shippingservice


3. Подготовка Kubernetes кластера

Поднимаем кластер k8s в yandex-cloud со следующими параметрами:

    Как минимум 4 ноды типа standard-v1 (Terraform способ)

cd terraform-k8s
terraform init
terraform plan
terraform apply

    Как минимум 4 ноды типа standard-v1 (yc cli)

yc managed-kubernetes cluster create k8s-4otus \
--network-id "enp67lv83b2vf4bjd1fu" \
--zone "ru-central1-c" \
--subnet-id "e9bmlk8v6q1ggh0iv0m8" \
--public-ip \
--release-channel RAPID \
--version 1.24 \
--node-service-account-name tfuser \
--cloud-id "b1g0g9jct4g64jqokd8b" \
--folder-id "b1gf0t46quh91ma3q0m1" \
--token "TOKEN" \
--no-user-output

yc managed-kubernetes node-group create k8s-4otus-node-pool \
--cluster-name k8s-4otus \
--fixed-size 2 \
--platform standard-v1 \
--memory 4 \
--cores 2 \
--disk-size 30 \
--disk-type network-hdd \
--version 1.24 \
--cloud-id "b1g0g9jct4g64jqokd8b" \
--folder-id "b1gf0t46quh91ma3q0m1" \
--token "TOKEN"

yc managed-kubernetes cluster get-credentials k8s-otus --external --force --folder-id b1gf0t46quh91ma3q0m1

    Проверяем

kubectl get nodes

cl16t82a9u5mdl34eodt-inyg   Ready    <none>   71s   v1.24.6
cl16t82a9u5mdl34eodt-ygex   Ready    <none>   79s   v1.24.6
cl16t82a9u5mdl34eodt-ygig   Ready    <none>   65s   v1.24.6
cl16t82a9u5mdl34eodt-yhug   Ready    <none>   78s   v1.24.6

    Т.к. в YandexCloud не реализована установка Istio как аддона, ставим Istio через Helm:

helm repo add istio https://istio-release.storage.googleapis.com/charts
helm repo update istio
kubectl create namespace istio-system
helm install istio-base istio/base -n istio-system --version 1.17.1
helm install istiod istio/istiod -n istio-system --wait --version 1.17.1
kubectl create namespace istio-ingress
kubectl label namespace istio-ingress istio-injection=enabled
helm install istio-ingress istio/gateway -n istio-ingress --wait --version 1.17.1

Проверяем

helm ls -n istio-system

NAME      	NAMESPACE   	REVISION	UPDATED                                	STATUS  	CHART        	APP VERSION
istio-base	istio-system	1       	2023-03-27 13:07:31.697794732 +0300 +03	deployed	base-1.17.1  	1.17.1     
istiod    	istio-system	1       	2023-03-27 13:07:48.77468327 +0300 +03 	deployed	istiod-1.17.1	1.17.1     


helm status istiod -n istio-system

NNAME: istiod
LAST DEPLOYED: Sat Mar 25 18:32:16 2023
NAMESPACE: istio-system
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
"istiod" successfully installed!

To learn more about the release, try:
  $ helm status istiod
  $ helm get all istiod

Next steps:
  * Deploy a Gateway: https://istio.io/latest/docs/setup/additional-setup/gateway/
  * Try out our tasks to get started on common configurations:
    * https://istio.io/latest/docs/tasks/traffic-management
    * https://istio.io/latest/docs/tasks/security/
    * https://istio.io/latest/docs/tasks/policy-enforcement/
    * https://istio.io/latest/docs/tasks/policy-enforcement/
  * Review the list of actively supported releases, CVE publications and our hardening guide:
    * https://istio.io/latest/docs/releases/supported-releases/
    * https://istio.io/latest/news/security/
    * https://istio.io/latest/docs/ops/best-practices/security/

For further documentation see https://istio.io website

Tell us how your install/upgrade experience went at https://forms.gle/hMHGiwZHPU7UQRWe9


kubectl get deployments -n istio-system --output wide

NAME     READY   UP-TO-DATE   AVAILABLE   AGE     CONTAINERS   IMAGES                         SELECTOR
istiod   1/1     1            1           2m41s   discovery    docker.io/istio/pilot:1.17.1   istio=pilot



Подготовка Kubernetes кластера | Задание со star

    Автоматизируйте создание Kubernetes кластера
    Кластер должен разворачиваться после запуска pipeline в GitLab

Подготовлен следующие манифесты для автоматического разворачивания кластера с Istio после запуска pipeline в GitLab
.gitlab-ci.yaml,

include: '.variables.yaml'

stages:
- k8s-ycloud
- dismiss

.base_yc:
before_script:
# Install YC CLI.
    - curl https://storage.yandexcloud.net/yandexcloud-yc/install.sh | bash -s -- -a && cp /root/yandex-cloud/bin/yc /usr/bin/
    - |
      cat <<EOF >> /root/.config/yandex-cloud/config.yaml
      current: default
      profiles:
        default:
          token: ${CI_YC_TOKEN}
          cloud-id: ${CI_CLOUD_ID}
          folder-id: ${CI_CLOUD_ID}
          compute-default-zone: ru-central1-c
      EOF
    - cat /root/.config/yandex-cloud/config.yaml


.managed_kubernetes_create: &managed_kubernetes_create
# Create yc managed kubernetes cluster
- >-
  yc managed-kubernetes cluster create ${CI_CLUSTER_NAME} --network-id ${CI_NETWORK_ID}
  --zone ${CI_ZONE} --subnet-id ${CI_SUBNET_ID} --public-ip --release-channel RAPID
  --version ${CI_K8S_VERSION} --node-service-account-name ${CI_NODE_SERVICE_ACCOUNT_NAME}
  --service-account-id ${CI_SERVICE_ACCOUNT_ID} --cloud-id ${CI_CLOUD_ID}
  --folder-id ${CI_FOLDER_ID} --token ${CI_YC_TOKEN} --no-user-output --enable-network-policy

.managed_node_group_create: &managed_node_group_create
- >-
  yc managed-kubernetes node-group create ${CI_NODE_POOL} --cluster-name ${CI_CLUSTER_NAME}
  --auto-scale min=${CI_AUTO_SCALE_INITIAL},max=${CI_AUTO_SCALE_MAX},initial=${CI_AUTO_SCALE_INITIAL}
  --platform ${CI_NODE_PLATFORM} --memory ${CI_NODE_MEMORY} --cores ${CI_NODE_CORES}
  --disk-type ${CI_NODE_DISK_TYPE} --disk-size ${CI_NODE_DISK_SIZE} --version ${CI_K8S_VERSION}
  --cloud-id ${CI_CLOUD_ID} --folder-id ${CI_FOLDER_ID} --token ${CI_YC_TOKEN}
  --network-interface subnets=${CI_SUBNET_ID},ipv4-address=nat


.kubectl_install: &kubectl_install
- wget -O /usr/local/bin/kubectl "https://storage.googleapis.com/kubernetes-release/release/${CI_KUBECTL_VER}/bin/linux/amd64/kubectl"
- chmod +x /usr/local/bin/kubectl
- |
      yc managed-kubernetes cluster get-credentials ${CI_CLUSTER_NAME} --context-name default \
  --external --force --cloud-id ${CI_CLOUD_ID} --folder-id ${CI_FOLDER_ID} --token ${CI_YC_TOKEN}
- ls -la /root/.kube/
- cat /root/.kube/config
- kubectl cluster-info --kubeconfig /root/.kube/config

.helm_install: &helm_install
- |
      export HELM_URL="https://get.helm.sh"
  export HELM_TAR_FILE="helm-${CI_HELM_VER}-linux-amd64.tar.gz"
  echo "install HELM ${CI_HELM_VER} from \"${HELM_URL}/${HELM_TAR_FILE}\""
  mkdir -p /tmp/helm
  wget -O "/tmp/${HELM_TAR_FILE}" "${HELM_URL}/${HELM_TAR_FILE}"
  tar -xzvf "/tmp/${HELM_TAR_FILE}" -C /tmp/helm
  mv /tmp/helm/linux-amd64/helm /usr/bin/helm
  rm -rf /tmp/helm
  chmod +x /usr/bin/helm
  helm version

.istio_install: &istio_install
- helm repo add istio https://istio-release.storage.googleapis.com/charts
- helm repo update istio
- kubectl create namespace istio-system || true
- helm upgrade --install istio-base istio/base -n istio-system --version ${CI_ISTIO_VERSION}
- helm ls -n istio-system
- helm upgrade --install istiod istio/istiod -n istio-system --version ${CI_ISTIO_VERSION} --wait
- helm ls -n istio-system
- helm status istiod -n istio-system
- kubectl create namespace istio-ingress || true
- kubectl label namespace istio-ingress istio-injection=enabled
- helm upgrade --install istio-ingress istio/gateway -n istio-ingress --version ${CI_ISTIO_VERSION} --wait

.dismiss:
extends: .base_yc
script:
- yc managed-kubernetes cluster delete ${CI_CLUSTER_NAME} --cloud-id ${CI_CLOUD_ID} --folder-id ${CI_FOLDER_ID} --token ${CI_YC_TOKEN}

k8s-ycloud:
extends: .base_yc
stage: k8s-ycloud
allow_failure: true
script:
- *managed_kubernetes_create
- *managed_node_group_create
- *kubectl_install
- *helm_install
- *istio_install

dismiss:
extends: .dismiss
stage: dismiss
when: manual


-------------------------------------------------------------------------------
 .variables.yaml
 
 variables:
  CI_CLUSTER_NAME: k8s-otus
  CI_NETWORK_ID: enp67lv83b2vf4bjd1fu
  CI_ZONE: ru-central1-a
  CI_SUBNET_ID: e9bmlk8v6q1ggh0iv0m8
  CI_K8S_VERSION: "1.24"
  CI_NODE_SERVICE_ACCOUNT_NAME: tfuser
  CI_SERVICE_ACCOUNT_ID: aje43vf2rvfqf5tahtuf
  CI_CLOUD_ID: aje4mkkkmtnektkhf0kp
  CI_FOLDER_ID: b1gf0t46quh91ma3q0m1
  CI_NODE_POOL: k8s-otus-node-pool
  CI_AUTO_SCALE_MIN: 1
  CI_AUTO_SCALE_MAX: 4
  CI_AUTO_SCALE_INITIAL: 1
  CI_NODE_PLATFORM: standard-v1
  CI_NODE_MEMORY: 4
  CI_NODE_CORES: 2
  CI_NODE_DISK_TYPE: network-hdd
  CI_NODE_DISK_SIZE: 30
  CI_KUBECTL_VER: v1.25.3
  CI_HELM_VER: v3.11.2
  CI_ISTIO_VERSION: 1.17.1
 
4. Continuous Integration | Задание со star

Подготовим pipeline, который будет содержать следующие стадии:

    Сборку Docker образа для каждого из микросервисов

    Push данного образа в Docker Hub В качестве тега образа используем tag коммита, инициирующего сборку (переменная CI_COMMIT_TAG в GitLab CI)

    Создаем файл .build.yaml следующего содержания

build:adservice:
  extends: build
  variables: 
    SERVICE: adservice
    SRVPATH: adservice

build:cartservice:
  extends: build
  variables: 
    SERVICE: cartservice
    SRVPATH: cartservice

build:checkoutservice:
  extends: build
  variables: 
    SERVICE: checkoutservice
    SRVPATH: checkoutservice

build:currencyservice:
  extends: build
  variables: 
    SERVICE: currencyservice
    SRVPATH: currencyservice
    
build:emailservice:
  extends: build
  variables: 
    SERVICE: emailservice
    SRVPATH: emailservice

build:frontend:
  extends: build
  variables: 
    SERVICE: frontend
    SRVPATH: frontend

build:loadgenerator:
  extends: build
  variables: 
    SERVICE: loadgenerator
    SRVPATH: loadgenerator
    
build:paymentservice:
  extends: build
  variables: 
    SERVICE: paymentservice
    SRVPATH: paymentservice
    
build:productcatalogservice:
  extends: build
  variables: 
    SERVICE: productcatalogservice
    SRVPATH: productcatalogservice
    
build:recommendationservice:
  extends: build
  variables: 
    SERVICE: recommendationservice
    SRVPATH: recommendationservice
    
build:shippingservice:
  extends: build
  variables: 
    SERVICE: shippingservice
    SRVPATH: shippingservice
    
...

    И добавляем в манифест pipeline описание джобы билда

build:
  extends: .docker
  stage: build
  allow_failure: true
  before_script:
    - docker login --username oauth --password ${CI_YC_TOKEN} cr.yandex
  script:
    - cd src/${SRVPATH}
    - docker build . -f Dockerfile -t cr.yandex/${CI_CR_YANDEX_ID}/${SERVICE}:${CI_COMMIT_SHORT_SHA}
    - docker push cr.yandex/${CI_CR_YANDEX_ID}/${SERVICE}:${CI_COMMIT_SHORT_SHA}

Образы у нас успешно собираются и также удачно у нас заканчивается бесплатный лимит времени на раннерах Гитлаба. Если что, придется отложить запуск CI/CD до следующего месяца :)
img_1.png

img.png

img_2.png



5. GitOps

Подготовка

    https://github.com/fluxcd/helm-operator/tree/master/chart/helm-operator

    Добавим официальный репозиторий Flux

helm repo add fluxcd https://charts.fluxcd.io
helm repo update fluxcd

    Установим CRD, добавляющую в кластер новый ресурс - HelmRelease:

kubectl apply -f https://raw.githubusercontent.com/fluxcd/helm-operator/1.4.4/deploy/crds.yaml

    Произведем установку Flux в кластер, в namespace flux

kubectl create namespace flux
helm upgrade --install flux fluxcd/flux -f flux.values.yaml --namespace flux

NOTES:
Get the Git deploy key by either (a) running

  kubectl -n flux logs deployment/flux | grep identity.pub | cut -d '"' -f2

or by (b) installing fluxctl through
https://fluxcd.io/legacy/flux/references/fluxctl/#installing-fluxctl
and running:

  fluxctl identity --k8s-fwd-ns flux

---
**Flux v1 is deprecated, please upgrade to v2 as soon as possible!**

    Наконец, добавим в свой профиль GitLab публичный ssh-ключ, при помощи которого flux получит доступ к нашему git-репозиторию.

kubectl -n flux logs deployment/flux | grep identity.pub | cut -d '"' -f2

    Установим Helm operator:

helm upgrade --install helm-operator fluxcd/helm-operator -f helm-operator.values.yaml --namespace flux

Release "helm-operator" does not exist. Installing it now.
NAME: helm-operator
LAST DEPLOYED: Sun Mar 19 18:04:06 2023
NAMESPACE: flux
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Flux Helm Operator docs https://fluxcd.io/legacy/helm-operator

Example:

AUTH_VALUES=$(cat <<-END
usePassword: true
password: "redis_pass"
usePasswordFile: true
END
)

kubectl create secret generic redis-auth --from-literal=values.yaml="$AUTH_VALUES"

`cat <<EOF | kubectl apply -f -
apiVersion: helm.fluxcd.io/v1
kind: HelmRelease
metadata:
  name: redis
  namespace: default
spec:
  releaseName: redis
  chart:
    repository: https://kubernetes-charts.storage.googleapis.com
    name: redis
    version: 10.5.7
  valuesFrom:
  - secretKeyRef:
      name: redis-auth
  values:
    master:
      persistence:
        enabled: false
    volumePermissions:
      enabled: true
    metrics:
      enabled: true
    cluster:
      enabled: false
EOF`

watch kubectl get hr

    Установим fluxctl на локальную машину для управления нашим CD инструментом.

curl -s https://fluxcd.io/install.sh | sudo bash

Пришло время проверить корректность работы Flux. Как мы уже знаем, Flux умеет автоматически синхронизировать состояние кластера и репозитория. Это касается не только сущностей HelmRelease , которыми мы будем оперировать для развертывания приложения, но и обыкновенных манифестов. Поместим манифест, описывающий namespace microservices-demo в директорию deploy/namespaces и сделаем push в GitLab:

kubectl get ns | grep microservices-demo  

microservices-demo   Active   4m10s

kubectl logs flux-658b8dd6fc-kllrm -n flux | grep microservices-demo 

...
ts=2023-03-27T10:35:03.326407059Z caller=sync.go:608 method=Sync cmd="kubectl apply -f -" took=1.220181903s err=null output="namespace/microservices-demo created"
...

Мы подобрались к сущностям, которыми управляет helm-operator - HelmRelease. Для описания сущностей такого вида создадим отдельную директорию deploy/releases и поместим туда файл frontend.yaml с описанием конфигурации релиза.

apiVersion: helm.fluxcd.io/v1
kind: HelmRelease
metadata:
  name: frontend
  namespace: microservices-demo
  annotations:
    fluxcd.io/ignore: "false"
    # fluxcd.io/automated: "true" Аннотация разрешает автоматическое обновление релиза в  Kubernetes кластере
    # в случае изменения версии Docker образа в Registry
    fluxcd.io/automated: "true"
    # Указываем Flux следить за обновлениями конкретных Docker образов
    # в Registry.
    # Новыми считаются только образы, имеющие версию выше текущей и
    # отвечающие маске семантического версионирования ~0.0 (например,
    # 0.0.1, 0.0.72, но не 1.0.0)
    flux.weave.works/tag.chart-image: semver:~v0.0
spec:
  releaseName: frontend
  helmVersion: v3
  # Helm chart, используемый для развертывания релиза. В нашем случае
  #указываем git-репозиторий, и директорию с чартом внутри него
  chart:
    git: git@gitlab.com:lookus84/microservices-demo.git
    ref: main
    path: deploy/charts/frontend
  # Переопределяем переменные Helm chart. В дальнейшем Flux может сам
  # переписывать эти значения и делать commit в git-репозиторий (например,
  # изменять тег Docker образа при его обновлении в Registry)
  values:
    image:
      repository: cr.yandex/crpn6n5ssda7s8tdsdf5/frontend
      tag: v0.0.1

HelmRelease | Проверка

    Ловим ошибку в логах helm-operator про kind: ServiceMonitor при деплое из чарта deploy/charts/frontend и поэтому ставим дополнительно prometheus-operator через kubectl apply из офф. репозитория (Bring`em on!)

cd ./kube-prometheus
# Create the namespace and CRDs, and then wait for them to be available before creating the remaining resources
# Note that due to some CRD size we are using kubectl server-side apply feature which is generally available since kubernetes 1.22.
# If you are using previous kubernetes versions this feature may not be available and you would need to use kubectl create instead.
kubectl apply --server-side -f manifests/setup
kubectl wait \
--for condition=Established \
--all CustomResourceDefinition \
--namespace=monitoring
kubectl apply -f manifests/

    Инициируем синхронизацию вручную

fluxctl --k8s-fwd-ns flux sync

Убедимся что HelmRelease для микросервиса frontend появился в кластере:

kubectl get helmrelease -n microservices-demo

NAME       RELEASE    PHASE       RELEASESTATUS   MESSAGE                                                                       AGE
frontend   frontend   Succeeded   deployed        Release was successful for Helm release 'frontend' in 'microservices-demo'.   18m


helm list -n microservices-demo
NAME    	NAMESPACE         	REVISION	UPDATED                                	STATUS  	CHART          	APP VERSION
frontend	microservices-demo	1       	2023-03-27 12:28:20.473164751 +0000 UTC	deployed	frontend-0.21.0	1.16.0   

Обновление образа

Т.к. у нас закончилось время на раннерах GitLab вместо сборки нам остается только тегирование образа до версии v0.0.2

docker tag cr.yandex/crpn6n5ssda7s8tdsdf5/frontend:41ff6a8d registry.gitlab.com/dpnev/microservices-demo/frontend:v0.0.5
docker push registry.gitlab.com/dpnev/microservices-demo/frontend:v0.0.5

Дождемся автоматического обновления релиза в Kubernetes кластере

watch helm history frontend -n microservices-demo                                                                    

REVISION        UPDATED                         STATUS          CHART           APP VERSION     DESCRIPTION     
1               Sun Mar 19 15:58:09 2023        superseded      frontend-0.21.0 1.16.0          Install complete
2               Sun Mar 19 17:37:43 2023        deployed        frontend-0.21.0 1.16.0          Upgrade complete

Проверим, изменилось ли что-либо в git-репозитории (в частности, в файле deploy/releases/frontend.yaml ) img_3.png
Обновление Helm chart

    Попробуем внести изменения в Helm chart frontend и поменять имя deployment на frontend-hipster
    Сделаем push измененного Helm chart в GitLab и понаблюдаем за процессом

│ts=2023-03-27T13:22:03.381530457Z caller=helm.go:69 component=helm version=v3 info="preparing upgrade for frontend" targetNamespace=microservices-demo release=frontend
ts=2023-03-27T13:22:03.418697642Z caller=helm.go:69 component=helm version=v3 info="resetting values to the chart's original version" targetNamespace=microservices-demo release=frontend
ts=2023-03-27T13:22:04.33451757Z caller=helm.go:69 component=helm version=v3 info="performing update for frontend" targetNamespace=microservices-demo release=frontend
ts=2023-03-27T13:22:04.348932428Z caller=helm.go:69 component=helm version=v3 info="creating upgraded release for frontend" targetNamespace=microservices-demo release=frontend
ts=2023-03-27T13:22:04.392185274Z caller=helm.go:69 component=helm version=v3 info="checking 5 resources for changes" targetNamespace=microservices-demo release=frontend
ts=2023-03-27T13:22:04.401779316Z caller=helm.go:69 component=helm version=v3 info="Looks like there are no changes for Service \"frontend\"" targetNamespace=microservices-demo release=frontend
ts=2023-03-27T13:22:04.429168343Z caller=helm.go:69 component=helm version=v3 info="Created a new Deployment called \"frontend-hipster\" in microservices-demo\n" targetNamespace=microservices-demo release=frontend
ts=2023-03-27T13:22:04.663116985Z caller=helm.go:69 component=helm version=v3 info="Deleting \"frontend\" in microservices-demo..." targetNamespace=microservices-demo release=frontend
ts=2023-03-27T13:22:04.731519077Z caller=helm.go:69 component=helm version=v3 info="updating status for upgraded release for frontend" targetNamespace=microservices-demo release=frontend
ts=2023-03-27T13:22:04.914229531Z caller=release.go:364 component=release release=frontend targetNamespace=microservices-demo resource=microservices-demo:helmrelease/frontend helmVersion=v3 info="upgrade succeeded" revision=598c6e2e16835f9fdc4e1ddd351fbfe8cd152488 phase=upgrade


Самостоятельное задание

Добавим манифесты HelmRelease для всех микросервисов входящих в состав HipsterShop
Из-за недоступности сборки приходится заморрочиться с tags & registry images:

#docker tag cr.yandex/crp9rjco6hqoojrh0kf0/frontend:41ff6a8d registry.gitlab.com/lookus84/microservices-demo/frontend:v0.0.2
#docker push registry.gitlab.com/lookus84/microservices-demo/frontend:v0.0.2
docker pull cr.yandex/crp9rjco6hqoojrh0kf0/adservice:41ff6a8d
docker tag cr.yandex/crp9rjco6hqoojrh0kf0/adservice:41ff6a8d registry.gitlab.com/lookus84/microservices-demo/adservice:v0.0.1
docker push registry.gitlab.com/lookus84/microservices-demo/adservice:v0.0.1

docker pull cr.yandex/crp9rjco6hqoojrh0kf0/cartservice:41ff6a8d
docker tag cr.yandex/crp9rjco6hqoojrh0kf0/cartservice:41ff6a8d registry.gitlab.com/lookus84/microservices-demo/cartservice:v0.0.1
docker push registry.gitlab.com/lookus84/microservices-demo/cartservice:v0.0.1

docker pull cr.yandex/crp9rjco6hqoojrh0kf0/checkoutservice:41ff6a8d
docker tag cr.yandex/crp9rjco6hqoojrh0kf0/checkoutservice:41ff6a8d registry.gitlab.com/lookus84/microservices-demo/checkoutservice:v0.0.1
docker push registry.gitlab.com/lookus84/microservices-demo/checkoutservice:v0.0.1

docker pull cr.yandex/crp9rjco6hqoojrh0kf0/currencyservice:41ff6a8d
docker tag cr.yandex/crp9rjco6hqoojrh0kf0/currencyservice:41ff6a8d registry.gitlab.com/lookus84/microservices-demo/currencyservice:v0.0.1
docker push registry.gitlab.com/lookus84/microservices-demo/currencyservice:v0.0.1

docker pull cr.yandex/crp9rjco6hqoojrh0kf0/emailservice:41ff6a8d
docker tag cr.yandex/crp9rjco6hqoojrh0kf0/emailservice:41ff6a8d registry.gitlab.com/lookus84/microservices-demo/emailservice:v0.0.1
docker push registry.gitlab.com/lookus84/microservices-demo/emailservice:v0.0.1

docker pull cr.yandex/crp9rjco6hqoojrh0kf0/loadgenerator:41ff6a8d
docker tag cr.yandex/crp9rjco6hqoojrh0kf0/loadgenerator:41ff6a8d registry.gitlab.com/lookus84/microservices-demo/loadgenerator:v0.0.1
docker push registry.gitlab.com/lookus84/microservices-demo/loadgenerator:v0.0.1

docker pull cr.yandex/crp9rjco6hqoojrh0kf0/paymentservice:41ff6a8d
docker tag cr.yandex/crp9rjco6hqoojrh0kf0/paymentservice:41ff6a8d registry.gitlab.com/lookus84/microservices-demo/paymentservice:v0.0.1
docker push registry.gitlab.com/lookus84/microservices-demo/paymentservice:v0.0.1

docker pull cr.yandex/crp9rjco6hqoojrh0kf0/productcatalogservice:41ff6a8d
docker tag cr.yandex/crp9rjco6hqoojrh0kf0/productcatalogservice:41ff6a8d registry.gitlab.com/lookus84/microservices-demo/productcatalogservice:v0.0.1
docker push registry.gitlab.com/lookus84/microservices-demo/productcatalogservice:v0.0.1

docker pull cr.yandex/crp9rjco6hqoojrh0kf0/recommendationservice:41ff6a8d
docker tag cr.yandex/crp9rjco6hqoojrh0kf0/recommendationservice:41ff6a8d registry.gitlab.com/lookus84/microservices-demo/recommendationservice:v0.0.1
docker push registry.gitlab.com/lookus84/microservices-demo/recommendationservice:v0.0.1

docker pull cr.yandex/crp9rjco6hqoojrh0kf0/shippingservice:41ff6a8d
docker tag cr.yandex/crp9rjco6hqoojrh0kf0/shippingservice:41ff6a8d registry.gitlab.com/lookus84/microservices-demo/shippingservice:v0.0.1
docker push registry.gitlab.com/lookus84/microservices-demo/shippingservice:v0.0.1

Проверим, что все микросервисы успешно развернулись в Kubernetes кластере:

kubectl delete ns microservices-demo
kubectl get pods -n microservices-demo -w

NAME                                    READY   STATUS     RESTARTS   AGE
adservice-65778c4cf6-t8jxl              1/1     Running    0          9m51s
checkoutservice-7d765687d8-fd967        1/1     Running    0          9m52s
currencyservice-86d9454dbd-x4vvn        1/1     Running    0          9m44s
emailservice-8587ccd6bd-p54qh           1/1     Running    0          9m51s
frontend-844976646c-h8gzn               1/1     Running    0          9m44s
loadgenerator-57d4cb45b4-dzmj7          0/1     Init:0/1   0          9m43s
paymentservice-7ffc98fdd8-9blnn         1/1     Running    0          9m35s
productcatalogservice-66944f6b6-xs2q8   1/1     Running    0          9m41s
recommendationservice-5d87f6fd-nzdsw    1/1     Running    0          9m34s
shippingservice-597478d856-vw527        1/1     Running    0          9m34s

Для loadgenerator не получается пройти успешное выполнение init контейнера, т.к. frontend отдает 500. Смотрим логи frontend

"error":"could not retrieve cart: rpc error: code = Unavailable desc = connection error: desc = \"transport: Error while dialing: dial tcp: lookup cartservice on 10.96.128.2:53: no such host

cartservice не задеплоился по каким-то причинам, смотрим лог helm-operator:

kubectl logs helm-operator-55769d46b8-wrjln -n flux | grep cartservice | tail -1

ts=2023-03-20T20:04:50.936076846Z caller=release.go:85 component=release release=cartservice targetNamespace=microservices-demo resource=microservices-demo:helmrelease/cartservice helmVersion=v3 error="failed to prepare chart for release: no cached repository for helm-manager-1067d9c6027b8c3f27b49e40521d64be96ea412858d8e45064fa44afd3966ddc found. (try 'helm repo update'): open /root/.cache/helm/repository/helm-manager-1067d9c6027b8c3f27b49e40521d64be96ea412858d8e45064fa44afd3966ddc-index.yaml: no such file or directory"

Оказывается чарт redis сменил свое расположение с "https://kubernetes-charts.storage.googleapis.com/" на "https://charts.helm.sh/stable/" Правим соответственно deploy/charts/cartservice/Chart.yaml Проверяем

kubectl get pods -n microservices-demo

Все сервисы поднялись!!!
Canary deployments с Flagger и Istio
Flagger.

Flagger - оператор Kubernetes, созданный для автоматизации canary deployments. Flagger может использовать: Istio, Linkerd, App Mesh или nginx для маршрутизации трафика Prometheus для анализа канареечного релиза
Установка Flagger

helm repo add flagger https://flagger.app
helm repo update flagger

    Установка CRD для Flagger

kubectl apply -f https://raw.githubusercontent.com/weaveworks/flagger/master/artifacts/flagger/crd.yaml

    Установка flagger с указанием использовать Istio: Установим сначала prometheus

helm install prometheus -n istio-system prometheus-community/prometheus

helm upgrade --install flagger flagger/flagger \
--namespace=istio-system \
--set crd.create=false \
--set meshProvider=istio \
--set metricsServer=http://prometheus-server:80

Istio | Sidecar Injection

Изменим созданное ранее описание namespace microservices-demo Последняя строка указывает на необходимость добавить в каждый pod sidecar контейнер с envoy proxy.

apiVersion: v1
kind: Namespace
metadata:
  name: microservices-demo
  labels:
    istio-injection: enabled

microservices-demo   Active   5h57m   fluxcd.io/sync-gc-mark=sha256.g7l4ABXyUdLLCK94FOGVhAixrw5ameYDiAjb02w1Q2k,istio-injection=enabled,kubernetes.io/metadata.name=microservices-demo

После синхронизации проверку можно выполнить командой

kubectl get ns microservices-demo --show-labels

Самый простой способ добавить sidecar контейнер в уже запущенные pod - удалить их:

kubectl delete pods --all -n microservices-demo

Или сразу весь namespace, у нас же GitOps :)

kubectl delete ns microservices-demo

После этого можно проверить, что контейнер с названием istioproxy появился внутри каждого pod:

kubectl describe pod -l app=frontend -n microservices-demo

...
  istio-proxy:
    Container ID:  containerd://f37dfca8e6fa992c56b6a7810064768b1e0394e4893da6087b6e19f154d78b13
    Image:         docker.io/istio/proxyv2:1.17.1
    Image ID:      docker.io/istio/proxyv2@sha256:2152aea5fbe2de20f08f3e0412ad7a4cd54a492240ff40974261ee4bdb43871d
    Port:          15090/TCP
    Host Port:     0/TCP
    Args:
      proxy
      sidecar
      --domain
      $(POD_NAMESPACE).svc.cluster.local
      --proxyLogLevel=warning
      --proxyComponentLogLevel=misc:error
      --log_output_level=default:info
      --concurrency
      2
    State:          Running
      Started:      Mon, 27 Mar 2023 16:53:07 +0300
...

Доступ к frontend. Istio | VirtualService. Istio | Gateway

    https://istio.io/docs/concepts/traffic-management/#virtual-services https://istio.io/docs/concepts/traffic-management/#gateways

На текущий момент у нас отсутствует ingress и мы не можем получить доступ к frontend снаружи кластера. В то же время Istio в качестве альтернативы классическому ingress предлагает свой набор абстракций. Чтобы настроить маршрутизацию трафика к приложению с использованием Istio, нам необходимо добавить ресурсы VirtualService и Gateway Создадим директорию deploy/istio и поместим в нее следующие манифесты:

    frontend-vs.yaml
    frontend-gw.yaml

Созданный Gateway можно увидеть следующим образом:

kubectl get gateway -n microservices-demo

NAME               AGE
frontend-gateway   17m

Для доступа снаружи нам понадобится EXTERNAL-IP (INGRESS_HOST) сервиса istio-ingress:

export INGRESS_NAME=istio-ingress
export INGRESS_NS=istio-ingress 
kubectl get svc "$INGRESS_NAME" -n "$INGRESS_NS"
export INGRESS_HOST=$(kubectl -n "$INGRESS_NS" get service "$INGRESS_NAME" -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
export INGRESS_PORT=$(kubectl -n "$INGRESS_NS" get service "$INGRESS_NAME" -o jsonpath='{.spec.ports[?(@.name=="http2")].port}')
export SECURE_INGRESS_PORT=$(kubectl -n "$INGRESS_NS" get service "$INGRESS_NAME" -o jsonpath='{.spec.ports[?(@.name=="https")].port}')
echo "INGRESS_HOST=$INGRESS_HOST, INGRESS_PORT=$INGRESS_PORT"
echo "INGRESS_HOST=$INGRESS_HOST, INGRESS_PORT=$SECURE_INGRESS_PORT"

img_4.png
Istio | Самостоятельное задание

В нашей ситуации ресурсы Gateway и VirtualService логически являются частью инфраструктурного кода, описывающего окружение микросервиса frontend. Поэтому, оправданно будет перенести манифесты в Helm chart. Дополним Helm chart frontend манифестами gateway.yaml и virtualService.yaml (на самом деле они уже там присутствовали из исходного репозитория express42). Правим только gateway.yaml в соответствии с документацией istio, т.к. мы устанавливали Istio, using Helm following the standard documentation

...
# The selector matches the ingress gateway pod labels.
# If you installed Istio using Helm following the standard documentation, this would be "istio=ingress"
spec:
selector:
#istio: ingressgateway <- remove
istio: ingress # <- add
...

Оригинальные манифесты удалим вместе с директорией deploy/istio.
Flagger | Canary

Перейдем непосредственно к настройке канареечных релизов. Добавим в Helm chart frontend еще один файл - canary.yaml В нем будем хранить описание стратегии, по которой необходимо обновлять данный микросервис.

    Узнать подробнее о Canary Custom Resource можно по ссылке https://docs.flagger.app/how-it-works#canary-custom-resource

Проверим, что Flagger инициализировал canary ресурс frontend:

kubectl get canary -n microservices-demo

NAME       STATUS         WEIGHT   LASTTRANSITIONTIME
frontend   Initializing   0        2023-03-21T07:20:31

Обновил pod, добавив ему к названию постфикс primary:

kubectl get pods -n microservices-demo -l app=frontend-primary

NAME                                        READY   STATUS    RESTARTS   AGE
frontend-hipster-primary-79544c5d7b-xvcl5   2/2     Running   0          117s

Попробуем провести релиз. Соберите новый образ frontend с тегом v0.0.6 и сделайте push в Registry. Через некоторое время в выводе kubectl describe canary frontend -n microservices-demo мы сможем наблюдать следующую картину:

docker build ./ -t registry.gitlab.com/dpnev/microservices-demo/frontend:v0.0.9
docker push registry.gitlab.com/dpnev/microservices-demo/frontend:v0.0.9

kubectl describe canary frontend -n microservices-demo

  Type     Reason  Age                 From     Message
  ----     ------  ----                ----     -------
  Warning  Synced  20m                 flagger  frontend-primary.microservices-demo not ready: waiting for rollout to finish: observed deployment generation less than desired generation
  Normal   Synced  20m (x2 over 20m)   flagger  all the metrics providers are available!
  Normal   Synced  20m                 flagger  Initialization done! frontend.microservices-demo
  Normal   Synced  17m                 flagger  Starting canary analysis for frontend.microservices-demo
  Warning  Synced  17m                 flagger  canary deployment frontend.microservices-demo not ready: waiting for rollout to finish: 1 old replicas are pending termination
  Normal   Synced  13m (x2 over 18m)   flagger  New revision detected! Scaling up frontend.microservices-demo
  Normal   Synced  12m (x2 over 17m)   flagger  Advance frontend.microservices-demo canary weight 10
  Normal   Synced  12m (x2 over 16m)   flagger  Advance frontend.microservices-demo canary weight 20
  Normal   Synced  11m (x2 over 16m)   flagger  Advance frontend.microservices-demo canary weight 30
  Normal   Synced  11m (x2 over 15m)   flagger  Advance frontend.microservices-demo canary weight 40
  Normal   Synced  10m (x2 over 15m)   flagger  Advance frontend.microservices-demo canary weight 50
  Normal   Synced  10m                 flagger  Copying frontend.microservices-demo template spec to frontend-primary.microservices-demo
  Normal   Synced  9m3s (x6 over 14m)  flagger  (combined from similar events): Promotion completed! Scaling down frontend.microservices-demo

kubectl get canaries -n microservices-demo 

NAME       STATUS      WEIGHT   LASTTRANSITIONTIME
frontend   Succeeded   0        2023-03-21T10:09:02Z

Ссылка на репозиторий - https://gitlab.com/dpnev/microservices-demo/
Полезное:

    https://cloud.yandex.ru/docs/security/domains/kubernetes
    https://istio.io/latest/docs/setup/install/helm/

yc managed-kubernetes cluster stop k8s-4otus

yc managed-kubernetes cluster start k8s-4otus

Полезные команды fluxctl


    синхронизация вручную

fluxctl --k8s-fwd-ns flux sync

    переменная окружения, указывающая на namespace, в который установлен flux (альтернатива ключу --k8s-fwd-ns )

export FLUX_FORWARD_NAMESPACE=flux

    принудительно запустить синхронизацию состояния git репозитория с кластером (при условии установленной переменной FLUX_FORWARD_NAMESPACE)

fluxctl sync

    посмотреть все workloads, которые находятся в зоне видимости flux

fluxctl list-workloads -a

    посмотреть все Docker образы, используемые в кластере (в namespace microservicesdemo)

fluxctl list-images -n microservices-demo

    выключить/включить автоматизацию управления workload

fluxctl deautomate
fluxctl automate 

    установить всем сервисам в workload microservices-demo:helmrelease/frontend политику обновления образов из Registry на базе семантического версионирования c маской 0.1.*

fluxctl policy -w microservices-demo:helmrelease/frontend --tag-all='semver:~0.1'

    принудительно инициировать сканирование Registry на предмет наличия свежих Docker образов

fluxctl release --workload=microservicesdemo:helmrelease/frontend --update-all-images



