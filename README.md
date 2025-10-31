Instalación de Odoo en Render


- Fase 1: Preparar el repositorio

  codigos:

  Dockerfile:
  
 # Imagen base Odoo 17
FROM odoo:17
# (Opcional) módulos propios
COPY ./extra-addons /mnt/extra-addons
# Puerto HTTP de Odoo
EXPOSE 8069
# Puerto por defecto de PostgreSQL
ENV PGPORT=5432
# 1) Inicializa la BD indicada en $PGDATABASE si está vacía (stop-after-init)
# 2) Después arranca el servidor normalmente
#
# NOTA: usamos $PGDATABASE para que la inicialización vaya contra esa BD
# y --db-filter la fije para evitar que Odoo “coja” otra por error.
CMD ["bash","-lc", "\
echo '==> Checking/initializing DB $PGDATABASE' && \
odoo -d $PGDATABASE -i base --without-demo=all \
--db_host=$PGHOST --db_port=$PGPORT \
--db_user=$PGUSER --db_password=$PGPASSWORD \
--addons-path=/usr/lib/python3/dist-packages/odoo/addons,/mnt/extra-addons \
--stop-after-init || true; \
echo '==> Starting Odoo server' && \
odoo --db_host=$PGHOST --db_port=$PGPORT \
--db_user=$PGUSER --db_password=$PGPASSWORD \
--addons-path=/usr/lib/python3/dist-packages/odoo/addons,/mnt/extra-addons \
--db-filter=$PGDATABASE \
--dev=all"]

Dockerfile end:

Manifest.py



Manifest.py end

Organiza tu repositorio con la siguiente estructura:

(en público o cuenta enlazada)

Extra - addons / Dockerfile(ruta) / read.me 

Extra - addons --> Dummy module ---> Init_.py, manifest_.py (comandos)

Extra - addons --> .gitkeep


Asegúrate de incluir un Dockerfile configurado para Odoo y un archivo .env. con las variables necesarias como DB_HOST, DB_NAME, DB_USER, DB_PASSWORD y DB_PORT.

- Fase 2: Crear una cuenta en Render

Accede a https://render.com

Crea una cuenta o inicia sesión con tu cuenta de GitHub

- Fase 3: Crear un servicio web en Render

Dentro del panel selecciona New

Elige Web Service

Render pedirá conectar un repositorio

- Fase 4: Conectar GitHub al servicio

En Public Git Repository ingresa la URL de tu repositorio, en mi caso:

https://github.com/notseergei2024/odooquests/edit/main/README.md

Render reconocerá el repositorio y mostrará configuración automática

Selecciona el tipo Docker

Selecciona la región Frankot

Selecciona el plan Free 

- Fase 5: Crear la base de datos

Odoo requiere PostgreSQL para funcionar.

Pasos recomendados en Render:

Crea una nueva base PostgreSQL desde el panel

Copia las credenciales que te proporciona

Dentro del servicio web en Render configura las variables de entorno

Variables: 

DB_HOST

DB_NAME 

DB_USER

DB_PASSWORD

- Fase 6: Realizar el despliegue

Haz clic en Deploy Web Service

Render comenzará a construir e iniciar Odoo

La primera vez puede tardar algunos minutos

Al finalizar tendrás una URL como:
https://tu-app-odoo.onrender.com

