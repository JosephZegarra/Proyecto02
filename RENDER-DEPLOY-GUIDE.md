# 🚀 Guía Paso a Paso: Desplegar en Render

## 📋 Resumen del Proceso

```mermaid
graph TD
    A[1. Preparar Código] --> B[2. Subir a GitHub]
    B --> C[3. Crear PostgreSQL en Render]
    C --> D[4. Importar Datos a Render]
    D --> E[5. Crear Web Service en Render]
    E --> F[6. Configurar Variables]
    F --> G[7. Deploy Automático]
    G --> H[8. Verificar Aplicación]
```

---

## ⚙️ Cómo Funciona en Render

### Arquitectura en Render

```
┌─────────────────────────────────────────┐
│          TU COMPUTADORA                 │
│  ┌──────────────────────────────┐      │
│  │  database-backup.sql         │      │
│  │  (27 socios + todos los datos)│     │
│  └────────────┬─────────────────┘      │
│               │ psql import            │
│               ▼                        │
└───────────────┼─────────────────────────┘
                │
                │ Internet
                ▼
┌─────────────────────────────────────────┐
│           RENDER CLOUD                  │
│                                         │
│  ┌─────────────────────────────┐       │
│  │  PostgreSQL Managed DB      │       │
│  │  (Se crea VACÍA primero)    │       │
│  │  Luego importas datos ──────┤       │
│  └────────────▲────────────────┘       │
│               │                        │
│               │ JDBC connection        │
│               │                        │
│  ┌────────────▼────────────────┐       │
│  │  Web Service (Docker)       │       │
│  │  - Usa tu Dockerfile        │       │
│  │  - Spring Boot App          │       │
│  │  - Puerto 8080              │       │
│  └─────────────────────────────┘       │
│               │                        │
│               ▼                        │
│     https://tu-app.onrender.com       │
└─────────────────────────────────────────┘
```

---

## 📝 PASO 1: Preparar tu Código

### 1.1 Verificar que tienes Git configurado

```powershell
git --version
```

Si no tienes Git, descárgalo de: https://git-scm.com/

### 1.2 Inicializar repositorio (si no lo has hecho)

```powershell
cd G:\Desarrollo\Página\PaginaVerificación\springboot-web

# Verificar si ya tienes git
git status

# Si no está inicializado:
git init
git add .
git commit -m "Initial commit con Docker configuration"
```

### 1.3 Crear repositorio en GitHub

1. Ve a https://github.com/
2. Clic en "New repository" (botón verde)
3. Configuración:
   - **Repository name**: `springboot-asociacion` (o el nombre que prefieras)
   - **Visibility**: Public (para usar Render gratuito) o Private (si tienes plan de pago)
   - **NO marques**: Add README, .gitignore, license
4. Clic en "Create repository"

### 1.4 Conectar tu código con GitHub

GitHub te mostrará comandos. Usa estos:

```powershell
# Agregar remote (usa la URL que GitHub te muestre)
git remote add origin https://github.com/TU_USUARIO/springboot-asociacion.git

# Si ya habías hecho commits:
git branch -M main
git push -u origin main

# Si no habías hecho commits:
git add .
git commit -m "Docker configuration ready"
git push -u origin main
```

**Usuario y contraseña de GitHub:**
- Usuario: Tu nombre de usuario de GitHub
- Contraseña: **Personal Access Token** (no tu contraseña normal)
  - Ve a GitHub → Settings → Developer settings → Personal access tokens → Generate new token
  - Selecciona permisos: `repo` completo
  - Copia el token (solo se muestra una vez)

---

## 🗄️ PASO 2: Crear PostgreSQL en Render

### 2.1 Crear cuenta en Render

1. Ve a https://render.com/
2. Clic en "Get Started for Free"
3. Regístrate con:
   - Email
   - O conecta con GitHub (recomendado)

### 2.2 Crear PostgreSQL Database

1. En el Dashboard de Render, clic en **"New +"** (arriba a la derecha)
2. Selecciona **"PostgreSQL"**

