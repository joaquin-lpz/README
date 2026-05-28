# 📑 Práctica de Investigación: Markdown e Inteligencia Artificial (LLM)
**Estudiante:** Joaquin López Guirao  
**Curso:** Técnico Superior en Administración de Sistemas Informáticos en Red (ASIR)

---

## 1. Introducción y Metodología
En esta investigación práctica se analiza el impacto del lenguaje de marcas Markdown en la interacción con Modelos de Lenguaje Grandes (LLMs). Para las pruebas y la experimentación se han seleccionado dos herramientas distintas:
1. **Gemini (Google)**
2. **ChatGPT (OpenAI)**

A continuación, se detalla el diseño de los prompts estructurados, las respuestas obtenidas y un análisis comparativo de su rendimiento.

---

## 2. Creación de Prompts en Markdown y Comparación de Resultados

### 🔹 Prompt 1: Creación de un Script de Automatización
A continuación se presenta el prompt diseñado estructuradamente en Markdown enviado a ambas IA.

```markdown
# Prompt: Generación de Script de Backup

## Rol
Actúa como un Administrador de Sistemas Linux Senior experto en Bash Scripting.

## Contexto
Tengo un servidor de producción Ubuntu Server 22.04 LTS. Necesito realizar una copia de seguridad diaria de la carpeta `/var/www/html` y de una base de datos MySQL llamada `prod_db`.

## Objetivo
Generar un script en Bash que:
1. Comprima la carpeta `/var/www/html` en un archivo `.tar.gz`.
2. Realice un volcado (*dump*) de la base de datos MySQL.
3. Guarde ambos archivos en la ruta `/backup` organizados por fecha (`AAAA-MM-DD`).
4. Elimine los backups con más de 7 días de antigüedad.

## Formato de salida
Proporciona el script dentro de un único bloque de código Bash con comentarios claros explicativos, seguido de una lista de 3 pasos rápidos para programarlo en el `cron`.
```
## 🟢RESPUESTA DE GEMINI
```markdown
#!/bin/bash
# Backup Script - ASIR
BACKUP_DIR="/backup"
FECHA=$(date +%Y-%m-%d)
DESTINO="$BACKUP_DIR/$FECHA"

mkdir -p "$DESTINO"

# 1. Backup de archivos web
tar -czf "$DESTINO/web_backup.tar.gz" /var/www/html

# 2. Backup de Base de Datos
mysqldump -u root -p prod_db > "$DESTINO/db_backup.sql"

# 3. Limpieza de backups antiguos (más de 7 días)
find "$BACKUP_DIR" -type d -mtime +7 -exec rm -rf {} \;
```
## 🟢RESPUESTA CHATGPT
```markdown
#!/bin/bash
# --- CONFIGURACIÓN ---
BACKUP_PATH="/backup"
NOW=$(date +"%Y-%m-%d")
TARGET_DIR="$BACKUP_PATH/$NOW"
WEB_DIR="/var/www/html"
DB_NAME="prod_db"

# Crear directorio si no existe
mkdir -p "$TARGET_DIR"

# [Paso 1] Comprimir directorio web
tar -czf "$TARGET_DIR/web_$NOW.tar.gz" "$WEB_DIR" 2>/dev/null

# [Paso 2] Dump de MySQL
mysqldump --single-transaction "$DB_NAME" > "$TARGET_DIR/db_$NOW.sql"

# [Paso 3] Retención de 7 días
find "$BACKUP_PATH" -mindepth 1 -maxdepth 1 -type d -mtime +7 -exec rm -rf {} \;
```
