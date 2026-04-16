# Expediente X

Prueba de concepto de una plataforma de salud digital que combina blockchain con inteligencia artificial para crear una historia clinica universal y portable. El paciente es el dueno de sus datos.

Este repositorio contiene todo lo necesario para levantar el entorno local de desarrollo y ejecutar la demo funcional del proyecto.

---

## Que hace este proyecto

El sistema tiene tres partes que trabajan juntas:

**Identidad digital en blockchain.** Cada paciente recibe un identificador unico (DID) registrado en una red Hyperledger Fabric local. Ese identificador es suyo, no de ninguna institucion. Nadie puede modificarlo ni borrarlo.

**Historia clinica universal.** Cuando un medico carga un registro medico, el sistema calcula una huella digital del documento (hash SHA-256), la guarda en la blockchain y sube el archivo cifrado a IPFS. Cualquier modificacion posterior al documento seria detectable de inmediato.

**Motor de inteligencia artificial.** Con base en el historial del paciente, el sistema calcula riesgo cardiovascular, riesgo de diabetes tipo 2, detecta interacciones peligrosas entre medicamentos y clasifica la urgencia medica segun los sintomas descritos.

---

## Advertencia sobre el estado del proyecto

Esto es una prueba de concepto desarrollada para un hackathon de salud. No esta lista para produccion. Algunas partes del sistema usan datos simulados y logica simplificada a proposito para poder hacer una demo sin necesidad de integraciones reales con terceros.

Lo que si funciona de verdad: la red blockchain, los smart contracts en Go, el servidor de IA en Python y el dashboard web.

Lo que esta simulado: las firmas criptograficas Ed25519, los transaction hashes de la blockchain en el backend, y los modelos de IA que usan reglas matematicas en lugar de modelos entrenados con datos reales.

---

## Contenido del repositorio

```
hackaton_2026/
|
+-- blockchain/
|   +-- docker-compose.yml          Red Fabric, IPFS, PostgreSQL y Redis
|   +-- chaincode/
|       +-- did-registry/           Smart contract: identidad digital
|       +-- health-record/          Smart contract: registros medicos
|       +-- consent-acl/            Smart contract: permisos del paciente
|       +-- audit-log/              Smart contract: trazabilidad de accesos
|
+-- backend/
|   +-- src/
|       +-- index.js                Servidor principal (puerto 3000)
|       +-- routes/
|           +-- identity.js         /api/identity
|           +-- records.js          /api/records
|           +-- consent.js          /api/consent
|           +-- ai.js               /api/ai (conecta con Python)
|           +-- auth.js             /api/auth
|   +-- .env                        Variables de entorno (ver seccion abajo)
|
+-- ai-engine/
|   +-- main.py                     Servidor FastAPI con 4 modelos (puerto 8000)
|   +-- requirements.txt
|
+-- frontend/
|   +-- src/
|       +-- App.jsx                 Dashboard de demo
|       +-- main.jsx
|
+-- data/
|   +-- patients/
|       +-- demo_patients.json      Tres pacientes sinteticos para la demo
|   +-- drugs/
|       +-- interaction_database.json  50 pares de interacciones medicamentosas
|
+-- scripts/
|   +-- start_all.sh               Levanta todos los servicios en un comando
|   +-- stop_all.sh                Detiene todos los servicios
|
+-- README.md                      Este archivo
```

---

## Requisitos previos

Antes de instalar, verifica que tu computadora cumpla con lo siguiente:

- Conexion a internet durante la instalacion (se descargaran dependencias)
- Al menos 8 GB de RAM (Docker consume bastante memoria con Hyperledger Fabric)
- Al menos 10 GB de espacio libre en disco
- Permisos de administrador en tu computadora

---

## Instalacion en macOS

El repositorio incluye un script de instalacion automatica llamado `setup_expedientex.sh` que hace todo por ti. Cubre macOS 12 o superior, tanto en computadoras Intel como en las que tienen chip Apple Silicon (M1, M2, M3).

### Paso 1: Descarga el repositorio

Si tienes Git instalado:

```bash
git clone https://github.com/xxtochoxx/ia_salud_blockchain.git
cd ia_salud_blockchain
```

Si no tienes Git, descarga el ZIP desde GitHub y descomprimelo en tu carpeta de usuario.

### Paso 2: Da permisos al script y ejecutalo

```bash
chmod +x setup_expedientex.sh
./setup_expedientex.sh
```

El script te pedira confirmacion antes de comenzar. Luego instalara automaticamente todo lo que necesita: Homebrew, Node.js 20, Python 3.11, Go 1.21, Docker Desktop e IPFS.

La instalacion puede tardar entre 10 y 20 minutos dependiendo de tu conexion a internet.

### Paso 3: Abre Docker Desktop

Si Docker no estaba instalado antes, el script lo instalara pero necesitas abrirlo manualmente la primera vez desde la carpeta Aplicaciones. Espera a que el icono de la ballena aparezca en la barra de menu superior y muestre que esta corriendo. Luego presiona Enter en la terminal para continuar.