3. **Configuración:**

   | Campo | Valor Recomendado | Notas |
   |-------|-------------------|-------|
   | **Name** | `asociacion-db` | El nombre que quieras |
   | **Database** | `AsociacionTitular` | ⚠️ Importante: mismo nombre que tu BD local |
   | **User** | `postgres` | Usuario por defecto |
   | **Region** | Oregon (US West) | Elige la más cercana a tus usuarios |
   | **PostgreSQL Version** | **18** | ⚠️ Importante: misma versión que local |
   | **Datadog API Key** | (vacío) | Solo para monitoreo avanzado |
   | **Plan** | **Free** | $0/mes por 90 días, luego $7/mes |

4. Clic en **"Create Database"**

5. **ESPERA 2-3 minutos** mientras Render crea la base de datos
   - Verás un spinner
   - Cuando esté lista, dirá "Available" con un ícono verde

### 2.3 Guardar Información de Conexión

Una vez creada, verás varias URLs. **Copia y guarda** estos datos:

```
Internal Database URL:
postgres://asociacion_db_user:XXXXXXXX@dpg-xxxxx-a/asociacion_db

External Database URL:
postgres://asociacion_db_user:XXXXXXXX@dpg-xxxxx-a.oregon-postgres.render.com/asociacion_db

PSQL Command:
PGPASSWORD=XXXXXXXX psql -h dpg-xxxxx-a.oregon-postgres.render.com -U asociacion_db_user asociacion_db
```

**Diferencias:**
- **Internal URL**: Para usar desde otros servicios de Render (tu app)
- **External URL**: Para conectarte desde tu PC

---

## 📤 PASO 3: Importar Datos a Render PostgreSQL

Aquí es donde **SÍ usamos** el archivo `database-backup.sql` que creamos.

### 3.1 Desde tu PC local

Abre PowerShell en la carpeta de tu proyecto:

```powershell
cd G:\Desarrollo\Página\PaginaVerificación\springboot-web
```

### 3.2 Importar con psql

**Método 1: Si psql está en tu PATH**

```powershell
# Usa el External Database URL que copiaste
psql "postgres://asociacion_db_user:PASSWORD@dpg-xxxxx-a.oregon-postgres.render.com/asociacion_db" < database-backup.sql
```

**Método 2: Si psql NO está en tu PATH**

```powershell
# Navega a la carpeta de PostgreSQL (ajusta la versión)
cd "C:\Program Files\PostgreSQL\18\bin"

# Ejecuta el import (usa ruta completa al archivo)
.\psql "postgres://asociacion_db_user:PASSWORD@dpg-xxxxx-a.oregon-postgres.render.com/asociacion_db" < "G:\Desarrollo\Página\PaginaVerificación\springboot-web\database-backup.sql"
```

### 3.3 Verificar Importación

```powershell
# Conectarte a la BD de Render
psql "postgres://asociacion_db_user:PASSWORD@dpg-xxxxx-a.oregon-postgres.render.com/asociacion_db"

# Dentro de psql:
SELECT COUNT(*) FROM socios;
-- Debería mostrar: 27

SELECT COUNT(*) FROM rol;
-- Debería mostrar: 7

\q  # Para salir
```

**Si ves los 27 socios: ✅ Importación exitosa!**

---

## 🚀 PASO 4: Crear Web Service en Render

### 4.1 Crear el Servicio

1. En Render Dashboard, clic en **"New +"**
2. Selecciona **"Web Service"**

### 4.2 Conectar Repositorio

1. **Conecta tu cuenta de GitHub** (si no lo has hecho)
2. Busca y selecciona tu repositorio: `springboot-asociacion`
3. Clic en **"Connect"**

### 4.3 Configurar Web Service

| Campo | Valor | Notas |
|-------|-------|-------|
| **Name** | `asociacion-app` | El nombre que quieras |
| **Region** | Oregon (US West) | ⚠️ Misma región que tu PostgreSQL |
| **Branch** | `main` | O la rama que uses |
| **Root Directory** | (vacío) | El Dockerfile está en la raíz |
| **Runtime** | **Docker** | ⚠️ Importante: NO "Java" |
| **Build Command** | (vacío) | Docker lo maneja automáticamente |
| **Start Command** | (vacío) | Docker usa ENTRYPOINT del Dockerfile |
| **Plan** | **Free** | $0/mes con limitaciones |

