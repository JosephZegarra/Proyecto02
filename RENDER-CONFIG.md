# 🎯 TUS VARIABLES DE ENTORNO PARA RENDER

## ✅ Credenciales de tu Base de Datos Render

Estas son las variables exactas que debes configurar cuando crees el **Web Service** en Render:

---

## 📋 Variables de Entorno (Copy-Paste)

### Base de Datos (OBLIGATORIAS)

```bash
SPRING_DATASOURCE_URL=jdbc:postgresql://dpg-d54rvaali9vc73en9b6g-a/asociaciontitular
SPRING_DATASOURCE_USERNAME=asociaciontitular_user
SPRING_DATASOURCE_PASSWORD=dyOCUkqKRSKQbd8kgj811WeKAwKbv3Y9
```

### Spring Boot (OBLIGATORIAS)

```bash
SPRING_PROFILES_ACTIVE=prod
JAVA_OPTS=-Xms256m -Xmx512m
```

### Email (OPCIONAL - solo si quieres envío de correos)

```bash
SPRING_MAIL_HOST=smtp.gmail.com
SPRING_MAIL_PORT=587
SPRING_MAIL_USERNAME=joseph.zegarra.g@gmail.com
SPRING_MAIL_PASSWORD=eniw nfxm cpit ngme
```

---

## 📝 Cómo Agregar en Render

Cuando crees el **Web Service**:

1. Ve a la sección **"Environment Variables"**
2. Haz clic en **"Add Environment Variable"** para cada una
3. **Copia y pega** exactamente los valores de arriba

**Tabla de Variables:**

| Key | Value |
|-----|-------|
| `SPRING_DATASOURCE_URL` | `jdbc:postgresql://dpg-d54rvaali9vc73en9b6g-a/asociaciontitular` |
| `SPRING_DATASOURCE_USERNAME` | `asociaciontitular_user` |
| `SPRING_DATASOURCE_PASSWORD` | `dyOCUkqKRSKQbd8kgj811WeKAwKbv3Y9` |
| `SPRING_PROFILES_ACTIVE` | `prod` |
| `JAVA_OPTS` | `-Xms256m -Xmx512m` |

---

## 🔍 Información de tu Base de Datos

```
Database Name: asociaciontitular
User: asociaciontitular_user
Password: dyOCUkqKRSKQbd8kgj811WeKAwKbv3Y9
Host: dpg-d54rvaali9vc73en9b6g-a
Region: Oregon (US West)

Internal URL (para la app):
postgresql://asociaciontitular_user:dyOCUkqKRSKQbd8kgj811WeKAwKbv3Y9@dpg-d54rvaali9vc73en9b6g-a/asociaciontitular

External URL (para conectarte desde tu PC):
postgresql://asociaciontitular_user:dyOCUkqKRSKQbd8kgj811WeKAwKbv3Y9@dpg-d54rvaali9vc73en9b6g-a.oregon-postgres.render.com/asociaciontitular
```

---

## ✅ Listo para Deploy

Con estas variables configuradas:
- ✅ Tu app se conectará a PostgreSQL en Render
- ✅ Usará el perfil de producción
- ✅ Tendrá la configuración optimizada para la nube

---

¡Guarda este archivo! Lo necesitarás cuando crees el Web Service 🚀
