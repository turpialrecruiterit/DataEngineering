# DataEngineering
Coder Project

Jorge López
Data Engineering

1era entrega:

import requests
import psycopg2

# URL base de la API y parámetros necesarios
base_url = "https://api.example.com/"
api_key = "your_api_key"

# Conexión a la base de datos de Redshift
conn = psycopg2.connect(
    host="your_host_name",
    database="your_database_name",
    user="your_user_name",
    password="your_password",
    port=5439 # puerto predeterminado de Redshift
)
cur = conn.cursor()

# Crear tabla en Redshift
cur.execute("""
CREATE TABLE IF NOT EXISTS ejemplo (
    columna1 INT,
    columna2 VARCHAR(50),
    columna3 DATE
);
""")

# Hacer solicitud a la API y extraer datos
response = requests.get(base_url, params={"api_key": api_key})
data = response.json()

# Insertar datos en la tabla de Redshift
for item in data:
    cur.execute("""
    INSERT INTO ejemplo (columna1, columna2, columna3)
    VALUES (%s, %s, %s);
    """, (item["valor_columna1"], item["valor_columna2"], item["valor_columna3"]))

# Confirmar los cambios y cerrar la conexión
conn.commit()
cur.close()
conn.close()

# 2da ENTREGA
# Jorge López

# Basado en el código desarrollado de la 1era Entrega,  los datos de la API son devueltos como una lista de diccionarios, donde cada diccionario representa un registro que contiene información de tres columnas (columna1, columna2 y columna3). Siendo esto correcto, el siguiente código debería adaptar el script para cargar los datos de la API en la tabla creada en Redshift:

import requests
import psycopg2

# URL base de la API y parámetros necesarios
base_url = "https://api.example.com/"
api_key = "your_api_key"

# Conexión a la base de datos de Redshift
conn = psycopg2.connect(
    host="your_host_name",
    database="your_database_name",
    user="your_user_name",
    password="your_password",
    port=5439 # puerto predeterminado de Redshift
)
cur = conn.cursor()

# Crear tabla en Redshift
cur.execute("""
CREATE TABLE IF NOT EXISTS ejemplo (
    columna1 INT,
    columna2 VARCHAR(50),
    columna3 DATE
);
""")

# Hacer solicitud a la API y extraer datos
response = requests.get(base_url, params={"api_key": api_key})
data = response.json()

# Insertar datos en la tabla de Redshift
for record in data:
    cur.execute("""
    INSERT INTO ejemplo (columna1, columna2, columna3)
    VALUES (%s, %s, %s);
    """, (record['columna1'], record['columna2'], record['columna3']))

# Confirmar los cambios y cerrar la conexión
conn.commit()
cur.close()
conn.close()

# He reemplazado item por record para hacer el código más fácil de entender. También he modificado la sección de inserción de datos en la tabla Redshift para que extraiga los valores de cada registro de la API y los inserte en las columnas correspondientes. 

3era entrega:

Para adaptar el script al entorno de Airflow y ejecutarlo dentro de un contenedor Docker, puedes seguir los siguientes pasos:
1.	Crea un archivo Python llamado my_dag.py (o el nombre que prefieras) que contendrá el código del flujo de trabajo (DAG) de Airflow.


PYTHON:
from airflow import DAG
from airflow.operators.python_operator import PythonOperator
from datetime import datetime
import requests
import psycopg2

def load_data_to_redshift():
    # URL base de la API y parámetros necesarios
    base_url = "https://api.example.com/"
    api_key = "your_api_key"

    # Conexión a la base de datos de Redshift
    conn = psycopg2.connect(
        host="your_host_name",
        database="your_database_name",
        user="your_user_name",
        password="your_password",
        port=5439  # puerto predeterminado de Redshift
    )
    cur = conn.cursor()

    # Crear tabla en Redshift
    cur.execute("""
        CREATE TABLE IF NOT EXISTS ejemplo (
            columna1 INT,
            columna2 VARCHAR(50),
            columna3 DATE
        );
    """)

    # Hacer solicitud a la API y extraer datos
    response = requests.get(base_url, params={"api_key": api_key})
    data = response.json()

    # Insertar datos en la tabla de Redshift
    for record in data:
        cur.execute("""
            INSERT INTO ejemplo (columna1, columna2, columna3)
            VALUES (%s, %s, %s);
        """, (record['columna1'], record['columna2'], record['columna3']))

    # Confirmar los cambios y cerrar la conexión
    conn.commit()
    cur.close()
    conn.close()

