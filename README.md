# 🐘 Entorno de Aprendizaje PostgreSQL con Docker

Una configuración de PostgreSQL amigable para principiantes, diseñada para estudiantes que están aprendiendo SQL y gestión de bases de datos en **Ubuntu 22.04 y 24.04**. ¡Este repositorio proporciona todo lo que necesitas para empezar a practicar SQL usando solo la terminal!

---

## 📋 Lo Que Necesitas

Este tutorial está diseñado para **Ubuntu 22.04 LTS** o **Ubuntu 24.04 LTS**.

### Instalar Docker y Docker Compose

Abre una terminal y ejecuta los siguientes comandos:

```bash
# Actualizar el sistema
sudo apt update && sudo apt upgrade -y

# Instalar dependencias necesarias
sudo apt install -y ca-certificates curl gnupg lsb-release

# Agregar la clave GPG oficial de Docker
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# Configurar el repositorio de Docker
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Instalar Docker Engine y Docker Compose
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Agregar tu usuario al grupo docker (para no usar sudo)
sudo usermod -aG docker $USER

# Aplicar los cambios (o cierra sesión y vuelve a entrar)
newgrp docker
```

Verifica la instalación:

```bash
docker --version
docker compose version
```

### Instalar pgAdmin

```bash
# Instalar pgAdmin desde el repositorio oficial
curl -fsS https://www.pgadmin.org/static/packages_pgadmin_org.pub | sudo gpg --dearmor -o /usr/share/keyrings/packages-pgadmin-org.gpg

sudo sh -c 'echo "deb [signed-by=/usr/share/keyrings/packages-pgadmin-org.gpg] https://ftp.postgresql.org/pub/pgadmin/pgadmin4/apt/$(lsb_release -cs) pgadmin4 main" > /etc/apt/sources.list.d/pgadmin4.list'

sudo apt update
sudo apt install -y pgadmin4-desktop
```

> **💡 Consejo para principiantes:** Docker es como un contenedor virtual para aplicaciones. ¡Te permite ejecutar PostgreSQL sin instalarlo directamente en tu sistema!

---

## 🚀 Inicio Rápido

### Paso 1: Clonar e Iniciar

Clona este repositorio y navega a la carpeta:

```bash
git clone <url-de-tu-repositorio>
cd compose-postgres
```

Inicia la base de datos PostgreSQL:

```bash
docker compose up -d
```

**¿Qué acaba de pasar?**
- Docker descargó PostgreSQL (si era necesario)
- Creó una base de datos llamada `exampledb`
- Inició PostgreSQL en el puerto 5432
- Tus datos están almacenados de forma segura en un volumen de Docker

Para verificar que está ejecutándose:

```bash
docker compose ps
```

Deberías ver el contenedor `postgres_local` con estado "Up".

---

## 🔌 Conectar con pgAdmin

**pgAdmin** es una herramienta visual que hace que trabajar con bases de datos sea más fácil que usar la línea de comandos.

### Pasos de Conexión:

1. Abre **pgAdmin** en tu computadora
2. Haz clic derecho en **Servers** (barra lateral izquierda) → **Create** → **Server**
3. En la pestaña **General**:
   - **Name**: `Mi Base de Datos de Aprendizaje` (o cualquier nombre que prefieras)
4. En la pestaña **Connection**:
   - **Host name/address**: `localhost`
   - **Port**: `5432`
   - **Maintenance database**: `exampledb`
   - **Username**: `admin`
   - **Password**: `admin123`
   - ✅ Marca **Save password**
5. Haz clic en **Save**

¡Tu servidor de base de datos debería aparecer ahora en la barra lateral izquierda! 🎉

---

## 📚 Tus Primeras Consultas SQL

Una vez conectado en pgAdmin, prueba estas consultas amigables para principiantes:

### Crear una tabla:

```sql
CREATE TABLE estudiantes (
    id SERIAL PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    edad INTEGER,
    email VARCHAR(100) UNIQUE
);
```

### Insertar algunos datos:

```sql
INSERT INTO estudiantes (nombre, edad, email) VALUES
('Ana García', 20, 'ana@ejemplo.com'),
('Carlos Pérez', 22, 'carlos@ejemplo.com'),
('María López', 21, 'maria@ejemplo.com');
```

