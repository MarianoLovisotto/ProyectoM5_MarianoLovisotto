# GitHub MCP Server 🚀
> **Servidor Model Context Protocol (MCP) para la automatización y control de GitHub mediante Inteligencia Artificial y Lenguaje Natural.**

---

## 📌 Descripción del Proyecto

**GitHub MCP Server** es un servidor desarrollado bajo el estándar abierto **Model Context Protocol (MCP)**. Su objetivo principal es actuar como puente bidireccional entre Modelos de Lenguaje Grande (LLMs) o agentes de Inteligencia Artificial (en entornos como Antigravity IDE, Claude Desktop, Cursor, etc.) y la API REST oficial de GitHub.

A través de este servidor, un agente de IA puede comprender intenciones expresadas por el usuario en lenguaje natural (por ejemplo: *"crea un repositorio llamado backend-api y añade un issue inicial"*) y traducirlas en llamadas estructuradas a herramientas (*tool calling*) para ejecutar operaciones en GitHub de manera segura, validada y autónoma.

---

## 💡 Utilidad del MCP

El protocolo MCP estandariza la forma en que los modelos de IA se conectan a fuentes de datos y herramientas externas:
- **Automatización de tareas repetitivas:** Permite crear repositorios, gestionar issues o realizar commits sin salir del chat con el asistente de código.
- **Acceso seguro y controlado:** La interacción se realiza mediante un Token de Acceso Personal (PAT) de GitHub, garantizando que el LLM solo ejecute acciones dentro de los permisos otorgados.
- **Interoperabilidad:** Al seguir la especificación oficial de MCP (`stdio`), este servidor es compatible con cualquier Host, IDE o cliente MCP disponible en el ecosistema.
- **Retroalimentación estructurada para el LLM:** Devuelve respuestas formateadas y códigos de error explicativos que guían al modelo a autocorregir sus parámetros en caso de fallos de validación.

---

## 🛠️ Tecnologías Utilizadas

