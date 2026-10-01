# Guía Práctica de Docker: `docker images`

Esta guía explica cómo usar **`docker images`** para consultar las imágenes locales de Docker, filtrarlas y elegir la información que se muestra.

---

## 1. `docker images`

El comando `docker images` lista las imágenes almacenadas localmente. Por defecto, muestra una tabla con el nombre o referencia de cada imagen, su ID y datos como el tamaño; las columnas pueden variar según la versión de Docker. No consulta las imágenes disponibles en registros remotos.

### Sintaxis Básica

```bash
docker images [OPCIONES] [REPOSITORIO[:ETIQUETA]]
```

El nombre del repositorio y la etiqueta son opcionales. Puedes indicarlos para limitar la lista a una imagen concreta.

### Opciones y Flags Principales

* `-a, --all`: Muestra todas las imágenes, incluidas las intermedias y las que no tienen etiqueta.
* `-f, --filter`: Filtra los resultados según una condición, por ejemplo `dangling=true` o `reference=mi-proyecto:*`.
* `-q, --quiet`: Muestra únicamente los ID de las imágenes.
* `--digests`: Incluye la columna con los digests, cuando están disponibles.
* `--format`: Personaliza la salida mediante una plantilla de Go o utiliza `json`.
* `--no-trunc`: Muestra los ID y otros valores sin truncarlos.

### Ejemplos de Uso

#### Listar las imágenes locales
```bash
docker images
```

#### Buscar una imagen por repositorio y etiqueta
```bash
docker images mi-proyecto:v1
```

#### Mostrar todas las imágenes, incluidas las intermedias
```bash
docker images -a
```

#### Mostrar solo los ID de las imágenes
```bash
docker images -q
```

#### Buscar imágenes sin etiqueta
```bash
docker images -f dangling=true
```

#### Filtrar las etiquetas de un repositorio
```bash
docker images -f 'reference=mi-proyecto:*'
```

#### Elegir las columnas de salida
```bash
docker images --format 'table {{.Repository}}\t{{.Tag}}\t{{.ID}}'
```

---

## 2. Cambiar el Nombre o la Etiqueta con `docker image tag`

`docker image tag` crea un nuevo nombre o etiqueta que apunta a una imagen local existente. No modifica la imagen ni elimina su referencia anterior: ambas mostrarán el mismo ID en `docker images`.

### Sintaxis Básica

```bash
docker image tag IMAGEN_ORIGEN[:ETIQUETA] IMAGEN_DESTINO[:ETIQUETA]
```

### Ejemplos de Uso

#### Asignar una etiqueta nueva a la misma imagen
```bash
docker image tag mi-proyecto:v1 mi-proyecto:v2
```

#### Asignar otro nombre y etiqueta
```bash
docker image tag mi-proyecto:v1 proyecto-renombrado:v2
```

#### Verificar las referencias de la imagen
```bash
docker images -f 'reference=mi-proyecto:*'
docker images proyecto-renombrado:v2
```

Si quieres conservar solo el nombre o etiqueta nuevos, comprueba primero que apuntan a la imagen correcta y luego elimina la referencia anterior:

```bash
docker rmi mi-proyecto:v1
```

> **Nota:** `docker rmi` elimina la referencia indicada; mientras la imagen tenga otras etiquetas, estas permanecen disponibles.

---

## 3. Flujo de Trabajo Típico

1. **Consultar las imágenes disponibles**:
   ```bash
   docker images
   ```
2. **Localizar una imagen específica**:
   ```bash
   docker images mi-proyecto:v1
   ```
3. **Usar su nombre y etiqueta para ejecutar un contenedor**, si es la imagen deseada:
   ```bash
   docker run --rm mi-proyecto:v1
   ```

> **Nota:** `docker images` solo consulta imágenes; para eliminar una imagen local, utiliza `docker rmi`.