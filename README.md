Aquí tienes un ejemplo de cómo podrías estructurar el `README.md` para tu proyecto en GitHub:

```markdown
# Proyecto 2: Cluster Kubernetes con MicroK8s

## Descripción General

Este proyecto tiene como objetivo el despliegue de una aplicación CMS (WordPress) en un clúster de Kubernetes utilizando la distribución **MicroK8s** en máquinas virtuales en la nube **AWS (IaaS)**. El clúster está compuesto por un nodo maestro (Master), tres nodos trabajadores (Worker), un servidor NFS para almacenamiento de datos y una base de datos MySQL.

### Aspectos Cumplidos
- **Cluster Kubernetes con MicroK8s.**
  - Nodo **Master** para el clúster de MicroK8s y MySQL.
  - Nodo **Worker 1** para WordPress.
  - Nodo **Worker 2** para WordPress.
  - Nodo **Worker 3** para Servidor NFS.
  
- **Servidor NFS** para almacenar los datos de WordPress.
- **2 Pods** de WordPress.
- **1 Pod** de MySQL.

### Aspectos No Desarrollados
- **Base de datos MySQL con alta disponibilidad** no completada.
- **Dominio propio** no configurado.

## Arquitectura del Sistema

El clúster de Kubernetes está compuesto por los siguientes componentes:
1. **Nodo Master:** Nodo que gestiona el clúster y coordina la base de datos MySQL.
2. **3 Nodos Worker:**
   - **Worker 1 y Worker 2:** Ejecutan las instancias de WordPress.
   - **Worker 3:** Ejecuta el servidor NFS.
3. **Servidor NFS:** Se utiliza para el almacenamiento persistente de los datos de WordPress.
4. **Base de Datos MySQL:** Se configura en el nodo Master para la gestión de datos de WordPress.

## Descripción del Ambiente de Desarrollo

El clúster se encuentra en la nube AWS, donde se crearon **4 instancias EC2** configuradas con MicroK8s:

- **1 instancia Master** (para el clúster MicroK8s y MySQL).
- **2 instancias Worker** (para WordPress).
- **1 instancia Worker** (para el servidor NFS).

### 3.1 EC2 en AWS

- **Tipo de Instancia:** T2.medium (o el tipo que se haya elegido).
- **Grupo de Seguridad:** Configurado para permitir tráfico en los puertos necesarios (por ejemplo, 80, 443, 6443).

### 3.2 Configuración de las Instancias

Asegúrate de seguir este historial de configuraciones en todas las instancias que forman parte del clúster:

1. Crear instancias EC2 en AWS.
2. Conectar a cada instancia a través de SSH utilizando la llave de ingreso configurada.

### 3.3 Instalar MicroK8s en cada instancia

En cada nodo, ejecutar los siguientes comandos:

```bash
sudo apt update
sudo snap install microk8s --classic
sudo microk8s status --wait-ready
sudo microk8s enable dashboard
sudo microk8s enable dns
sudo microk8s enable registry
sudo microk8s enable istio
sudo microk8s enable community
sudo microk8s start
sudo microk8s kubectl get all --all-namespaces
```

### 3.4 Crear el Clúster

Para agregar nodos al clúster, primero en el **nodo master**, ejecutar:

```bash
sudo microk8s add-node
```

Y luego, en cada **nodo worker**, ejecutar la siguiente línea:

```bash
microk8s join 192.168.1.230:25000/92b2db237428470dc4fcfc4ebbd9dc81/2c0cb3284b05 --worker
```

Este proceso debe repetirse en cada nodo que se desee agregar al clúster.

### 3.5 Manifiestos (Kubernetes YAML)

- **MySQL Deployment:**
  - Contiene la configuración de PV (PersistentVolume), PVC (PersistentVolumeClaim), Deployment y Service para la base de datos MySQL.
  
```bash
microk8s kubectl apply -f mysql-deployment.yaml
```

- **WordPress Deployment:**
  - Configura el deployment de WordPress, los volúmenes persistentes y el servicio.

```bash
microk8s kubectl apply -f wordpress-deployment.yaml
```

- **Ingress Config:**
  - Configuración de ingress para el acceso a la aplicación.

```bash
microk8s kubectl apply -f ingress-config.yaml
```

## Descripción del Ambiente de Ejecución (Resultados)

La aplicación es accesible a través de la siguiente URL:

[http://52.204.20.172.nip.io/](http://52.204.20.172.nip.io/)

### Comando para Verificar la Configuración del Clúster

```bash
microk8s kubectl get all -o wide
```

Este comando mostrará el estado del clúster y todos los recursos desplegados, como pods, servicios, etc.

## Conclusión

Este proyecto permite el despliegue de una aplicación WordPress en un clúster Kubernetes usando MicroK8s, con almacenamiento persistente a través de NFS y una base de datos MySQL gestionada en el nodo Master. A pesar de no haber logrado la alta disponibilidad de la base de datos ni configurar el dominio propio, se cumplió con el despliegue básico de los componentes esenciales de la infraestructura.

## Contactos

- **Juan Manuel Garzón** - [jmgarzonv@eafit.edu.co](mailto:jmgarzonv@eafit.edu.co)
- **David Grisales** - [dgrisalesp@eafit.edu.co](mailto:dgrisalesp@eafit.edu.co)
- **Profesor Edwin Montoya** - [emontoya@eafit.edu.co](mailto:emontoya@eafit.edu.co)
```

Este formato de README proporciona una descripción clara del proyecto, las configuraciones realizadas y cómo replicar el ambiente de desarrollo.
