# XML Semantic Integration API
Backend desarrollado en Python (FastAPI) para la validación, orquestación y actualización de documentos XML basados en esquemas XSD institucionales (TecNM, PRODEP, Rizoma). El sistema interactúa con un servidor Nginx configurado para servir los esquemas estáticos y manejar políticas de CORS.

## Stack Tecnológico y Librerías

El núcleo de la API (`main.py`) requiere las siguientes librerías para manejar el enrutamiento, la validación de datos, la seguridad por tokens y la manipulación de archivos:

* **FastAPI:** Framework principal para la creación de la API REST.
* **Uvicorn:** Servidor ASGI necesario para ejecutar FastAPI en desarrollo y producción.
* **Pydantic:** Utilizado para la definición y validación de modelos de datos (`UserCredentials`, `UpdateXmlData`, etc.).
* **Python-Multipart:** Dependencia necesaria de FastAPI para procesar `UploadFile` y `File(...)` en el endpoint `/upload`.
* **PyJWT / dependencias de Auth:** (Implícito para las funciones `create_jwt_token` y `verify_jwt_from_cookie`).
* **Librerías estándar:** `xml.etree.ElementTree`, `json`, `os`, `re` (Nativas de Python, no requieren instalación adicional).

## Instalación del entorno virtual
1. Creación y activación del entorno
    ```bash
    python -m venv venv
    source venv/Scripts/activate  # En Windows
    ```
2. Instalación de librerías base
    ```bash
    python -m venv venv
    source venv/Scripts/activate  # En Windows
    ```
## Configuración del servidor Nginx
El ecosistema requiere que Nginx esté ejecutándose localmente en el puerto `8080` para servir los archivos físicos a los que FastAPI hace peticiones.

Debes asegurarte de que tu archivo `nginx.conf` incluya la directiva `autoindex on`; y defina los siguientes `alias` apuntando a tus directorios locales en el disco `C:/`:
```bash
server {
    listen       8080;
    server_name  localhost;
    autoindex on;
    # Exposición de esquemas y archivos Rizoma / SECIHTI
    location /SECIHTIServ/ {
        alias "C:/servidores_locales/SECIHTIServ/";
        add_header Access-Control-Allow-Origin *;
    }
    # Exposición de esquemas y archivos TecNM
    location /TecNMServ/ {
        alias "C:/servidores_locales/TecNMServ/";
        add_header Access-Control-Allow-Origin *;
    }
    # Exposición de esquemas y archivos PRODEP
    location /PRODEPServ/ {
        alias "C:/servidores_locales/PRODEPServ/";
        add_header Access-Control-Allow-Origin *;
    }
}
```

## Ejecución del Proyecto
1. **Iniciar Nginx**: Asegúrate de que el servicio de Nginx esté corriendo y que las carpetas en `C:/servidores_locales/` existan y contengan los esquemas correspondientes.
2. **Iniciar FastApi**:
    Ejecuta el servidor ASGI apuntando al archivo principal de la aplicación:
        ```bash
        uvicorn main:app --reload --port 8000
        ```
3. **Documentación interactiva**: Una vez en ejecución, la documentación de Swagger estará disponible en `http://localhost:8000/docs`

## Arquitectura de la API
- **Autenticación**: Gestión de sesiones mediante cookies HTTPOnly para almacenar tokens JWT (`/login`, `/logout`). 