Aquí tienes la descripción para el README de GitHub en formato detallado, organizada en secciones de acuerdo a lo que mencionaste:

---

# Entorno de Desarrollo

## 3. Descripción del ambiente de desarrollo

Este proyecto se despliega en un clúster de Kubernetes (MicroK8s) sobre instancias EC2 en AWS, con la siguiente configuración:

### 3.1 EC2 en AWS

- **Tipo de Instancia:**
  - Se utilizaron 4 instancias EC2 con el tipo `t2.medium` (ajustable según los recursos disponibles y necesidades).
  
- **Llave de Ingreso:**
  - Se generó una llave SSH para acceso a las instancias EC2. La llave debe ser configurada en el archivo de configuración de AWS para poder acceder remotamente a las instancias.

- **Grupo de Seguridad:**
  - El grupo de seguridad está configurado para permitir acceso en los siguientes puertos:
    - **22** (SSH) para administración de las instancias.
    - **80** (HTTP) y **443** (HTTPS) para servicios web.
    - **25000** (para la conexión de nodos de Kubernetes).
  
### 3.2 Configuración de las instancias

A continuación se encuentra el historial de configuración de las instancias:

1. **Instancia Master (Nodo Principal):**
   - Instalación de MicroK8s.
   - Configuración de red y seguridad.
   - Configuración inicial de Kubernetes.

2. **Instancia Worker (Nodo de trabajo para WordPress):**
   - Instalación de MicroK8s.
   - Configuración como nodo trabajador en el clúster.
   
3. **Instancia Worker (Nodo de trabajo para NFS):**
   - Instalación de MicroK8s.
   - Configuración para trabajar con almacenamiento en red (NFS).

### 3.3 Instalación de MicroK8s

Sigue estos pasos para instalar MicroK8s en cada instancia:

1. **Actualizar el sistema:**

   ```bash
   sudo apt update
   ```

2. **Instalar MicroK8s:**

   ```bash
   sudo snap install microk8s --classic
   ```

3. **Verificar que MicroK8s está listo:**

   ```bash
   sudo microk8s status --wait-ready
   ```

4. **Habilitar las extensiones necesarias:**

   ```bash
   sudo microk8s enable dashboard
   sudo microk8s enable dns
   sudo microk8s enable registry
   sudo microk8s enable istio
   sudo microk8s enable community
   ```

5. **Iniciar MicroK8s:**

   ```bash
   sudo microk8s start
   ```

6. **Verificar todos los recursos del clúster:**

   ```bash
   sudo microk8s kubectl get all --all-namespaces
   ```

### 3.4 Crear el clúster

1. En el nodo master, ejecutar:

   ```bash
   sudo microk8s add-node
   ```

2. En cada nodo worker, ejecutar el comando proporcionado para unirse al clúster:

   ```bash
   microk8s join 192.168.1.230:25000/92b2db237428470dc4fcfc4ebbd9dc81/2c0cb3284b05 --worker
   ```

   Este proceso debe ser realizado en cada nodo trabajador que se desea agregar al clúster.

### 3.5 Manifiestos

Los manifiestos de Kubernetes para desplegar los servicios incluyen configuraciones para los deployments, servicios y volúmenes.

1. **Mysql-deployment:**

   Este archivo incluye la configuración del Persistent Volume (PV), Persistent Volume Claim (PVC), Deployment y Service para MySQL.

   ```bash
   microk8s kubectl apply -f mysql-deployment.yaml
   ```

2. **Wordpress-deployment:**

   Este archivo contiene la configuración del Deployment, los volúmenes y el Service para WordPress.

   ```bash
   microk8s kubectl apply -f wordpress-deployment.yaml
   ```

3. **Ingress Config:**

   Este archivo configura el Ingress para enrutar el tráfico HTTP hacia los servicios correspondientes.

   ```bash
   microk8s kubectl apply -f ingress-config.yaml
   ```

## 4. Descripción del ambiente de ejecución (Resultados)

- **IP pública del clúster:**  
  `http://52.204.20.172.nip.io/`

- **Comando para visualizar la configuración del clúster:**

   Para ver el estado completo del clúster y los recursos distribuidos:

   ```bash
   microk8s kubectl get all -o wide
   ```

---
