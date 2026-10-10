# Jenkins en Docker

Configuración de un servidor **Jenkins** dentro de un contenedor **Docker** y primer uso mediante un trabajo de prueba ("Hola Mundo").

Práctica realizada en la **Universidad Tecnológica del Valle de Toluca (UTVT)**, Ingeniería en Desarrollo y Gestión de Software.

## Contenido del repositorio

```
jenkins-docker/
├── docker-compose.yml   # Definición del contenedor de Jenkins
├── Jenkinsfile          # Pipeline de ejemplo (Hola Mundo)
├── .gitignore
└── README.md
```

## Requisitos

| Herramienta | Versión utilizada |
|---|---|
| Docker | 29.7.2 |
| Docker Desktop | Plan Personal |
| Imagen `jenkins/jenkins` | `lts-jdk21` |
| Jenkins | 2.580.1 |

## Inicio rápido

### 1. Levantar Jenkins

```bash
docker compose up -d
```

Esto descarga la imagen `jenkins/jenkins:lts-jdk21`, crea el volumen `jenkins_home` y deja el contenedor en ejecución en segundo plano.

Equivalente con `docker run`:

```bash
docker volume create jenkins_home

docker run -d --name jenkins --restart unless-stopped \
  -p 8081:8080 \
  -v jenkins_home:/var/jenkins_home \
  jenkins/jenkins:lts-jdk21
```

### 2. Abrir Jenkins

Entra desde el navegador a:

```
http://127.0.0.1:8081
```

### 3. Obtener la contraseña inicial

En el primer acceso Jenkins muestra la pantalla **Unlock Jenkins**. La contraseña se obtiene con:

```bash
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

### 4. Configuración inicial

1. Pega la contraseña en el campo **Administrator password**.
2. Selecciona **Install suggested plugins** y espera a que termine la instalación.
3. Crea el primer **usuario administrador** (usuario, contraseña, nombre completo y correo).
4. Confirma la **URL de Jenkins** (`http://127.0.0.1:8081/`) y selecciona **Save and Finish**.
5. Selecciona **Start using Jenkins** para entrar al panel principal.

## Primer uso: trabajo de prueba

### Opción A: proyecto de estilo libre (freestyle)

1. En el panel, selecciona **Nueva Tarea**.
2. Escribe un nombre (por ejemplo, `pipe-hola-mundo-jfa`) y elige **Crear un proyecto de estilo libre**.
3. En **Build Steps**, selecciona **Añadir un nuevo paso** y luego **Ejecutar línea de comandos (shell)**.
4. Escribe el comando:

   ```bash
   echo "¡Hola Mundo, soy Jesus Felipe desde Jenkins!"
   ```

5. Selecciona **Save**, luego **Construir ahora**.
6. Abre la construcción **#1** y revisa **Console Output**. Debe terminar con `Finished: SUCCESS`.

### Opción B: Pipeline con `Jenkinsfile`

El archivo `Jenkinsfile` de este repositorio define el mismo ejemplo como código:

```groovy
pipeline {
    agent any

    stages {
        stage('Saludo') {
            steps {
                echo '¡Hola Mundo, soy Jesus Felipe desde Jenkins!'
            }
        }
    }
}
```

Para usarlo, crea una tarea de tipo **Pipeline** y pega el contenido en **Pipeline script**, o configúrala con **Pipeline script from SCM** apuntando a este repositorio.

## Comandos útiles

| Comando | Descripción |
|---|---|
| `docker ps` | Lista los contenedores en ejecución |
| `docker logs jenkins` | Muestra los registros de Jenkins |
| `docker stop jenkins` | Detiene el contenedor |
| `docker start jenkins` | Inicia el contenedor detenido |
| `docker exec -it jenkins bash` | Abre una terminal dentro del contenedor |
| `docker volume inspect jenkins_home` | Muestra los detalles del volumen |
| `docker compose down` | Detiene y elimina el contenedor (conserva el volumen) |

> **Importante:** no uses `docker compose down -v` ni `docker volume rm jenkins_home` si quieres conservar tu configuración, usuarios y trabajos.

## Persistencia de datos

Los datos de Jenkins (configuración, usuarios, plugins y trabajos) se guardan en el volumen `jenkins_home`, montado en `/var/jenkins_home`. Por eso se conservan aunque el contenedor se detenga, se elimine o se actualice.

## Seguridad

- No subas al repositorio contraseñas, tokens ni el contenido del volumen `jenkins_home`.
- La contraseña inicial de administrador solo sirve para el primer acceso.
- Si expones Jenkins fuera de tu equipo, usa HTTPS y contraseñas seguras.

## Autor

**Jesus Felipe Aviles**
GitHub: [JesusFelipeA](https://github.com/JesusFelipeA)

## Referencias

- [Installing Jenkins: Docker](https://www.jenkins.io/doc/book/installing/docker/)
- [Imagen oficial jenkins/jenkins en Docker Hub](https://hub.docker.com/r/jenkins/jenkins)
- [Docker Volumes](https://docs.docker.com/engine/storage/volumes/)
