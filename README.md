# ST0263-242-TÓPICOS ESPECIALES DE TELEMATICA
# Proyecto 2 - Cluster Kubernetes con MicroK8s en AWS
## Objetivo
El objetivo de este proyecto es desplegar una aplicación CMS Wordpress utilizando un clúster Kubernetes creado con MicroK8s en diferentes máquinas virtuales en la nube AWS (IaaS).

### Integrantes:
- Juan Manuel Garzón - V-jmgarzonv@eafit.edu.co
- David Grisales - P-dgrisalesp@eafit.edu.co

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
- ** Instancias :**
![imagen](https://github.com/user-attachments/assets/4167a90e-909d-4203-8f87-dbfc765a2b0a)

- **Tipo de instancia y llave de ingreso:** 
![imagen](https://github.com/user-attachments/assets/4c1cb3b3-0a07-4992-a81b-80fb0f540b63)

- **Grupo de seguridad:** 
![imagen](https://github.com/user-attachments/assets/0273706a-cf95-46dc-8808-52d2de3a1d44)

### Configuración de las instancias

Historial de todas las configuraciones realizadas en las instancias EC2.

### Instalación de MicroK8s
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