### Paso 4: Recarga el terminal

Cuando el script termine, ejecuta este comando para que los nuevos programas instalados queden disponibles:

```bash
source ~/.zshrc
```

---

## Instalacion en Windows

En Windows el script `.sh` no funciona directamente porque los scripts Bash no son compatibles con la terminal de Windows. Tienes dos opciones.

### Opcion A: Usar WSL 2 (recomendada)

WSL 2 es una capa de Linux que Microsoft integra en Windows 10 y 11. Una vez instalada, puedes usar el script de instalacion exactamente igual que en macOS.

**Instalar WSL 2:**

Abre PowerShell como administrador y ejecuta:

```powershell
wsl --install
```

Reinicia tu computadora cuando se lo pida. Al reiniciar, se abrira una ventana para que crees un usuario y contrasena de Linux. Una vez creado, dentro de esa terminal de Linux ejecuta los mismos comandos de la seccion de macOS.

```bash
chmod +x setup_expedientex.sh
./setup_expedientex.sh
```

**Nota importante para WSL:** Docker Desktop en Windows tiene integracion con WSL 2. Cuando instales Docker Desktop, en su configuracion ve a Settings > Resources > WSL Integration y activa la distribucion de Linux que instalaste (normalmente se llama Ubuntu).

## Como levantar el proyecto

Una vez que la instalacion este completa, tienes dos formas de arrancar todo.

### Forma rapida: script unico

En macOS o en WSL desde la carpeta del proyecto:

```bash
cd ~/hackaton_2026
./scripts/start_all.sh
```

Este script levanta Docker con toda la infraestructura (Fabric, IPFS, PostgreSQL y Redis), luego inicia el motor de IA en Python, el backend en Node.js y finalmente el frontend en React.

### Donde abrir la aplicacion

Una vez que todos los servicios esten corriendo, abre tu navegador en:

| Que es | URL |
|--------|-----|
| Dashboard principal de la demo | http://localhost:5173 |
| API del backend (verificar que corre) | http://localhost:3000/health |
| Documentacion interactiva del motor IA | http://localhost:8000/docs |
| Interfaz de IPFS | http://localhost:8080 |

### Como detener todo

```bash
cd ~/hackaton_2026
./scripts/stop_all.sh
```

O cierra cada terminal con Control+C y luego ejecuta `docker compose down` dentro de la carpeta `blockchain`.

---

## Usuarios de prueba para la demo

El sistema tiene tres usuarios cargados de forma fija para la demo. La contrasena de todos es `demo1234`.

| Nombre | Rol en el sistema |
|--------|------------------|
| Juan Garcia | Paciente (tiene hipertension y diabetes, es el caso mas interesante) |
| Dra. Maria Rodriguez | Medico |
| Admin Clinica | Administrador |

---

## Que hace cada endpoint del motor de IA

El motor de IA corre en Python con FastAPI en el puerto 8000. Puedes consultar la documentacion interactiva completa en http://localhost:8000/docs mientras el servidor este corriendo.

**POST /risk/cardiovascular**
Calcula un score de riesgo entre 0 y 100 basado en edad, presion arterial, colesterol, tabaquismo e IMC. Usa una version simplificada de la escala Framingham.

**POST /risk/diabetes**
Calcula el porcentaje de riesgo de diabetes tipo 2 usando los criterios de la ADA 2024 y el puntaje FINDRISC simplificado.

**POST /drug-check**
Recibe una lista de medicamentos del paciente y los compara contra una base de datos de 50 pares de interacciones conocidas. Clasifica cada interaccion como GRAVE, MODERADA o LEVE.

**POST /triage**
Recibe una descripcion de sintomas en texto libre y clasifica la urgencia medica segun un protocolo CTAS simplificado con 30 reglas clinicas.

---
## Tecnologias usadas

| Componente | Tecnologia | Version |
|-----------|------------|---------|
| Red blockchain | Hyperledger Fabric | 2.5 |
| Smart contracts | Go | 1.21 |
| Almacenamiento descentralizado | IPFS (kubo) | latest |
| Base de datos | PostgreSQL | 15 |
| Cache | Redis | 7 |
| Backend API | Node.js + Express | 20 LTS |
| Motor de IA | Python + FastAPI | 3.11 |
| Frontend | React + Vite | 18 |
| Contenedores | Docker + Docker Compose | 24 |
| Identidad digital | W3C DID (simulado) | Spec 1.0 |

---

## Licencia

Este proyecto fue desarrollado como parte de una participacion en un hackathon de salud. El codigo es de uso libre para fines academicos y de demostracion. No debe usarse en entornos con datos reales de pacientes sin pasar por un proceso de cumplimiento regulatorio completo.

---

## Contacto

Para preguntas sobre el proyecto o el codigo, abre un issue en este repositorio.
