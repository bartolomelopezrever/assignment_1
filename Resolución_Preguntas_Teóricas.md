## Assignment 1 - Bartolomé López Reverte

### **Parte 1: Preguntas Teóricas**

**1. ¿Qué es DevOps y por qué es importante en el contexto de la implementación y gestión de aplicaciones en contenedores como Kubernetes?**

DevOps es una filosofía y práctica que busca la colaboración estrecha entre equipos de desarrollo y operaciones. Su objetivo es acelerar el ciclo de vida del desarrollo de software, desde la concepción hasta la entrega y el soporte. En el ámbito de contenedores como Kubernetes, el método de trabajo DevOps es crucial porque garantiza despliegues consistentes y rápidos, adaptándose a las demandas exigentes en cuanto a agilidad por parte de los clientes. Además, los contenedores garantizan que el software se ejecute de la misma manera en cualquier entorno, lo que reduce los problemas al trabajar en equipo.

**2. ¿Cuál es el propósito principal de Kubernetes en un entorno de DevOps?**

Kubernetes tiene como principal propósito la orquestación de contenedores. Es decir, se emplea Kubernetes con el objetivo de gestionar de forma eficiente los contenedores, permitiendo a los equipos DevOps centrarse en el desarrollo sin preocuparse por la infraestructura. 

**3. ¿Qué es Ansible y cómo se utiliza en la automatización de la infraestructura y configuración?**

Ansible es una herramienta open source que nos permite automatizar tareas de infraestructura y configuración, desde la instalación de software hasta la configuración de servidores. Esta herramienta nos permite preparar la infraestructura en diferentes nodos de forma automatizada mediante la utilización de archivos playbooks, que están diseñados para desplegar una configuración personalizada de forma replicable. 

**4. ¿Por qué es importante usar Ansible como herramienta de automatización para instalar y gestionar Kubernetes?**

Utilizar Ansible para gestionar Kubernetes es esencial debido a su capacidad para garantizar despliegues uniformes y reducir errores. En un entorno complejo como Kubernetes, Ansible simplifica la instalación y configuración, asegurando que cada componente se instale y configure adecuadamente. Un aspecto a destcar de Ansible es que las tareas son idempotentes, lo que significa que se pueden ejecutar varias veces y obtener el mismo resultado, garantizando que el estado final sea el deseado.

**5. ¿Qué es un nodo gestionado en un clúster de Kubernetes y cuál es su función en la orquestación de contenedores?**

Los nodos gestionados en Kubernetes son máquinas donde se ejecutan contenedores, que ofrecen beneficios para el equipo DevOps, pues mejoran la eficiencia en los despliegues y permiten optimizar recursos y reducir costes. Su función principal es alojar un entorno de ejecución para los contenedores, garantizando que tengan los recursos necesarios. 

---

### **Parte 2: Ejercicio Práctico**

En este repositorio de github podrás encontrar la documentación y el historial de la terminal: https://github.com/bartolomelopezrever/assignment_1

---

### **Parte 3: Preguntas de Seguimiento**

**6. Describe los pasos clave que seguiste para instalar Kubernetes 1.28 en los nodos gestionados utilizando Ansible.**

- **Preparativos:** Se prepararon las máquinas virtuales y se configuró Ansible para la automatización.
- **Configuración inicial:** Se definieron los nodos objetivo en un archivo de inventario y se verificó la conectividad entre la máquina controladora y los nodos de Kubernetes.
- **Instalación de Kubernetes:** Se utilizó un playbook de Ansible para instalar los paquetes necesarios, agregar la clave GPG y el repositorio de Kubernetes, y finalmente instalar los componentes de Kubernetes.
- **Instalación y Configuración de cri-o:** Se instaló y configuró cri-o en los nodos utilizando un playbook de Ansible.
- **Configuración del Clúster de Kubernetes:** Se configuró el sistema para Kubernetes, se inicializó el nodo maestro, se configuró `kubectl`, se instaló el plugin de red Calico y se unió el nodo trabajador al clúster.

