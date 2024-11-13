# Proyecto 2 - Cluster Kubernetes con MicroK8s en AWS

## Objetivo
El objetivo de este proyecto es desplegar una aplicación CMS Wordpress utilizando un clúster Kubernetes creado con MicroK8s en diferentes máquinas virtuales en la nube AWS (IaaS).

### Integrantes:
- Juan Manuel Garzón - V-jmgarzonv@eafit.edu.co
- David Grisales - P-dgrisalesp@eafit.edu.co

Profesor:
- Edwin Montoya - emontoya@eafit.edu.co

## Descripción general de la actividad

Este proyecto consiste en la implementación de un CMS Wordpress a través de un clúster de Kubernetes utilizando MicroK8s. El clúster se configura con al menos 3 máquinas virtuales, un servidor NFS y una base de datos MySQL de alta disponibilidad.

### Aspectos cumplidos:
- **Clúster de Kubernetes con MicroK8s.**
- **Un master y 3 workers:**
  - **Master:** Para el clúster de MicroK8s y MySQL.
  - **Worker 1:** Para WordPress.
  - **Worker 2:** Para WordPress.
  - **Worker 3:** Para el servidor NFS.
- **Servidor NFS para almacenamiento de datos.**
- **WordPress (2 pods).**
- **MySQL (1 pod).**

### Aspectos no desarrollados:
- La base de datos MySQL no se configuró con alta disponibilidad.
- No se logró configurar un dominio propio.

## Arquitectura del sistema

La arquitectura del sistema incluye un clúster de Kubernetes con MicroK8s, configurado en la nube AWS con 4 instancias EC2:
- **1 master:** para la gestión del clúster y la base de datos MySQL.
- **2 workers:** para desplegar los pods de WordPress.
- **1 worker:** para desplegar el servidor NFS.

## Descripción del ambiente de desarrollo

El clúster se creó en la nube AWS utilizando instancias EC2, configuradas con MicroK8s. A continuación se detallan los pasos de configuración y comandos utilizados.

### EC2 en AWS
- **Tipo de instancia y llave de ingreso:** Detalles específicos de la instancia y la llave utilizada.
- **Grupo de seguridad:** Detalles de configuración del grupo de seguridad.

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
