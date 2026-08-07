

# dbx-mcp-server

Un servidor de Protocolo de Contexto de Modelo (MCP) que proporciona integración con Dropbox, permitiendo que clientes compatibles con MCP interactúen con Dropbox a través de un conjunto de herramientas potentes.

**Aviso Importante:** Este proyecto no está afiliado, respaldado ni patrocinado por Dropbox. Es una integración independiente que funciona con la API pública de Dropbox.

## Tabla de Contenidos

- [Inicio Rápido](#quick-start)
- [Instalación](#installation)
- [Autenticación](#authentication)
- [Herramientas Disponibles](#available-tools)
- [Permisos de Dropbox Requeridos](#required-dropbox-permissions)
- [Ejemplos de Uso](#usage-examples)
- [Desarrollo](#development)
- [Licencia](#license)

## Inicio Rápido

1. Clona el repositorio
2. Ejecuta `npm install` para instalar las dependencias
3. Ejecuta `npm run build` para compilar el proyecto
4. Ejecuta `npm run setup`
5. Configura tu cliente MCP para utilizar el servidor

## Prerrequisitos

Registra una aplicación de Dropbox en [Dropbox App Console](https://www.dropbox.com/developers/apps):

- Selecciona la API con "Scoped access" (acceso por ámbito)
- Elige el tipo de acceso que necesite tu aplicación
- Nombra tu aplicación y haz clic en "Create app"
- En "Permissions", selecciona los permisos deseados para las acciones que utilizarás, por ejemplo:
    - `files.metadata.read`
    - `files.content.read`
    - `files.content.write`
    - `sharing.write`
    - `account_info.read`
- Agrega `http://localhost` como tu URI de redireccionamiento
- Toma nota de tu App key y App secret

## Instalación

1. **Clona el repositorio**

   ```bash
   git clone https://github.com/your-username/dbx-mcp-server.git
   cd dbx-mcp-server
   ```

2. **Instala las dependencias y compila**

   ```bash
   npm install
   npm run build
   ```

3. **Ejecuta el script de configuración**

   ```bash
   npm run setup
   ```

4. **Agrega a la configuración de MCP**

   Agrega lo siguiente a tu archivo de configuración de MCP:

   ```json
   {
     "mcpServers": {
       "dbx": {
         "command": "node",
         "args": ["/path/to/dbx-mcp-server/build/index.js"]
       }
     }
   }
   ```

## Autenticación

El servidor utiliza OAuth 2.0 con PKCE para una autenticación segura con Dropbox.

### Variables de Entorno

Requeridas:

- `DROPBOX_APP_KEY`: La clave (key) de tu aplicación de Dropbox
- `DROPBOX_APP_SECRET`: El secreto (secret) de tu aplicación de Dropbox
- `DROPBOX_REDIRECT_URI`: URI de redireccionamiento de OAuth
- `TOKEN_ENCRYPTION_KEY`: Clave de 32+ caracteres para el cifrado de tokens

Opcionales:

- `TOKEN_REFRESH_THRESHOLD_MINUTES`: Minutos antes de la expiración para renovar el token (predeterminado: 5)
- `MAX_TOKEN_REFRESH_RETRIES`: Número máximo de intentos de renovación (predeterminado: 3)
- `TOKEN_REFRESH_RETRY_DELAY_MS`: Retardo entre intentos de renovación en ms (predeterminado: 1000)

## Herramientas Disponibles

### Operaciones de Archivos

- `list_files`: Lista archivos en un directorio
- `upload_file`: Sube un archivo
- `download_file`: Descarga un archivo
- `safe_delete_item`: Elimina de forma segura con soporte para papelera de reciclaje
- `create_folder`: Crea una nueva carpeta
- `copy_item`: Copia un archivo o carpeta
- `move_item`: Mueve o renombra un archivo/carpeta

### Metadatos y Búsqueda

- `get_file_metadata`: Obtiene metadatos de archivo/carpeta
- `search_file_db`: Busca archivos y carpetas
- `get_sharing_link`: Crea enlaces de compartición
- `get_file_content`: Obtiene el contenido de un archivo

### Operaciones de Cuenta

- `get_account_info`: Obtiene información de la cuenta

## Permisos de Dropbox Requeridos

A continuación se muestra una correspondencia entre las acciones del servidor y los ámbitos (scopes) mínimos de OAuth de Dropbox requeridos:

| Acción del Servidor         | Ámbitos Requeridos de Dropbox                        |
|---------------------- |-----------------------------------------------|
| list_files            | `files.metadata.read`                         |
| upload_file           | `files.content.write`, `files.metadata.write` |
| download_file         | `files.content.read`                          |
| safe_delete_item      | `files.metadata.write`                        |
| create_folder         | `files.metadata.write`                        |
| copy_item             | `files.content.write`, `files.metadata.write` |
| move_item             | `files.content.write`, `files.metadata.write` |
| get_file_metadata     | `files.metadata.read`                         |
| search_file_db        | `files.metadata.read`                         |
| get_sharing_link      | `sharing.write`                               |
| get_file_content      | `files.content.read`                          |
| get_account_info      | `account_info.read`                           |

**Ámbitos Adicionales (si es necesario):**

- `sharing.read`: Ver configuraciones de compartición y colaboradores
- `file_requests.read` / `file_requests.write`: Para funcionalidades de solicitudes de archivos
- `contacts.read` / `contacts.write`: Para acceder a contactos de Dropbox

Para más detalles sobre los ámbitos de Dropbox, consulta la [Documentación de Permisos de Dropbox](https://www.dropbox.com/developers/reference/oauth-guide#scopes).

## Ejemplos de Uso

```typescript
// List files in root directory
await mcp.useTool("dbx-mcp-server", "list_files", { path: "" });

// Upload a file
await mcp.useTool("dbx-mcp-server", "upload_file", {
  path: "/test.txt",
  content: Buffer.from("Hello World").toString("base64"),
});

// Search for files
await mcp.useTool("dbx-mcp-server", "search_file_db", {
  query: "report",
  path: "/Documents",
  max_results: 10,
});
```

## Desarrollo

Construido con:

- TypeScript
- SDK de Model Context Protocol
- Dropbox SDK v10.34.0
- Dropbox API v2

## Licencia

Licencia MIT

Derechos de autor (c) 2025 Contribuidores de MCP Server

Por la presente se concede permiso, libre de cargos, a cualquier persona que obtenga una copia
de este software y de los archivos de documentación asociados (el "Software"), para utilizar
el Software sin restricción, incluyendo sin limitación los derechos
a usar, copiar, modificar, fusionar, publicar, distribuir, sublicenciar y/o vender
copias del Software, y a permitir a las personas a las que se les proporcione el Software
a hacer lo mismo, sujeto a las siguientes condiciones:

El aviso de derechos de autor anterior y este aviso de permiso se incluirán en todas
las copias o partes sustanciales del Software.

EL SOFTWARE SE PROPORCIONA "TAL CUAL", SIN GARANTÍA DE NINGÚN TIPO, EXPRESA O
IMPLÍCITA, INCLUYENDO PERO NO LIMITADO A LAS GARANTÍAS DE COMERCIABILIDAD,
APTITUD PARA UN PROPÓSITO PARTICULAR Y NO INFRACCIÓN. EN NINGÚN CASO LOS
AUTORES O TITULARES DE LOS DERECHOS DE AUTOR SERÁN RESPONSABLES DE NINGUNA RECLAMACIÓN, DAÑOS U OTRAS
RESPONSABILIDADES, YA SEA EN UNA ACCIÓN DE CONTRATO, AGRAVIO O DE OTRA MANERA, DECURRADAS DE,
FUERA DE O EN CONEXIÓN CON EL SOFTWARE O SU USO U OTRO TIPO DE ACCIONES EN EL
SOFTWARE.
