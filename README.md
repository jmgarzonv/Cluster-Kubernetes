# ST0263-242-TÓPICOS ESPECIALES DE TELEMATICA
# Proyecto 2 - Cluster Kubernetes con MicroK8s en AWS
## Objetivo
El objetivo de este proyecto es desplegar una aplicación CMS Wordpress utilizando un clúster Kubernetes creado con MicroK8s en diferentes máquinas virtuales en la nube AWS (IaaS).

### Integrantes:
- Juan Manuel Garzón - jmgarzonv@eafit.edu.co
- David Grisales - dgrisalesp@eafit.edu.co

Profesor:
- Edwin Montoya - emontoya@eafit.edu.co

## 1. Descripción general de la actividad

Este proyecto consiste en la implementación de un CMS Wordpress a través de un clúster de Kubernetes utilizando MicroK8s. El clúster se configura con al menos 3 máquinas virtuales, un servidor NFS y una base de datos MySQL de alta disponibilidad.

### 1.1 Aspectos cumplidos:
- **Clúster de Kubernetes con MicroK8s.**
- **Un master y 3 workers:**
  - **Master:** Para el clúster de MicroK8s y MySQL.
  - **Worker 1:** Para WordPress.
  - **Worker 2:** Para WordPress.
  - **Worker 3:** Para el servidor NFS.
- **Servidor NFS para almacenamiento de datos.**
- **WordPress (2 pods).**
- **MySQL (1 pod).**

### 1.2 Aspectos no desarrollados:
- La base de datos MySQL no se configuró con alta disponibilidad.
- No se logró configurar un dominio propio.

## Arquitectura del sistema

La arquitectura del sistema incluye un clúster de Kubernetes con MicroK8s, configurado en la nube AWS con 4 instancias EC2:
- **1 master:** para la gestión del clúster y la base de datos MySQL.
- **2 workers:** para desplegar los pods de WordPress.
- **1 worker:** para desplegar el servidor NFS.

## 3. Descripción del ambiente de desarrollo

El clúster se creó en la nube AWS utilizando instancias EC2, configuradas con MicroK8s. A continuación se detallan los pasos de configuración y comandos utilizados.

### 3.1 EC2 en AWS
- Instancias :
![imagen](https://github.com/user-attachments/assets/4167a90e-909d-4203-8f87-dbfc765a2b0a)

- Tipo de instancia y llave de ingreso:
![imagen](https://github.com/user-attachments/assets/4c1cb3b3-0a07-4992-a81b-80fb0f540b63)

- Grupo de seguridad:

![imagen](https://github.com/user-attachments/assets/0273706a-cf95-46dc-8808-52d2de3a1d44)

### Configuración de las instancias

Historial de todas las configuraciones realizadas en las instancias EC2.

### Instalación de MicroK8s

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
## Creación del clúster

Para crear un clúster de MicroK8s y unir los nodos worker, sigue los pasos a continuación.

1. En el nodo maestro, ejecuta el siguiente comando:

```bash
sudo microk8s add-node
```
En cada worker, ejecutar el siguiente comando para unirlo al clúster:
```
microk8s join 192.168.1.230:25000/92b2db237428470dc4fcfc4ebbd9dc81/2c0cb3284b05 --worker
```
### 3.4 Manifiestos
NFS PersistentVolume (nfs-pv.yaml)
```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: nfs-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  nfs:
    path: /mnt/nfs/shared
    server: 172.31.28.61
```
NFS PersistentVolumeClaim (nfs-pvc.yaml)
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nfs-pvc
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 5Gi
```
MySQL Deployment (mysql-deployment.yaml)
```
apiVersion: v1
kind: Service
metadata:
  name: wordpress-mysql
spec:
  ports:
  - port: 3306
  selector:
    app: mysql
  type: NodePort
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql
spec:
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - image: mysql:5.6
        name: mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          value: password
        - name: MYSQL_PASSWORD
          value: password
        - name: MYSQL_DATABASE
          value: wordpress
        - name: MYSQL_USER
          value: wordpress
        ports:
        - containerPort: 3306
          name: mysql
        volumeMounts:
        - name: mysql-persistent-storage
          mountPath: /var/lib/mysql
      volumes:
      - name: mysql-persistent-storage
        persistentVolumeClaim:
          claimName: mysql-pv-claim
```
WordPress Deployment (wordpress-deployment.yaml)
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress
spec:
  replicas: 2
  selector:
    matchLabels:
      app: wordpress
  template:
    metadata:
      labels:
        app: wordpress
    spec:
      containers:
      - name: wordpress
        image: wordpress:php7.4-apache
        env:
        - name: WORDPRESS_DB_HOST
          value: wordpress-mysql
        - name: WORDPRESS_DB_USER
          value: wordpress
        - name: WORDPRESS_DB_PASSWORD
          value: password
        - name: WORDPRESS_DB_NAME
          value: wordpress
        ports:
        - containerPort: 80
        volumeMounts:
        - mountPath: /var/www/html
          name: wordpress-storage
      volumes:
      - name: wordpress-storage
        persistentVolumeClaim:
          claimName: nfs-pvc
```
Ingress Config (ingress-config.yaml)
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: wordpress-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
    - host: 52.204.20.172.nip.io
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: wordpress-service
                port:
                  number: 80
```
Aplicar los manifiestos
```
microk8s kubectl apply -f mysql-deployment.yaml
microk8s kubectl apply -f wordpress-deployment.yaml
microk8s kubectl apply -f ingress-config.yaml
```
### 4.Descripción del ambiente de ejecución

La aplicación se puede acceder a través de la siguiente URL: http://52.204.20.172.nip.io/

Comando para visualizar la configuración del clúster
```
microk8s kubectl get all -o wide
```
