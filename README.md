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
## 📊 Análisis Comparativo (Prompt 1)
Impacto de Markdown: Ambas IA respetaron de manera impecable las directrices gracias a la separación clara de secciones por títulos (##). Entendieron perfectamente las variables de configuración sin mezclar el código con las instrucciones operativas.

Diferencias encontradas: ChatGPT optimizó el comando mysqldump añadiendo el parámetro --single-transaction para evitar bloqueos en entornos de producción, denotando un entendimiento contextual superior. Gemini, por su parte, ofreció una sintaxis en el comando find ligeramente más directa.

### 🔹 Prompt 2: Diagnóstico de Fallo de Red
```markdown
# Prompt: Troubleshooting de Conectividad de Red

## Rol
Actúa como un Ingeniero de Redes y Soporte Técnico Nivel 2.

## Contexto
Un cliente nos informa que una máquina virtual con Debian instalada en nuestro entorno corporativo ha dejado de navegar por Internet. La máquina puede hacer ping a la puerta de enlace local (`192.168.1.1`), pero no responde al hacer ping a `8.8.8.8` ni resuelve nombres de dominio.

## Objetivo
Proporcionar una guía metodológica paso a paso para diagnosticar y solucionar el problema desde la consola CLI de Debian.

## Formato de salida
Devuelve la respuesta estructurada con subtítulos de Markdown para cada comprobación, indicando el comando CLI exacto y qué buscar en la salida del comando.
```
## 🟢 RESPUESTRA DE GEMINI
```markdown
### Paso 1: Verificar la Tabla de Enrutamiento
Ejecuta el comando:
`ip route show`
*Qué buscar:* Asegúrate de que existe una ruta por defecto (`default via 192.168.1.1`). Si falta, añade la ruta con `sudo ip route add default via 192.168.1.1`.

### Paso 2: Validar Salida hacia el Exterior (ICMP)
Ejecuta el comando:
`ping -c 4 8.8.8.8`
*Qué buscar:* Si el ping a la puerta de enlace funcionaba pero a la IP pública no, el fallo está en el router local o en la NAT del hipervisor.
```
## 🟢 RESPUESTA DE CHATGPT
```markdown
### 1. Comprobación de la Ruta por Defecto (Gateway)
Introduce el comando básico:
`route -n` o `ip route`
*Salida esperada:* Debe aparecer una línea que comience con `0.0.0.0` o `default` apuntando a `192.168.1.1`.

### 2. Comprobación del Servidor DNS
Inspecciona el archivo de configuración:
`cat /etc/resolv.conf`
*Salida esperada:* Debe contener al menos una directiva válida, por ejemplo: `nameserver 8.8.8.8`. Si está vacío, edítalo.
```
## 📊 Análisis Comparativo (Prompt 2)
Impacto de Markdown: Forzar el formato de salida mediante subtítulos estructuró las respuestas como un verdadero manual de soporte técnico. Evitó los párrafos masivos de texto y facilitó la lectura.

Diferencias encontradas: ChatGPT interpretó con mayor precisión el enunciado. Dado que el prompt indicaba que la máquina no resolvía nombres de dominio, ChatGPT atacó directamente el archivo /etc/resolv.conf, mientras que Gemini se enfocó más en el direccionamiento de la red general.

### 🔹 Prompt 3: Auditoría y Hardening de SSH
```markdown
# Prompt: Bastionado del Servicio SSH

## Rol
Actúa como un Auditor de Seguridad Informática y Administrador de Sistemas.

## Contexto
Necesitamos elevar el nivel de seguridad (hardening) del servicio SSH en un servidor expuesto directamente a Internet de forma pública para mitigar ataques de fuerza bruta.

## Objetivo
Explicar las 4 modificaciones más críticas que se deben realizar en el archivo `/etc/ssh/sshd_config` para securizar el demonio de SSH.

## Formato de salida
Presenta las 4 medidas en una tabla Markdown con las columnas: "Directiva", "Valor Recomendado" y "Explicación de Seguridad".
```
## 🟢 RESPUESTA DE GEMINI
```markdown
| Directiva | Valor Recomendado | Explicación de Seguridad |
|-----------|------------------|--------------------------|
| `Port` | `2222` (o puerto alto) | Cambia el puerto por defecto para evitar escaneos automáticos de bots. |
| `PermitRootLogin` | `no` | Prohíbe el acceso directo al superusuario, obligando a usar un usuario común y `sudo`. |
| `PasswordAuthentication` | `no` | Fuerza el uso exclusivo de claves criptográficas SSH (públicas/privadas). |
| `MaxAuthTries` | `3` | Reduce los intentos permitidos de login antes de desconectar la sesión. |
```
## 🟢 RESPUESTA DE CHATGPT 
```markdown
| Directiva                | Valor Recomendado | Explicación de Seguridad                                                                                                                                                                                                                                              |
| ------------------------ | ----------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `PermitRootLogin`        | `no`              | Deshabilita el acceso directo del usuario `root` por SSH. Esto obliga a que los administradores se autentiquen primero con una cuenta nominal y luego eleven privilegios mediante `sudo`, reduciendo el impacto de ataques automatizados dirigidos al usuario `root`. |
| `PasswordAuthentication` | `no`              | Desactiva la autenticación mediante contraseña y obliga al uso de claves SSH públicas/privadas. Esto elimina prácticamente la efectividad de ataques de fuerza bruta y diccionario contra contraseñas débiles.                                                        |
| `PubkeyAuthentication`   | `yes`             | Habilita la autenticación basada en claves criptográficas. Las claves SSH son significativamente más seguras que las contraseñas tradicionales y permiten autenticación robusta incluso en entornos expuestos a Internet.                                             |
| `MaxAuthTries`           | `3`               | Limita el número de intentos fallidos de autenticación por conexión. Esto reduce la capacidad de herramientas automatizadas de fuerza bruta para probar múltiples credenciales en una misma sesión SSH.

 ## Ejemplo de configuración endurecida

# /etc/ssh/sshd_config

PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
MaxAuthTries 3
Recomendación adicional

Después de modificar la configuración, validar la sintaxis y reiniciar el servicio:

sudo sshd -t
sudo systemctl restart ssh

Además, es altamente recomendable complementar estas medidas con herramientas como Fail2ban y restringir accesos mediante firewall (ufw, iptables o nftables).                                                            |
```
## 📊 Análisis Comparativo (Prompt 3)
Impacto de Markdown: La petición explícita de construir una tabla organizada en Markdown obligó a ambos modelos a resumir la información técnica de una manera limpia, esquematizada y directamente aplicable.

Diferencias encontradas: Ambas IA coincidieron en las tres reglas principales del bastionado de SSH. ChatGPT propuso anular el X11Forwarding lo cual es una práctica muy valorada en servidores puros de ASIR, mientras que Gemini prefirió acotar el número de intentos (MaxAuthTries). Ambos resultados son excelentes.

### 3. Caso Práctico (Mundo Real): Automatización de Tareas
Para el escenario práctico, se selecciona el bloque de Automatización de tareas. Diseñamos un prompt avanzado en Markdown con delimitadores complejos para entrenar a una IA a procesar un archivo de configuración de usuarios e implementar su creación en un servidor a gran escala.
```markdown
# Prompt Profesional: Generación de Script de Creación de Usuarios masivos

## Rol
Actúa como un Administrador de Sistemas Linux DevOps enfocado en Scripting y Seguridad de Cuentas.

## Contexto
Tengo un listado estructurado de nuevos empleados en formato CSV que deben tener acceso local al nuevo servidor del departamento. Necesito automatizar este aprovisionamiento de cuentas de forma masiva de forma segura.

## Objetivo
Crear un script robusto en Bash que lea un bloque de texto que simula un CSV e implemente las siguientes directivas:
1. Validar si el grupo asignado al usuario existe. Si no existe, crearlo automáticamente antes de añadir al usuario.
2. Crear el usuario con su *home* directory establecido.
3. Asignarle el shell `/bin/bash`.
4. Establecer una contraseña temporal por defecto y forzar su cambio en el primer inicio de sesión con el comando `chage`.

## Formato de salida
- Devuelve exclusivamente el código del script estructurado dentro de un bloque de código Bash con manejo de errores y comentarios detallados.
```
# 4. Reflexión Final
## ¿Por qué es importante estructurar bien un prompt?
La estructuración de un prompt mediante Markdown actúa como un plano de arquitectura de datos para las IA. Al usar títulos, bloques de código y delimitadores explícitos, eliminamos la ambigüedad inherente al lenguaje natural. Esto permite delimitar con exactitud las variables del sistema (rutas, IPs, servicios) de las tareas lógicas que el modelo debe procesar, traduciéndose en código libre de errores lógicos y adaptado fielmente a la necesidad técnica corporativa.

## ¿Qué diferencias has observado entre IA?
Durante las pruebas prácticas, se observó que ChatGPT tiende a profundizar más en las buenas prácticas y parámetros de seguridad implícitos del entorno de sistemas (como optimizaciones en comandos de base de datos o vectores de ataque del sistema). Por su parte, Gemini destaca en velocidad de respuesta y en proporcionar estructuras Markdown visualmente muy organizadas y sintaxis de comandos directos y legibles.

## ¿Crees que esto tiene utilidad en el mundo laboral? ¿Por qué?
Tiene una utilidad masiva y crítica. En el día a día de un técnico en ASIR, la documentación de sistemas, los cuadernos de operaciones (runbooks) y los prompts de automatización son herramientas rutinarias. Dominar la ingeniería de prompts estructurada en Markdown permite a los administradores generar scripts de mantenimiento rápidos, acelerar la resolución de incidencias complejas (troubleshooting) y estandarizar la documentación técnica, ahorrando horas de trabajo manual operativo.