**Limitaciones del Plan Free:**
- ⏸️ Se suspende tras 15 min de inactividad
- ⏱️ Primer request tras suspensión: 30-60 segundos
- 💾 512 MB RAM
- ⏰ 750 horas/mes gratis

### 4.4 NO hagas clic en "Create Web Service" todavía

Primero configuraremos las variables de entorno.

---

## 🔐 PASO 5: Configurar Variables de Entorno

Antes de crear el servicio, necesitas configurar cómo tu app se conectará a PostgreSQL.

### 5.1 En la misma página, baja hasta "Environment Variables"

Clic en **"Add Environment Variable"** para cada una:

| Key | Value | Explicación |
|-----|-------|-------------|
| `SPRING_DATASOURCE_URL` | `<Internal Database URL>` | ⚠️ Usa la **Internal** URL que copiaste antes |
| `SPRING_DATASOURCE_USERNAME` | `asociacion_db_user` | Usuario de Render (puede ser diferente a "postgres") |
| `SPRING_DATASOURCE_PASSWORD` | `<password>` | La contraseña de la URL |
| `SPRING_DATASOURCE_DRIVER_CLASS_NAME` | `org.postgresql.Driver` | Driver de PostgreSQL |
| `SPRING_JPA_HIBERNATE_DDL_AUTO` | `validate` | ⚠️ Importante: `validate` (no `update`) |
| `SPRING_JPA_SHOW_SQL` | `false` | En producción, no mostrar SQL |
| `SPRING_PROFILES_ACTIVE` | `prod` | Perfil de producción |
| `JAVA_OPTS` | `-Xms256m -Xmx512m` | Memoria JVM |

**Configuración de Email (opcional, si quieres que funcione):**

| Key | Value |
|-----|-------|
| `SPRING_MAIL_HOST` | `smtp.gmail.com` |
| `SPRING_MAIL_PORT` | `587` |
| `SPRING_MAIL_USERNAME` | `joseph.zegarra.g@gmail.com` |
| `SPRING_MAIL_PASSWORD` | `eniw nfxm cpit ngme` |
| `SPRING_MAIL_PROPERTIES_MAIL_SMTP_AUTH` | `true` |
| `SPRING_MAIL_PROPERTIES_MAIL_STARTTLS_ENABLE` | `true` |

### 5.2 ¿Por qué `validate` en lugar de `update`?

```
Local (desarrollo):  ddl-auto=update  ← Hibernate modifica la BD automáticamente
Producción (Render): ddl-auto=validate ← Solo valida, NO modifica (más seguro)
```

En producción:
- ✅ **validate**: Verifica que las entidades coincidan con la BD
- ❌ **update**: Puede modificar la BD automáticamente (riesgoso)
- ❌ **create**: Elimina TODO y recrea (NUNCA en producción)

---

## 🎬 PASO 6: Deploy

### 6.1 Iniciar el Deploy

1. Verifica que todas las variables estén correctas
2. Clic en **"Create Web Service"**
3. Render comenzará el deploy automáticamente

### 6.2 Proceso de Deploy

Verás logs en tiempo real. El proceso tarda **5-10 minutos** la primera vez:

```
==> Cloning from GitHub...
==> Building with Dockerfile...
==> Stage 1: Building with Maven...
    Downloading dependencies... (2-3 min)
    Compiling application... (1-2 min)
    Creating JAR file...
==> Stage 2: Creating runtime image...
    Copying JAR...
    Creating image...
==> Pushing image...
==> Starting service...
    Your service is live 🎉
```

### 6.3 Estados del Deploy

| Estado | Significado | Acción |
|--------|-------------|--------|
| 🔵 **Building** | Compilando con Dockerfile | Espera 5-10 min |
| 🟢 **Live** | ✅ Aplicación funcionando | ¡Listo! |
| 🔴 **Build Failed** | ❌ Error en compilación | Revisa logs |
| 🟡 **Deploy Failed** | ❌ Error al iniciar | Revisa variables de entorno |

---

## ✅ PASO 7: Verificar Aplicación

### 7.1 Obtener URL de tu Aplicación

