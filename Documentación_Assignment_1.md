## **Documentación: Assignment 1 - Bartolomé López Reverte**

**Instalación de Kubernetes 1.28 con Ansible: Ejercicio Práctico**

➡️ **1. Preparativos**

**Objetivo:** Preparar las máquinas virtuales y configurar Ansible para la automatización.

- Máquina controladora Ansible
- Nodo maestro de Kubernetes
- Nodo trabajador de Kubernetes

**Instrucciones:** En la máquina controladora de Ansible, instala Ansible y configura el acceso SSH a los nodos maestro y trabajador.


➡️ **2. Configuración inicial**

**Objetivo:** Definir los nodos objetivo en un archivo de inventario.

```
[maestro]
nodo_maestro ansible_ssh_host=direccion_ip_del_nodomaestro

[trabajador]
nodo_trabajador ansible_ssh_host=direccion_ip_del_nodotrabajador
```

**Instrucciones:** Una vez definidos los nodos objetivo en el `inventory.ini`, comprueba la conexión ejecutando el siguiente playbook.

**Descripción:** Este playbook verifica la conectividad entre la máquina controladora y los nodos de Kubernetes. Es esencial asegurarse de que Ansible pueda comunicarse con todos los nodos antes de continuar.


```yaml
---
- name: Comprobar que la conexión es correcta
  hosts: all

  tasks:
  - name: Ping
    ping:
```

➡️ **3. Instalación de Kubernetes**

**Objetivo:** Usar Ansible para instalar Kubernetes en ambos nodos.

**Descripción:** Este playbook instala Kubernetes en los nodos. Kubernetes es el orquestador de contenedores que nos permite administrar y escalar aplicaciones en contenedores.

```yaml
---
- name: Instalar Kubernetes 1.28.2
  hosts: all
  become: yes
  tasks:
    - name: Instalar paquetes necesarios
      apt:
        name: ['apt-transport-https', 'curl']
        state: present
      become: yes

    - name: Agregar clave GPG de Kubernetes
      apt_key:
        url: https://packages.cloud.google.com/apt/doc/apt-key.gpg
        state: present
      become: yes

    - name: Agregar repositorio de Kubernetes
      apt_repository:
        repo: deb https://apt.kubernetes.io/ kubernetes-xenial main
        state: present
      become: yes

    - name: Instalar componentes de Kubernetes
      apt:
        name: 
          - 'kubelet=1.28.2-00'
          - 'kubeadm=1.28.2-00'
        state: present
        update_cache: yes
      become: yes
```

➡️ **4. Instalación y Configuración de cri-o**

**Objetivo:** Instalar y configurar cri-o en los nodos.

**Descripción:** cri-o es un runtime de contenedores ligero que se integra perfectamente con Kubernetes. Es esencial tener un runtime de contenedores para que Kubernetes pueda ejecutar y gestionar contenedores.

**Playbook para instalar cri-o:**

```yaml
---
---
- name: Instalar cri-o en Ubuntu 22.04
  hosts: all
  become: yes
  vars:
    OS: xUbuntu_22.04
    CRIO_VERSION: 1.26
  tasks:
    - name: Añadir repositorio de cri-o
      shell: |
        echo "deb https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/{{ OS }}/ /" | tee /etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list

    - name: Añadir repositorio de cri-o con versión específica
      shell: |
        echo "deb http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/{{ CRIO_VERSION }}/{{ OS }}/ /" | tee /etc/apt/sources.list.d/devel:kubic:libcontainers:stable:cri-o:{{ CRIO_VERSION }}.list

    - name: Añadir llave del repositorio cri-o con versión específica
      apt_key:
        url: "https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/{{ CRIO_VERSION }}/{{ OS }}/Release.key"
        state: present

    - name: Añadir llave del repositorio cri-o
      apt_key:
        url: "https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/{{ OS }}/Release.key"
        state: present

    - name: Actualizar repositorios
      apt:
        update_cache: yes

    - name: Instalar cri-o y cri-o-runc
      apt:
        name:
          - cri-o
          - cri-o-runc
        state: present
```

➡️ **5. Configuración del Clúster de Kubernetes**:

**Objetivo:** Inicializar el nodo maestro y unir el nodo trabajador al clúster.

**Descripción:** Este conjunto de instrucciones inicializa el clúster de Kubernetes en el nodo maestro y luego une el nodo trabajador al clúster, creando así un clúster de Kubernetes funcional.

⚠️ Antes de continuar, asegúrate de que tienes un runtime instalado.

**Importante:** Antes de ejecutar el siguiente playbook, es esencial que cri-o esté inicializado y funcionando correctamente en el nodo maestro y trabajador. cri-o actúa como el runtime de contenedores para Kubernetes, permitiendo que los contenedores se ejecuten y se gestionen dentro del clúster.