### Consultar los datos:

```sql
SELECT * FROM estudiantes;
```

### Filtrar resultados:

```sql
SELECT nombre, email FROM estudiantes WHERE edad > 20;
```

---

## 📁 Scripts SQL Automáticos

¿Quieres ejecutar scripts SQL automáticamente cuando la base de datos inicie?

1. Coloca tus archivos `.sql` en la carpeta `init/`
2. Detén y elimina la base de datos actual:
   ```bash
   docker compose down -v
   ```
3. Inicia de nuevo:
   ```bash
   docker compose up -d
   ```

¡Tus scripts se ejecutarán automáticamente! Esto es perfecto para crear tablas e insertar datos de prueba.

---

## 🛠️ Comandos Útiles

| Comando | Qué hace |
|---------|----------|
| `docker compose up -d` | Inicia la base de datos en segundo plano |
| `docker compose down` | Detiene la base de datos (mantiene los datos) |
| `docker compose down -v` | Detiene y **elimina todos los datos** (inicio limpio) |
| `docker compose ps` | Verifica si el contenedor está ejecutándose |
| `docker compose logs postgres` | Ver los logs de la base de datos |
| `docker compose exec postgres psql -U admin -d exampledb` | Conectar por línea de comandos |

---

## ❓ Solución de Problemas

### "Port 5432 is already in use" (El puerto 5432 ya está en uso)
Otra instancia de PostgreSQL está ejecutándose. Puedes:
- Detenerla: `sudo systemctl stop postgresql`
- O cambiar el puerto en `docker-compose.yml`: `"5433:5432"`

### "Connection refused" (Conexión rechazada) en pgAdmin
- Verifica si el contenedor está ejecutándose: `docker compose ps`
- Reinicia el contenedor: `docker compose restart`
- Revisa los logs: `docker compose logs postgres`

### "Quiero empezar completamente de cero"
Elimina todo y comienza de nuevo:
```bash
docker compose down -v
docker compose up -d
```

### No veo mis scripts de init ejecutándose
Los scripts de init solo se ejecutan la **primera vez** que se crea la base de datos. Para volver a ejecutarlos:
```bash
docker compose down -v  # Esto elimina la base de datos
docker compose up -d    # Esto la crea de nuevo
```

---

## 📖 Recursos de Aprendizaje

**Tutoriales Gratuitos de SQL:**
- [PostgreSQL Tutorial](https://www.postgresqltutorial.com/) - Amigable para principiantes con ejemplos
- [W3Schools SQL](https://www.w3schools.com/sql/) - Ejercicios interactivos
- [SQLBolt](https://sqlbolt.com/) - Aprende SQL con lecciones interactivas
- [Tutorial SQL en Español](https://www.w3schools.com/sql/default.asp) - Versión en español

**Ayuda con pgAdmin:**
- [Documentación de pgAdmin](https://www.pgadmin.org/docs/)
- [Video: Primeros Pasos con pgAdmin](https://www.youtube.com/results?search_query=pgadmin+tutorial+español)

**Datasets de Práctica:**
- [Bases de Datos de Ejemplo](https://www.postgresqltutorial.com/postgresql-getting-started/postgresql-sample-database/)

---

## ⚠️ Notas Importantes

- **¡Esto es solo para aprender!** No uses estas credenciales (`admin`/`admin123`) en proyectos reales
- Tus datos persisten incluso después de detener los contenedores (a menos que uses la bandera `-v`)
- Cada vez que uses `docker compose down -v`, todas tus tablas y datos se eliminan
- Para proyectos reales, usa variables de entorno para las contraseñas (crea un archivo `.env`)

---

## 🎓 ¿Qué Sigue?

Una vez que te sientas cómodo con SQL básico:

1. Aprende sobre **JOINs** para conectar múltiples tablas
2. Practica **índices** para hacer las consultas más rápidas
3. Prueba **vistas** y **procedimientos almacenados**
4. Aprende sobre **transacciones** y **restricciones**
5. Explora **backup y restore** con `pg_dump`

¡Feliz aprendizaje! 🚀📊