**7. ¿Cuáles son las ventajas y desventajas de utilizar Ansible para automatizar la instalación de Kubernetes en comparación con otras herramientas de automatización?**

**Ventajas:**
- **Idempotencia:** Ansible garantiza que las operaciones se realicen de manera idempotente, lo que significa que se pueden ejecutar múltiples veces sin cambiar el resultado después de la primera aplicación exitosa.
- **Extensibilidad:** Ansible tiene una amplia variedad de módulos y plugins, y se puede extender fácilmente para adaptarse a necesidades específicas.
- **Documentación clara:** Los playbooks de Ansible son fáciles de leer y entender, actuando como documentación en sí mismos.

**Desventajas:**
- **Rendimiento:** En comparación con herramientas que utilizan agentes, Ansible puede ser más lento debido a su naturaleza sin agente. Aunque Ansible puede trabajar con múltiples servidores al mismo tiempo utilizando paralelismo, todavía tiene que establecer y cerrar conexiones SSH para cada servidor. Esto puede introducir una latencia adicional, especialmente si la autenticación SSH es compleja o si hay alguna latencia en la red.
- **Curva de aprendizaje:** Aunque Ansible es relativamente fácil de aprender, puede requerir tiempo para dominar características avanzadas.

**8. ¿Qué comandos de kubectl utilizarías para verificar el estado de los nodos y los pods en el clúster de Kubernetes recién instalado?**

- Para verificar el estado de los nodos: `kubectl get nodes`.
- Para verificar el estado de los pods: `kubectl get pods`. En el caso de que quisieramos observar los del sistema `kubectl get pods -n kube-system`.

**9. ¿Cuál es la importancia de la alta disponibilidad en un clúster de Kubernetes y cómo se puede lograr?**

La alta disponibilidad es crucial para garantizar que las aplicaciones y servicios en un clúster de Kubernetes estén siempre disponibles y operativos, incluso en caso de fallos en uno o más nodos. Se puede lograr mediante:

- **Múltiples nodos maestros:** Tener varios nodos maestros en diferentes zonas o regiones para garantizar que el clúster siga funcionando si un nodo maestro falla.
- **Almacenamiento persistente:** Utilizar soluciones de almacenamiento que ofrezcan redundancia y replicación.
- **Replicas y autoescalado:** Asegurarse de que los pods se repliquen en diferentes nodos y utilizar el autoescalado para adaptarse a las demandas cambiantes.

**10. ¿Qué medidas de seguridad considerarías al implementar un clúster de Kubernetes en producción?**

- **Red de pod segura:** Implementar políticas de red para controlar el tráfico entre pods.
- **Actualizaciones regulares:** Mantener el clúster y todas sus herramientas y aplicaciones actualizadas.
- **Limitar el acceso:** Restringir el acceso al API server de Kubernetes solo a IPs confiables.
- **Escaneo de vulnerabilidades:** Realizar escaneos regulares de vulnerabilidades en imágenes de contenedores.

---

### **Parte 4: Evaluación de la Configuración**

En este repositorio de github podrás encontrar la documentación, los archivos .yaml y el historial de la terminal: https://github.com/bartolomelopezrever/assignment_1

---

### **Parte 5: Discusión y Retroalimentación**

A nivel personal, enfrentarme a este ejercicio práctico me ha servido para adquirir conocimientos generales sobre el proceso de instalación de Kubernetes a través de Ansible. Por ejemplo, antes de realizar este ejercicio no conocía el concepto de "runtime", o el de "plugin de red". Sin embargo, gracias a la realización de esta tarea he podido comprobar que estos dos últimos conceptos están relacionados a herramientas que son claves a la hora de configurar un clúster de Kubernetes.

En definitiva, la experiencia global al realizar esta tarea ha sido buena. En algunos momentos, por desconocimiento, se siente frustración al no saber como solventar los errores que te encuentras, pero considero que exponerse a este tipo de situaciones ayudan a avanzar en el proceso de aprendizaje y es un buen método de enseñanza.