- **[TypeScript](https://www.typescriptlang.org/):** Tipado estático robusto y moderno (ES2022 / NodeNext).
- **[Node.js](https://nodejs.org/):** Entorno de ejecución en servidor.
- **[@modelcontextprotocol/sdk](https://www.npmjs.com/package/@modelcontextprotocol/sdk):** SDK oficial de MCP para la creación de servidores y gestión del protocolo Stdio JSON-RPC.
- **[@octokit/rest](https://www.npmjs.com/package/@octokit/rest):** SDK oficial y tipado de GitHub para interactuar con la API REST.
- **[Zod](https://zod.dev/):** Validación estricta y definición declarativa de esquemas de entrada para las tools del LLM.
- **[Dotenv](https://www.npmjs.com/package/dotenv):** Carga y gestión segura de variables de entorno.
- **[Vitest](https://vitest.dev/):** Suite de pruebas unitarias rápida y moderna para TypeScript.
- **[tsx](https://www.npmjs.com/package/tsx):** Ejecución directa de TypeScript en desarrollo sin paso manual de transpilación.

---

## 📂 Estructura del Proyecto

```text
MCP/
├── screenshots/
│   ├── build.png
│   ├── mcp-inspector.png
│   ├── tests.png
│   └── tools.png
├── src/
│   ├── errors/
│   │   └── index.ts
│   ├── github/
│   │   ├── client.ts
│   │   └── operations.ts
│   ├── schemas/
│   │   └── index.ts
│   ├── tools/
│   │   └── index.ts
│   └── server.ts
├── tests/
│   ├── errors.test.ts
│   ├── github.test.ts
│   └── schemas.test.ts
├── .env.example
├── .gitignore
├── package-lock.json
├── package.json
├── tsconfig.json
└── README.md
```

---

## 🏗️ Arquitectura y Flujo de Datos

El flujo de comunicación entre el usuario, el modelo de IA y GitHub sigue una arquitectura de capas bien delimitada:

```
┌──────────────┐
│   Usuario    │  "Crea un repositorio llamado 'mi-proyecto'"
└──────┬───────┘
       │
       ▼
┌──────────────────────────────────────────────┐
│               Host MCP / IDE                 │  (Antigravity, Claude Desktop, Cursor)
│  ┌────────────────────────────────────────┐  │
│  │             LLM / Agente               │  │  Interpreta el prompt y decide qué tool usar
│  └───────────────────┬────────────────────┘  │
└──────────────────────┼───────────────────────┘
                       │ JSON-RPC (Stdio)
                       ▼
┌──────────────────────────────────────────────┐
│           GitHub MCP Server                  │
│                                              │
│  1. src/server.ts                            │  Recibe la petición por StdioServerTransport
│  2. src/tools/index.ts                       │  Identifica la herramienta invocada
│  3. src/schemas/index.ts (Zod)               │  Valida tipos, longitudes y expresiones regulares
│  4. src/github/operations.ts                 │  Ejecuta la lógica de negocio
│  5. src/github/client.ts (Octokit)           │  Autentica la llamada mediante GITHUB_TOKEN
│  6. src/errors/index.ts                      │  Captura y transforma excepciones en respuestas MCP
└──────────────────────┬───────────────────────┘
                       │ HTTPS / REST
                       ▼
┌──────────────────────────────────────────────┐
│                 GitHub API                   │
└──────────────────────────────────────────────┘
```

---

## ⚙️ Instalación y Puesta en Marcha

### Prerrequisitos
- **Node.js** v18 o superior instalado.
- **npm** (o gestor de paquetes de preferencia).
- Una cuenta activa en [GitHub](https://github.com/).

### Pasos de instalación

1. **Clonar el repositorio:**
   ```bash
   git clone https://github.com/MarianoLovisotto/ProyectoM5_MarianoLovisotto.git
   cd ProyectoM5_MarianoLovisotto
   ```

2. **Instalar dependencias:**
   ```bash
   npm install
   ```

3. **Compilar el proyecto:**
   Genera los archivos JavaScript en la carpeta `dist/`:
   ```bash
   npm run build
   ```

---

## 🔑 Variables de Entorno y Token de GitHub

El servidor requiere un **GitHub Personal Access Token (PAT)** para autenticar las operaciones en tu cuenta.

### Cómo obtener tu GitHub Token:
1. Inicia sesión en [GitHub](https://github.com/).
2. Dirígete a **Settings** (Configuración) > **Developer Settings** > **Personal access tokens** > **Tokens (classic)**.
3. Haz clic en **Generate new token** (Generate new token (classic)).
4. asígnale un nombre descriptivo (por ejemplo: `MCP-GitHub-Server`).
5. Selecciona los permisos (*scopes*) necesarios:
   - `repo` (Acceso completo a repositorios públicos y privados, creación de commits, issues, etc.).
6. Genera el token y **cópialo de inmediato** (no volverá a mostrarse).

### Configuración local:
Crea un archivo `.env` en la raíz del proyecto a partir de la plantilla:

```bash
cp .env.example .env
```

Edita el archivo `.env` y coloca tu token:
```env
GITHUB_TOKEN=ghp_tuTokenPersonalDeGithubAqui1234567890
```

---

## 🔌 Configuración en IDE o Editor (Manage MCP)

Para integrar este servidor con clientes MCP (como **Antigravity IDE**, **Claude Desktop**, o configuraciones tipo `mcp_config.json`), se debe registrar en el bloque `mcpServers`.

### Ejemplo de configuración (`mcp_config.json`):

```json
{
  "mcpServers": {
    "github-mcp-server": {
      "command": "node",
      "args": [
        "node_modules/tsx/dist/cli.mjs",
        "src/server.ts"
      ],
      "env": {
        "GITHUB_TOKEN": "tu_token_de_github_aqui"
      }
    }
  }
}
```

---

## 🧰 Tools Disponibles

El servidor expone 5 herramientas especializadas para interactuar con GitHub:

| Herramienta | Descripción | Parámetros Principales |
|---|---|---|
| `create_repository` | Crea un nuevo repositorio en la cuenta del usuario autenticado. | `name` (string, 3-100 caracteres), `description` (opcional), `private` (booleano, default: `false`). |
| `list_repositories` | Lista los repositorios del usuario ordenados por fecha de actualización. | `perPage` (número entero entre 1 y 100, default: `10`). |
| `create_issue` | Crea un nuevo issue en un repositorio específico. | `owner` (string), `repo` (string), `title` (string, mín. 3 caracteres), `body` (string). |
| `list_issues` | Lista los issues de un repositorio filtrando por su estado. | `owner` (string), `repo` (string), `state` (`"open"`, `"closed"`, `"all"`, default: `"open"`). |
| `create_commit` | Crea o actualiza un archivo en el repositorio mediante un commit directo. | `owner` (string), `repo` (string), `path` (ruta del archivo), `content` (texto), `message` (mensaje del commit), `branch` (default: `"main"`). |

---

## 🛡️ Validaciones

Cada tool cuenta con validación previa basada en esquemas de **Zod** ([`src/schemas/index.ts`](src/schemas/index.ts)):
- **Nombres de repositorio:** Longitud delimitada y caracteres permitidos (`repositoryNameSchema`).
- **Valores por defecto seguros:** Los repositorios se crean como públicos (`private: false`) por defecto y la paginación inicializa en 10 elementos.
- **Tipado estricto en Enums:** Los estados de issues solo aceptan `"open"`, `"closed"` o `"all"`.
- **Manejo de excepciones amigable:** Errores de validación disparan un `ValidationError` formateado para que el LLM reciba exactamente qué campo falló y por qué motivo.

---

## 🧪 Testing

El proyecto cuenta con pruebas unitarias implementadas con **Vitest**.

### Cómo correr los tests:
```powershell
npm test
```

*(En Windows PowerShell con restricciones de script, ejecutar con `npm.cmd test` o `npx.cmd vitest run`).*

### Modo interactivo (Watch mode):
```powershell
npx vitest
```

### Qué cubren los tests:
1. **`schemas.test.ts`:** Valida que las reglas de Zod acepten entradas correctas y rechacen entradas no conformes (ej: nombres de repo inválidos, issues sin título).
2. **`errors.test.ts`:** Comprueba la correcta transformación y aislamiento de errores (`AuthenticationError`, `GitHubAPIError`).
3. **`github.test.ts`:** Mocks y aserciones de llamadas a funciones de operaciones.

---

## 🔍 Verificación con MCP Inspector

El **MCP Inspector** es la herramienta gráfica oficial para depurar servidores MCP antes de conectarlos a un entorno de producción o IDE.

### Validar antes de usar:
1. Verifica que el archivo `.env` exista y contenga un token válido.
2. Asegúrate de haber ejecutado `npm run build` para que los archivos en `dist/` estén actualizados.

### Ejecución recomendada:

```powershell
npx @modelcontextprotocol/inspector node dist/src/server.js
```

O en modo directo con `tsx`:
```powershell
npx @modelcontextprotocol/inspector npx tsx src/server.ts
```

### Qué verificar en el Inspector:
- La terminal abrirá una interfaz web local en tu navegador (usualmente `http://localhost:5173`).
- **Pestaña Tools:** Verifica que aparezcan listadas las 5 herramientas (`create_repository`, `list_repositories`, etc.).
- **Inspección de Schemas:** Haz clic en cada herramienta y revisa los campos requeridos y tipos de datos.
- **Ejecución de prueba:** Ejecuta `list_repositories` con `{ "perPage": 5 }` y confirma que recibes el JSON con los repositorios de tu cuenta de GitHub.

---

## 🩺 Troubleshooting (Resolución de Problemas)

| Problema / Error | Posible Causa | Solución Recomendada |
|---|---|---|
| **`Error: GITHUB_TOKEN no configurado`** | No existe el archivo `.env` o la variable `GITHUB_TOKEN` está vacía. | Crea el archivo `.env` en la raíz del proyecto tomando como base `.env.example` y asigna tu token personal de GitHub. |
| **`AuthenticationError: No fue posible autenticarse con GitHub` (401)** | El token ingresado es inválido, ha expirado o fue revocado en GitHub. | Genera un nuevo Personal Access Token (classic) en GitHub con permisos `repo` y actualiza el valor en tu `.env`. |
| **El IDE / Editor no detecta las Tools** | La ruta al archivo en la configuración del IDE es incorrecta o el proyecto no está compilado. | Usa **rutas absolutas** completas en la configuración (`mcp_config.json`) y asegúrate de ejecutar `npm run build` antes de conectar el cliente. |
| **Error por logs o conexión terminada abruptamente** | Emisión accidental de `console.log` en el código fuente que corrompe el canal `stdio`. | MCP utiliza `stdout` exclusivamente para mensajes JSON-RPC. Nunca agregues `console.log` en los métodos del servidor. Para registrar información de depuración, utiliza `console.error` (que escribe a `stderr`). |
| **Los tests fallan al ejecutarse** | La política de ejecución de scripts de PowerShell bloquea `npm` en Windows. | Ejecuta las pruebas usando `npm.cmd test` o ejecuta `Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned` en PowerShell como Administrador. |
| **La build falla (`npm run build`)** | Errores de sintaxis o incompatibilidad de tipos en TypeScript. | Revisa los errores emitidos por el compilador `tsc`. Asegúrate de que las extensiones de importación usen `.js` en módulos ESM (`from "./client.js"`). |

---

## 📸 Evidencias de Funcionamiento

A continuación se presentan las capturas que evidencian el correcto funcionamiento de cada componente del proyecto:

### 1. Build ejecutándose correctamente
Compilación limpia sin errores de TypeScript generando la distribución en `dist/`:
![Build exitoso](screenshots/build.png)

---

### 2. Ejecución de Tests Unitarios
Suite completa de pruebas ejecutada con Vitest pasando al 100%:
![Tests corriendo](screenshots/tests.png)

---

### 3. Herramientas MCP registradas
Inspección visual de las tools expuestas por el servidor:
![Tools registradas](screenshots/tools.png)

---

### 4. MCP Inspector detectando y listando repositorios
Validación de la conexión y ejecución de herramientas con la API de GitHub en tiempo real:
![MCP Inspector detectando repositorios](screenshots/mcp-inspector.png)

---

## 👤 Autor

- **Mariano Lovisotto**
- Repositorio del Proyecto: [ProyectoM5_MarianoLovisotto](https://github.com/MarianoLovisotto/ProyectoM5_MarianoLovisotto)

