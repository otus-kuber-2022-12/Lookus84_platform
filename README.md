 Лекция 5. ДЗ: Volumes, Storages, StatefulSetStatefulSet
  
 Подготовка
В корне вашего репозитория создайте каталог kubernetes-volumes .
Все файлы этого ДЗ помещайте в этот каталог.


Установка и запуск kind
kind - инструмент для запуска Kuberenetes при помощи Docker контейнеров.


Запуск
В этом ДЗ мы развернем StatefulSet c - локальным S3 хранилищем

Применение StatefulSet
Закомитьте конфигурацию под именем minio-statefulset.yaml .
kubectl может брать конфигурацию по HTTP:
В результате применения конфигурации должно произойти следующее:

Запуститься под с MinIO
(kubernetes-volumes)kubectl get pod
NAME      READY   STATUS    RESTARTS   AGE
minio-0   1/1     Running   0          5m51s


Создаться PVC
(kubernetes-volumes)kubectl get pvc
NAME           STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-minio-0   Bound    pvc-92b38749-a57e-4708-bf69-7c64dfeeb29c   10Gi       RWO            standard       6m20s

Динамически создаться PV на этом PVC с помощью дефолотного StorageClass 
(kubernetes-volumes)kubectl get pv
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                  STORAGECLASS   REASON   AGE
pvc-92b38749-a57e-4708-bf69-7c64dfeeb29c   10Gi       RWO            Delete           Bound    default/data-minio-0   standard                5m33s


Применение Headless Service
Для того, чтобы наш StatefulSet был доступен изнутри кластера, создадим Headless Service
Закомитьте конфигурацию под именем minio-headless-service.yaml

Проверка работы MinIO
Проверить работу Minio можно с помощью консольного клиента .
Также для проверки ресурсов k8s помогут команды:
mc
kubectl get statefulsets
kubectl get pods
kubectl get pvc
kubectl get pv
kubectl describe <resource> <resource_name>

Создадим внутри пода в директории, в которую смонтирован каталог файловой системы хоста, файл 1.txt

kubernetes-volumes)kubectl exec  -it minio-0  -- /bin/sh
@minio-0:/data$ touch 1.txt

Проверим, какой каталог смонтирован в под minio
(kubernetes-volumes)kubectl get pv -o yaml
apiVersion: v1
items:
- apiVersion: v1
  kind: PersistentVolume
  metadata:
    annotations:
      pv.kubernetes.io/provisioned-by: rancher.io/local-path
    creationTimestamp: "2023-01-22T13:59:57Z"
    finalizers:
    - kubernetes.io/pv-protection
    name: pvc-92b38749-a57e-4708-bf69-7c64dfeeb29c
    resourceVersion: "1052"
    uid: 56d5c9fd-c641-446c-9929-71bb5d49a1c2
  spec:
    accessModes:
    - ReadWriteOnce
    capacity:
      storage: 10Gi
    claimRef:
      apiVersion: v1
      kind: PersistentVolumeClaim
      name: data-minio-0
      namespace: default
      resourceVersion: "1034"
      uid: 92b38749-a57e-4708-bf69-7c64dfeeb29c
    hostPath:
      path: /var/local-path-provisioner/pvc-92b38749-a57e-4708-bf69-7c64dfeeb29c_default_data-minio-0
      type: DirectoryOrCreate
    nodeAffinity:
      required:
        nodeSelectorTerms:
        - matchExpressions:
          - key: kubernetes.io/hostname
            operator: In
            values:
            - kind-control-plane
    persistentVolumeReclaimPolicy: Delete
    storageClassName: standard
    volumeMode: Filesystem
  status:
    phase: Bound
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""


Посмотрим, создался ли этот файл внутри контейнера kind

docker ps
CONTAINER ID   IMAGE                  COMMAND                  CREATED          STATUS          PORTS                       NAMES
ae5aa00976a5   kindest/node:v1.25.3   "/usr/local/bin/entr…"   42 minutes ago   Up 42 minutes   127.0.0.1:34559->6443/tcp   kind-control-plane

(kubernetes-volumes)docker exec -it ae5aa00976a5 /bin/sh
# cd /var/local-path-provisioner/pvc-92b38749-a57e-4708-bf69-7c64dfeeb29c_default_data-minio-0
# ls -la
total 12
drwxrwxrwx 3 root root 4096 Jan 22 14:11 .
drwxr-xr-x 3 root root 4096 Jan 22 13:59 ..
drwxr-xr-x 5 root root 4096 Jan 22 14:00 .minio.sys
-rw-r--r-- 1 root root    0 Jan 22 14:11 1.txt

Задание со *.

Создадим секрет 
(kubernetes-volumes)kubectl apply -f miniosecret.yaml 
secret/mysecret created


(kubernetes-volumes)kubectl get secret
NAME       TYPE     DATA   AGE
mysecret   Opaque   2      3m25s


подключим его в стейтфуллсет
---------------------------------
        image: minio/minio:RELEASE.2019-07-10T00-34-56Z
        envFrom:
        - secretRef:
            name: mysecret
 ----------------------------------
 
 (kubernetes-volumes)kubectl apply -f minio-statefulset-with-secret.yaml 
statefulset.apps/minio configured


посмотрим env  в поде
(kubernetes-volumes)kubectl exec  -it minio-0  -- /bin/sh
/ # env
KUBERNETES_SERVICE_PORT=443
KUBERNETES_PORT=tcp://10.96.0.1:443
HOSTNAME=minio-0
MINIO_ACCESS_KEY_FILE=access_key
SHLVL=1
HOME=/root
MINIO_ACCESS_KEY=minio
MINIO_UPDATE=off
TERM=xterm
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
KUBERNETES_PORT_443_TCP_PORT=443
KUBERNETES_PORT_443_TCP_PROTO=tcp
MINIO_SECRET_KEY_FILE=secret_key
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
MINIO_SECRET_KEY=minio123
KUBERNETES_SERVICE_HOST=10.96.0.1
PWD=/


Видим, что данные из секрета появились.


kind delete cluster