# Definir el flujo de trabajo (DAG) de Airflow
dag = DAG(
    'my_dag',
    description='Carga de datos desde API a Redshift',
    schedule_interval=None,
    start_date=datetime(2023, 5, 23),
    catchup=False
)

# Definir la tarea que ejecuta el código de carga de datos
load_data_task = PythonOperator(
    task_id='load_data_to_redshift',
    python_callable=load_data_to_redshift,
    dag=dag
)

# Establecer las dependencias entre tareas (en este caso, solo hay una tarea)
load_data_task

2.	Creación de  un archivo Dockerfile en el directorio del proyecto con el siguiente contenido:

FROM apache/airflow:2.1.4
COPY my_dag.py /opt/airflow/dags/my_dag.py
RUN pip install psycopg2 requests


3.	Construye la imagen de Docker ejecutando el siguiente comando en la terminal desde el directorio del proyecto:
docker build -t my_airflow .

4.	Ejecuta el contenedor Docker utilizando el siguiente comando:
docker run -d -p 8080:8080 -v /ruta/a/tu/directorio/dags:/opt/airflow/dags my_airflow

Asegúrate de reemplazar /ruta/a/tu/directorio/dags con la ruta local al directorio que contiene el archivo my_dag.py.

5.	Accede a la interfaz web de Airflow en tu navegador ingresando localhost:8080. Verás el DAG my_dag disponible en la interfaz de Airflow.
6.	Activa el DAG y verifica que se ejecuta correctamente dentro


#Importar el módulo smtplib para el envío de correos electrónicos. Asegúrate de tener configurados los detalles de tu servidor SMTP, como el servidor, el puerto, el usuario y la contraseña.
import smtplib


Importar el módulo smtplib para el envío de correos electrónicos. Asegúrate de tener configurados los detalles de tu servidor SMTP, como el servidor, el puerto, el usuario y la contraseña.

Define una función para enviar alertas por correo electrónico. Esta función recibirá el asunto y el cuerpo del mensaje como parámetros y utilizará el módulo smtplib para enviar el correo electrónico. Asegúrate de modificar los detalles del servidor SMTP en la función enviar_alerta_smtp según tus configuraciones.

def enviar_alerta_smtp(asunto, cuerpo):
    servidor_smtp = "tu_servidor_smtp"
    puerto_smtp = tu_puerto_smtp
    usuario_smtp = "tu_usuario_smtp"
    contraseña_smtp = "tu_contraseña_smtp"
    destinatario = "correo_destinatario"

    mensaje = f"Subject: {asunto}\n\n{cuerpo}"
    try:
        with smtplib.SMTP(servidor_smtp, puerto_smtp) as servidor:
            servidor.starttls()
            servidor.login(usuario_smtp, contraseña_smtp)
            servidor.sendmail(usuario_smtp, destinatario, mensaje)
        print("Alerta enviada correctamente por correo electrónico.")
    except Exception as e:
        print("Error al enviar la alerta por correo electrónico:", str(e))


Agrega la lógica para verificar los thresholds y enviar alertas cuando se superen. Puedes agregar esta lógica después de obtener los datos y antes de insertarlos en la tabla de Redshift. Aquí hay un ejemplo básico que compara el valor de la columna1 con un threshold predefinido y envía una alerta si se supera:

# Threshold para la columna1
threshold_columna1 = 100

# Insertar datos en la tabla de Redshift
for record in data:
    columna1 = record['columna1']
    if columna1 > threshold_columna1:
        asunto = "Alerta: Valor de columna1 supera el threshold"
        cuerpo = f"El valor de columna1 es {columna1}, que supera el threshold de {threshold_columna1}."
        enviar_alerta_smtp(asunto, cuerpo)

    cur.execute("""



