# Docker

##### [Inicio]

## Introducción

Docker es una plataforma de software que se utiliza para crear, implementar y ejecutar aplicaciones en contenedores. Los contenedores son entornos de software que contienen todo lo necesario para que una aplicación se ejecute, incluidas las bibliotecas, los archivos binarios y el código. Docker permite a los desarrolladores empaquetar una aplicación con todas sus dependencias en un contenedor y luego implementarla en cualquier sistema que ejecute Docker. Esto hace que sea fácil mover aplicaciones entre diferentes entornos de desarrollo y producción.

## Cómo empezar

Para un servidor, lo óptimo es instalar [Docker Engine](https://docs.docker.com/engine/). Los siguientes pasos muestran como instalarlo en Ubuntu:

- Desinstalar versiones anteriores para evitar conflictos

```sh
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

- Configurar el repositorio de Docker

```sh
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

- Instalar los paquetes de Docker

```sh
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

- Verifica que todo funciona correctamente

```sh
sudo docker run hello-world
```

[Inicio]:../README.md
