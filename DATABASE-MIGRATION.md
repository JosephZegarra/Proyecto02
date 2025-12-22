# 📦 Migración de Base de Datos - Docker y Render

## ✅ ¿Qué se ha configurado?

Tu proyecto ahora tiene los datos de tu base de datos local listos para ser usados en Docker y para desplegar en Render.

### Archivos Creados

1. **`database-backup.sql`** - Backup completo de tu base de datos `AsociacionTitular`
2. **`docker-init/01-init.sql`** - Script que Docker usará para inicializar PostgreSQL con tus datos
3. **`docker-compose.yml`** (actualizado) - Configurado para cargar datos automáticamente

---

## 🐳 Uso con Docker Local

### Primera vez (con datos)

```bash
# Detener y eliminar contenedores existentes Y volúmenes
docker-compose down -v

# Levantar con datos frescos de tu backup
docker-compose up -d
```

**¿Qué pasa?**
- PostgreSQL se crea vacío
- Automáticamente ejecuta `docker-init/01-init.sql`
- Tu contenedor tendrá TODOS tus datos (usuarios, roles, etc.)
- ✅ Ya puedes iniciar sesión con tus credenciales existentes

### Ver logs de inicialización

```bash
docker-compose logs postgres
```

### Verificar que los datos se cargaron

```bash
docker exec springboot-postgres psql -U postgres -d AsociacionTitular -c "SELECT COUNT(*) FROM socios;"
```

---

## ☁️ Uso para Render

**IMPORTANTE:** Render NO soporta docker-compose para múltiples servicios en el plan gratuito. Debes seguir este proceso:

### Paso 1: Crear PostgreSQL en Render

1. Ve a [Render Dashboard](https://dashboard.render.com/)
2. Clic en "New +" → "PostgreSQL"
3. Configuración:
   - **Name**: `asociacion-db` (o el nombre que prefieras)
   - **Database**: `AsociacionTitular`
   - **User**: `postgres` (o el que prefieras)
   - **Region**: Elige la más cercana
   - **PostgreSQL Version**: 18
   - **Plan**: Free

4. Clic en "Create Database"
5. **Espera 2-3 minutos** a que esté disponible

### Paso 2: Obtener Connection String

En el dashboard de tu PostgreSQL en Render, copia:
- **Internal Database URL** (para conexión desde tu app en Render)
- **External Database URL** (para importar datos desde tu PC)

Se verá algo así:
```
postgres://usuario:password@host-region.render.com/database
```

### Paso 3: Importar tus datos a Render

Desde tu PC local, ejecuta:

```powershell
# Usar el External Database URL que copiaste
psql "postgres://usuario:password@host.render.com/database" < database-backup.sql
```

**Alternativa si no tienes psql en PATH:**

```powershell
# Navega a la carpeta de PostgreSQL (ajusta la ruta según tu instalación)
cd "C:\Program Files\PostgreSQL\18\bin"

# Ejecuta el import
.\psql "postgres://usuario:password@host.render.com/database" < "G:\Desarrollo\Página\PaginaVerificación\springboot-web\database-backup.sql"
```

### Paso 4: Deploy de la App en Render

1. **Sube tu código a GitHub** (si no lo has hecho)
   ```bash
   git add .
   git commit -m "Added Docker configuration"
   git push origin main
   ```

2. En Render Dashboard:
   - Clic en "New +" → "Web Service"
   - Conecta tu repositorio GitHub
   - Configuración:
     - **Name**: `asociacion-app`
     - **Runtime**: Docker
     - **Branch**: main
     - **Plan**: Free

3. **Variables de Entorno** (en Render):
   ```
   SPRING_DATASOURCE_URL=<Internal Database URL>
   SPRING_DATASOURCE_USERNAME=postgres
   SPRING_DATASOURCE_PASSWORD=<tu password>
   SPRING_PROFILES_ACTIVE=prod
   JAVA_OPTS=-Xms256m -Xmx512m
   ```

4. Clic en "Create Web Service"

### Paso 5: Verificar Deploy

- La app tardará 5-10 minutos en el primer deploy
- Ve a la URL que Render te asigna: `https://tu-app.onrender.com`
- Intenta iniciar sesión con tus credenciales

---

## 🔄 Actualizar Datos

### En Docker Local

Si cambias datos y quieres crear un nuevo backup:

```powershell
# Exportar desde PostgreSQL local
pg_dump -U postgres -d AsociacionTitular --no-owner --no-acl --clean --if-exists -f database-backup.sql

# Copiar a docker-init
Copy-Item database-backup.sql docker-init/01-init.sql -Force

# Recrear contenedor
docker-compose down -v
docker-compose up -d
```

### En Render

Si actualizas datos y quieres sincronizar a Render:

```powershell
# 1. Exportar datos actualizados
pg_dump -U postgres -d AsociacionTitular --no-owner --no-acl --clean --if-exists -f database-backup.sql

# 2. Importar a Render
psql "<External Database URL de Render>" < database-backup.sql
```

---

## ⚠️ Notas Importantes

### Docker Local
- **Primera vez**: `docker-compose down -v` elimina datos anteriores
- Los scripts en `docker-init/` solo se ejecutan si el volumen está VACÍO
- Si el contenedor ya existe con datos, los scripts NO se ejecutarán

### Render
- PostgreSQL gratuito **expira en 90 días**
- Después necesitarás plan de pago (~$7/mes)
- **Haz backups regulares** con `pg_dump`
- La app gratuita se suspende tras 15 min de inactividad

---

## 🛠️ Troubleshooting

### Docker: "Los datos no se cargan"

```bash
# Asegúrate de eliminar el volumen
docker-compose down -v

# Verifica que el script existe
ls docker-init/

# Reconstruir todo
docker-compose up -d

# Ver logs de PostgreSQL
docker-compose logs postgres | grep "init"
```

### Render: "No puedo importar datos"

1. Verifica que tienes `psql` instalado
2. Usa el **External Database URL**, no el Internal
3. Si hay errores de permisos, usa `--no-owner --no-acl` en pg_dump
4. Intenta conectarte primero: `psql "<URL>" -c "SELECT 1;"`

---

## 📋 Resumen de Comandos Rápidos

```powershell
# Docker: Iniciar con datos frescos
docker-compose down -v && docker-compose up -d

# Docker: Ver datos cargados
docker exec springboot-postgres psql -U postgres -d AsociacionTitular -c "SELECT COUNT(*) FROM socios;"

# Exportar datos actualizados
pg_dump -U postgres -d AsociacionTitular --no-owner --no-acl --clean --if-exists -f database-backup.sql

# Importar a Render
psql "<External Database URL>" < database-backup.sql
```

---

¡Tu base de datos está lista para Docker y Render! 🚀
