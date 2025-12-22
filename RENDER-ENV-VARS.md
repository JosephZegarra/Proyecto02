# 🔧 Configuración de Variables de Entorno para Render

## 📝 Tu Base de Datos en Render

- **Service ID**: `dpg-d54rvaali9vc73en9b6g-a`
- **Ya creada**: ✅

---

## ⚙️ Variables de Entorno a Configurar

Cuando crees el **Web Service** en Render, debes agregar estas variables de entorno:

### 1. Base de Datos (OBLIGATORIAS)

En el Dashboard de Render, ve a tu **PostgreSQL Database** (`dpg-d54rvaali9vc73en9b6g-a`) y copia las **Connection Strings**.

| Variable | Valor | Dónde obtenerlo |
|----------|-------|-----------------|
| `SPRING_DATASOURCE_URL` | `jdbc:postgresql://[INTERNAL_HOST]/[DATABASE]` | Ver instrucciones abajo ⬇️ |
| `SPRING_DATASOURCE_USERNAME` | Usuario de Render | Dashboard → PostgreSQL → Connection → User |
| `SPRING_DATASOURCE_PASSWORD` | Contraseña de Render | Dashboard → PostgreSQL → Connection → Password |

#### 🔍 Cómo obtener el `SPRING_DATASOURCE_URL`:

1. En Render Dashboard, ve a tu PostgreSQL: `dpg-d54rvaali9vc73en9b6g-a`
2. Busca **"Internal Database URL"**:
   ```
   postgres://user:password@dpg-d54rvaali9vc73en9b6g-a/database_name
   ```

3. **Convierte** a formato JDBC:
   ```
   Render te da:
   postgres://user:password@dpg-d54rvaali9vc73en9b6g-a/database_name
   
   Tú usas (en la variable):
   jdbc:postgresql://dpg-d54rvaali9vc73en9b6g-a/database_name
   ```

**Ejemplo real:**
```
Internal Database URL de Render:
postgres://asociacion_db_user:ABC123xyz@dpg-d54rvaali9vc73en9b6g-a/AsociacionTitular

Tu SPRING_DATASOURCE_URL será:
jdbc:postgresql://dpg-d54rvaali9vc73en9b6g-a/AsociacionTitular
```

---

### 2. Spring Boot (OBLIGATORIAS)

| Variable | Valor |
|----------|-------|
| `SPRING_PROFILES_ACTIVE` | `prod` |
| `JAVA_OPTS` | `-Xms256m -Xmx512m` |

---

### 3. Email (OPCIONAL - solo si quieres envío de correos)

| Variable | Valor |
|----------|-------|
| `SPRING_MAIL_HOST` | `smtp.gmail.com` |
| `SPRING_MAIL_PORT` | `587` |
| `SPRING_MAIL_USERNAME` | `joseph.zegarra.g@gmail.com` |
| `SPRING_MAIL_PASSWORD` | `eniw nfxm cpit ngme` |

---

## 🎯 Cómo Configurar en Render

### Durante la Creación del Web Service

1. **New +** → **Web Service**
2. Conecta tu repositorio GitHub
3. Baja hasta **"Environment Variables"**
4. Haz clic en **"Add Environment Variable"**
5. Agrega cada variable una por una:

```
Key: SPRING_DATASOURCE_URL
Value: jdbc:postgresql://dpg-d54rvaali9vc73en9b6g-a/AsociacionTitular
```

```
Key: SPRING_DATASOURCE_USERNAME
Value: [tu usuario de Render]
```

```
Key: SPRING_DATASOURCE_PASSWORD
Value: [tu contraseña de Render]
```

```
Key: SPRING_PROFILES_ACTIVE
Value: prod
```

```
Key: JAVA_OPTS
Value: -Xms256m -Xmx512m
```

6. Continúa con el resto de la configuración
7. **Create Web Service**

---

### Después de Crear el Web Service

Si ya creaste el servicio sin las variables:

1. Ve a tu **Web Service** en Render Dashboard
2. Click en **"Environment"** (menú lateral izquierdo)
3. Click en **"Add Environment Variable"**
4. Agrega las variables
5. **Save Changes**
6. Render hará **redeploy automático**

---

## 📋 Template de Variables (Copy-Paste)

Copia esto y reemplaza los valores entre corchetes:

```bash
# Base de datos
SPRING_DATASOURCE_URL=jdbc:postgresql://dpg-d54rvaali9vc73en9b6g-a/AsociacionTitular
SPRING_DATASOURCE_USERNAME=[tu_usuario_render]
SPRING_DATASOURCE_PASSWORD=[tu_password_render]

# Spring Boot
SPRING_PROFILES_ACTIVE=prod
JAVA_OPTS=-Xms256m -Xmx512m

# Email (opcional)
SPRING_MAIL_HOST=smtp.gmail.com
SPRING_MAIL_PORT=587
SPRING_MAIL_USERNAME=joseph.zegarra.g@gmail.com
SPRING_MAIL_PASSWORD=eniw nfxm cpit ngme
```

---

## ✅ Verificación

Para obtener tus valores reales:

### Paso 1: Ver tu PostgreSQL en Render

```
Dashboard → PostgreSQL (dpg-d54rvaali9vc73en9b6g-a) → Info
```

Verás algo como:

```
Database: AsociacionTitular
User: asociacion_db_xxxx_user
Password: 01A2B3c4D5e6F7g8H9i0
Internal Database URL: postgres://asociacion_db_xxxx_user:01A2B3c4D5e6F7g8H9i0@dpg-d54rvaali9vc73en9b6g-a/AsociacionTitular
```

### Paso 2: Convertir a Variables

```
SPRING_DATASOURCE_URL=jdbc:postgresql://dpg-d54rvaali9vc73en9b6g-a/AsociacionTitular
SPRING_DATASOURCE_USERNAME=asociacion_db_xxxx_user
SPRING_DATASOURCE_PASSWORD=01A2B3c4D5e6F7g8H9i0
```

---

## 🔍 Diferencias: Local vs Render

| Configuración | Local (Docker) | Render (Producción) |
|---------------|----------------|---------------------|
| **Perfil** | Default | `prod` |
| **URL** | `localhost:5432` | `dpg-d54rvaali9vc73en9b6g-a` |
| **Usuario** | `postgres` | Usuario de Render |
| **Configuración** | `application.properties` | Variables de entorno |
| **DDL-Auto** | `update` | `validate` |
| **Show SQL** | `true` | `false` |

---

## ⚠️ Importante

> [!WARNING]
> **NO hagas commit de contraseñas**
> 
> - `application.properties` (local) → puede tener contraseñas (solo para desarrollo)
> - `application-prod.properties` → usa variables `${VARIABLE}` (SIN valores hardcoded)
> - Render → configuras las variables en el Dashboard (no en el código)

---

## 🚀 Próximos Pasos

1. ✅ Ya creaste archivos: `application-prod.properties` 
2. ⏭️ Sigue la guía [RENDER-DEPLOY-GUIDE.md](file:///G:/Desarrollo/Página/PaginaVerificación/springboot-web/RENDER-DEPLOY-GUIDE.md)
3. Cuando llegues a "Configurar Variables de Entorno", usa los valores de este archivo

---

¡Tu proyecto YA está listo para Render! Solo falta subir a GitHub y configurar el Web Service 🎉
