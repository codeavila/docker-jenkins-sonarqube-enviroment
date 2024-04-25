# Docker Compose para Jenkins y SonarQube

Este repositorio contiene un archivo `docker-compose.yml` para desplegar Jenkins y SonarQube en contenedores Docker. La configuración está diseñada para facilitar la integración y el análisis de código en un entorno de desarrollo.

## Servicios Incluidos

### Jenkins

**Contenedor: jenkins_server_container**

Jenkins es un servidor de automatización que se utiliza para la integración continua y la entrega continua. Este contenedor se configura con:
- **Puertos**: 8081 para la interfaz web y 50000 para la conexión de agentes esclavos.
- **Volumen**: `jenkins_data_volume` para almacenar los datos y configuraciones de Jenkins de manera persistente.
- **Red**: `jenkins_sonarqube_network` para permitir la comunicación con SonarQube.

### SonarQube

**Contenedor: sonarqube_server_container**

SonarQube es una plataforma para realizar análisis de calidad de código. Este contenedor se configura con:
- **Puerto**: 9001 para la interfaz web.
- **Volúmenes**:
  - Configuraciones, datos, registros y extensiones son almacenados en volúmenes locales para la persistencia.
- **Red**: `jenkins_sonarqube_network` para interactuar con Jenkins.

## Configuración de la Red

**Nombre: jenkins_sonarqube_network**

Los servicios utilizan una red puente (`bridge`) para facilitar la comunicación interna entre Jenkins y SonarQube sin exposición a la red externa. Esto es ideal para entornos de desarrollo donde la seguridad y la simplicidad son prioritarias.

## Cómo Usar

Para ejecutar este entorno, asegúrate de tener Docker y Docker Compose instalados en tu máquina. Luego, sigue estos pasos:

1. Clona este repositorio.
2. Navega al directorio donde se encuentra este `README.md` y el `docker-compose.yml`.
3. Ejecuta el siguiente comando:
```bash
docker compose up -d
```

Accede a Jenkins visitando http://localhost:8081 en tu navegador.

Accede a SonarQube visitando http://localhost:9001 en tu navegador.

Para detener y remover los contenedores, utiliza:

```bash
docker-compose down
```