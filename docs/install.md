# Guía de Instalación de Docker Engine en Ubuntu

Esta guía explica el proceso paso a paso para instalar **Docker Engine** en Ubuntu, basándose en la documentación oficial de Docker.

---

## 1. Requisitos Previos

### Requisitos de Sistema Operativo
Docker Engine es compatible con versiones de 64 bits de las siguientes distribuciones de Ubuntu:
- Ubuntu 26.04 (LTS)
- Ubuntu 24.04 (LTS)
- Ubuntu 22.04 (LTS)

Es compatible con las arquitecturas `x86_64` (o `amd64`), `armhf`, `arm64`, `s390x` y `ppc64le`.

### Consideraciones sobre el Firewall
- Si utilizas `ufw` o `firewalld`, ten en cuenta que cuando expones puertos de contenedores con Docker, estos **omiten** las reglas del firewall.
- Docker solo es compatible con `iptables-nft` e `iptables-legacy`. Las reglas creadas directamente con `nft` no son compatibles. Si defines reglas personalizadas, agrégalas a la cadena `DOCKER-USER` utilizando `iptables` o `ip6tables`.

---

## 2. Desinstalar Versiones Anteriores o Conflictivas

Antes de instalar la versión oficial de Docker Engine, debes desinstalar cualquier paquete antiguo o no oficial (así como `containerd` y `runc` si se instalaron de forma independiente).

Ejecuta el siguiente comando para limpiar paquetes previos:

```bash
sudo apt remove $(dpkg --get-selections docker.io docker-compose docker-compose-v2 docker-doc docker-buildx podman-docker containerd runc | cut -f1)
```

> **Nota:** La eliminación de los paquetes no borra automáticamente las imágenes, contenedores, volúmenes ni redes almacenados en `/var/lib/docker/`.

---

## 3. Métodos de Instalación

El método recomendado por Docker es configurar el **repositorio de apt de Docker** para facilitar futuras instalaciones y actualizaciones.

### Paso 1: Configurar el Repositorio de Docker

1. Actualiza el índice de paquetes de `apt` e instala los paquetes necesarios para permitir que `apt` use un repositorio sobre HTTPS:

   ```bash
   sudo apt update
   sudo apt install ca-certificates curl
   ```

2. Crea el directorio para la llave GPG de Docker y descárgala:

   ```bash
   sudo install -m 0755 -d /etc/apt/keyrings
   sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
   sudo chmod a+r /etc/apt/keyrings/docker.asc
   ```

3. Agrega el repositorio de Docker a las fuentes de `apt`:

   ```bash
   sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
   Types: deb
   URIs: https://download.docker.com/linux/ubuntu
   Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
   Components: stable
   Architectures: $(dpkg --print-architecture)
   Signed-By: /etc/apt/keyrings/docker.asc
   EOF
   ```

4. Actualiza nuevamente el índice de paquetes para incluir el nuevo repositorio:

   ```bash
   sudo apt update
   ```

---

### Paso 2: Instalar Paquetes de Docker

#### Opción A: Instalar la última versión disponible

Ejecuta el siguiente comando para instalar Docker Engine, la interfaz de línea de comandos (CLI), el runtime `containerd`, y los complementos de Buildx y Compose:

```bash
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

#### Opción B: Instalar una versión específica

1. Consulta las versiones disponibles en el repositorio:

   ```bash
   apt list --all-versions docker-ce
   ```

2. Selecciona la versión deseada e instálala especificando la variable de entorno:

   ```bash
   VERSION_STRING=5:29.8.2-1~ubuntu.24.04~noble
   sudo apt install docker-ce=$VERSION_STRING docker-ce-cli=$VERSION_STRING containerd.io docker-buildx-plugin docker-compose-plugin
   ```

---

## 4. Verificar la Instalación

Comprueba que Docker Engine se haya instalado y esté funcionando correctamente ejecutando la imagen de prueba `hello-world`:

```bash
sudo docker run hello-world
```

Este comando descarga una imagen de prueba, la ejecuta en un contenedor, imprime un mensaje de confirmación y finaliza.

---

## 5. Post-Instalación (Opcional)

Por defecto, la ejecución de comandos `docker` requiere permisos de superusuario (`sudo`). Si deseas ejecutar Docker como un usuario sin privilegios de `root`:

1. Crea el grupo `docker` (si no existe):
   ```bash
   sudo groupadd docker
   ```
2. Agrega tu usuario al grupo `docker`:
   ```bash
   sudo usermod -aG docker $USER
   ```
3. Reinicia tu sesión o ejecuta:
   ```bash
   newgrp docker
   ```
4. Comprueba que puedes ejecutar Docker sin `sudo`:
   ```bash
   docker run hello-world
   ```

---

## 6. Desinstalación

Si deseas eliminar completamente Docker Engine:

1. Desinstala los paquetes de Docker:
   ```bash
   sudo apt purge docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-ce-rootless-extras
   ```

2. Elimina todas las imágenes, contenedores y volúmenes almacenados:
   ```bash
   sudo rm -rf /var/lib/docker
   sudo rm -rf /var/lib/containerd
   ```