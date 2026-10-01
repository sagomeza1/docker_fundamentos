# Guía Práctica de Docker: `docker build`, `docker run`, `docker ps` y `docker stats`

Esta guía explica cómo crear imágenes con **`docker build`**, ejecutar contenedores con **`docker run`**, consultarlos con **`docker ps`** y supervisar su consumo de recursos con **`docker stats`**.

---

## 1. `docker build`

El comando `docker build` se utiliza para construir una imagen de Docker a partir de un archivo de definición llamado **`Dockerfile`** y un contexto de construcción (archivos y carpetas del proyecto).

### Sintaxis Básica

```bash
docker build [OPCIONES] PATH | URL | -
```

El parámetro más común para `PATH` es `.` (el directorio actual).

### Opciones y Flags Principales

* `-t, --tag`: Asigna un nombre y opcionalmente una etiqueta (tag) a la imagen en formato `nombre:etiqueta`. Si no especificas etiqueta, se usa `latest` por defecto.
* `-f, --file`: Especifica la ruta del `Dockerfile` si este no se llama `Dockerfile` o no está en la raíz del directorio.
* `--build-arg`: Define variables en tiempo de compilación que se pueden pasar al `Dockerfile` (usando la instrucción `ARG`).
* `--no-cache`: Fuerza a Docker a ignorar las capas en caché y construir la imagen desde cero.
* `--target`: En un `Dockerfile` multietapa (multi-stage), especifica hasta qué etapa de construcción llegar.

### Ejemplos de Uso

#### Construir una imagen básica en el directorio actual
```bash
docker build -t mi-aplicacion:1.0 .
```

#### Construir usando un Dockerfile con nombre o ubicación personalizada
```bash
docker build -f docker/Dockerfile.prod -t mi-aplicacion:prod .
```

#### Pasar variables de entorno en tiempo de construcción
```bash
docker build --build-arg NODE_ENV=production -t mi-node-app .
```

#### Construir ignorando la caché existente
```bash
docker build --no-cache -t mi-aplicacion:latest .
```

---

## 2. `docker run`

El comando `docker run` toma una imagen de Docker y crea un nuevo contenedor ejecutable a partir de ella.

### Sintaxis Básica

```bash
docker run [OPCIONES] IMAGEN [COMANDO] [ARGUMENTOS...]
```

### Opciones y Flags Principales

* `-d, --detach`: Ejecuta el contenedor en segundo plano (modo desatendido/daemon) y muestra el ID del contenedor.
* `-it`: Combinación de `-i` (interactivo) y `-t` (asigna una pseudo-TTY). Se utiliza para interactuar con la terminal interna del contenedor.
* `-p, --publish`: Mapea un puerto del host a un puerto del contenedor (`puerto_host:puerto_contenedor`).
* `-v, --volume`: Monta un volumen o un directorio del host dentro del contenedor (`ruta_host:ruta_contenedor`).
* `--name`: Asigna un nombre personalizado y único al contenedor.
* `-e, --env`: Define variables de entorno dentro del contenedor.
* `--rm`: Elimina automáticamente el contenedor cuando este se detiene o finaliza su ejecución.
* `--network`: Conecta el contenedor a una red de Docker específica.

### Ejemplos de Uso

#### Ejecutar un servidor web Nginx en segundo plano con puerto mapeado
```bash
docker run -d -p 8080:80 --name mi-web nginx
```
*Acceso desde el navegador host en `http://localhost:8080`.*

#### Interactuar con un contenedor en modo bash (ej. Ubuntu)
```bash
docker run -it --rm ubuntu bash
```
*Abre una consola de Ubuntu; al salir (`exit`), el contenedor se elimina automáticamente.*

#### Pasar variables de entorno y montar un directorio del host
```bash
docker run -d \
  --name mi-app-node \
  -p 3000:3000 \
  -e DB_HOST=postgres-db \
  -v $(pwd)/src:/app/src \
  mi-node-app
```

---

## 3. `docker ps`

El comando `docker ps` lista los contenedores en ejecución. Muestra datos como el ID, la imagen, el estado y el nombre; para incluir los contenedores detenidos, usa `-a`.

### Sintaxis Básica

```bash
docker ps [OPCIONES]
```

### Opciones y Flags Principales

* `-a, --all`: Incluye los contenedores detenidos.
* `-f, --filter`: Filtra la lista, por ejemplo por nombre o estado.
* `-q, --quiet`: Muestra solo los ID de los contenedores.

### Ejemplos de Uso

#### Ver los contenedores en ejecución
```bash
docker ps
```

#### Ver también los contenedores detenidos
```bash
docker ps -a
```

#### Buscar un contenedor por nombre
```bash
docker ps -a --filter name=mi-web
```

---

## 4. `docker stats`

El comando `docker stats` muestra en tiempo real el consumo de recursos de los contenedores en ejecución, como CPU, memoria y tráfico de red. Pulsa `Ctrl+C` para dejar de observar la salida.

### Sintaxis Básica

```bash
docker stats [OPCIONES] [CONTENEDOR...]
```

Si no indicas un contenedor, supervisa todos los que están en ejecución. Puedes indicar su nombre o ID para limitar la consulta.

### Opciones y Flags Principales

* `--no-stream`: Muestra una sola lectura y termina, en vez de actualizar la salida continuamente.
* `-a, --all`: Incluye también los contenedores detenidos en la lista.
* `--format`: Personaliza las columnas mostradas o usa el formato `json`.

### Ejemplos de Uso

#### Supervisar todos los contenedores en ejecución
```bash
docker stats
```

#### Supervisar solo el contenedor `mi-web`
```bash
docker stats mi-web
```

#### Obtener una sola lectura del consumo
```bash
docker stats --no-stream mi-web
```

---

## Flujo de Trabajo Típico

1. **Crear el Dockerfile** en la raíz del proyecto.
2. **Construir la imagen**:
   ```bash
   docker build -t mi-proyecto:v1 .
   ```
3. **Ejecutar el contenedor**:
   ```bash
   docker run -d -p 80:80 --name app-activa mi-proyecto:v1
   ```
4. **Verificar que el contenedor está corriendo**:
   ```bash
   docker ps
   ```
5. **Consultar su consumo de recursos**:
   ```bash
   docker stats --no-stream app-activa
   ```