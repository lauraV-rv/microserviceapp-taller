# Taller: Despliegue de Microservicios con Vagrant + Docker

Este taller consiste en desplegar una aplicación de microservicios (basada en el repositorio `microservice-app-example`) usando Vagrant para aprovisionar la infraestructura (múltiples VMs) y Docker para ejecutar cada componente en contenedores.  
La solución final incluye:

- Servicios desplegados en VMs independientes (aislamiento por componente).
- **Red privada entre VMs** para comunicación interna y salida a Internet (NAT) para instalar dependencias, clonar repositorio y descargar imágenes base.
- **HAProxy** como Gateway/Reverse Proxy para enrutar el tráfico hacia los servicios.
- **Balanceo de carga (Round Robin)** para el servicio TODOs API con dos instancias.
- **Redis** como canal de eventos para operaciones CREATE/DELETE de TODOs.
- **Log Message Processor** como consumidor de eventos desde Redis.
- **Zipkin** como componente de trazabilidad distribuida (servicio “faltante” descubierto en ejecución) y validado con trazas reales.

---

**¿Qué incluye esta entrega?**

En este repositorio se incluyen:

- Vagrantfile con la definición de todas las VMs.
- Diagrama de arquitectura / infraestructura.

El Vagrantfile realiza el aprovisionamiento base por VM, incluyendo:

- red privada 192.168.56.0/24 para comunicación interna
- NAT para salida a Internet (para descargar imágenes Docker, instalar paquetes y clonar repositorio)
- instalación de Docker y dependencias mínimas
- clonado del repositorio base microservice-app-example

**Nota:** En esta entrega no se incluyen los Dockerfile creados durante el taller.
El despliegue final se ejecutó construyendo imágenes y levantando contenedores dentro de cada VM según la receta de cada servicio.

---

## Diseño de la arquitectura 


![Arquitectura](./imagenes/arquitectura.png)

---

## Componentes desplegados (VMs)

| VM | Rol/Servicio | IP privada | Puerto(s) interno(s) | Notas |
|---|---|---:|---:|---|
| `haproxy` | HAProxy (Gateway + LB) | `192.168.56.10` | `80` | Enruta `/`, `/login`, `/todos` y balancea TODOs |
| `frontend` | Frontend (Vue + Nginx) | `192.168.56.11` | `8080` | UI de la aplicación |
| `auth` | Auth API (Go) | `192.168.56.12` | `8000` | Genera JWT y valida usuario vía Users API |
| `users` | Users API (Spring Boot) | `192.168.56.13` | `8083` | Endpoints protegidos con JWT |
| `todos` | TODOs API #1 (NodeJS) | `192.168.56.14` | `8082` | Publica eventos a Redis |
| `todos2` | TODOs API #2 (NodeJS) | `192.168.56.17` | `8082` | Segunda instancia para balanceo |
| `redis` | Redis | `192.168.56.16` | `6379` | Canal `log_channel` |
| `processor` | Log Message Processor (Python) | `192.168.56.15` | — | Consume eventos de Redis |
| `zipkin` | Zipkin (Tracing) | `192.168.56.18` | `9411` | UI + API de trazas |


\
**Zipkin (servicio faltante)**

Se integró Zipkin como componente de trazabilidad distribuida para observar el flujo de las peticiones entre microservicios.

- Zipkin UI: http://localhost:9411

Para validar que Zipkin está recibiendo trazas y registrando servicios:

- curl http://localhost:9411/api/v2/services

Salida esperada (ejemplo):

["auth-api","todos-api"]

\
Aquí pegaré la evidencia de mi ejecución donde se confirma que Zipkin detecta servicios y recibe trazas.

\
<img width="1918" height="761" alt="captura zipkin" src="https://github.com/user-attachments/assets/667c921a-4273-4f72-b8f1-5eb3137ab227" />

<img width="1917" height="783" alt="captura zipkin 2" src="https://github.com/user-attachments/assets/1e76860e-334a-4030-9bd1-8efdec417293" />



**Log Message Processor (eventos desde Redis)**

El microservicio Log Message Processor consume eventos publicados por TODOs API en Redis (log_channel) y los imprime en la salida estándar.
A continuación se muestra un ejemplo de salida del contenedor mostrando eventos CREATE/DELETE:

\
<img width="1902" height="237" alt="captura logs processor" src="https://github.com/user-attachments/assets/259a5267-5716-4513-929c-bffda46198b0" />

