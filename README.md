# Cervalid

### Plataforma web de certificación académica con trazabilidad blockchain para la inserción laboral

**Cervalid** es una plataforma orientada a instituciones educativas que permite gestionar información académica, emitir certificados y facilitar su verificación por parte de empresas y reclutadores.

Su propuesta de valor consiste en ofrecer trazabilidad del recorrido académico para que las instituciones puedan respaldar las credenciales emitidas y los empleadores puedan consultar su validez de manera sencilla.

[Características](#características-principales) · [Arquitectura](#arquitectura) · [Instalación](#instalación-con-docker-recomendada) · [Desarrollo local](#ejecución-en-modo-desarrollo-local) · [Documentación](#documentación)

---

## Índice

- [Descripción del proyecto](#descripción-del-proyecto)
- [Características principales](#características-principales)
- [Arquitectura](#arquitectura)
- [Tecnologías](#tecnologías)
- [Requisitos](#requisitos)
- [Instalación con Docker](#instalación-con-docker-recomendada)
- [Ejecución en modo desarrollo local](#ejecución-en-modo-desarrollo-local)
- [Servicios y puertos](#servicios-y-puertos)
- [Verificación del funcionamiento](#verificación-del-funcionamiento)
- [Persistencia y mantenimiento](#persistencia-y-mantenimiento)
- [Estructura de repositorios](#estructura-de-repositorios)
- [Seguridad](#seguridad)
- [Solución de problemas](#solución-de-problemas)
- [Estado del proyecto](#estado-del-proyecto)
- [Contribuciones](#contribuciones)
- [Licencia](#licencia)

---

## Descripción del proyecto

La información académica es relevante para los procesos de selección laboral, pero verificarla puede requerir tiempo y coordinación con las instituciones que la emitieron.

Cervalid busca simplificar este proceso mediante una plataforma que conecta la gestión académica institucional con la emisión y verificación de credenciales.

La plataforma contempla distintos perfiles de usuario, como administradores institucionales, estudiantes y reclutadores, con acceso según sus permisos.

### Objetivo

Facilitar la gestión, trazabilidad y verificación de credenciales académicas, proporcionando información útil para la inserción laboral y reduciendo las dificultades asociadas a la comprobación de antecedentes académicos.

## Características principales

### Gestión académica

- Registro y administración de estudiantes.
- Gestión de perfiles académicos.
- Gestión de historiales y registros académicos.
- Registro de competencias y logros.
- Emisión y administración de certificados.
- Seguimiento de eventos académicos mediante una línea de tiempo.

### Verificación de credenciales

- Consulta de información de verificación mediante identificadores y códigos.
- Comprobación de hashes asociados a documentos y registros.
- Presentación de resultados de verificación.
- Consulta de eventos de trazabilidad disponibles públicamente.
- Integración con infraestructura blockchain para el anclaje de información, según la configuración del entorno.

### Administración institucional

- Gestión de usuarios y roles.
- Contexto institucional para usuarios vinculados a una o varias instituciones.
- Separación de acceso entre instituciones.
- Registro de eventos relevantes para auditoría.
- Paneles de gestión y consulta.

> La disponibilidad de cada operación depende de la implementación y configuración de la versión que se esté ejecutando.

## Arquitectura

Cervalid está organizado como una aplicación con frontend, backend, base de datos y un componente blockchain.

La API sigue una arquitectura de monolito modular: los módulos del backend mantienen responsabilidades separadas sin necesidad de desplegar cada módulo como un microservicio independiente.

```text
                         USUARIO
                            |
                            v
                   +------------------+
                   |     Frontend     |
                   |   React + Vite   |
                   +------------------+
                            |
                       REST / HTTP
                            |
                            v
                   +------------------+
                   |     Backend      |
                   | Spring Boot API  |
                   |   Java / REST    |
                   +------------------+
                      |            |
                      v            v
              +---------------+  +------------------+
              |  PostgreSQL   |  |  Red blockchain  |
              |   Persistencia|  |      Anvil       |
              +---------------+  +------------------+
                                         |
                                         v
                                 +------------------+
                                 | DocumentRegistry |
                                 | Contrato Solidity|
                                 +------------------+
```

En el entorno local con Docker Compose, Anvil proporciona una blockchain de desarrollo. No debe confundirse con una red blockchain pública de producción.

## Tecnologías

| Componente | Tecnologías |
|---|---|
| Frontend | React, Vite, Bootstrap |
| Backend | Java, Spring Boot, Spring Security |
| Persistencia | Spring Data JPA, PostgreSQL |
| Autenticación | JWT y cookies HTTP |
| Blockchain | Solidity, Hardhat, ethers.js, Anvil |
| Contenedores | Docker, Docker Compose |
| Control de versiones | Git y GitHub |

## Requisitos

### Para ejecutar con Docker

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) en Windows o macOS, o Docker Engine con Docker Compose en Linux.
- Git.
- Acceso a Internet para descargar las imágenes necesarias.

No necesitas instalar Java, Maven, Node.js, PostgreSQL ni Hardhat por separado para ejecutar el entorno completo con Docker, siempre que los servicios estén correctamente definidos en `compose.yaml`.

### Para desarrollo local

- Git.
- Java 17, compatible con la configuración actual del backend.
- Maven, o utilizar el Maven Wrapper del proyecto si está disponible.
- Node.js 22 y npm, de acuerdo con el Dockerfile actual del frontend.
- PostgreSQL.
- Node.js y npm para las herramientas de blockchain.
- Una instancia de Anvil para las pruebas blockchain.

Consulta las versiones de los archivos de configuración de cada repositorio antes de instalar o actualizar dependencias.

## Instalación con Docker (recomendada)

Este procedimiento está pensado para una instalación desde el repositorio principal de Cervalid.

### 1. Clonar el repositorio

```bash
git clone --recurse-submodules https://github.com/97samir/cervalid-platform.git
cd cervalid
```

Si los submódulos ya están configurados en `.gitmodules`, el comando anterior descargará también los repositorios de frontend, backend y blockchain.

Si ya clonaste el repositorio sin los submódulos, ejecuta:

```bash
git submodule update --init --recursive
```

### 2. Configurar las variables de entorno

Revisa los archivos de ejemplo disponibles y crea los archivos de configuración locales que solicite el proyecto.

Por ejemplo, si el repositorio incluye un archivo `.env.example`:

```bash
cp .env.example .env
```

En Windows PowerShell puedes utilizar:

```powershell
Copy-Item .env.example .env
```

Configura las variables necesarias para el entorno de desarrollo, como las credenciales de la base de datos, la clave JWT y los parámetros de blockchain.

No copies secretos de producción ni publiques archivos `.env` con credenciales reales.

> Antes de ejecutar el proyecto desde un clon limpio, verifica que la versión publicada de `compose.yaml` indique claramente qué archivos de entorno necesita cada servicio.

### 3. Construir e iniciar los servicios

Desde la raíz del repositorio, donde se encuentra `compose.yaml`, ejecuta:

```bash
docker compose up -d --build
```

Este comando construye las imágenes que tengan una configuración `build` y arranca los servicios definidos en Compose.

La configuración debe garantizar el orden de inicio: PostgreSQL y Anvil deben estar disponibles antes de que se ejecuten los procesos que dependen de ellos. El despliegue del contrato debe finalizar correctamente antes de que el backend lo utilice.

### 4. Comprobar el estado de los servicios

```bash
docker compose ps -a
```

Para consultar los registros de ejecución:

```bash
docker compose logs --tail=100
```

También puedes consultar cada servicio individualmente:

```bash
docker compose logs cervalid-frontend
docker compose logs cervalid-api
docker compose logs cervalid-postgres
docker compose logs cervalid-anvil
docker compose logs cervalid-contract-init
```

El servicio de inicialización del contrato puede aparecer como `Exited (0)` cuando termina correctamente. Esto es esperado si se ejecuta como un proceso de una sola vez.

### 5. Abrir la aplicación

Cuando los servicios estén listos, accede a:

- **Frontend:** http://localhost:5173/
- **Backend:** http://localhost:8081/
- **PostgreSQL:** `localhost:5432`
- **Anvil RPC:** http://localhost:8545/

El backend expone una API REST. La raíz `/` no tiene por qué mostrar una página web; para probar la API, utiliza los endpoints definidos en el proyecto.

### 6. Detener la aplicación

```bash
docker compose down
```

Este comando detiene y elimina los contenedores, pero conserva los datos de los volúmenes nombrados.

Para volver a iniciar los servicios:

```bash
docker compose up -d
```

## Ejecución en modo desarrollo local

El modo local permite modificar y probar cada componente directamente desde su propio entorno de desarrollo.

### Frontend

Entra en el directorio del frontend:

```bash
cd frontend
npm ci
npm run dev
```

Verifica que la variable `VITE_API_URL` apunte a la dirección de la API que estés utilizando.

Con la configuración local habitual:

```dotenv
VITE_API_URL=http://localhost:8081
```

Abre http://localhost:5173/.

### Backend

Desde el directorio del backend, configura las variables de entorno necesarias y asegúrate de que PostgreSQL y Anvil estén disponibles.

Si utilizas Maven instalado:

```bash
mvn spring-boot:run
```

Si el proyecto incluye Maven Wrapper, puedes utilizar:

```bash
./mvnw spring-boot:run
```

En Windows PowerShell:

```powershell
.\mvnw.cmd spring-boot:run
```

El backend utiliza el puerto `8081` en la configuración actual. Revisa los perfiles de Spring y los archivos de configuración para asegurarte de que las direcciones de la base de datos y blockchain correspondan al entorno elegido.

### Blockchain

El componente blockchain utiliza Solidity, Hardhat y Anvil para compilar, desplegar y probar los contratos inteligentes de Cervalid.

#### 1. Instalar las dependencias

Desde la carpeta `blockchain`, ejecuta:

```bash
npm ci
```

#### 2. Compilar los contratos

```bash
npx hardhat compile
```

#### 3. Iniciar Anvil con persistencia local

Abre una terminal **WSL** y sitúate en la carpeta `blockchain` del proyecto. Ejecuta:

```bash
mkdir -p data
anvil --state ./data/cervalid-state.json --state-interval 30
```

- `mkdir -p data` crea el directorio de persistencia si todavía no existe.
- `anvil` inicia una blockchain local para desarrollo y pruebas.
- `--state ./data/cervalid-state.json` indica dónde guardar y recuperar el estado de la blockchain.
- `--state-interval 30` configura el guardado periódico del estado cada 30 segundos.

**Importante:** mantén esta terminal abierta mientras utilices la blockchain. En el primer inicio, Anvil creará una blockchain nueva. En los siguientes inicios, podrá recuperar el estado guardado si el archivo sigue disponible.

El directorio `data/` contiene información local de desarrollo y no debe publicarse en GitHub. Por ello, está excluido mediante la regla `data/` en `blockchain/.gitignore`. Al clonar el repositorio, no necesitas descargar esta carpeta: el comando `mkdir -p data` la crea automáticamente.

#### 4. Desplegar el contrato inteligente

Con Anvil ejecutándose, abre una segunda terminal WSL y vuelve a la carpeta `blockchain`.

Configura las variables de entorno requeridas por el script de despliegue, incluyendo `BLOCKCHAIN_RPC_URL` y `BLOCKCHAIN_PRIVATE_KEY`, utilizando una cuenta de pruebas de Anvil.

Después, ejecuta:

```bash
node scripts/deploy.js
```

El script despliega el contrato cuando corresponde y muestra la dirección resultante. Si el entorno utiliza una dirección de contrato compartida con el backend, asegúrate de que la configuración se actualice correctamente.

**Nota:** si empiezas con una blockchain nueva, tendrás que desplegar nuevamente el contrato y configurar su nueva dirección en el backend. No reutilices las claves de las cuentas de prueba en producción.

No ejecutes simultáneamente una instancia local y otra en Docker utilizando el mismo puerto del host. Si utilizas Docker Compose para levantar todo Cervalid, sigue el procedimiento de instalación con Docker en lugar de iniciar Anvil manualmente.

## Servicios y puertos

| Servicio | Puerto del host | Propósito |
|---|---:|---|
| Frontend | `5173` | Interfaz web |
| Backend | `8081` | API REST |
| PostgreSQL | `5432` | Base de datos |
| Anvil | `8545` | RPC blockchain local |

Los puertos pueden cambiar si modificas el archivo `compose.yaml`.

## Verificación del funcionamiento

Después de iniciar los servicios, realiza estas comprobaciones:

1. El frontend carga correctamente en el navegador.
2. El backend inicia sin errores de conexión a PostgreSQL.
3. PostgreSQL acepta conexiones y mantiene sus datos después de reiniciar los contenedores.
4. Anvil responde en el puerto RPC configurado.
5. El proceso de despliegue del contrato termina correctamente.
6. La API puede conectarse al contrato desplegado.
7. La aplicación puede completar los flujos de autenticación y verificación que estén implementados en la versión ejecutada.

Para consultar los registros:

```bash
docker compose logs -f cervalid-api
```

Para salir del seguimiento de registros sin detener el contenedor, pulsa `Ctrl + C`.

## Persistencia y mantenimiento

Docker Compose utiliza volúmenes nombrados para conservar los datos de servicios como PostgreSQL y Anvil.

### Reiniciar los servicios

```bash
docker compose restart
```

### Reconstruir las imágenes

```bash
docker compose up -d --build
```

### Consultar el consumo de recursos

```bash
docker stats
```

### Eliminar contenedores y redes del proyecto

```bash
docker compose down
```

### Eliminar también los volúmenes

```bash
docker compose down -v
```

**Advertencia:** el último comando elimina los volúmenes asociados al proyecto, incluidos los datos persistidos de PostgreSQL y Anvil. Utilízalo solamente si quieres reiniciar completamente el entorno de desarrollo y aceptas perder esos datos.

## Estructura de repositorios

Cervalid separa el código de cada componente para mantener una organización modular y facilitar su evolución independiente.

```text
cervalid/
├── compose.yaml
├── .env.example
├── .gitignore
├── README.md
├── docs/
├── scripts/
├── frontend/       # Repositorio independiente
├── backend/        # Repositorio independiente
└── blockchain/     # Repositorio independiente
```

Los directorios `frontend`, `backend` y `blockchain` pueden integrarse como submódulos Git del repositorio principal. En ese caso, `.gitmodules` registra las URL de los repositorios y sus rutas locales.

### Repositorios relacionados

- **Frontend:** `https://github.com/97samir/cervalid-frontend`
- **Backend:** `https://github.com/97samir/cervalid-backend`
- **Blockchain:** `https://github.com/97samir/cervalid-blockchain`

Sustituye las URL de ejemplo por las direcciones reales cuando los repositorios estén publicados.

## Seguridad

Cervalid debe ejecutarse con las precauciones adecuadas para el entorno utilizado.

- No publiques claves privadas, contraseñas reales ni tokens.
- No reutilices las cuentas y claves de desarrollo de Anvil en producción.
- Mantén los archivos de entorno fuera del control de versiones.
- Utiliza HTTPS y una configuración de cookies segura en entornos publicados.
- Limita la información académica expuesta por los endpoints públicos.
- Verifica la autorización y el aislamiento de datos entre instituciones.
- No expongas PostgreSQL ni RPC de Anvil a redes públicas sin una necesidad y controles de seguridad explícitos.
- No interpretes una validación local o un hash coincidente como prueba de que una credencial está anclada en una blockchain pública.

La configuración predeterminada de Docker de este proyecto está orientada al desarrollo y a las pruebas locales. No debe utilizarse directamente como configuración de producción.

## Solución de problemas

### El frontend no puede conectarse al backend

Comprueba que `VITE_API_URL` tenga la dirección accesible desde el navegador:

```dotenv
VITE_API_URL=http://localhost:8081
```

Recuerda que las variables `VITE_*` se incorporan durante la compilación del frontend. Si modificas la URL, reconstruye la imagen:

```bash
docker compose up -d --build cervalid-frontend
```

### El backend no puede conectarse a PostgreSQL

Consulta los registros:

```bash
docker compose logs --tail=100 cervalid-postgres
docker compose logs --tail=100 cervalid-api
```

Verifica el nombre del servicio, el puerto interno `5432`, la base de datos y las credenciales configuradas.

Dentro de Docker, el backend debe conectarse al nombre del servicio de PostgreSQL, no a `localhost`.

### El despliegue del contrato falla

Consulta:

```bash
docker compose logs cervalid-anvil
docker compose logs cervalid-contract-init
```

Comprueba que Anvil esté saludable, que el artefacto del contrato exista y que la clave privada corresponda a una cuenta con saldo de prueba en la red local.

### Un puerto ya está en uso

Comprueba si otro proceso o contenedor está utilizando el puerto requerido. Detén el servicio que genera el conflicto o cambia el mapeo correspondiente en `compose.yaml`.

### Quiero empezar desde cero

Si deseas borrar también los datos persistentes de desarrollo, revisa la advertencia de la sección [Persistencia y mantenimiento](#-persistencia-y-mantenimiento) antes de ejecutar:

```bash
docker compose down -v
docker compose up -d --build
```

## Estado del proyecto

Cervalid se encuentra en desarrollo. La plataforma integra componentes web, una API backend, persistencia relacional y herramientas blockchain para gestionar y verificar información académica.

Las funcionalidades disponibles deben evaluarse según la versión publicada de cada repositorio. Los despliegues de prueba no constituyen por sí mismos una garantía de disponibilidad, integridad legal o validez oficial de las credenciales.

## Contribuciones

Las contribuciones y sugerencias son bienvenidas.

Para proponer cambios:

1. Revisa el README y las instrucciones del repositorio correspondiente.
2. Crea una rama para tu modificación.
3. Realiza cambios acotados y verificables.
4. Ejecuta las pruebas disponibles.
5. Envía un Pull Request con una descripción clara del cambio y sus motivos.

Para cambios que involucren varios componentes, documenta los requisitos de compatibilidad entre frontend, backend y blockchain.

## Licencia

Copyright 2025-2026 © Cervalid. Todos los derechos reservados.

Términos y condiciones `LICENSE`.

---

**Cervalid** — Trazabilidad académica para conectar la formación con las oportunidades laborales.
