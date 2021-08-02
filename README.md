# Kubernetes
Laboratorio de Kubernetes + Teoría 

# Kubernetes 

 En Kubernetes se utilizan los objetos de la API de Kubernetes para describir el estado deseado del clúster: qué aplicaciones u otras cargas de trabajo se quieren ejecutar, qué imagenes de contenedores usan, el número de replicas, qué red y qué recursos de almacenamiento quieres que tengan disponibles, etc. 

 Se especifica el estado deseado del clúster mediante la creación de objetos usando la API de Kubernetes, típicamente mediante la interfaz de línea de comandos, kubectl.

 El Master de Kubernetes es un conjunto de tres daemons que se ejecutan en un único nodo del clúster, que se denomina nodo master. Estos daemons son: 
 
 - ***kube-apiserver*** 
 - ***kube-controller-manager*** 
 - ***kube-scheduler***


Los restantes nodos no master contenidos en tu clúster, ejecutan los siguientes dos daemons:

- ***kubelet***, el cual se comunica con el Master de Kubernetes.

- ***kube-proxy***, un proxy de red que implementa los servicios de red de Kubernetes en cada nodo.


### **Objetos de Kubernetes**

Los objetos básicos de Kubernetes incluyen:

- Pod
- Service
- Volume
- Namespace

### Kubernetes contiene abstracciónes de nivel superior llamadas Controladores. Los Controladores se basan en los objetos básicos y proporcionan funcionalidades adicionales sobre ellos. Incluyen:

- ReplicaSet
- Deployment
- StatefulSet
- DaemonSet
- Job