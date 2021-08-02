## Laboratorio01 Kubernetes*

>Elementos necesarios para el desarrollo del Laboratorio01:

- Ubuntu version 20.04.2.0 (puede ser una version inferior o superior)
- Kubernetes (kubectl)
- Minikube 

Pasos a seguir para la creacion del Laboratio01 Kubernetes:

>1. Descargar la kubectl (cliente de kubernetes) en Linux(Ubuntu):

`````
$ curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.21.0/bin/windows/amd64/kubectl.exe
 
 % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 45.6M  100 45.6M    0     0  4800k      0  0:00:09  0:00:09 --:--:-- 5286k
`````
>2. Habilita los permisos de ejecución del binario kubectl.
`````
chmod +x ./kubectl
`````
>3. Mueve el binario dentro de tu PATH.
`````
sudo mv ./kubectl /usr/local/bin/kubectl
`````
>4. Comprueba que la versión que se ha instalado es la más reciente.
`````
$ kubectl version --client=true

Client Version: version.Info{Major:"1", Minor:"21", GitVersion:"v1.21.2", GitCommit:"092fbfbf53427de67cac1e9fa54aaa09a28371d7", GitTreeState:"clean", BuildDate:"2021-06-16T12:59:11Z", GoVersion:"go1.16.5", Compiler:"gc", Platform:"windows/amd64"}
`````
>5. Nos muestra los nodos del cluster de Kubernetes
`````
alberto@alberto-VirtualBox:~$ kubectl get nodes
NAME       STATUS   ROLES                  AGE     VERSION
minikube   Ready    control-plane,master   4m32s   v1.21.2
`````
>6. Nos muestra Los contextos de kubectl
`````
alberto@alberto-VirtualBox:~$ kubectl config get-contexts
CURRENT   NAME       CLUSTER    AUTHINFO   NAMESPACE
*         minikube   minikube   minikube   default
`````
>7. Nos muestra los namespace por defecto del cluster de kubernetes
`````
alberto@alberto-VirtualBox:~$ kubectl get ns
NAME              STATUS   AGE
default           Active   11m
kube-node-lease   Active   11m
kube-public       Active   11m
kube-system       Active   11m
`````
>8. Nos muestra los pods que estan corriendo en el ns kube-system
`````
alberto@alberto-VirtualBox:~$ kubectl -n kube-system get pods
NAME                               READY   STATUS    RESTARTS   AGE
coredns-558bd4d5db-dn9tc           1/1     Running   0          13m
etcd-minikube                      1/1     Running   0          13m
kube-apiserver-minikube            1/1     Running   0          13m
kube-controller-manager-minikube   1/1     Running   0          13m
kube-proxy-zkqg2                   1/1     Running   0          13m
kube-scheduler-minikube            1/1     Running   0          13m
storage-provisioner                1/1     Running   1          13m
`````
>9. Nos muestra los pods que estan corriendo en el ns kube-system con mas detalles
`````
alberto@alberto-VirtualBox:~$ kubectl -n kube-system get pods -o wide
NAME                               READY   STATUS    RESTARTS   AGE   IP             NODE       NOMINATED NODE   READINESS GATES
coredns-558bd4d5db-dn9tc           1/1     Running   0          15m   172.17.0.2     minikube   <none>           <none>
etcd-minikube                      1/1     Running   0          15m   192.168.49.2   minikube   <none>           <none>
kube-apiserver-minikube            1/1     Running   0          15m   192.168.49.2   minikube   <none>           <none>
kube-controller-manager-minikube   1/1     Running   0          15m   192.168.49.2   minikube   <none>           <none>
kube-proxy-zkqg2                   1/1     Running   0          15m   192.168.49.2   minikube   <none>           <none>
kube-scheduler-minikube            1/1     Running   0          15m   192.168.49.2   minikube   <none>           <none>
storage-provisioner                1/1     Running   1          15m   192.168.49.2   minikube   <none>           <none>
`````
>10. Eliminamos el pod que deseamos
`````
alberto@alberto-VirtualBox:~$ kubectl -n kube-system delete pod coredns-558bd4d5db-dn9tc
pod "coredns-558bd4d5db-dn9tc" deleted
`````
>11. Se verifica en automatico que se genero un nuevo pod cuando eliminamos el pod mencionado anteriormente
`````
alberto@alberto-VirtualBox:~$ kubectl -n kube-system get pods
NAME                               READY   STATUS    RESTARTS   AGE
coredns-558bd4d5db-mm298           1/1     Running   0          41s
etcd-minikube                      1/1     Running   0          20m
kube-apiserver-minikube            1/1     Running   0          20m
kube-controller-manager-minikube   1/1     Running   0          20m
kube-proxy-zkqg2                   1/1     Running   0          20m
kube-scheduler-minikube            1/1     Running   0          20m
storage-provisioner                1/1     Running   1          20m
`````
>12. Creacion de nuestro primer pod: 01-pod.yaml    
`````
vim 01-pod.yaml

apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
   containers:
     name: ngnix
     image: nginx:alpine            
~
`````
>13. Aplicamos el manifiesto de nuestro pod creado
`````
alberto@alberto-VirtualBox:~/LabKubernetes/pods$ kubectl apply -f 01-pod.yaml
pod/nginx created
`````
>14. Revisamos que nuestro pod este corriendo
`````
alberto@alberto-VirtualBox:~/LabKubernetes/pods$ kubectl get pods
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          84s
`````
>15. Estamos dentro de nuestro contenedor corriendo Ngninx
`````
 alberto@alberto-VirtualBox:~/LabKubernetes/pods$ kubectl exec -it nginx -- sh
/ # 
/ # 
/ # ps fax
PID   USER     TIME  COMMAND
    1 root      0:00 nginx: master process nginx -g daemon off;
   34 nginx     0:00 nginx: worker process
   35 nginx     0:00 nginx: worker process
   36 nginx     0:00 nginx: worker process
   37 nginx     0:00 nginx: worker process
   38 root      0:00 sh
   46 root      0:00 ps fax
/ # 
`````
>16. Eliminamos el pod nuevamente
`````
alberto@alberto-VirtualBox:~/LabKubernetes/pods$ kubectl delete pod nginx
pod "nginx" deleted
`````
>17. Creamos nuestro segundo pod: 02-pod.yaml
`````
vim 02-pod.yaml

apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx:alpine
    env:
    - name: MI_VARIABLE
      value: "alberto"
    - name: MI_OTRA_VARIABLE
      value: "eduardo"
    - name: DD_AGENT_HOST
      valueFrom:
        fieldRef:
          fieldPath: status.hostIP
    resources:
      requests:
        memory: "64Mi"
        cpu: "200m"
      limits:
        memory: "128Mi"
        cpu: "500m"
    readinessProbe:
      httpGet:
        path: /
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 10
    livenessProbe:
      tcpSocket:
        port: 80
      initialDelaySeconds: 15
      periodSeconds: 20
    ports:
    - containerPort: 80

`````
>18. Aplicamos el manifiesto de nuestro pod creado
`````
alberto@alberto-VirtualBox:~/LabKubernetes/pods$ kubectl apply -f 02-pod.yaml
pod/nginx created
`````
>19. Verificamos que el pod este corriendo
`````
alberto@alberto-VirtualBox:~/LabKubernetes/pods$ kubectl get pods
NAME    READY   STATUS    RESTARTS   AGE
nginx   0/1     Running   0          8s
`````
>20. Vemos el estado de nuestro pod y nos muestra todo el yaml de nueto pod + las variables
`````
alberto@alberto-VirtualBox:~/LabKubernetes/pods$ kubectl get pod nginx -o yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","kind":"Pod","metadata":{"annotations":{},"name":"nginx","namespace":"default"},"spec":{"containers":[{"env":[{"name":"MI_VARIABLE","value":"alberto"},{"name":"MI_OTRA_VARIABLE","value":"eduardo"},{"name":"DD_AGENT_HOST","valueFrom":{"fieldRef":{"fieldPath":"status.hostIP"}}}],"image":"nginx:alpine","livenessProbe":{"initialDelaySeconds":15,"periodSeconds":20,"tcpSocket":{"port":80}},"name":"nginx","ports":[{"containerPort":80}],"readinessProbe":{"httpGet":{"path":"/","port":80},"initialDelaySeconds":5,"periodSeconds":10},"resources":{"limits":{"cpu":"500m","memory":"128Mi"},"requests":{"cpu":"200m","memory":"64Mi"}}}]}}
  creationTimestamp: "2021-07-22T22:22:33Z"
  name: nginx
  namespace: default
  resourceVersion: "3003"
  uid: efe9044f-5f0f-44c7-89d8-ca21556a71d6
spec:
  containers:
  - env:
    - name: MI_VARIABLE
      value: alberto
    - name: MI_OTRA_VARIABLE
      value: eduardo
    - name: DD_AGENT_HOST
      valueFrom:
        fieldRef:
          apiVersion: v1
          fieldPath: status.hostIP
    image: nginx:alpine
    imagePullPolicy: IfNotPresent
    livenessProbe:
      failureThreshold: 3
      initialDelaySeconds: 15
      periodSeconds: 20
      successThreshold: 1
      tcpSocket:
        port: 80
      timeoutSeconds: 1
    name: nginx
    ports:
    - containerPort: 80
      protocol: TCP
    readinessProbe:
      failureThreshold: 3
      httpGet:
        path: /
        port: 80
        scheme: HTTP
      initialDelaySeconds: 5
      periodSeconds: 10
      successThreshold: 1
      timeoutSeconds: 1
    resources:
      limits:
        cpu: 500m
        memory: 128Mi
      requests:
        cpu: 200m
        memory: 64Mi
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-rj6x9
      readOnly: true
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  nodeName: minikube
  preemptionPolicy: PreemptLowerPriority
  priority: 0
  restartPolicy: Always
  schedulerName: default-scheduler
  securityContext: {}
  serviceAccount: default
  serviceAccountName: default
  terminationGracePeriodSeconds: 30
  tolerations:
  - effect: NoExecute
    key: node.kubernetes.io/not-ready
    operator: Exists
    tolerationSeconds: 300
  - effect: NoExecute
    key: node.kubernetes.io/unreachable
    operator: Exists
    tolerationSeconds: 300
  volumes:
  - name: kube-api-access-rj6x9
    projected:
      defaultMode: 420
      sources:
      - serviceAccountToken:
          expirationSeconds: 3607
          path: token
      - configMap:
          items:
          - key: ca.crt
            path: ca.crt
          name: kube-root-ca.crt
      - downwardAPI:
          items:
          - fieldRef:
              apiVersion: v1
              fieldPath: metadata.namespace
            path: namespace
status:
  conditions:
  - lastProbeTime: null
    lastTransitionTime: "2021-07-22T22:22:33Z"
    status: "True"
    type: Initialized
  - lastProbeTime: null
    lastTransitionTime: "2021-07-22T22:22:43Z"
    status: "True"
    type: Ready
  - lastProbeTime: null
    lastTransitionTime: "2021-07-22T22:22:43Z"
    status: "True"
    type: ContainersReady
  - lastProbeTime: null
    lastTransitionTime: "2021-07-22T22:22:33Z"
    status: "True"
    type: PodScheduled
  containerStatuses:
  - containerID: docker://8080b7bc9826653151097c6917f00053eb1ade86eb667310a3e2e3ed720e5b28
    image: nginx:alpine
    imageID: docker-pullable://nginx@sha256:ad14f3471c0fd650022d3d2ff370e02c51113679070486e510dfcbe64d00565a
    lastState: {}
    name: nginx
    ready: true
    restartCount: 0
    started: true
    state:
      running:
        startedAt: "2021-07-22T22:22:37Z"
  hostIP: 192.168.49.2
  phase: Running
  podIP: 172.17.0.2
  podIPs:
  - ip: 172.17.0.2
  qosClass: Burstable
  startTime: "2021-07-22T22:22:33Z"
`````
>21. Creacion de nuestro tercer pod: 03-deployment.yaml (Manifiesto Deployments)
`````
vim 03-deployment.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:alpine
        env:
        - name: MI_VARIABLE
          value: "alberto"
        - name: MI_OTRA_VARIABLE
          value: "eduardo"
        - name: DD_AGENT_HOST
          valueFrom:
            fieldRef:
              fieldPath: status.hostIP
        resources:
          requests:
            memory: "64Mi"
            cpu: "200m"
          limits:
            memory: "128Mi"
            cpu: "500m"
        readinessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 5
          periodSeconds: 10
        livenessProbe:
          tcpSocket:
            port: 80
          initialDelaySeconds: 15
          periodSeconds: 20
        ports:
        - containerPort: 80
`````
>22. Aplicamos el manifiesto de nuestro pod creado
`````
alberto@alberto-VirtualBox:~/LabKubernetes/pods$ kubectl apply -f 03-deployment.yaml
deployment.apps/nginx-deployment created
`````
>23. Revisamos que se creo 2 pods segun las replicas declaradas en el manifiesto
`````
alberto@alberto-VirtualBox:~/LabKubernetes/pods$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-549f78d6c4-6bpxg   0/1     Running   0          8s
nginx-deployment-549f78d6c4-lbwvk   0/1     Running   0          8s
`````
>24. Eliminamos el pod para que por defecto nos cree otro
`````
alberto@alberto-VirtualBox:~/LabKubernetes/pods$ kubectl delete pod nginx-deployment-549f78d6c4-lbwvk
pod "nginx-deployment-549f78d6c4-lbwvk" deleted
`````
>25. Verificamos y efectivamente nos creo otro pod
`````
alberto@alberto-VirtualBox:~/LabKubernetes/pods$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-549f78d6c4-6bpxg   1/1     Running   0          2m59s
nginx-deployment-549f78d6c4-qmjk7   1/1     Running   0          15s
`````
>26. Creacion de nuestro cuarto pod: 05-statefulset.yaml (Manifiesto StatefulSet y Volumenes)
`````
vim 05-statefulset.yaml

apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: my-csi-app-set
spec:
  selector:
    matchLabels:
      app: mypod
  serviceName: "my-frontend"
  replicas: 1
  template:
    metadata:
      labels:
        app: mypod
    spec:
      containers:
      - name: my-frontend
        image: busybox
        args:
        - sleep
        - infinity
        volumeMounts:
        - mountPath: "/data"
          name: csi-pvc
  volumeClaimTemplates:
  - metadata:
      name: csi-pvc
    spec:
      accessModes:
      - ReadWriteOnce
      resources:
        requests:
          storage: 5Gi
      storageClassName: do-block-storage
`````
>27. Aplicamos el manifiesto de nuestro pod creado
`````
alberto@alberto-VirtualBox:~/Kubernetes/pods$ kubectl apply -f 05-statefulset.yaml
statefulset.apps/my-csi-app-set created
`````
>28. Revisamos que se creo 2 pods segun las replicas declaradas en el manifiesto
`````
alberto@alberto-VirtualBox:~/Kubernetes/pods$ kubectl get pods -o wide
NAME               READY   STATUS    RESTARTS   AGE   IP       NODE     NOMINATED NODE   READINESS GATES
my-csi-app-set-0   0/1     Pending   0          16s   <none>   <none>   <none>           <none>
`````
>29. Vamos a poder ver el estado y los eventos del pod creado
`````
alberto@alberto-VirtualBox:~/Kubernetes/pods$ kubectl describe pod my-csi-app-set-0
Name:           my-csi-app-set-0
Namespace:      default
Priority:       0
Node:           <none>
Labels:         app=mypod
                controller-revision-hash=my-csi-app-set-7bb75ddfc8
                statefulset.kubernetes.io/pod-name=my-csi-app-set-0
Annotations:    <none>
Status:         Pending
IP:             
IPs:            <none>
Controlled By:  StatefulSet/my-csi-app-set
Containers:
  my-frontend:
    Image:      busybox
    Port:       <none>
    Host Port:  <none>
    Args:
      sleep
      infinity
    Environment:  <none>
    Mounts:
      /data from csi-pvc (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-8zvqw (ro)
Conditions:
  Type           Status
  PodScheduled   False 
Volumes:
  csi-pvc:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  csi-pvc-my-csi-app-set-0
    ReadOnly:   false
  kube-api-access-8zvqw:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason            Age                 From               Message
  ----     ------            ----                ----               -------
  Warning  FailedScheduling  14s (x3 over 103s)  default-scheduler  0/1 nodes are available: 1 pod has unbound immediate PersistentVolumeClaims.
`````
>30. Nos muestra el pvc (persistent volume claim) un enlace de kubernetes con el proveedor
`````
alberto@alberto-VirtualBox:~/Kubernetes/pods$ kubectl get pvc
NAME                       STATUS    VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS       AGE
csi-pvc-my-csi-app-set-0   Pending                                      do-block-storage   9m32s
`````
>31. Nos decribe el pvc (persistent volume claim)  
`````
alberto@alberto-VirtualBox:~/Kubernetes/pods$ kubectl describe pvc csi-pvc-my-csi-app-set-0
Name:          csi-pvc-my-csi-app-set-0
Namespace:     default
StorageClass:  do-block-storage
Status:        Pending
Volume:        
Labels:        app=mypod
Annotations:   <none>
Finalizers:    [kubernetes.io/pvc-protection]
Capacity:      
Access Modes:  
VolumeMode:    Filesystem
Used By:       my-csi-app-set-0
Events:
  Type     Reason              Age                 From                         Message
  ----     ------              ----                ----                         -------
  Warning  ProvisioningFailed  42s (x42 over 10m)  persistentvolume-controller  storageclass.storage.k8s.io "do-block-storage" not found
`````
>32. Creamos nuestro sexto pod 06-randompod.yaml (Service NodePort)
`````
alberto@alberto-VirtualBox:~/Kubernetes/pods$ kubectl apply -f 06-randompod.yaml
pod/ubuntu created
`````
>33. Validamos el pod creado
`````
alberto@alberto-VirtualBox:~/Kubernetes/pods$ kubectl get pod
NAME     READY   STATUS    RESTARTS   AGE
ubuntu   1/1     Running   0          12s
`````
>34. Creamos nuestro septimo pod 07-hello-deployment-svc-clusterIP.yaml 
`````
alberto@alberto-VirtualBox:~/Kubernetes/pods$ kubectl apply -f 07-hello-deployment-svc-clusterIP.yaml
deployment.apps/hello created
service/hello created
`````
>35. Validamos que se crearon los pods declarados en el manifiesto
`````
alberto@alberto-VirtualBox:~/Kubernetes/pods$ kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
hello-868bcb8b84-hsgsj   1/1     Running   0          19s
hello-868bcb8b84-l48sx   1/1     Running   0          19s
hello-868bcb8b84-mxtkt   1/1     Running   0          19s
ubuntu                   1/1     Running   0          42s
`````
>36. Nos permite ver los sevicios de los pods creados 
`````
alberto@alberto-VirtualBox:~/Kubernetes/pods$ kubectl get all
NAME                         READY   STATUS    RESTARTS   AGE
pod/hello-868bcb8b84-hsgsj   1/1     Running   0          27s
pod/hello-868bcb8b84-l48sx   1/1     Running   0          27s
pod/hello-868bcb8b84-mxtkt   1/1     Running   0          27s
pod/ubuntu                   1/1     Running   0          50s

NAME                 TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
service/hello        ClusterIP   10.102.159.219   <none>        8080/TCP   27s
service/kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP    24h

NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/hello   3/3     3            3           27s

NAME                               DESIRED   CURRENT   READY   AGE
replicaset.apps/hello-868bcb8b84   3         3         3       27s
`````
>37. Nos describe el servicio "hello" y nos muestra los endpoints que son las ips' de esos pods.
`````
alberto@alberto-VirtualBox:~/Kubernetes/pods$ kubectl describe svc hello
Name:              hello
Namespace:         default
Labels:            <none>
Annotations:       <none>
Selector:          role=hello
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                10.102.159.219
IPs:               10.102.159.219
Port:              <unset>  8080/TCP
TargetPort:        8080/TCP
Endpoints:         172.17.0.5:8080,172.17.0.6:8080,172.17.0.7:8080
Session Affinity:  None
Events:            <none>
`````
>38. Realizamos un curl a "hello" y nos responde con el pod: hello-868bcb8b84-rmftn
`````
root@ubuntu:/# curl http://hello:8080
Hello, world!
Version: 1.0.0
Hostname: hello-868bcb8b84-rmftn
root@ubuntu:/# 
`````
>39. Creamos nuestro octavo pod 08-hello-deployment-svc-nodePort.yaml
`````
alberto@alberto-VirtualBox:~/Kubernetes/pods$ kubectl apply -f 08-hello-deployment-svc-nodePort.yaml
deployment.apps/hello created
service/hello created   
`````
>40. Nos permite ver los sevicios de los pods creados 
`````
alberto@alberto-VirtualBox:~/Kubernetes/pods$ kubectl get all
NAME                         READY   STATUS    RESTARTS   AGE
pod/hello-868bcb8b84-h9zrz   1/1     Running   0          9s
pod/hello-868bcb8b84-jqcss   1/1     Running   0          9s
pod/hello-868bcb8b84-ntlgx   1/1     Running   0          9s

NAME                 TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/hello        NodePort    10.97.223.74   <none>        8080:30000/TCP   9s
service/kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP          27h

NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/hello   3/3     3            3           9s

NAME                               DESIRED   CURRENT   READY   AGE
replicaset.apps/hello-868bcb8b84   3         3         3       9s
alberto@alberto-VirtualBox:~/Kubernetes/pods$ kubectl get nodes -o wide
NAME       STATUS   ROLES                  AGE   VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION     CONTAINER-RUNTIME
minikube   Ready    control-plane,master   27h   v1.21.2   192.168.49.2   <none>        Ubuntu 20.04.2 LTS   5.8.0-59-generic   docker://20.10.7
`````
>41. Creamos nuestro decimo pod 10-hello-v1-v2-deployment-svc.yaml
`````   
alberto@alberto-VirtualBox:~/Kubernetes/pods$ kubectl apply -f 10-hello-v1-v2-deployment-svc.yaml
deployment.apps/hello-v1 created
deployment.apps/hello-v2 created
service/hello-v1 created
service/hello-v2 created
`````
>42. Nos permite ver los sevicios de los pods creados 
`````
alberto@alberto-VirtualBox:~/Kubernetes/pods$ kubectl get all
NAME                            READY   STATUS              RESTARTS   AGE
pod/hello-v1-dc7b59fb4-95lcj    1/1     Running             0          17s
pod/hello-v1-dc7b59fb4-f44xv    1/1     Running             0          17s
pod/hello-v1-dc7b59fb4-jtbbv    1/1     Running             0          17s
pod/hello-v2-59cc6d787c-jh58z   1/1     Running             0          17s
pod/hello-v2-59cc6d787c-m72m8   0/1     ContainerCreating   0          17s
pod/hello-v2-59cc6d787c-r7bd4   0/1     ContainerCreating   0          17s
pod/ubuntu                      1/1     Running             0          20m

NAME                 TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
service/hello-v1     ClusterIP   10.110.179.139   <none>        8080/TCP   17s
service/hello-v2     ClusterIP   10.107.185.229   <none>        8080/TCP   17s
service/kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP    27h

NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/hello-v1   3/3     3            3           17s
deployment.apps/hello-v2   1/3     3            1           17s

NAME                                  DESIRED   CURRENT   READY   AGE
replicaset.apps/hello-v1-dc7b59fb4    3         3         3       17s
replicaset.apps/hello-v2-59cc6d787c   3         3         1       17s
`````
>43. Instalamos ingress (Es un nuevo tipo de recurs que nos permite crear accesos a nuestros servicios basados en el PATH)  
`````
alberto@alberto-VirtualBox:~/Kubernetes/pods$ minikube addons enable ingress
    ▪ Using image k8s.gcr.io/ingress-nginx/controller:v0.44.0
    ▪ Using image docker.io/jettech/kube-webhook-certgen:v1.5.1
    ▪ Using image docker.io/jettech/kube-webhook-certgen:v1.5.1
🔎  Verifying ingress addon...
🌟  The 'ingress' addon is enabled
`````
>44. Creamos nuestro undecimo pod 11-hello-ingress.yaml
`````
alberto@alberto-VirtualBox:~/Kubernetes/pods$ kubectl apply -f 11-hello-ingress.yaml
ingress.networking.k8s.io/hello-app created
alberto@alberto-VirtualBox:~/Kubernetes/pods$ kubectl get ing
NAME        CLASS    HOSTS   ADDRESS   PORTS   AGE
hello-app   <none>   *                 80      11s
`````
>45. Validamos que se creo un LoadBalancer y poder llegar al pod de nuestro servicio
`````
alberto@alberto-VirtualBox:~/Kubernetes/pods$ kubectl -n ingress-nginx get svc
NAME                                 TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
ingress-nginx-controller             LoadBalancer    10.107.215.116   <none>        80:30094/TCP,443:30953/TCP   5m8s
ingress-nginx-controller-admission   ClusterIP   10.108.157.3     <none>        443/TCP                      5m8s
`````
>46. Creamos nuestro pod 13-pod-configmap.yaml
`````
alberto@alberto-VirtualBox:~/Kubernetes/pods$ kubectl apply -f 13-pod-configmap.yaml
pod/nginx created
`````
>47. Validamos que esta corriendo de manera correcta
`````
alberto@alberto-VirtualBox:~/Kubernetes/pods$ kubectl get pods
NAME     READY   STATUS    RESTARTS   AGE
nginx    1/1     Running   0          6s
ubuntu   1/1     Running   0          41m
`````
>48. Validamos que tenemos nuestras variables de entorno creadas
`````
alberto@alberto-VirtualBox:~/Kubernetes/pods$ kubectl exec -it nginx -- sh
/ # env
KUBERNETES_SERVICE_PORT=443
KUBERNETES_PORT=tcp://10.96.0.1:443
UI_PROPERTIES_FILE_NAME=user-interface.properties
HOSTNAME=nginx
SHLVL=1
HOME=/root
PKG_RELEASE=1
TERM=xterm
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
NGINX_VERSION=1.21.1
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
KUBERNETES_PORT_443_TCP_PORT=443
NJS_VERSION=0.6.1
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
PLAYER_INITIAL_LIVES=3
KUBERNETES_SERVICE_HOST=10.96.0.1
PWD=/
/ # ls /config/
game.properties            user-interface.properties
/ # cat /config/game.properties
enemy.types=aliens,monsters
player.maximum-lives=5
/ # cat /config/user-interface.properties
color.good=purple
color.bad=yellow
allow.textmode=true
/ # 
`````
