# 🐳 Docker - Guía de Uso

## 📋 Archivos Creados

He creado los siguientes archivos para dockerizar tu aplicación:

1. **Dockerfile** - Configuración multi-stage para construir la imagen
2. **.dockerignore** - Excluye archivos innecesarios del build
3. **docker-compose.yml** - Orquestación de la app con PostgreSQL

---

## 🚀 Opción 1: Ejecutar solo la aplicación (Docker)

### Construir la imagen
```bash
docker build -t spring-boot-docker .
```

### Ejecutar el contenedor (conectándose a PostgreSQL local)
```bash
docker run -p 8080:8080 ^
  -e SPRING_DATASOURCE_URL=jdbc:postgresql://host.docker.internal:5432/AsociacionTitular ^
  -e SPRING_DATASOURCE_USERNAME=postgres ^
  -e SPRING_DATASOURCE_PASSWORD=jizg2222001 ^
  --name springboot-app ^
  spring-boot-docker
```

**Nota:** Usa `host.docker.internal` en lugar de `localhost` para conectarte a servicios que corren en tu máquina host desde dentro del contenedor Docker.

---

## 🎯 Opción 2: Ejecutar con Docker Compose (Recomendado)

Esta opción levanta automáticamente:
- ✅ Contenedor de PostgreSQL
- ✅ Tu aplicación Spring Boot
- ✅ Red Docker para comunicación
- ✅ Volumen persistente para la base de datos

### Comandos básicos

#### Iniciar todos los servicios
```bash
docker-compose up -d
```

#### Ver logs en tiempo real
```bash
docker-compose logs -f app
```

#### Detener los servicios
```bash
docker-compose down
```

#### Detener y eliminar volúmenes (⚠️ elimina los datos de la BD)
```bash
docker-compose down -v
```

#### Reconstruir la imagen
```bash
docker-compose up -d --build
```

---

## 🔍 Comandos Útiles

### Ver contenedores en ejecución
```bash
docker ps
```

### Ver logs de un contenedor
```bash
docker logs springboot-app
docker logs springboot-postgres
```

### Acceder al shell del contenedor
```bash
docker exec -it springboot-app sh
```

### Acceder a PostgreSQL
```bash
docker exec -it springboot-postgres psql -U postgres -d AsociacionTitular
```

### Ver uso de recursos
```bash
docker stats
```

### Eliminar imagen
```bash
docker rmi spring-boot-docker
```

---

## ⚙️ Variables de Entorno

Puedes personalizar las siguientes variables al ejecutar el contenedor:

| Variable | Descripción | Valor por defecto |
|----------|-------------|-------------------|
| `SPRING_DATASOURCE_URL` | URL de la base de datos | - |
| `SPRING_DATASOURCE_USERNAME` | Usuario de PostgreSQL | postgres |
| `SPRING_DATASOURCE_PASSWORD` | Contraseña de PostgreSQL | jizg2222001 |
| `SPRING_PROFILES_ACTIVE` | Perfil de Spring | prod |
| `JAVA_OPTS` | Opciones JVM | -Xms256m -Xmx512m |

---

## 🛠️ Troubleshooting

### El contenedor no inicia
```bash
docker logs springboot-app
```

### No se puede conectar a la base de datos
1. Verifica que PostgreSQL esté corriendo:
   ```bash
   docker ps | grep postgres
   ```
2. Verifica la conectividad:
   ```bash
   docker exec -it springboot-app ping postgres
   ```

### Limpiar todo y empezar de nuevo
```bash
docker-compose down -v
docker system prune -a
docker-compose up -d --build
```

---

## 📌 Notas Importantes

1. **Seguridad**: Las credenciales están en texto plano en `docker-compose.yml`. Para producción, usa variables de entorno o secretos de Docker.

2. **Perfiles de Spring**: Considera crear un `application-prod.properties` específico para producción.

3. **Puerto 8080**: Asegúrate de que el puerto 8080 no esté en uso antes de ejecutar los contenedores.

4. **Persistencia**: Los datos de PostgreSQL se guardan en un volumen Docker (`postgres_data`). No se perderán al detener los contenedores, solo si usas `docker-compose down -v`.

---

## 🎉 Acceso a la Aplicación

Una vez que los contenedores estén corriendo:

- **Aplicación**: http://localhost:8080
- **PostgreSQL**: localhost:5432

---

¡Tu aplicación Spring Boot está lista para correr en Docker! 🚀