---

**Playbook: Configurar el sistema para Kubernetes**

**Descripción:** Este playbook realiza configuraciones esenciales en el sistema operativo para que Kubernetes funcione correctamente:

- **Cargar el módulo br_netfilter:** Este módulo permite que Kubernetes realice el filtrado de paquetes a nivel de puente, esencial para la gestión de redes en contenedores.
  
- **Establecer bridge-nf-call-iptables a 1:** Esta configuración permite que los paquetes que atraviesan el puente de red (bridge) sean procesados por las reglas de iptables. Es crucial para que las políticas de red de Kubernetes funcionen correctamente.
  
- **Habilitar el enrutamiento IP:** Esta configuración permite que el nodo reenvíe paquetes IP, lo cual es esencial para el enrutamiento de tráfico entre pods en diferentes nodos.
  
- **Hacer permanentes las configuraciones:** Asegura que las configuraciones anteriores persistan después de reiniciar el sistema.


```yaml
---
- name: Configurar el sistema para Kubernetes
  hosts: all
  become: yes
  tasks:
    - name: Cargar el módulo br_netfilter
      modprobe:
        name: br_netfilter
        state: present
      become: yes

    - name: Establecer bridge-nf-call-iptables a 1
      sysctl:
        name: net.bridge.bridge-nf-call-iptables
        value: '1'
        state: present
        reload: yes
      become: yes

    - name: Habilitar el enrutamiento IP
      sysctl:
        name: net.ipv4.ip_forward
        value: '1'
        state: present
        reload: yes
      become: yes

    - name: Hacer permanentes las configuraciones
      lineinfile:
        path: /etc/sysctl.conf
        line: "{{ item }}"
        state: present
      loop:
        - "net.bridge.bridge-nf-call-iptables=1"
        - "net.ipv4.ip_forward=1"
      become: yes
  ```

> Una vez que hemos ejecutado el playbook anterior, y nos hemos asegurado de que cri-o está activo, podemos continuar con el proceso de inicialización del nodo maestro y de unión entre nodo maestro-trabajador.

* En primer lugar, en el nodo maestro, inicializamos el clúster de Kubernetes:

```bash
sudo kubeadm init --pod-network-cidr=192.168.0.0/16
```

Nota: El CIDR `192.168.0.0/16` es específico para Calico.

* Desde la máquina controladora de Ansible, configura `kubectl`:

```bash
mkdir -p $HOME/.kube
sudo scp nodo_maestro:/etc/kubernetes/admin.conf $HOME/.kube/config
```

* Instala el plugin de red Calico desde la máquina controladora:

```bash
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
```

Desde el nodo trabajador, únete al clúster usando el comando `kubeadm join` proporcionado al finalizar la inicialización del nodo maestro.

➡️ **6. Verificación**:

**Objetivo:** Confirmar que el clúster de Kubernetes está funcionando correctamente.

**Descripción:** Estas instrucciones nos permiten verificar que el clúster de Kubernetes ha sido configurado correctamente y está operativo.

```bash
kubectl get nodes
```

Verifica los pods:

```bash
kubectl get pods -n kube-system
```

---

### :monocle_face: Problemas experimentados y soluciones

Durante el proceso de configuración e instalación del clúster de Kubernetes, se encontraron varios desafíos que requirieron ajustes y cambios en la estrategia inicial. A continuación, se detallan estos problemas y las soluciones implementadas:

---

**Descripción del problema:** 
Al intentar configurar el clúster de Kubernetes utilizando `containerd` como runtime de contenedores y `flannel` como plugin de red, se experimentaron problemas de conectividad entre los nodos. A pesar de seguir las instrucciones y configuraciones recomendadas, los nodos no lograban comunicarse de manera adecuada, lo que impedía que el clúster funcionara correctamente.

**Solución implementada:** 
Tras varios intentos de solucionar el problema y revisar la configuración, se decidió cambiar la estrategia. Se optó por utilizar `cri-o` como runtime de contenedores y `calico` como plugin de red. Estos cambios resultaron ser efectivos, ya que, una vez implementados, la conectividad entre los nodos se estableció correctamente y el clúster comenzó a funcionar de manera óptima.

**Reflexión:** 
La elección de herramientas y tecnologías en un ecosistema tan amplio y en constante evolución como el de Kubernetes puede ser desafiante. A veces, ciertas combinaciones de herramientas pueden no ser compatibles o requerir configuraciones muy específicas para funcionar correctamente. Es esencial estar dispuesto a adaptarse y probar diferentes soluciones hasta encontrar la combinación que mejor se adapte a las necesidades y al entorno específico.