Render te asigna una URL automáticamente:

```
https://asociacion-app.onrender.com
```

O puedes configurar un dominio personalizado después.

### 7.2 Probar la Aplicación

1. **Abre la URL en tu navegador**
   
   **Primera vez (después de suspensión):**
   - ⏱️ Puede tardar 30-60 segundos
   - Verás "Starting service..." o página en blanco
   - ⏳ Espera y recarga

2. **Deberías ver tu página de login**

3. **Intenta iniciar sesión** con las credenciales que usabas localmente
   - Usuario: (tu DNI o usuario)
   - Contraseña: (tu contraseña)

4. **Si puedes iniciar sesión: ✅ TODO FUNCIONA!**

### 7.3 Ver Logs

En Render Dashboard:
1. Clic en tu Web Service
2. Pestaña **"Logs"**
3. Verás logs en tiempo real

Busca:
```
Started SpringbootWebApplication in X.XXX seconds
```

---

## 🛠️ Troubleshooting

### ❌ "Build Failed"

**Error común:** `Error resolving dependencies`

**Solución:**
- Verifica que `pom.xml` está en el repositorio
- Asegúrate de que el Dockerfile está en la raíz del repo

### ❌ "Application failed to start"

**Error común:** No puede conectarse a PostgreSQL

**Verificar:**
1. Variables de entorno correctas (especialmente `SPRING_DATASOURCE_URL`)
2. Usa **Internal Database URL**, no External
3. Usuario y contraseña correctos
4. PostgreSQL está "Available" (verde)

**Ver logs:**
```
Database connection failed
```

**Solución:** Revisa variables de entorno desde Render Dashboard → tu servicio → Environment

### ❌ "502 Bad Gateway"

**Causa:** La aplicación no responde en el puerto correcto

**Solución:** 
- Asegúrate de que Spring Boot está en puerto 8080
- Render detecta automáticamente el puerto desde EXPOSE en Dockerfile

### ❌ No puedo iniciar sesión

**Causas posibles:**
1. Datos no se importaron correctamente

   **Verificar:**
   ```powershell
   psql "<External Database URL>"
   SELECT COUNT(*) FROM socios;
   ```

2. Passwords no coinciden

   **Nota:** Si las contraseñas en local estaban hasheadas, deben ser las mismas en Render

---

## 🔄 Actualizaciones Futuras

### Cuando hagas cambios en tu código:

```powershell
# 1. Commit cambios
git add .
git commit -m "Descripción del cambio"
git push

# 2. Render detecta el push y redeploya automáticamente
# No necesitas hacer nada más
```

### Cuando cambies datos en la BD:

```powershell
# 1. Exportar datos actualizados
pg_dump -U postgres -d AsociacionTitular --no-owner --no-acl --clean --if-exists -f database-backup.sql

# 2. Importar a Render
psql "<External Database URL>" < database-backup.sql
```

---

## 📊 Resumen del Flujo Completo

```
1. Código → GitHub
   └─ git push

2. Render PostgreSQL
   ├─ Crear BD vacía (Web UI)
   └─ Importar datos (psql desde tu PC)

3. Render Web Service
   ├─ Conectar a GitHub repo
   ├─ Configurar variables de entorno
   └─ Deploy automático (usa Dockerfile)

4. Resultado
   └─ https://tu-app.onrender.com (LIVE!)
```

---

## ✅ Checklist Final

Antes de empezar, asegúrate de tener:

- [ ] Cuenta de GitHub
- [ ] Código subido a repositorio de GitHub
- [ ] Cuenta de Render creada
- [ ] PostgreSQL 18 instalado localmente (para psql)
- [ ] Archivo `database-backup.sql` generado
- [ ] Connection strings de Render guardados

Durante el proceso:

- [ ] PostgreSQL creado en Render (Available ✅)
- [ ] Datos importados correctamente (27 socios ✅)
- [ ] Web Service creado con runtime Docker
- [ ] Variables de entorno configuradas
- [ ] Deploy completado (Live ✅)
- [ ] Login funciona ✅

---

¡Listo! Ahora tienes tu aplicación Spring Boot corriendo en la nube con Render! 🚀
