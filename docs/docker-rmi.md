# Guía Práctica de Docker: `docker rmi`

Esta guía explica cómo usar **`docker rmi`** para eliminar imágenes locales de Docker y qué hacer cuando una imagen todavía está asociada a un contenedor.

---

## 1. `docker rmi`

El comando `docker rmi` elimina una o varias imágenes locales. No elimina contenedores ni imágenes almacenadas en un registro remoto. Si una imagen tiene varias etiquetas, eliminar una de ellas puede quitar solo esa etiqueta sin borrar los datos de la imagen.

### Sintaxis Básica

```bash
docker rmi [OPCIONES] IMAGEN [IMAGEN...]
```

Puedes identificar cada imagen por su nombre y etiqueta (`nombre:etiqueta`) o por su ID. Si no indicas una etiqueta en el nombre, Docker usa `latest` por defecto.

### Opciones y Flags Principales

* `-f, --force`: Fuerza la eliminación de la imagen o su etiqueta cuando Docker lo permite. Úsalo con cuidado; no elimina los contenedores que dependen de ella.
* `--no-prune`: Evita eliminar imágenes padre sin etiqueta que queden sin uso al borrar la imagen indicada.

### Ejemplos de Uso

#### Consultar las imágenes locales antes de eliminarlas
```bash
docker images
```

#### Eliminar una imagen por nombre y etiqueta
```bash
docker rmi mi-proyecto:v1
```

#### Eliminar una imagen por su ID
```bash
docker rmi 7d9495d03763
```
*Sustituye el ID del ejemplo por uno obtenido con `docker images`.*

#### Eliminar varias imágenes a la vez
```bash
docker rmi mi-proyecto:v1 mi-proyecto:v2
```

#### Forzar la eliminación cuando sea necesario
```bash
docker rmi -f mi-proyecto:v1
```
*`-f` no detiene ni elimina contenedores. Comprueba primero si todavía necesitas la imagen o sus etiquetas.*

---

## 2. Si la Imagen Está en Uso

Docker puede impedir que elimines una imagen utilizada por un contenedor. Consulta los contenedores asociados antes de volver a intentarlo:

```bash
docker ps -a --filter ancestor=mi-proyecto:v1
```

Si ya no necesitas un contenedor de la lista, detenlo si está en ejecución y luego elimínalo, sustituyendo `mi-contenedor` por su nombre o ID:

```bash
docker stop mi-contenedor
docker rm mi-contenedor
docker rmi mi-proyecto:v1
```

Si el contenedor ya está detenido, omite `docker stop`. Al terminar, ejecuta `docker images` para verificar que la imagen o etiqueta ya no aparece en la lista.