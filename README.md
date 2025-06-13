# Agro_IoT_vCERO

# 🌱 Sistema de Monitoreo Ambiental con FastAPI y Raspberry Pi

Este proyecto implementa un backend en FastAPI que permite la **recolección, almacenamiento, visualización y respaldo de datos ambientales** enviados por un microcontrolador (ESP32). Se ejecuta en una Raspberry Pi 4, almacena datos en una base de datos SQLite y genera gráficas visuales en tiempo real.

---

## 🧰 Tecnologías utilizadas

- **Python 3.10+**
- **FastAPI** (backend web)
- **SQLite** (almacenamiento local)
- **Matplotlib** y **Plotly** (visualización de datos)
- **Pandas** (manejo de DataFrames)
- **rclone** *(opcional)* para respaldos en Google Drive
- **ESP32** como nodo de envío de datos (formato JSON)

---

## ⚙️ Funcionalidades principales

| Ruta                  | Método | Descripción |
|-----------------------|--------|-------------|
| `/`                   | GET    | Retorna un mensaje básico para confirmar que el servidor está activo |
| `/datos`              | POST   | Recibe datos desde el ESP32 y los guarda en `datos_riego.db`. También registra la temperatura actual del CPU de la RPi. |
| `/datos`              | GET    | Devuelve el último JSON recibido para inspección rápida |
| `/visualizar`         | GET    | Genera una imagen PNG con gráficas no interactivas (matplotlib) de las variables clave, y la muestra en HTML. Actualiza cada 30 s |
| `/monitoreo`          | GET    | Muestra una versión **interactiva** (plotly) de las gráficas, organizadas en cuadrícula. Se puede elegir cuántos registros ver (`?limite=100/500/1000`) |
| `/enviar?key=XXXX`    | GET    | Ejecuta un script de respaldo o envío de datos por correo (`SCRIPT_PATH`) validando una clave secreta. Muestra una imagen del resultado |

---

## 📦 Variables monitoreadas (base de datos `registros`)

- `T_amb`, `H_amb` → Temperatura y humedad ambiente
- `T_suelo`, `H_suelo`, `H_suelo_mV` → Datos del suelo
- `T_int`, `Gas` → Temperatura interna y gases
- `Flow_*` → Caudalímetros S401, S201, Aichi
- `voltaje`, `corriente` → Monitoreo eléctrico
- `TIME_LOG` → Marca de tiempo enviada
- `Tiempo_*` → Tiempos del sistema de riego
- `Temp_RPi_C` → Temperatura de la CPU de la Raspberry Pi

---

## 🖼️ Visualización

### `/visualizar`
- Genera una imagen `.png` estática con `matplotlib`
- Organizada en subplots
- Se actualiza automáticamente cada 30 s

### `/monitoreo`
- Gráficas **interactivas** con `plotly`
- Cuadrícula con 3 columnas
- Tooltip, zoom, selección de variables
- Selector desplegable de registros (`10`, `50`, `100`, `500`, `1000`)
- Autoactualización cada 60 s

---

## 🔐 Seguridad

- La ruta `/enviar` está protegida con una clave `CLAVE_SECRETA`.
- Asegura que solo usuarios autorizados puedan ejecutar el script externo (`enviar_bd.sh`).

---

## 🧪 Ejecución

Para iniciar el servidor:

bash
uvicorn main:app --host 0.0.0.0 --port 8123 --reload

Si usas screen (recomendado en RPi):
screen -S fastapi
uvicorn main:app --host 0.0.0.0 --port 8123 --reload
Luego Ctrl + A, D para dejarlo en segundo plano

## 🧠 Autor
Héctor A.
Proyecto de monitoreo ambiental IoT para fines educativos, experimentales y de aplicación en entornos rurales.
Incluye trazabilidad, riego inteligente y respaldo local/remoto.
