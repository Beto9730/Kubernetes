# Kubernetes
Laboratorio de Kubernetes + Teoría 

# Kubernetes 

 En Kubernetes se utilizan los objetos de la API de Kubernetes para describir el estado deseado del clúster: qué aplicaciones u otras cargas de trabajo se quieren ejecutar, qué imagenes de contenedores usan, el número de replicas, qué red y qué recursos de almacenamiento quieres que tengan disponibles, etc. 

 Se especifica el estado deseado del clúster mediante la creación de objetos usando la API de Kubernetes, típicamente mediante la interfaz de línea de comandos, kubectl.

 El Master de Kubernetes es un conjunto de tres daemons que se ejecutan en un único nodo del clúster, que se denomina nodo master. Estos daemons son: 
 
 - ***kube-apiserver*** 
 - ***kube-controller-manager*** : 
 - ***kube-scheduler*** : se encarga de mover los contenedores de un lugar a otro


Los restantes nodos no master contenidos en tu clúster, ejecutan los siguientes dos daemons:

- ***kubelet***,  : el cual se comunica con el Master de Kubernetes, es como un servicio de kubernetes que permite conectar todos los workes y todos los servicios de kubernetes entre si 
- 
- ***kube-proxy***, un proxy de red que implementa los servicios de red de Kubernetes en cada nodo.


### **Objetos de Kubernetes**

Los objetos básicos de Kubernetes incluyen:

- Pod         : pod es un set de contenedores puede ser 1 o mas contenedores.
- Service     : una forma abstracta de exponer una aplicación que se ejecuta en un conjunto de nodos como un servicio de red.
- Volume      : en teoría, un volumen es un directorio al que pueden acceder todos los contenedores en un pod , un pod especifica qué volúmenes contiene y la ruta.
- Namespace   : Es como una division de recursos de tu cluster de kubernetes , te permite separar tu carga en tu cluster de kubernetes.

### Kubernetes contiene abstracciónes de nivel superior llamadas Controladores. Los Controladores se basan en los objetos básicos y proporcionan funcionalidades adicionales sobre ellos. Incluyen:

- ReplicaSet  : mantiene un conjunto estable de réplicas de Pods ejecutándose en todo momento.
- Deployment  : es un template para crear pods , declaro las replicas kubernetes se encarga que corran todo el tiempo.
- StatefulSet : una forma de crear pods que tienen un volumen( este disco va estar atado a este pod y la data no se va a perder) (base de datos)
- DaemonSet   : es una forma de deployar un pod , pero este pod va estar deployado en todos los nodos.
- Job         : un Job crea uno o más Pods y se asegura de que un número específico de ellos termina de forma satisfactoria.
