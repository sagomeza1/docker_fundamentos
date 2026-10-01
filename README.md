# Fundamentos de Docker

Docker permite empaquetar una aplicación y sus dependencias en una **imagen**, y ejecutar esa imagen de forma aislada en uno o varios **contenedores**. Así, el entorno necesario para ejecutar una aplicación puede reproducirse en distintos equipos.

## Conceptos básicos

- **Dockerfile**: archivo con las instrucciones para construir una imagen.
- **Imagen**: plantilla inmutable que contiene la aplicación y lo necesario para ejecutarla.
- **Contenedor**: instancia en ejecución de una imagen. Se puede iniciar, detener y eliminar sin borrar la imagen.
- **Registro**: servicio desde el que se descargan y al que se publican imágenes, como Docker Hub.

El ciclo habitual consiste en definir la aplicación en un Dockerfile, construir una imagen, ejecutar un contenedor y consultar su estado. Después, se pueden reutilizar o eliminar las imágenes según sea necesario.

![Flujo de trabajo de Docker: Dockerfile, imagen y contenedor](imgs/flujo-trabajo-docker.png)

## Probar este proyecto

Este repositorio incluye un Dockerfile basado en Nginx que copia el contenido de `sitio/` a la carpeta pública del servidor. Con Docker instalado y en ejecución, desde la raíz del proyecto:

```bash
docker build -t docker-fundamentos .
docker run --rm -d --name sitio-docker -p 8080:80 docker-fundamentos
```

Abre [http://localhost:8080](http://localhost:8080) para ver el sitio. Para detener el contenedor:

```bash
docker stop sitio-docker
```

`--rm` hace que Docker elimine el contenedor cuando se detiene; la imagen `docker-fundamentos` permanece disponible para volver a ejecutarla.

## Documentación

Sigue estas guías para profundizar en cada paso:

1. [Instalar Docker Engine en Ubuntu](docs/install.md)
2. [Construir imágenes y ejecutar contenedores](docs/docker-build-run.md)
3. [Listar, filtrar y etiquetar imágenes](docs/docker-images.md)
4. [Eliminar imágenes con `docker rmi`](docs/docker-rmi.md)

La guía de construcción y ejecución también incluye ejemplos de `docker ps`, `docker stats`, montajes de directorios y volúmenes.